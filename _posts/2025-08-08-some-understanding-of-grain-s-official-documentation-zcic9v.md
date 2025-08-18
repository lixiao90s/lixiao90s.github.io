---
title: Grain官方文档的一些理解
date: '2025-08-08 14:31:29'
permalink: /post/some-understanding-of-grain-s-official-documentation-zcic9v.html
tags:
  - orleans
  - 服务器
  - .Net
  - c#
layout: post
published: true
---





## 灵活放置

- grain被激活时，由runtime决定 哪个silo激活
- 放置策略

  - 随机
  - 本地哟偶先
  - 负载均衡
  - 自定义

## 持久化

- 将grain状态保存到外部存储系统（一般是数据库或者云存储），当被停用时状态不丢失，激活时恢复到之前状态，这样业务将无感。
- ```c#
  public sealed class ShoppingCartGrain(
      [PersistentState(
              stateName: "ShoppingCart",
              storageName: "shopping-cart")]
          IPersistentState<Dictionary<string, CartItem>> cart) : Grain, IShoppingCartGrain

  //配置地方
  if (builder.Environment.IsDevelopment())
  {
      builder.Host.UseOrleans((_, builder) =>
      {
          builder
              .UseLocalhostClustering()
              .AddMemoryGrainStorage("shopping-cart") // <-- Here it is!
              .AddStartupTask<SeedProductStoreTask>();
      });
  }
  ```

## 无状态工作者(Stateless workers)

- 不存储任何需要持久化的状态，每次调用都是全新操作

## Grain call filters (调用筛选器)

- 关注核心的业务逻辑，同时将日志、授权、错误处理等通用功能集中到可重用的筛选器组件中，极大地提高了代码的可维护性和可扩展性。
- 您可以把它想象成 ASP.NET Core 中的中间件 (Middleware)，或者其他框架中的“拦截器” (Interceptor)。它的核心作用就是 在不修改 grain 本身代码的情况下，为 grain 的方法调用添加额外的通用逻辑 。
- 面向切面编程 (AOP)
- 这是最经典的用例。您希望记录每个 grain 方法的调用情况，比如：哪个方法被调用了、参数是什么、执行了多长时间、是否成功等。如果没有筛选器，您就需要在每个 grain 的每个方法里都写重复的日志代码。

  - - 集中管理日志逻辑。
    - 轻松监控系统性能和瓶颈。
    - 调试和追踪问题。
    - ```c#
      // 1. 实现筛选器接口
      public class LoggingGrainCallFilter : IIncomingGrainCallFilter
      {
          private readonly ILogger<LoggingGrainCallFilter> _logger;

          public LoggingGrainCallFilter(ILogger<LoggingGrainCallFilter> logger)
          {
              _logger = logger;
          }

          public async Task Invoke(IIncomingGrainCallContext context)
          {
              var stopwatch = Stopwatch.StartNew();
              _logger.LogInformation(
                  "Grain Call Starting: {Grain}.{Method} with arguments {Arguments}",
                  context.Grain.GetType().Name,
                  context.ImplementationMethod.Name,
                  context.Arguments);

              try
              {
                  // 调用链中的下一个筛选器，或者直接调用 grain 方法
                  await context.Invoke();

                  stopwatch.Stop();
                  _logger.LogInformation(
                      "Grain Call Succeeded: {Grain}.{Method} executed in {ElapsedMilliseconds}ms",
                      context.Grain.GetType().Name,
                      context.ImplementationMethod.Name,
                      stopwatch.ElapsedMilliseconds);
              }
              catch (Exception ex)
              {
                  stopwatch.Stop();
                  _logger.LogError(
                      ex,
                      "Grain Call Failed: {Grain}.{Method} executed in {ElapsedMilliseconds}ms",
                      context.Grain.GetType().Name,
                      context.ImplementationMethod.Name,
                      stopwatch.ElapsedMilliseconds);
                  
                  // 必须重新抛出异常，否则调用方会认为调用成功了
                  throw;
              }
          }
      }

      // 2. 在 Silo 中注册筛选器
      builder.Host.UseOrleans(siloBuilder =>
      {
          // 将筛选器注册为全局筛选器，应用到所有 grain
          siloBuilder.AddIncomingGrainCallFilter<LoggingGrainCallFilter>();
      });
      ```

