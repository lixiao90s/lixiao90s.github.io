---
title: Unity Shader 参考文档 - 第三部分
date: '2025-07-31 09:06:46'
permalink: /post/unity-shader-reference-document-part-3-z2bufyb.html
tags:
  - shader
  - unity
layout: post
published: true
---





---

## Pragma（编译指令）

### 基础编译指令

|指令|描述|
| ------| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`#pragma target 2.0`​|Shader编绎目标级别，默认值为2.5<br />• 2.0: 基础功能<br />• 2.5: derivatives<br />• 3.0: 2.5 + interpolators10 + samplelod + fragcoord<br />• 3.5: 3.0 + interpolators15 + mrt4 + integers + 2darray + instancing<br />• 4.0: 3.5 + geometry<br />• 4.5: 3.5 + compute + randomwrite<br />• 4.6: 4.0 + cubearray + tesshw + tessellation<br />• 5.0: 4.0 + compute + randomwrite + tesshw + tessellation|
|​`#pragma require xxx`​|表明shader需要的特性功能<br />• interpolators10: 至少支持10个插值器<br />• interpolators15: 至少支持15个插值器<br />• interpolators32: 至少支持32个插值器<br />• mrt4: 至少支持4个Multiple Render Targets<br />• mrt8: 至少支持8个Multiple Render Targets<br />• derivatives: 片断着色器支持偏导函数(ddx/ddy)<br />• samplelod: 纹理LOD采样<br />• fragcoord: 将像素的位置传入到片断着色器中<br />• integers: 支持真正的整数类型<br />• 2darray: 2D纹理数组<br />• cubearray: Cubemap纹理数组<br />• instancing: GPU实例化<br />• geometry: 几何着色器<br />• compute: Compute Shader<br />• randomwrite: 可以编写任意位置的一些纹理和缓冲区|
|​`#pragma shader_feature`​|变体声明，常用于不需要程序控制开关的关键字，在编缉器的材质上设置，打包时会自动过滤|
|​`#pragma shader_feature_local`​|声明本地变体(shader_feature)，unity2019才支持的功能，每个Shader最多可以有64个本地变体|
|​`#pragma multi_compile`​|变体声明，在打包时会把所有变体都打包进去，这是它与feature的区别|
|​`#pragma multi_compile_local`​|声明本地变体(multi_compile)，unity2019才支持的功能|
|​`#pragma multi_compile_fog`​|雾类型定义：FOG_EXP FOG_EXP2 FOG_LINEAR|
|​`#pragma skip_variants XXX01 XXX02...`​|剔除指定的变体，可同时剔除多个|

### 内置渲染管线特定指令

|指令|描述|
| ------| ---------------------------------------------------------------------------------|
|​`#pragma multi_compile_fwdbase`​|定义在LightMode = ForwardBase的Pass中，生成Unity在ForwardBase中需要的各种内置宏|
|​`#pragma multi_compile_fwdadd`​|定义在LightMode=ForwardAdd的Pass中，生成Unity在ForwardAdd中需要的各种内置宏|
|​`#pragma multi_compile_shadowcaster`​|定义在LightMode=ShadowCaster的Pass中，会自动生成SHADOWS_DEPTH和SHADOW_CUBE宏|

### URP特定指令

|指令|描述|
| ------| ---------------------------------------------------|
|​`#pragma prefer_hlslcc gles`​|将HLSL代码转换为GLSL代码时，优先使用HLSLcc转换器|
|​`#include "XXX.hlsl"`​|引入hlsl文件|
|​`#include_with_pragmas "XXX.hlsl"`​|引入hlsl文件，同时也会使用hlsl文件中的#pragma指令|
|​`#pragma editor_sync_compilation`​|强制某个Shader以同步的方式进行编绎|

### 平台相关指令

