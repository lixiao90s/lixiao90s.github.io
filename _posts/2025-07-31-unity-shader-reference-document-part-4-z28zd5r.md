---
title: Unity Shader 参考文档 - 第四部分
date: '2025-07-31 09:07:01'
permalink: /post/unity-shader-reference-document-part-4-z28zd5r.html
tags:
  - shader
  - unity
layout: post
published: true
---





---

## Pipeline（渲染管线）

### 应用程序阶段（Application Stage）

|阶段|描述|
| ------| ---------------------------------------------------------------------------------------|
|​`0.Application Stage`​|此阶段一般由CPU将需要在屏幕上绘制的几何体、摄像机位置、光照纹理等输出到管线的几何阶段|

### 几何阶段（Geometry Stage）

|阶段|描述|
| ------| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`1.模型和视图变换（Model and View Transform）`​|模型和视图变换阶段分为模型变换和视图变换。模型变换的目的是将模型从本地空间变换到世界空间当中，而视图变换的目的是将摄像机放置于坐标原点（以使裁剪和投影操作更简单高效），将模型从世界空间变换到相机空间(观察空间)，以便后续步骤的操作|
|​`2.顶点着色（Vertex Shading）`​|顶点着色阶段的目的在于确定模型上顶点处的光照效果，其输出结果（颜色、向量、纹理坐标等）会被发送到光栅化阶段以进行插值操作|
|​`3.几何、曲面细分着色器`​|【可选项】分为几何着色器(Geometry Shader)和曲面细分着色器(Tessellation Shader)，主要是对顶点进行增加与删除修改等操作|
|​`4.投影（Projection）`​|投影阶段分为正交投影与透视投影，将上面的观察空间变换到齐次裁剪空间|
|​`5.裁剪（Clipping）`​|齐次裁剪空间会通过透视除法变换到归一化的设备坐标NDC中，然后再根据图元在视体的位置分为三种裁剪情况：<br />1.当图元完全位于视体内部，那么它可以直接进行下一个阶段<br />2.当图元完全位于视体外部，则不会进入下一个阶段，直接丢弃<br />3.当图元部分位于视体内部，则需要对位于视体内的图元进行裁剪处理|
|​`6.屏幕映射（Screen Mapping）`​|屏幕映射阶段的主要目的，是将之前步骤得到的坐标映射到对应的屏幕坐标系上|

### 光栅化阶段（Rasterizer Stage）

|阶段|描述|
| ------| ----------------------------------------------------------------------------------------------------------------------------|
|​`7.三角形设定（Triangle Setup）`​|此阶段主要是将从几何阶段得到的一个个顶点通过计算来得到一个个三角形网格|
|​`8.三角形遍历（Triangle Traversal）`​|此阶段将进行逐像素遍历检查操作，以检查出该像素是否被上一步得到的三角形所覆盖，这个查找过程被称为三角形遍历|
|​`9.像素着色(Pixel Shading)`​|对应于ShaderLab中的frag函数，主要目的是定义像素的最终输出颜色|
|​`10.混合（Merging）`​|主要任务是合成当前储存于缓冲器中的由之前的像素着色阶段产生的片段颜色。此阶段还负责可见性问题（深度测试、模版测试等）的处理|

### Shader Lab

|阶段|描述|
| ------| ----------------------------------------------------------------------------------------------|
|​`1.appdata`​|将应用程序阶段的内容传递到顶点着色器中|
|​`2.vertex(顶点着色器)`​|本地空间>(本地到世界空间矩阵)>世界空间>(世界到观察空间矩阵)>观察空间>(投影矩阵)>齐次裁剪空间|
|​`3.透视除法`​|齐次裁剪空间作透视除法(clip.xyzw/clip.w)，变换到归一化设备坐标NDC|
|​`4.视口变换`​|从NDC坐标变换到屏幕坐标|
|​`5.fragment(片断着色器)`​|用从顶点着色器的输出来当输入进行逐片断的颜色计算并输出|