- 例子2 授权赛选器

  - ‍

    ```c#
    // 1. 定义一个特性，用来标记需要授权的方法
    [AttributeUsage(AttributeTargets.Method)]
    public class RequiresAdminRoleAttribute : Attribute
    {
    }

    // 2. 实现筛选器
    public class AuthorizationGrainCallFilter : IIncomingGrainCallFilter
    {
        public async Task Invoke(IIncomingGrainCallContext context)
        {
            // 检查目标方法是否标记了我们的特性
            var requiresAdmin = context.ImplementationMethod.GetCustomAttribute<RequiresAdminRoleAttribute>();

            if (requiresAdmin is not null)
            {
                // 从调用上下文中获取用户信息 (这通常需要您自己实现)
                var userRole = RequestContext.Get("UserRole") as string;

                if (userRole != "Admin")
                {
                    // 如果用户不是 Admin，则直接抛出异常，阻止方法调用
                    throw new UnauthorizedAccessException("User does not have the required 'Admin' role.");
                }
            }

            // 如果检查通过，则继续调用
            await context.Invoke();
        }
    }

    // 3. 在 Grain 方法上使用特性
    public class SuperSecretGrain : Grain, ISuperSecretGrain
    {
        [RequiresAdminRole]
        public Task<string> GetTheSecretCode()
        {
            return Task.FromResult("12345");
        }

        public Task<string> GetPublicInfo()
        {
            return Task.FromResult("This is public info.");
        }
    }

    // 4. 别忘了在 Silo 中注册筛选器！
    siloBuilder.AddIncomingGrainCallFilter<AuthorizationGrainCallFilter>();
    ```

- 3. 统一的错误处理 (Error Handling)

      ```c#
      public class ErrorHandlingGrainCallFilter : IIncomingGrainCallFilter
      {
          public async Task Invoke(IIncomingGrainCallContext context)
          {
              try
              {
                  await context.Invoke();
              }
              catch (BusinessValidationException ex)
              {
                  // 捕获我们自定义的业务逻辑异常
                  // 并且将其转换为一个特定的结果，而不是让它冒泡到客户端
                  context.Result = new { ErrorCode = ex.ErrorCode, Message = ex.Message };
              }
              catch (Exception)
              {
                  // 对于所有其他未知的异常，我们只返回一个通用的错误信息
                  // 并且在服务器端记录详细日志（可以在日志筛选器中完成）
                  throw new ApplicationException("An unexpected error occurred. Please try again later.");
              }
          }
      }

      // 注册筛选器
      siloBuilder.AddIncomingGrainCallFilter<ErrorHandlingGrainCallFilter>();
      ```

## 

### Request context

它的主要作用是在一次完整的请求-响应生命周期中，共享那些 不属于 grain 本身状态 、但又对处理逻辑很重要的 临时性数据 。

1.

```c#
// 设置一个唯一的追踪 ID，用于追踪整个调用链
RequestContext.Set("TraceId", Guid.NewGuid()); 
// 设置当前用户的 ID
RequestContext.Set("UserId", "user-123");

// 现在发起对第一个 grain 的调用
var grainA = client.GetGrain<IGrainA>(0);
await grainA.DoSomething(); 
```

2.

```c#
public class GrainB : Grain, IGrainB
{
    public async Task DoWork()
    {
        // 直接从 RequestContext 中读取数据
        var traceId = RequestContext.Get("TraceId");
        var userId = RequestContext.Get("UserId");

        _logger.LogInformation("GrainB is doing work for user {UserId} with trace {TraceId}", userId, traceId);

        // ... 业务逻辑 ...

        // 调用下一个 grain，RequestContext 中的数据会自动传递过去
        var grainC = this.GrainFactory.GetGrain<IGrainC>(0);
        await grainC.DoMoreWork();
    }
}
```

### RequestContext 的主要应用场景

1. ‍

    分布式追踪 (Distributed Tracing) ：这是最常见的用途。在请求的最开始生成一个唯一的 TraceId (或 CorrelationId )，并放入 RequestContext 。之后，在每个 grain 的日志中都带上这个 ID。当系统出现问题时，您就可以通过这个 TraceId 在日志系统中（如 ELK, Splunk, Datadog）串联起一次完整请求经过的所有路径，极大地简化了分布式环境下的问题排查。
