---
title: Unity Shader 参考文档 - 第二部分
date: '2025-07-31 09:06:34'
permalink: /post/unity-shader-reference-document-part-2-zv2tg1.html
tags:
  - shader
  - unity
layout: post
published: true
---





---

## BuildIn Variables（内置变量）

### 基础函数

|函数|描述|
| ------| ------------------------------------------------------------------------------------------|
|​`UNITY_INITIALIZE_OUTPUT(type,name)`​|由于HLSL编缉器不接受没有初始化的数据，所以为了支持所有平台，从而需要使用此方法进行初始化|
|​`TRANSFORM_TEX(i.uv,_MainTex)`​|对UV进行Tiling与Offset变换|
|​`float3 UnityWorldSpaceLightDir( float3 worldPos )`​|返回顶点到灯光的向量|

### Camera and Screen

#### 内置渲染管线

|变量|描述|
| ------| ----------------------------------------------------------------------------------------------------|
|​`_WorldSpaceCameraPos`​|主相机的世界坐标位置，类型：float3|
|​`UnityWorldSpaceViewDir(i.worldPos)`​|世界空间下的相机方向(顶点到主相机)，类型：float3|
|​`_CameraDepthTexture`​|深度纹理，需要在脚本中开启相机的深度：Camera.main.depthTextureMode = DepthTextureMode.Depth|
|​`ComputeScreenPos(float4 pos)`​|pos为裁剪空间下的坐标位置，返回的是某个投影点下的屏幕坐标位置|
|​`_ScreenParams`​|屏幕的相关参数，单位为像素。x表示屏幕的宽度，y表示屏幕的高度，z表示1+1/屏幕宽度，w表示1+1/屏幕高度|

#### URP渲染管线

|变量|描述|
| ------| ----------------------------------------------|
|​`_WorldSpaceCameraPos`​|相机在世界空间下的位置坐标(xyz)|
|​`_ZBufferParams`​|Z Buffer参数，包含近远裁剪面信息|
|​`_CameraDepthTexture`​|深度纹理，在PipelineAsset中勾选DepthTexture|
|​`_CameraOpaqueTexture`​|抓屏纹理，在PipelineAsset中勾选OpaqueTexture|
|​`_ScreenParams`​|屏幕的相关参数，单位为像素|
|​`_ScaledScreenParams`​|同上，但是有考虑到RenderScale的影响|

### Time

|变量|描述|
| ------| ----------------------------------------------------------------------------------|
|​`_Time`​|时间，主要用于在Shader做动画，类型：float4<br />x = t/20<br />y = t<br />z = t*2w = t*3|
|​`_SinTime`​|t是时间的正弦值，返回值(-1~1)<br />x = t/8<br />y = t/4<br />z = t/2<br />w = t|
|​`_CosTime`​|t是时间的余弦值，返回值(-1~1)<br />x = t/8<br />y = t/4<br />z = t/2<br />w = t|
|​`unity_DeltaTime`​|dt是时间增量，smoothDt是平滑时间<br />x = dt<br />y = 1/dt<br />z = smoothDt<br />w = 1/smoothDt|

### Lighting (内置渲染管线)

|变量|描述|
| ------| --------------------------------------------------------------|
|​`_LightColor0`​|主平行灯的颜色值，rgb = 颜色x亮度；a = 亮度|
|​`_WorldSpaceLightPos0`​|平行灯：(xyz=位置,z=0)，已归一化<br />其它类型灯：(xyz=位置,z=1)|
|​`unity_WorldToLight`​|从世界空间转换到灯光空间下，等同于旧版的_LightMatrix0|

### Fog and Ambient (内置渲染管线)

|变量|描述|
| ------| --------------------------------------------------------------|
|​`unity_AmbientSky`​|环境光（Gradient）中的Sky Color|
|​`unity_AmbientEquator`​|环境光（Gradient）中的Equator Color|
|​`unity_AmbientGround`​|环境光（Gradient）中的Ground Color|
|​`UNITY_LIGHTMODEL_AMBIENT`​|环境光(Color)中的颜色，等同于环境光（Gradient）中的Sky Color|