---

## Platform Differences（平台差异）

### 裁剪空间(规范化立方体)

|平台|描述|
| ------| ------------------------------------------|
|​`OpenGL`​|裁剪空间下坐标范围(-1,1)|
|​`DirectX`​|裁剪空间下坐标范围(1,0)|
|​`UNITY_NEAR_CLIP_VALUE`​|裁剪空间下的近剪裁值，(DX为1,OpenGL为-1)|

### ReversedZ

|类型|描述|
| ------| --------------------------------------------------------------------------------------------------------------------------------------------------|
|​`Reversed direction(反向方向)`​|DirectX 11、DirectX 12、PS4、Xbox One、Metal这些平台都属于反向方向<br />深度值从近裁剪面到远裁剪面的值为[1 ~ 0]<br />裁剪空间下的Z轴范围为[near,0]|
|​`Traditional direction(传统方向)`​|除以上反向方向的平台以外都属于传统方向<br />深度值从近裁剪面到远裁剪面的值为[0 ~ 1]<br />裁剪空间下的Z轴范围为：<br />DX平台=[0,far]<br />OpenGL平台=[-near,far]|
|​`UNITY_REVERSED_Z`​|判断当前平台是否开启ReversedZ|
|​`SystemInfo.usesReversedZBuffer`​|利用C#判断当前平台是否支持ReversedZ|

---

## Computer Shader（计算着色器）

|内容|描述|
| ------| ------------------------------------------------------------------------------------------------------|
|​`SystemInfo.supportsComputeShaders`​|C#中检测硬件是否支持Computer Shader。OpenGLES 3.1及以上支持Compute Shader|
|​`#pragma kernel CSMain SOME_DEFINE DEFINE_WITH_VALUE=1337`​|声明执行函数为CSMain，可通过多行声明多个函数。SOME_DEFINE和DEFINE_WITH_VALUE是预定义宏，此项为可选项|
|​`ComputerShader.Dispatch(k,X,Y,Z)`​|k表示要执行的核函数索引，XYZ表示开启X*Y*Z个线程组，注意是组(每个组中会有具体的线程)!|
|​`[numthreads(x,y,z)]`​|每个线程组中的总线程数(x*y*z)|
|​`SV_GroupID`​|当前线程所在的线程组ID。[(0,0,0)~(X-1,Y-1,Z-1)]|
|​`SV_GroupThreadID`​|当前线程在所在线程组内的ID。[(0,0,0)~(x-1,y-1,z-1)]|
|​`SV_DispatchThreadID`​|当前线程的全局唯一ID。值为线程组*线程数+当前线程，是个三维坐标|
|​`SV_GroupIndex`​|当前线程在所在线程内的下标，int类型。[0~X*Y*Z-1]|

---

## GLSL（OpenGL着色语言）

### 基本类型

|类型|描述|
| ------| ----------------------------------|
|​`void`​|空类型，即不返回任何值|
|​`bool`​|布尔类型，即真或者假，true false|
|​`int`​|带符号的整数|
|​`float`​|带符号的浮点数|
|​`vec2,vec3,vec4`​|n维浮点数向量|
|​`bvec2,bvec3,bvec4`​|n维布尔向量|
|​`ivec2,ivec3,ivec4`​|n维向整数向量|
|​`mat2,mat3,mat4`​|2x2,3x3,4x4浮点数矩阵|
|​`clamp(a,min,max)`​|将a限制在min和max之间|
|​`sampler2D`​|2D纹理|
|​`samplerCube`​|立方体纹理|

---

## Other（其他语法）

### 内置渲染管线