|指令|描述|
| ------| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`#pragma only_renderers`​|仅编译指定平台的Shader<br />• d3d11 - Direct3D 11/12<br />• glcore - OpenGL 3.x/4.x<br />• gles - OpenGL ES 2.0<br />• gles3 - OpenGL ES 3.x<br />• metal - iOS/Mac Metal<br />• vulkan - Vulkan<br />• d3d11_9x - Direct3D 11 9.x功能级别<br />• xboxone - Xbox One<br />• ps4 - PlayStation 4<br />• psp2 - PlayStation Vita<br />• n3ds - Nintendo 3DS<br />• wiiu - Nintendo Wii U|
|​`#pragma exclude_renderers`​|剔除掉指定平台的相关代码|
|​`#pragma once`​|通常添加在通用的.hlsl文件中，以此来避免多次或者重复引用和编绎|

### 其他指令

|指令|描述|
| ------| ------------------------------------------------------|
|​`#define NAME`​|定义一个叫NAME的字段|
|​`#define NAME 1`​|定义一个叫NAME的字段并且它的值为1|
|​`#error xxx`​|多用于分支的判断中，利用此语句可直接输出一条报错信息|
|​`#pragma fragmentoption ARB_precision_hint_fastest`​|最快的，意思就是会用低精度|
|​`#pragma fragmentoption ARB_precision_hint_nicest`​|最佳的，会用高精度|
|​`#pragma enable_d3d11_debug_symbols`​|开启调试，便于在调试工具中进行查看分析|
|​`#pragma skip_optimizations <gles/vulkan...>`​|调试用，禁用某平台的优化|
|​`#pragma shader_feature EDITOR_VISUALIZATION`​|开启Material Validation，Scene视图中的模式|

---

## Render State（渲染状态）

### Cull（背面剔除）

```hlsl
Cull Back | Front | Off
```

- **Back**: 表示剔除背面，也就是显示正面，这也是最常用的设置
- **Front**: 表示剔除前面，只显示背面
- **Off**: 表示关闭剔除，也就是正反面都渲染

### 模版测试（Stencil）

```hlsl
Stencil
{
    Ref [_Stencil]
    ReadMask [_StencilReadMask]
    WriteMask [_StencilWriteMask]
    Comp [_StencilComp]
    Pass [_StencilOp]
    Fail [_Fail]
    ZFail [_ZFail]
}
```

#### Comp（比较操作）

|操作|描述|
| ------| ----------------------------------------------------------|
|​`Less`​|相当于"<"操作，即仅当左边<右边，模板测试通过，渲染像素|
|​`Greater`​|相当于">"操作，即仅当左边>右边，模板测试通过，渲染像素|
|​`Lequal`​|相当于"<="操作，即仅当左边<=右边，模板测试通过，渲染像素|
|​`Gequal`​|相当于">="操作，即仅当左边>=右边，模板测试通过，渲染像素|
|​`Equal`​|相当于"="操作，即仅当左边=右边，模板测试通过，渲染像素|
|​`NotEqual`​|相当于"!="操作，即仅当左边!=右边，模板测试通过，渲染像素|
|​`Always`​|不管公式两边为何值，模板测试总是通过，渲染像素|
|​`Never`​|不管公式两边为何值，模板测试总是失败，像素被抛弃|

#### 模版缓冲区的更新

|操作|描述|
| ------| ---------------------------------------------------------------------|
|​`Keep`​|保留当前缓冲中的内容，即stencilBufferValue不变|
|​`Zero`​|将0写入缓冲，即stencilBufferValue值变为0|
|​`Replace`​|将参考值写入缓冲，即将referenceValue赋值给stencilBufferValue|
|​`IncrSat`​|将当前模板缓冲值加1，如果stencilBufferValue超过255了，那么保留为255|
|​`DecrSat`​|将当前模板缓冲值减1，如果stencilBufferValue超过为0，那么保留为0|
|​`Invert`​|将当前模板缓冲值（stencilBufferValue）按位取反|
|​`IncrWrap`​|当前缓冲的值加1，如果缓冲值超过255了，那么变成0|
|​`DecrWrap`​|当前缓冲的值减1，如果缓冲值已经为0，那么变成255|