### GPU Instancing

|内容|描述|
| ----------------| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|实现步骤|用于对多个对象(网格一样，材质一样，但是材质属性不一样)合批，单个合批最大上限为511个对象<br />1. #pragma multi_compile_instancing 添加此指令后会使材质面板上曝露Instaning开关<br />2. UNITY_VERTEX_INPUT_INSTANCE_ID 在顶点着色器的输入(appdata)和输出(v2f可选)中添加(uint instanceID : SV_InstanceID)<br />3. 构建需要实例化的额外数据<br />4. UNITY_SETUP_INSTANCE_ID(v) 放在顶点着色器/片断着色器(可选)中最开始的地方<br />5. UNITY_TRANSFER_INSTANCE_ID(v, o) 当需要将实例化ID传到片断着色器时，在顶点着色器中添加<br />6. UNITY_ACCESS_INSTANCED_PROP(arrayName, propName) 在片断着色器中访问具体的实例化变量|
|Instancing选项|对GPU Instancing进行一些设置<br />• #pragma instancing_options forcemaxcount:batchSize 强制设置单个批次内Instancing的最大数量<br />• #pragma instancing_options maxcount:batchSize 设置单个批次内Instancing的最大数量|

---

## BuildIn Transformation（内置变换）

### 空间变换(矩阵)

#### 内置渲染管线

|矩阵|描述|
| ------| --------------------|
|​`UNITY_MATRIX_MVP`​|模型空间>>投影空间|
|​`UNITY_MATRIX_MV`​|模型空间>>观察空间|
|​`UNITY_MATRIX_V`​|视图空间|
|​`UNITY_MATRIX_P`​|投影空间|
|​`UNITY_MATRIX_VP`​|视图空间>投影空间|
|​`unity_ObjectToWorld`​|本地空间>>世界空间|
|​`unity_WorldToObject`​|世界空间>本地空间|

#### URP渲染管线

|函数|描述|
| ------| ------------------------------|
|​`GetObjectToWorldMatrix()`​|本地空间到世界空间的矩阵|
|​`GetWorldToObjectMatrix()`​|世界空间到本地空间的矩阵|
|​`GetWorldToViewMatrix()`​|世界空间到观察空间的矩阵|
|​`GetWorldToHClipMatrix()`​|世界空间到齐次裁剪空间的矩阵|
|​`GetViewToHClipMatrix()`​|观察空间到齐次裁剪空间的矩阵|

### 空间变换(方法)

#### 内置渲染管线

|函数|描述|
| ------| --------------------------------------------|
|​`UnityObjectToClipPos(v.vertex)`​|将模型空间下的顶点转换到齐次裁剪空间|
|​`UnityObjectToWorldNormal(v.normal)`​|将模型空间下的法线转换到世界空间(已归一化)|
|​`UnityObjectToWorldDir (v.tangent)`​|将模型空间下的切线转换到世界空间(已归一化)|
|​`UnityWorldSpaceLightDir (i.worldPos)`​|世界空间下顶点到灯光方向的向量(未归一化)|
|​`UnityWorldSpaceViewDir (i.worldPos)`​|世界空间下顶点到视线方向的向量(未归一化)|

#### URP渲染管线

|函数|描述|
| ------| ------------------------------------------|
|​`float3 TransformObjectToWorld (float3 positionOS)`​|从本地空间变换到世界空间|
|​`float3 TransformWorldToObject (float3 positionWS)`​|从世界空间变换到本地空间|
|​`float3 TransformWorldToView(float3 positionWS)`​|从世界空间变换到视图空间|
|​`float4 TransformObjectToHClip(float3 positionOS)`​|将模型空间下的顶点变换到齐次裁剪空间|
|​`float4 TransformWorldToHClip(float3 positionWS)`​|将世界空间下的顶点变换到齐次裁剪空间|
|​`float4 TransformWViewToHClip (float3 positionVS)`​|将视图空间下的顶点变换到齐次裁剪空间|
|​`float3 TransformObjectToWorldNormal (float3 normalOS)`​|将法线从本地空间变换到世界空间(已归一化)|