|语法|描述|
| ------| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`CGPROGRAM/ENDCG`​|cg代码的开始与结束|
|​`CGINCLUDE/ENDCG`​|通常用于定义多段vert/frag函数，然后这段CG代码会插入到所有Pass的CG中，根据当前Pass的设置来选择加载|
|​`Fallback "name"`​|备胎，当Shader中没有任何SubShader可执行时，则执行FallBack。默认值为Off，表示没有备胎。示例:FallBack "Diffuse"|
|​`GrabPass`​|GrabPass{} 抓取当前屏幕存储到_GrabTexture中，每个有此命令的Shader都会每帧执行<br />GrabPass { "TextureName" } 抓取当前屏幕存储到自定义的TextureName中，每帧中只有第一个拥有此命令的Shader执行一次<br />GrabPass也支持Name与Tags|

### URP渲染管线

|语法|描述|
| ------| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`include`​|​`#include "Packages/com.unity.render-pipelines.core/ShaderLibrary/Color.hlsl"#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Core.hlsl"#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/Lighting.hlsl"#include "Packages/com.unity.render-pipelines.universal/ShaderLibrary/ShaderGraphFunctions.hlsl"#include "Packages/com.unity.render-pipelines.core/ShaderLibrary/UnityInstancing.hlsl"`​|
|​`CBUFFER_START(UnityPerMaterial)/CBUFFER_END`​|将材质属性面板中的变量定义在这个常量缓冲区中，用于支持SRP Batcher|
|​`HLSLPROGRAM/ENDHLSL`​|HLSL代码的开始与结束|
|​`HLSLINCLUDE/ENDHLSL`​|通常用于定义多段vert/frag函数，然后这段CG代码会插入到所有Pass的CG中，根据当前Pass的设置来选择加载|
|​`Fallback "name"`​|备胎，当Shader中没有任何SubShader可执行时，则执行FallBack。默认值为Off，表示没有备胎。比如URP下默认的紫色报错Shader:Fallback "Hidden/Universal Render Pipeline/FallbackError"|

### 通用语法

|语法|描述|
| ------| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`LOD`​|Shader LOD，可利用脚本来控制LOD级别，通常用于不同配置显示不同的SubShader。注意SubShader要从高往低写，要不然会无法生效|
|​`Category{}`​|定义一组所有SubShader共享的命令，位于SubShader外面|
|​`CustomEditor "name"`​|自定义材质面板，name为自定义的脚本名称。可利用此功能对材质面板进行个性化自定义|
|​`Name "MyPassName"`​|给当前Pass指定名称，以便利用UsePass进行调用|
|​`UsePass "Shader/NAME"`​|调用其它Shader中的Pass，注意Pass的名称要全部大写！Shader的路径也要写全，以便能找到具体是哪个Shader的哪个Pass。另外加了UsePass后，也要注意相应的Properties要自行添加|

---

## Substance Painter（材质绘制）

### 参考网址

|内容|描述|
| ------| ----------------------------------------------------------------------------|
|​`URL`​|https://substance3d.adobe.com/documentation/spdoc/shader-api-89686018.html|

### 材质参数

#### 颜色(RGB)

```glsl
//: param custom {"default":[1,0.9568,0.8392],"label":"灯光颜色(RGB)","widget":"color","group":"灯光","description":"tooltip在这里"}
uniform vec3 _LightColor;
uniform vec4 _BaseColor;(当为4维颜色时,alpha会变成滑条)
```

#### 整型

```glsl
//: param custom { "default": 0, "label": "Int","min": 0, "max": 10,"step": 1,"group":"Int" }
uniform int u_int1;
uniform ivec2 u_int2;
uniform ivec3 u_int3;
uniform ivec4 u_int4;
```

#### 浮点值

```glsl
//: param custom { "default": 1, "label": "Float", "min": 0, "max": 2,"step": 0.1,"group":"Float" }
uniform float u_float1;
uniform vec2 u_float2;
uniform vec3 u_float3;
uniform vec4 u_float4;
```

#### 开关

```glsl
//: param custom { "default": false, "label": "Boolean","group":"Toogle" }
uniform bool u_bool;
```

#### 枚举