### 深度缓冲

|设置|描述|
| --------------| ---------------------------------------------------------------------------------------------|
|`ZTest (Less|Greater|
|​`ZTest[unity_GUIZTestMode]`​|unity_GUIZTestMode用于UI材质中，此值默认为LEqual，仅当UI中Canvas模式为Overlay时，值为Always|
|`ZWrite On|Off`|
|`ZClip True|False`|
|​`Offset Factor, Units`​|深度偏移，offset = (m * factor) + (r * units)，默认值为0,0|

### 颜色遮罩

```hlsl
ColorMask RGB | A | 0 | R、G、B、A的任意组合
```

颜色遮罩，默认值为：RGBA，表示写入RGBA四个通道。

### 混合（Blend）

```hlsl
源颜色*SrcFactor + 目标颜色*DstFactor
```

#### 混合因子

|因子|描述|
| ------| ---------------------|
|​`One`​|源或目标的完整值|
|​`Zero`​|0|
|​`SrcColor`​|源的颜色值|
|​`SrcAlpha`​|源的Alpha值|
|​`DstColor`​|目标的颜色值|
|​`DstAlpha`​|目标的Alpha值|
|​`OneMinusSrcColor`​|1-源颜色得到的值|
|​`OneMinusSrcAlpha`​|1-源Alpha得到的值|
|​`OneMinusDstColor`​|1-目标颜色得到的值|
|​`OneMinusDstAlpha`​|1-目标Alpha得到的值|

#### 常用的几种混合类型

```hlsl
Blend SrcAlpha OneMinusSrcAlpha  // Traditional transparency
Blend One OneMinusSrcAlpha       // Premultiplied transparency
Blend One One                     // Additive
Blend OneMinusDstColor One       // Soft Additive
Blend DstColor Zero              // Multiplicative
Blend DstColor SrcColor          // 2x Multiplicative
```

#### 混合操作的具体运算符

|操作|描述|
| ------| ------------------|
|​`Add`​|源+目标|
|​`Sub`​|源-目标|
|​`RevSub`​|目标-源|
|​`Min`​|源与目标中最小值|
|​`Max`​|源与目标中最大值|

### 其他

|设置|描述|
| --------------------| --------|
|`AlphaToMask On|Off`|
|`Conservative True|False`|

---

## Transformation（空间变换）

### 空间变换(矩阵)

|矩阵|描述|
| ------| ---------------------------------------------------------------------------|
|​`UNITY_MATRIX_M`​|模型变换矩阵:unity_ObjectToWorld|
|​`UNITY_MATRIX_I_M`​|模型变换逆矩阵:unity_WorldToObject|
|​`UNITY_MATRIX_V`​|视图变换矩阵:unity_MatrixV|
|​`UNITY_MATRIX_I_V`​|视图变换逆矩阵:unity_MatrixInvV|
|​`UNITY_MATRIX_P`​|投影变换矩阵:OptimizeProjectionMatrix(glstate_matrix_projection)|
|​`UNITY_MATRIX_I_P`​|投影变换逆矩阵:ERROR_UNITY_MATRIX_I_P_IS_NOT_DEFINED|
|​`UNITY_MATRIX_VP`​|视图投影变换矩阵:unity_MatrixVP|
|​`UNITY_MATRIX_I_VP`​|视图投影变换逆矩阵:_InvCameraViewProj|
|​`UNITY_MATRIX_MV`​|模型视图变换矩阵:mul(UNITY_MATRIX_V, UNITY_MATRIX_M)|
|​`UNITY_MATRIX_T_MV`​|模型视图变换转置矩阵:transpose(UNITY_MATRIX_MV)|
|​`UNITY_MATRIX_IT_MV`​|模型视图变换转置逆矩阵:transpose(mul(UNITY_MATRIX_I_M, UNITY_MATRIX_I_V))|
|​`UNITY_MATRIX_MVP`​|模型视图投影变换矩阵:mul(UNITY_MATRIX_VP, UNITY_MATRIX_M)|

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
| ------| ------------------------------------|
|​`float3 TransformObjectToWorld(float3 positionOS)`​|模型空间>>世界空间|
|​`float3 TransformWorldToObject(float3 positionWS)`​|世界空间>>模型空间|
|​`float3 TransformWorldToView(float3 positionWS)`​|世界空间>>视图空间|
|​`float4 TransformObjectToHClip(float3 positionOS)`​|模型空间>>齐次裁剪空间 (比MVP高效)|
|​`float4 TransformWorldToHClip(float3 positionWS)`​|世界空间>>齐次裁剪空间|
|​`float4 TransformWViewToHClip(float3 positionVS)`​|视图空间>>齐次裁剪空间|
|​`real3 TransformObjectToWorldDir(real3 dirOS)`​|(向量)模型空间>>世界空间|
|​`real3 TransformWorldToObjectDir(real3 dirWS)`​|(向量)世界空间>>模型空间|
|​`real3 TransformWorldToViewDir(real3 dirWS)`​|(向量)世界空间>>视图空间|
|​`real3 TransformWorldToHClipDir(real3 directionWS)`​|(向量)世界空间>齐次裁剪空间|
|​`float3 TransformObjectToWorldNormal(float3 normalOS)`​|(法线)模型空间>>世界空间(已归一化)|
|​`float3 TransformWorldToObjectNormal(float3 normalWS)`​|(法线)世界空间>>模型空间(已归一化)|

### 基础变换矩阵

|矩阵类型|矩阵定义|
| ---------------| ----------|
|平移矩阵|​`float4x4 M_translate = float4x4(    1, 0, 0, T.x,    0, 1, 0, T.y,    0, 0, 1, T.z,    0, 0, 0, 1);`​|
|缩放矩阵|​`float4x4 M_scale = float4x4(    S.x, 0, 0, 0,    0, S.y, 0, 0,    0, 0, S.z, 0,    0, 0, 0, 1);`​|
|旋转矩阵(X轴)|​`float4x4 M_rotationX = float4x4(    1, 0, 0, 0,    0, cos(θ), -sin(θ), 0,    0, sin(θ), cos(θ), 0,    0, 0, 0, 1);`​|
|旋转矩阵(Y轴)|​`float4x4 M_rotationY = float4x4(    cos(θ), 0, sin(θ), 0,    0, 1, 0, 0,    -sin(θ), 0, cos(θ), 0,    0, 0, 0, 1);`​|
|旋转矩阵(Z轴)|​`float4x4 M_rotationZ = float4x4(    cos(θ), -sin(θ), 0, 0,    sin(θ), cos(θ), 0, 0,    0, 0, 1, 0,    0, 0, 0, 1);`​|

### 变换规则

```
将P点从A空间变换到B空间
P_B = M_AB * P_A
     = (M_BA)^-1 * P_A
     = (M_BA)^T * P_A