### 基础变换矩阵

|矩阵类型|矩阵定义|
| ---------------| ----------|
|平移矩阵|​`float4x4 M_translate = float4x4(    1, 0, 0, T.x,    0, 1, 0, T.y,    0, 0, 1, T.z,    0, 0, 0, 1);`​|
|缩放矩阵|​`float4x4 M_scale = float4x4(    S.x, 0, 0, 0,    0, S.y, 0, 0,    0, 0, S.z, 0,    0, 0, 0, 1);`​|
|旋转矩阵(X轴)|​`float4x4 M_rotationX = float4x4(    1, 0, 0, 0,    0, cos(θ), sin(θ), 0,    0, -sin(θ), cos(θ), 0,    0, 0, 0, 1);`​|
|旋转矩阵(Y轴)|​`float4x4 M_rotationY = float4x4(    cos(θ), 0, sin(θ), 0,    0, 1, 0, 0,    -sin(θy), 0, cos(θ), 0,    0, 0, 0, 1);`​|
|旋转矩阵(Z轴)|​`float4x4 M_rotationZ = float4x4(    cos(θ), sin(θ), 0, 0,    -sin(θ), cos(θ), 0, 0,    0, 0, 1, 0,    0, 0, 0, 1);`​|

---

## Lighting（光照）

### 光照模型

#### Lambertian

```
Diffuse = Ambient + Kd * LightColor * max(0,dot(N,L))
```

- **Diffuse**: 最终物体上的漫反射光强
- **Ambient**: 环境光强度，为了简化计算，环境光强采用一个常数表示
- **Kd**: 物体材质对光的反射系数
- **LightColor**: 光源的强度
- **N**: 顶点的单位法线向量
- **L**: 顶点指向光源的单位向量

#### Phong

```
Specular = SpecularColor * Ks * pow(max(0,dot(R,V)), Shininess)
```

- **Specular**: 最终物体上的反射高光光强
- **SpecularColor**: 反射光的颜色
- **Ks**: 反射强度系数
- **R**: 反射向量，可使用2 * N * dot(N,L) - L或者reflect(-L,N)获得
- **V**: 观察方向
- **N**: 顶点的单位法线向量
- **L**: 顶点指向光源的单位向量
- **Shininess**: 乘方运算来模拟高光的变化

#### Blinn-Phong

```
Specular = SpecularColor * Ks * pow(max(0,dot(N,H)), Shininess)
```

- **H**: 入射光线L与视线V的中间向量，也称为半角向量H = normalize(L+V)

#### Disney Principled BRDF

```
f(l,v) = diffuse + D(h)F(v,h)G(l,v,h)/4cos(n·l)cos(n·v)
```

- **f(l,v)** : 双向反射分布函数的最终值，l表示光的方向，v表示视线的方向
- **diffuse**: 漫反射
- **D(h)** : 法线分布函数(Normal Distribution Function)
- **F(v,h)** : 菲涅尔方程(Fresnel Equation)
- **G(l,v,h)** : 几何函数(Geometry Function)

### 法线贴图

#### 内置渲染管线

1. appdata中定义NORMAL与TANGENT语义
2. v2f中声明三个变量用于组成成切线空间下的旋转矩阵
3. 在顶点着色器中执行切线空间转换
4. 在片断着色器中计算出世界空间下的法线

#### URP渲染管线

1. Attributes中定义NORMAL与TANGENT语义
2. Varyings中声明三个变量用于组成成切线空间下的旋转矩阵
3. 在顶点着色器中执行切线空间转换
4. 在片断着色器中计算出世界空间下的法线

### 阴影

#### 内置渲染管线

- **生成阴影**: 添加"LightMode" = "ShadowCaster"的Pass
- **采样阴影**: 使用UNITY_SHADOW_COORDS、TRANSFER_SHADOW、UNITY_LIGHT_ATTENUATION

#### URP渲染管线

- **投射阴影**: 添加LightMode=ShadowCaster的pass即可渲染进lightmap产生投影
- **接收阴影**: 使用相关宏定义来控制阴影接收

### 雾效