```glsl
//: param custom {
//:   "default": -1,
//:   "label": "Combobox",
//:   "widget": "combobox",
//:   "values": {
//:     "Value -1": -1,
//:     "Value 0": 0,
//:     "Value 10": 10
//:   },
//:   "group":"Enum"
//: }
uniform int u_combobox;
```

#### 自定义纹理

```glsl
//: param custom { "default": "", "default_color": [1.0, 1.0, 1.0, 1.0], "label": "Texture","usage": "texture","group":"Texture" }
//: param custom { "default": "", "label": "Texture","usage": "environment","group":"Texture" }
uniform sampler2D u_sampler1;
vec4 tex = texture(u_sampler1, inputs.tex_coord);
```

#### 内置通道图

```glsl
//: param auto channel_basecolor
//: param auto channel_ambientocclusion
//: param auto channel_anisotropyangle
//: param auto channel_anisotropylevel
//: param auto channel_blendingmask
//: param auto channel_diffuse
//: param auto channel_displacement
//: param auto channel_emissive
//: param auto channel_glossiness
//: param auto channel_height
//: param auto channel_ior
//: param auto channel_metallic
//: param auto channel_normal
//: param auto channel_opacity
//: param auto channel_reflection
//: param auto channel_roughness
//: param auto channel_scattering
//: param auto channel_specular
//: param auto channel_specularlevel
//: param auto channel_transmissive
uniform SamplerSparse channel_tex;
vec4 tex = textureSparse(channel_basecolor,i.sparse_coord);
```

#### 用户自定义通道图

```glsl
//: param auto channel_user0
//: param auto channel_user1
//: param auto channel_user2
//: param auto channel_user3
//: param auto channel_user4
//: param auto channel_user5
//: param auto channel_user6
//: param auto channel_user7
uniform SamplerSparse channel_userTex;
```

#### 模型纹理

```glsl
//: param auto texture_ambientocclusion (AO)
//: param auto texture_curvature (曲率)
//: param auto texture_id (ID图)
//: param auto texture_normal (切线空间下的法线纹理)
//: param auto texture_normal_ws (世界空间下的法线纹理)
//: param auto texture_position (世界空间下的位置纹理)
//: param auto texture_thickness (厚度纹理)
uniform sampler2D u_meshTexture;
```

### 片断输出

|输出|描述|
| ------| ------|
|​`最终输出(emissiveColorOutput + albedoOutput * diffuseShadingOutput + specularShadingOutput)`​|​`void alphaOutput(1);diffuseShadingOutput(vec3(0,0,0));specularShadingOutput(vec3(0,0,0));emissiveColorOutput(vec3(0,0,0));albedoOutput(vec3(1,1,1));sssCoefficientsOutput(vec4(0,0,0,0));`​|

---

## Experience（经验总结）

|经验|描述|
| ------| -------------------------------------------------------------------------------------|
|​`分支`​|尽量不要使用if或者switch去做大量的分支，否则在部分机型上会卡到怀疑人生!|
|​`Stencil`​|红米9C上开启深度图同时Shader中启用Stencil时会导致资源显示不可见，关闭任一个即可解决|

---

## Miscellaneous（杂项技巧）

### PS中的混合公式（A、B为图层）

