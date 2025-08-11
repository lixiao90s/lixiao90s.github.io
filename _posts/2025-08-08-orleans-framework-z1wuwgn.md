---
title: Orleans框架
date: '2025-08-08 14:31:23'
permalink: /post/orleans-framework-z1wuwgn.html
tags:
  - 服务器
  - .Net
  - c#
layout: post
published: true
---





基本概念理解

## Virtual Actor核心机制

- 自动管理生命周期
- **自动实例化**

  - **触发条件**：消息发送至未激活Actor时，运行时自动创建Grain实例并调用`ActivateAsync()`​
  - **资源回收**：闲置实例触发`DeactivateAsync()`​清理，内存释放

- **故障恢复**

  - **服务器宕机**：运行时在下一请求时自动在新节点重建Grain，无需应用层监控
- **位置透明**

  - **调用抽象**：通过Grain ID调用Actor，物理位置由运行时目录服务动态映射，缓存命中率\>90%
- **扩展模式**

  - **无状态Worker**：同一Grain多实例并行处理请求，适用无状态场景（如只读缓存）

## Grain

Grain是Orleans中的**虚拟Actor**，代表一个独立的状态实体或计算单元（如玩家、订单、房间）。每个Grain拥有唯一的标识符（Grain ID），通过接口定义其行为

![image](/assets/images/image-20250808153128-2szytyr.png)在基于Orleans框架的游戏开发中，**Grains作为分布式Actor模型的核心单元**，将**需要独立状态、并发安全、生命周期管理**的实体抽象为Grains（如玩家、房间、全局服务）

### **2. 单线程执行的技术实现**

Orleans通过以下机制实现Grain的单线程执行：

Orleans运行时确保**每个激活的Grain在任何时刻仅在一个线程上执行**。这意味着对Grain内部状态的访问天然无需锁或其他同步机制

**异步消息队列**：  
所有对Grain的调用（即使代码表现为方法调用）均被转化为**异步消息**，存入该Grain专属的`WorkItemGroup`​队列

**协作式调度**：  
运行时使用**少量线程（通常等于CPU核心数）**  处理所有Grain的消息队列。消息按顺序从队列中取出，由同一线程**连续执行直至完成**（无抢占）

**状态隔离性**：  
Grain的状态（如玩家血量、位置）仅能通过消息修改，外部无法直接访问，从物理上杜绝共享内存冲

‍

## Silo

Silo是**物理资源单位**（如一台服务器）

![image](/assets/images/image-20250808154654-6l2blxd.png)

‍

## 调用机制本质：消息传递

1. **调用方**（如`PlayerGrain`​）向`RoomGrain`​发送请求消息。
2. **运行时调度**：

    - 若`RoomGrain`​与调用方在**同一Silo**，消息通过**本地队列**直接传递，无网络开销

      - 若在**不同Silo**，消息经**TCP连接**（默认端口11111）路由至目标Silo的`RoomGrain`​
    - ​`RoomGrain`​处理 **：消息进入其专属**​**​`WorkItemGroup`​**​**队列，** 单线程串行执行（即使多个请求并发到达）
3. **单线程执行模型**：

    - 每个Grain（包括`RoomGrain`​）绑定独立队列，请求**严格串行处理**，避免状态竞争

      - 例如：玩家A和B同时向`RoomGrain`​发送消息，消息按到达顺序依次处理。
4. **状态隔离性**：

    - ​`RoomGrain`​封装房间状态（如玩家列表、位置），外部仅能通过消息修改，天然线程安全
5. **消息顺序保障**：

    - 同一发送方的消息默认按序传递（除非标记`[Unordered]`​）

**所有Grain交互均为消息驱动**，所谓“直接调用”只是语法糖，底层仍由Orleans消息系统保障顺序性，这样可以避免资源竞争等问题，也是实现无锁编程的关键

‍

## 直接调用消息转化位异步消息的实现

1. **Grain引用抽象**  
    当开发者调用`GrainFactory.GetGrain<T>()`​时，Orleans运行时**动态生成代理对象**（如`PlayerGrainReference`​），而非真实Grain实例

    - 代理对象实现与Grain接口相同的公开方法（如`Move()`​、`Attack()`​）。
    - 调用代理方法时，实际触发消息封装流程。
2. **透明代理技术**  
    基于.NET的`RealProxy`​或`DispatchProxy`​，在方法调用时拦截参数并构造消息体

    ```csharp
    // 示例：代理对象拦截方法调用
    public class GrainReferenceProxy : DispatchProxy {
        protected override object Invoke(MethodInfo method, object[] args) {
            var request = new InvokeMethodRequest(method.Name, args); // 封装方法名和参数
            return SendMessage(request); // 转入消息发送
        }
    }
    ```

3. **消息结构定义**  
    每个方法调用被封装为`InvokeMethodRequest`​对象，包含：

- **目标Grain标识**（Grain ID）

- **方法签名**（方法名、参数类型）
- **参数值**（序列化后的二进制数据）  
  。

4. **高效序列化**

- 默认使用`Bond`​或`MessagePack`​二进制序列化，压缩数据体积
- 若方法标记`[Immutable]`​，参数按值传递；否则按引用传递（需生成Grain引用）。

**远程调用路径（跨Silo）**

|**步骤**|**技术实现**|
| --| --------------------------------------------------------------------|
|**网关路由**|客户端通过配置的Gateway端口（默认30000）发送消息至Silo集群<br />。|
|**一致性哈希定位**|根据Grain ID哈希值确定目标Silo，路由表缓存于本地目录服务（`LocalGrainDirectory`​）<br />。|
|**Silo间通信**|使用自定义二进制协议（或gRPC）经TCP端口（默认11111）传输|

‍

	是对于Orleans中基本概念的理解，喜欢这套框架的很大原因是无锁开发，高并发的天然优势，以及方便扩展集群，再加上本身又在从事unity开发，对于C#用起来也更得心应手。

具体API和开发手册可以查看https://learn.microsoft.com/zh-cn/dotnet/orleans/

‍