#### 内置渲染管线

- **方案一**: 常规方案，使用UNITY_FOG_COORDS、UNITY_TRANSFER_FOG、UNITY_APPLY_FOG
- **方案二**: 当在v2f中有定义worldPos时，可以把worldPos.w利用起来做为雾效值

#### URP渲染管线

- 使用#pragma multi_compile_fog
- 在Varyings中定义fogCoord
- 在顶点着色器中添加ComputeFogFactor
- 在片断着色器中添加MixFog

---

## Tags（标签）

### 基础语法

```hlsl
Tags { "TagName1" = "Value1" "TagName2" = "Value2" }
```

### RenderPipeline（URP）

|标签|描述|
| ------| ---------------------------------------------------------------------------------------------------------------------------------|
|​`"RenderPipeline" = "UniversalPipeline"`​|渲染管线标记，对应的管线C#代码UniversalRenderPipeline.cs中的Shader.globalRenderPipeline = UniversalPipeline,LightweightPipeline|

### Queue（渲染队列）

|队列|值|描述|
| ------| ------| ----------------------------------------------------------------|
|​`"Queue" = "Background"`​|1000|此队列的对象最先进行渲染|
|​`"Queue" = "Geometry"`​|2000|默认值，通常用于不透明对象，比如场景中的物件与角色等|
|​`"Queue" = "AlphaTest"`​|2450|要么完全透明要么完全不透明，多用于利用贴图来实现边缘透明的效果|
|​`"Queue" = "Transparent"`​|3000|常用于半透明对象，渲染时从后往前进行渲染|
|​`"Queue" = "Overlay"`​|4000|此渲染队列用于叠加效果，最后渲染的东西应该放在这里|

### LightMode（Pass中）

#### 内置渲染管线

|模式|描述|
| ------| ------------------------------------------------------------|
|​`ForwardBase`​|用于前向渲染路径，支持环境光、主像素光、球谐光照与烘焙光照|
|​`ForwardAdd`​|用于前向渲染路径，支持额外的逐像素光照，每盏灯一个Pass|
|​`Deferred`​|用于延迟渲染|
|​`ShadowCaster`​|深度渲染与Shadowmap|
|​`MotionVectors`​|运动矢量|
|​`Always`​|永远渲染|

#### URP渲染管线

|模式|描述|
| ------| --------------------------------------------------|
|​`"LightMode" = "UniversalForward"`​|用于前向渲染路径，所有的灯光都在这一个pass中执行|
|​`"LightMode" = "SRPDefaultUnlit"`​|用于在额外需要一个pass时使用|
|​`"LightMode" = "ShadowCaster"`​|用于生成阴影贴图ShadowMap|
|​`"LightMode" = "DepthOnly"`​|用于生成相机下的深度信息|
|​`"LightMode" = "DepthNormals"`​|用于生成相机下的深度法线信息|
|​`"LightMode" = "Meta"`​|仅在光照烘焙时才会使用此Pass|
|​`"LightMode" = "Universal2D"`​|用于URP使用2D渲染器时绘制物体的Pass|

### RenderType

|类型|描述|
| ------| --------------------------------------------|
|​`"RenderType" = "Opaque"`​|大多数不透明着色器|
|​`"RenderType" = "Transparent"`​|大多数半透明着色器，比如粒子、特效、字体等|
|​`"RenderType" = "TransparentCutout"`​|透贴着色器，多用于植被等|
|​`"RenderType" = "Background"`​|多用于天空盒着色器|
|​`"RenderType" = "Overlay"`​|GUI、光晕着色器等|

### 其他标签

|标签|描述|
| ------| --------------------------------|
|​`"DisableBatching" = "True"`​|禁用批处理|
|​`"ForceNoShadowCasting" = "True"`​|强制关闭阴影投射|
|​`"IgnoreProjector" = "True"`​|不受投影器影响|
|​`"CanUseSpriteAtlas" = "True"`​|支持精灵打包图集|
|​`"PreviewType" = "Plane"`​|材质面板中的预览模型显示为平面|
|​`"PerformanceChecks" = "True"`​|开启性能检测提示|