2. ‍

    传递用户身份或租户信息 ：在多租户的应用中，您可以在请求入口处将用户的身份令牌 (token)、用户ID或租户ID放入 RequestContext 。这样，调用链中的任何一个 grain 都可以知道当前操作属于哪个用户或租户，而无需在每个方法签名中都传递这些信息。这对于实现授权逻辑（如我们之前讨论的调用筛选器）尤其有用。
3. ‍

    传递客户端信息 ：例如客户端的设备类型 ( "iOS" , "Android" , "Web" )、IP 地址或者语言偏好 ( "en-US" , "zh-CN" )。这可以让 grain 根据不同的客户端环境返回不同的数据或执行不同的逻辑。
4. ‍

    功能开关或 A/B 测试 ：您可以将一些功能开关的标志位放入 RequestContext ，来控制在本次请求中是否启用某个新功能，从而实现灰度发布或 A/B 测试。

### 需要注意的关键点

- 线程安全 ： RequestContext 是基于 AsyncLocal<T> 实现的，这意味着它存储的数据是与当前的异步控制流（async control flow）绑定的，是线程安全的。
- 非持久化 ： RequestContext 中的数据是 临时的、非持久的 。它只在一次请求的生命周期内有效。它 不是 用来替代 grain 的持久化状态的。
- 数据大小 ：由于 RequestContext 的数据需要在每次 grain 间调用时进行序列化和网络传输，所以不应该在里面存放大量的数据。只应存放少量的、关键的元数据。

‍

## 分布式 ACID 事务(Distributed ACID transactions)

- 原子性 (Atomicity) ：一个事务中的所有操作，要么 全部成功 ，要么 全部失败 回滚。不会出现“钱扣了，但对方没收到”的中间状态。
- 一致性 (Consistency) ：事务必须使系统从一个有效的状态转移到另一个有效的状态。例如，转账前后，银行系统所有账户的总金额应该是不变的。
- 隔离性 (Isolation) ：并发执行的事务之间互不干扰。Orleans 提供了 可串行化 (Serializable) 的隔离级别，这是最严格的隔离级别，它保证了并发事务的最终结果与某个顺序执行它们的结果完全相同。
- 持久性 (Durability) ：一旦事务成功提交，其结果就是永久性的，即使系统发生故障也不会丢失。
- 分布式 （Distributed ）：这意味着一个 ACID 事务可以跨越 多个不同的 grain ，而这些 grain 可能位于集群中 不同的物理服务器 (Silo) 上

- 官方例子关键代码
- ```c#
  // (这是 BankAccount 示例中的代码结构)

  public interface IAccountGrain : IGrainWithIntegerKey
  {
      // [Transaction] 特性指明这个方法必须在一个事务中执行
      [Transaction(TransactionOption.Join)]
      Task Withdraw(uint amount);

      [Transaction(TransactionOption.Join)]
      Task Deposit(uint amount);

      [Transaction(TransactionOption.CreateOrJoin)]
      Task<uint> GetBalance();
  }

  // (这是 BankAccount 示例中的代码结构)

  public class AccountGrain : Grain, IAccountGrain
  {
      // 使用 ITransactionalState 来存储账户余额,前面有个IPersistState
      private readonly ITransactionalState<Balance> _balance;

      public AccountGrain(
          [TransactionalState("balance")] ITransactionalState<Balance> balance)
      {
          _balance = balance ?? throw new ArgumentNullException(nameof(balance));
      }

      // 在方法中，我们就像操作普通对象一样修改状态
      public Task Withdraw(uint amount)
      {
          return _balance.PerformUpdate(b => b.Value -= amount);
      }

      public Task Deposit(uint amount)
      {
          return _balance.PerformUpdate(b => b.Value += amount);
      }

      public Task<uint> GetBalance()
      {
          return _balance.PerformRead(b => b.Value);
      }
  }

  //执行事务
  // (这是 BankAccount 示例中的代码结构)

  // 注入 ITransactionClient
  private readonly ITransactionClient _transactionClient;

  public async Task Transfer(IAccountGrain fromAccount, IAccountGrain toAccount, uint amountToTransfer)
  {
      try
      {
          // 使用 TransactionClient 来执行一个事务性的 lambda 表达式
          // 在这个 lambda 表达式中进行的所有 grain 调用，都会被自动包含在同一个事务里
          await _transactionClient.RunTransaction(
              TransactionOption.Create, // 创建一个新事务
              async () =>
              {
                  // 1. 从 fromAccount 取款
                  await fromAccount.Withdraw(amountToTransfer);

                  // 2. 向 toAccount 存款
                  await toAccount.Deposit(amountToTransfer);
              });
      }
      catch (Exception ex)
      {
          // 如果在执行过程中发生任何错误 (比如 fromAccount 余额不足抛出异常)
          // 整个事务会自动回滚。
          // fromAccount 的取款操作会被撤销，toAccount 的存款操作也根本不会发生。
          Console.WriteLine($"Transaction failed: {ex.Message}");
      }
  }
  ```