```

---

## Predefined Macros（预定义宏）

### Target platform

|宏|描述|
| ------| ----------------------------------------|
|​`SHADER_API_D3D11`​|Direct3D 11|
|​`SHADER_API_GLCORE`​|桌面OpenGL核心(GL3/4)|
|​`SHADER_API_GLES`​|OpenGl ES 2.0|
|​`SHADER_API_GLES3`​|OpenGl ES 3.0/3.1|
|​`SHADER_API_METAL`​|IOS/Mac Metal|
|​`SHADER_API_VULKAN`​|Vulkan|
|​`SHADER_API_D3D11_9X`​|Direct3D 11 9.x功能级别|
|​`SHADER_API_PS4`​|PS4平台，SHADER_API_PSSL同时也会被定义|
|​`SHADER_API_XBOXONE`​|Xbox One|
|​`SHADER_API_MOBILE`​|所有移动平台(GLES/GLES3/METAL)|
|​`SHADER_API_DESKTOP`​|Window、Mac和Linux桌面平台|

### 流程控制

|宏|描述|
| ------| --------------------------------------------------------------------------------------------|
|​`#define UNITY_BRANCH [branch]`​|添加在if语句上面，表示此语句会根据判断只执行符合条件的代码，有跳转指令|
|​`#define UNITY_FLATTEN [flatten]`​|添加在if语句上面，表示此语句会执行全部代码，然后根据条件来选择某个具体的结果，没有跳转指令|
|​`#define UNITY_UNROLL [unroll]`​|添加在for语句上面，表示此循环在编绎时会展开所有循环代码|
|​`#define UNITY_UNROLLX(_x) [unroll(_x)]`​|添加在for语句上面，功能同上，同时可以指定具体的展开次数|
|​`#define UNITY_LOOP [loop]`​|添加在for语句上面，表示此循环不可展开，正常执行跳转|