|混合模式|公式|
| ----------| ------|
|正常|​`A*(1-B.a)+B*(B.a)`​|
|变暗|​`min(A,B)`​|
|变亮|​`max(A,B)`​|
|正片叠底|​`A*B`​|
|滤色|​`1-((1-A)*(1-B))`​|
|颜色加深|​`A-((1-A)*(1-B))/B`​|
|颜色减淡|​`A+(A*B)/(1-B)`​|
|线性加深|​`A+B-1`​|
|线性减淡|​`A+B`​|
|叠加|​`half4 a = step(A,0.5);half4 c = a*A*B*2+(1-a)*(1-(1-A)*(1-B)*2);`​|
|强光|​`half4 a = step(B,0.5);half4 c =a*A*B*2+(1-a)*(1-(1-A)*(1-B)*2);`​|
|柔光|​`half4 a = step(B,0.5);half4 c =a*(A*B*2+A*A*(1-B*2))+(1-a)*(A*(1-B)*2+sqrt(A)*(2*B-1)`​|
|亮光|​`half4 a = step(B,0.5);half4 c =a*(A-(1-A)*(1-2*B)/(2*B))+(1-a)*(A+A*(2*B-1)/(2*(1-B)));`​|
|点光|​`half4 a = step(B,0.5);half4 c =a*(min(A,2*B))+(1-a)*(max(A,( B*2-1)));`​|
|线性光|​`A+2*B-1`​|
|排除|​`A+B-A*B*2`​|
|差值|​`abs(A-B)`​|
|深色|​`half4 a = step(B.r+B.g+B.b,A.r+A.g+A.b);half4 c =a*(B)+(1-a)*(A);`​|
|浅色|​`half4 a = step(B.r+B.g+B.b,A.r+A.g+A.b);half4 c =a*(A)+(1-a)*(B);`​|
|减去|​`A-B`​|
|划分|​`A/B`​|

### UV技巧

|技巧|代码|
| -----------------------------------| ------|
|UV重映射到中心位置|​`float2 centerUV = uv * 2 - 1`​|
|画圆|​`float circle = smoothstep(_Radius, (_Radius + _CircleFade), length(uv * 2 - 1));`​|
|画矩形|​`float2 centerUV = abs(i.uv.xy * 2 - 1);float rectangleX = smoothstep(_Width, (_Width + _RectangleFade), centerUV.x);float rectangleY = smoothstep(_Heigth, (_Heigth + _RectangleFade), centerUV.y);float rectangleClamp = clamp((rectangleX + rectangleY), 0.0, 1.0);`​|
|黑白棋盘格|​`float2 uv = i.uv * 格子密度;uv = floor(uv) * 0.5;float c = frac(uv.x + uv.y) * 2;return c;`​|
|蜂窝格|​`float2 uv = i.uv * 格子密度;uv.y += floor(uv.x) * 0.5;uv = fmod(uv,1);uv = uv*2-1;uv = abs(uv);float d = max((uv.x*0.9+uv.y*0.5),uv.y);d = step(d,_Size);return d;`​|
|极坐标|​`float2 centerUV = (i.uv * 2 - 1);float atan2UV = 1 - abs(atan2(centerUV.g, centerUV.r) / 3.14);`​|
|将0-1的值控制在某个自定义的区间内|​`frac(x*n+n);`​|
|旋转|​`fixed t=_Time.y;float2 rot= cos(t)*i.uv+sin(t)*float2(i.uv.y,-i.uv.x);`​|
|从中心缩放纹理|​`half2 offset = (0.5-i.uv.xy)*_Offset;half4 baseMap = SAMPLE_TEXTURE2D(_MainTex, sampler_MainTex, i.uv.xy + offset);`​|
|序列图|​`float2 splitUV = uv * (1/_Sequence.xy) + float2(0,_Sequence.y - 1/_Sequence.y);float time = _Time.y * _Sequence.z;uv = splitUV + float2(floor(time *_Sequence.x)/_Sequence.x,1-floor(time)/_Sequence.y);`​|

### 顶点技巧

|技巧|代码|
| -------------------------| ------------------------------------|
|模型中心点坐标|​`float3 objCenterPos = mul( unity_ObjectToWorld, float4( 0, 0, 0, 1 ) ).xyz;`​|
|获取模型的旋转角度(Y轴)|​`float cosA = unity_ObjectToWorld._11;float sinA = unity_ObjectToWorld._13;float angle = atan2(sinA, cosA);angle *= 180 / 3.1415926;if (angle < 0) angle += 360;`​|
|BillBoard|在顶点着色器中添加相机空间转换代码|
|网格阴影|​`half4 worldPos = mul(unity_ObjectToWorld, v.vertex);worldPos.y = 2.47;worldPos.xz += fixed2(阴影X方向,阴影Z方向)*v.vertex.y;o.pos = mul(UNITY_MATRIX_VP,worldPos);`​|