### `ITransactionalState<T>`​ 与 `IPersistentState<T>`​ 的关键区别

为了更好地理解它，我们可以将它与我们之前讨论过的标准持久化组件 `IPersistentState<T>`​ 进行对比：

|特性|​`IPersistentState<T>`​ (标准持久化)|​`ITransactionalState<T>`​ (事务性状态)|
| :-----| :--------------------------------------------------------------------------------------------| :-----------------------------------------------------------------------------------------------------------------|
|**核心关注点**|单个 Grain 状态的**持久化（Durability）** 。确保 Grain 失活或服务器重启后，状态能从存储中恢复。|跨多个 Grain 操作的**原子性（Atomicity）** 和**隔离性（Isolation）** 。确保一组操作要么全部成功，要么全部失败。|
|**操作方式**|通过 `.State`​ 属性直接访问和修改内存中的状态，然后显式调用 `.WriteStateAsync()`​ 将其写入存储。|通过 `.PerformUpdate()`​ 和 `.PerformRead()`​ 方法来“请求”对状态进行修改或读取。|
|**谁来控制写入**|**开发者**。您需要自己决定何时调用 `WriteStateAsync()`​。|**Orleans 事务管理器**。开发者从不直接调用写入方法。状态的最终提交或回滚由事务协调器自动处理。|
|**适用场景**|适用于绝大多数不需要跨 Grain 协调的、独立的有状态服务。例如，保存用户的购物车、个人资料等。|必须用于需要多个 Grain 协同完成一个业务逻辑的场景。最典型的例子就是银行转账，涉及两个账户 Grain 的状态同步更新。|

---

### `ITransactionalState<T>`​ 的工作机制

​`ITransactionalState<T>`​ 通过两个核心方法来运作：

1. ​**​`Task<TResult> PerformRead(Func<T, TResult> readFunc)`​** ​

    - **作用**：以事务安全的方式读取状态。
    - **解释**：当你调用此方法时，Orleans 会确保你读取到的数据在当前事务的隔离级别（通常是“可串行化”）下是一致的。这意味着你不会读到其他并发事务尚未提交的“脏数据”。
    - **示例**：在 `AccountGrain`​ 中获取余额。

      ```csharp
      public Task<decimal> GetBalance()
      {
          return _balance.PerformRead(balance => balance.Value);
      }
      ```
2. ​**​`Task PerformUpdate(Action<T> updateAction)`​** ​

    - **作用**：以事务安全的方式更新状态。
    - **解释**：这是最关键的部分。当你调用此方法时，你并不是立即修改了状态。相反，你是在向当前事务提交一个“意图”——“我计划对状态进行这样的修改”。这个修改操作会被记录下来，但**不会立即持久化**。
    - **示例**：在 `AccountGrain`​ 中存款。

      ```csharp
      public Task Deposit(decimal amount)
      {
          return _balance.PerformUpdate(balance => balance.Value += amount);
      }
      ```

‍

## 

### Orleans Streams 的核心概念

Orleans Streams 是一个强大的流式处理框架，它将流处理的复杂性与 Grain 模型无缝集成。您可以把它想象成一个内置于 Orleans 的、 为 Grain "量身定做" 的、虚拟化的发布/订阅（Pub/Sub）系统 。