### Shader target model

|宏|描述|
| ------| ------------------------------------------------|
|​`#if SHADER_TARGET < 30`​|对应于#pragma target的值，2.0就是20，3.0就是30|

### Unity version

|宏|描述|
| ------| -------------------------------|
|​`#if UNITY_VERSION >= 500`​|Unity版本号判断，500表示5.0.0|

### Platform difference helpers

|宏|描述|
| ------| ------------------------------------------------------------------|
|​`UNITY_UV_STARTS_AT_TOP`​|一般此判断当前平台是DX(UV原点在左上角)还是OpenGL(UV原点在左下角)|
|​`UNITY_NO_SCREENSPACE_SHADOWS`​|定义移动平台不进行Cascaded ScreenSpace Shadow|

### UI

|宏|描述|
| ------| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`UNITY_UI_CLIP_RECT`​|当父级物体有Rect Mask 2D组件时激活<br />需要先手动定义此变体#pragma multi_compile _ UNITY_UI_CLIP_RECT<br />同时需要声明：_ClipRect(一个四维向量)<br />UnityGet2DClipping (float2 position, float4 clipRect)即可实现遮罩|

### Lighting

|宏|描述|
| ------| ------------------------------------------------------------------------|
|​`UNITY_SHOULD_SAMPLE_SH`​|是否进行计算SH（光照探针与顶点着色）|
|​`UNITY_SAMPLE_FULL_SH_PER_PIXEL`​|光照贴图uv和来自SHL2的环境颜色在顶点和像素内插器中共享|
|​`HANDLE_SHADOWS_BLENDING_IN_GI`​|当同时定义了SHADOWS_SCREEN与LIGHTMAP_ON时开启|
|​`UNITY_SHADOW_COORDS(N)`​|定义一个float4类型的变量_ShadowCoord，语义为第N个TEXCOORD|
|​`V2F_SHADOW_CASTER;`​|用于"LightMode" = "ShadowCaster"中，相当于定义了float4 pos:SV_POSITION|

### Other

|宏|描述|
| ------| -----------------------------------------------------------------------|
|​`UNITY_SHADER_NO_UPGRADE`​|另Shader不自动更新API，只需把语句用注释的形式写在shader中任意位置即可|

### URP特定宏

|宏|描述|
| ------| --------------------------------------------------------------------------------|
|​`UNITY_NO_DXT5nm`​|法线纹理是否不采用DXT5压缩，移动平台是，非移动平台否|
|​`UNITY_ASTC_NORMALMAP_ENCODING`​|当在移动平台中，法线纹理采用类DXT5nm的压缩时启用|
|​`SHADERGRAPH_PREVIEW`​|在ShaderGraph中的Custom Function自义定节点上使用，用于判断是否处于SG面板预览中|