### 颜色技巧

|技巧|代码|
| -----------------------| --------------------|
|去色|​`dot(rgb,fixed3(0.22,0.707,0.071))`​|
|RGB2HSV|使用专门的转换函数|
|HSV2RGB|使用专门的转换函数|
|利用HSV对颜色进行调整|​`half3 hsv = RGB2HSV(baseMap.rgb);baseMap.rgb = HSV2RGB(float3((hsv.x + _HSVValue.x), (hsv.y * _HSVValue.y), (hsv.z * _HSVValue.z)));`​|
|随机(单通道)|​`frac(sin(dot(uv, float2(127.1, 311.7))) * 43758.5453);`​|
|随机(三通道)|​`float3 q = float3(dot(p,float2(127.1,311.7)),dot(p,float2(269.5,183.3)),dot(p,float2(419.2,371.9)));return frac(sin(q)*43758.5453);`​|

### 深度技巧

|技巧|代码|
| --------------------| ------|
|从深度重建世界坐标|​`float2 screenUV = i.positionCS/_ScreenParams.xy;half depthMap = SAMPLE_TEXTURE2D(_CameraDepthTexture, sampler_CameraDepthTexture, screenUV).r;float3 positionWS = ComputeWorldSpacePosition(screenUV, depth, UNITY_MATRIX_I_VP);`​|

### 光照技巧

|技巧|代码|
| --------| ------|
|Matcap|​`o.normalWS = TransformObjectToWorldNormal(v.normalOS);o.uv.zw = mul(UNITY_MATRIX_V, float4(o.normalWS, 0.0)).xy * 0.5 + 0.5;`​|

### 其他技巧

|技巧|代码|
| ----------| ----------------------------------------------------------------------------------|
|菲涅尔|​`half3 worldViewDir = normalize (UnityWorldSpaceViewDir (i.worldPos));float ndotv = dot (i.normal, worldViewDir);float fresnel = (0.2 + 2.0 * pow (1.0 - ndotv, 2.0));`​|
|XRay射线|新建一个Pass，设置Blend，Zwrite Off关闭深度写入，Ztest greater深度测试设置为大于|
|Dither|使用专门的Dither函数（2x2、4x4、8x8）|

---

## 总结

这个完整的Unity Shader参考文档涵盖了：

1. **Semantics（语义）**  - 数据传递的关键字
2. **Properties（属性）**  - 材质面板中的可调参数
3. **Math（数学运算）**  - 常用的数学函数和运算
4. **BuildIn Variables（内置变量）**  - Unity提供的内置变量和函数
5. **BuildIn Transformation（内置变换）**  - 空间变换矩阵和方法
6. **Lighting（光照）**  - 各种光照模型和实现
7. **Tags（标签）**  - 渲染管线和队列控制
8. **Pragma（编译指令）**  - 编译控制和变体管理
9. **Render State（渲染状态）**  - 渲染状态设置
10. **Transformation（空间变换）**  - 详细的变换矩阵和方法
11. **Predefined Macros（预定义宏）**  - Unity预定义的宏
12. **Pipeline（渲染管线）**  - 渲染管线流程
13. **Platform Differences（平台差异）**  - 不同平台的差异
14. **Computer Shader（计算着色器）**  - 计算着色器相关内容
15. **GLSL（OpenGL着色语言）**  - GLSL语法
16. **Other（其他语法）**  - 其他shader语法
17. **Substance Painter（材质绘制）**  - Substance Painter中的shader语法
18. **Experience（经验总结）**  - 实际开发中的经验
19. **Miscellaneous（杂项技巧）**  - 实用的shader技巧和算法

这个文档可以作为Unity Shader开发的完整参考手册，帮助开发者快速查找和使用各种shader功能。