‍

1.虚拟化（Virtualization） ：与 Grain 一样，流也是虚拟的。这意味着您不需要显式地创建或销毁一个流。流永远存在，就像 Grain 一样。您只需通过一个唯一的标识符（ StreamId ）就可以向它发布消息或订阅它。

2.生产者与消费者解耦 ：生产者向一个流发布消息，而不需要知道谁在监听这个流，有多少个监听者，或者它们当前是否处于激活状态。同样，消费者订阅一个流，而不需要知道谁在发布消息。

3.可靠性与持久性 ：Orleans Streams 通过**流提供程序（Stream Provider）** 来支持不同的底层消息队列技术。这意味着流中的消息可以是持久化的（例如，使用 Azure Event Hubs 或 RabbitMQ），确保了即使在消费者离线或系统重启后，消息也不会丢失。

4.自动化的生命周期管理 ：Orleans 负责管理消费者的订阅生命周期。当一个订阅流的 Grain 被激活时，Orleans 会自动为其恢复订阅。如果消费者处理消息失败，Orleans 还提供了内置的重试和错误处理机制。

5.背压（Backpressure） ：系统会自动监测消费者的处理速度。如果消费者跟不上消息产生的速度，流提供程序会减慢或暂停向其递送消息，防止消费者被压垮。

‍

Stream (流)

- 它是一个逻辑上的 通道 ，用于传输一系列有序的事件。
- 每个流都有一个唯一的标识符（一个 Guid 和一个 string 类型的命名空间），例如 StreamId.Create("my-namespace", someGuid) 。
- 流是 虚拟的、持久的 。即使没有任何生产者或消费者连接到它，流也“存在”。数据可以发布到一个流中，即使当时没有消费者；当消费者稍后订阅时，它可以从流的特定点开始接收数据。

2.Stream Producer (流生产者)

- 任何可以将事件发布到流中的实体。通常是一个 Grain，但也可以是 Orleans 集群外部的客户端。
- 生产者获取一个流的句柄 ( IAsyncStream<T> )，然后调用 .OnNextAsync(event) 来发布事件。
- 示例 : 在 Streaming\Simple\Grains\ProducerGrain.cs 中， ProducerGrain 就是一个生产者。它会定期向一个流中发送 int 型的事件。

3.Stream Consumer (流消费者)

- 订阅流并处理事件的实体，通常也是一个 Grain。
- 消费者通过订阅一个流来接收事件。当新事件到达时，Orleans 运行时会调用消费者 Grain 中预定义的处理方法（例如 OnNextAsync ）。
- 隐式订阅 vs. 显式订阅 :

  - 隐式订阅 ( [ImplicitStreamSubscription("MyStreamNamespace")] ) : 这是最常见的方式。您可以在 Grain 类上使用此特性。Orleans 会自动为具有相同 Guid 的 Grain 订阅相应命名空间下的流。例如，一个 ConsumerGrain 被激活并传入一个 Guid，它会自动订阅到 StreamId.Create("MyStreamNamespace", thatSameGuid) 。
  - 显式订阅 : 消费者可以动态地获取流的句柄，并调用 .SubscribeAsync(IAsyncObserver<T>) 来手动订阅或退订。
- 示例 : 在 Streaming\Simple\Grains\ConsumerGrain.cs 中， ConsumerGrain 就是一个消费者。它隐式地订阅流，并在收到事件时将其打印出来。

4.Stream Provider (流提供程序)

- 这是流的物理实现。它负责事件的传输、持久化和传递保证。
- Orleans 提供了多种内置的流提供程序：

  - MemoryStreamProvider : 用于测试和开发。事件存储在内存中，Silo 重启后会丢失。
  - AzureQueueStreamProvider : 使用 Azure Queue Storage 作为传输媒介。
  - EventHubStreamProvider : 使用 Azure Event Hubs，适用于大规模、高吞吐量的场景。
  - AWSSQSStreamProvider : 使用 Amazon SQS。
- 官方例子 Streaming\CustomDataAdapter 示例那样，创建自己的提供程序来对接任何消息系统（如 RabbitMQ, Kafka 等）。
