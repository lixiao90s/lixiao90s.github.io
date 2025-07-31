---
title: Unity Shader 参考文档 - 第一部分
date: '2025-07-31 09:05:58'
permalink: /post/unity-shader-reference-document-part-1-z21bblp.html
tags:
  - shader
  - unity
layout: post
published: true
---





---

## Semantics（语义）

### 应用程序到顶点着色器的数据（appdata）

|语义|描述|
| ------| ------------------|
|​`float4 vertex : POSITION;`​|顶点的本地坐标|
|​`uint vid : SV_VertexID;`​|顶点的索引ID|
|​`float3 normal : NORMAL;`​|顶点的法线信息|
|​`float4 tangent : TANGENT;`​|顶点的切线信息|
|​`float4 texcoord : TEXCOORD0;`​|顶点的UV1信息|
|​`float4 texcoord1 : TEXCOORD1;`​|顶点的UV2信息|
|​`float4 texcoord2 : TEXCOORD2;`​|顶点的UV3信息|
|​`float4 texcoord3 : TEXCOORD3;`​|顶点的UV4信息|
|​`fixed4 color : COLOR;`​|顶点的顶点色信息|

### 顶点着色器到片断着色器的数据 (v2f)

|语义|描述|
| ------| --------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`float4 pos:SV_POSITION;`​|顶点的齐次裁剪空间下的坐标，默认情况下用POSITION也可以(PS4下不支持)，但是为了支持所有平台，所以最好使用SV_POSITION|
|​`TEXCOORD0~N`​|例如TEXCOORD0、TEXCOORD1、TEXCOORD2...等等|
|​`COLOR0~N`​|例如COLOR0、COLOR1、COLOR2...等等，主要用于低精度数据，由于平台适配问题，不建议在v2f中使用COLORn|
|​`float face:VFACE`​|如果渲染表面朝向摄像机，则Face节点输出正值1，如果远离摄像机，则输出负值-1|
|​`UNITY_VPOS_TYPE screenPos : VPOS`​|当前片断在屏幕上的位置(单位是像素,可除以_ScreenParams.xy来做归一化)，此功能仅支持#pragma target 3.0及以上编译指令|
|​`uint vid : SV_VertexID`​|顶点着色器可以接收具有"顶点编号"作为无符号整数的变量，当需要从纹理或ComputeBuffers中获取额外的顶点数据时比较有用，此语义仅支持#pragma target 3.5及以上|

**注意事项：**

- OpenGL ES2.0支持最多8个
- OpenGL ES3.0支持最多16个

### 片断着色器输出的数据（fragOutput）

|语义|描述|
| ------| --------------------------------------------------------------------------------------------------------------------|
|​`fixed4 color : SV_Target;`​|默认RenderTarget，也是默认输出的屏幕上的颜色，同时支持SV_Target1、SV_Target2...等等|
|​`fixed depth : SV_Depth;`​|通过在片断着色器中输出SV_DEPTH语义可以更改像素的深度值，注意此功能相对会消耗性能，在没有特别需求的情况下尽量不要用|

---

## Properties（属性）

### 基础属性类型

|属性|描述|
| ------| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`_Int("Int", Int) = 1`​|类型：整型<br />Cg/HLSL：int<br />取决于在Cg/HLSL中是用float还是int来声明的，如果定义为float则实际使用的就是浮点数，定义为int会被识别为int类型（去小数点直接取整）|
|​`_Float ("Float", Float ) = 0`​|类型：浮点数值<br />Cg/HLSL：可根据需要定义不同的浮点精度<br />• float：32位精度，常用于世界坐标位置以及UV坐标<br />• half：16位精度，范围[-6W,6W]，常用于本地坐标位置,方向等<br />• fixed：11位精度，范围[-2,2]，1/265的小数精度，常用于纹理与颜色等低精度的情况|
|​`_Slider ("Slider", Range(0, 1)) = 0`​|类型：数值滑动条<br />本身还是Float类型，只是通过Range(min,max)来控制滑动条的最小值与最大值|
|​`_Color("Color", Color) = (1,1,1,1)`​|类型：颜色属性<br />Cg/HLSL：float4/half4/fixed4|
|​`_Vector ("Vector", Vector) = (0,0,0,0)`​|类型：四维向量<br />在Properties中无法定义二维或者三维向量，只能定义四维向量|

### 纹理属性

|属性|描述|
| ------| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`_MainTex ("Texture", 2D) = "white" {}`​|类型：2D纹理<br />Cg/HLSL：sampler2D/sampler2D_half/sampler2D_float<br />默认值有white、black、gray、bump以及空，空就是white|
|​`_MainTexArray ("TextureArray", 2DArray) = "white" {}`​|类型：2D纹理数组<br />TEXTURE2D_ARRAY(_MainTexArray); SAMPLER(sampler_MainTexArray);<br />SAMPLE_TEXTURE2D_ARRAY(_MainTexArry, sampler_MainTexArry, i.uv,_Index);<br />默认值有white、black、gray、bump以及空，空就是white<br />仅支持DX10、OpenGL3.0、Metal及以上版本|
|​`_MainTex3D ("Texture", 3D) = "white" {}`​|类型：3D纹理<br />Cg/HLSL：sampler3D/sampler3D_half/sampler3D_float|
|​`_MainCube ("Texture", Cube) = "white" {}`​|类型：立方体纹理<br />Cg/HLSL：samplerCUBE/samplerCUBE_half/samplerCUBE_float|
|​`_MainTex ("Texture", Any) = "white" {}`​|类型：通用纹理<br />支持2D、3D、Cube|

### Attributes（属性修饰符）

|修饰符|描述|
| --------| -------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|​`[Header(xxx)]`​|用于在材质面板中当前属性的上方显示标题xxx，注意只支持英文、数字、空格以及下划线|
|​`[HideInInspector]`​|在材质面板中隐藏此条属性，在不希望暴露某条属性时可以快速将其隐藏|
|​`[Space(n)]`​|使材质面板属性之前有间隔，n为间隔的数值大小|
|​`[HDR]`​|标记为属性为高动态范围|
|​`[PowerSlider(3)]`​|滑条曲率，必须加在range属性前面，用于控制滑动的数值比例|
|​`[IntRange]`​|必须使用在Range属性之上，以使在材质面板中滑动时只能生成整数|
|​`[Toggle]`​|开关，加在数值类型前，可使材质面板中的数值变成开关，0是关，1是开|
|​`[ToggleOff]`​|与Toggle相当，0是开，1是关|
|​`[Enum(Off, 0, On, 1)]`​|数值枚举，可直接在cg中使用此关键字来替代数字，最多可定义7个，超出后无法以下拉列表框的形式展现|
|​`[KeywordEnum (Enum0, Enum1, Enum2, Enum3, Enum4, Enum5, Enum6, Enum7, Enum8)]`​|关键字枚举，需要#pragma multi_compile _XXX_ENUM0 _XXX_ENUM1 ...来依次声明变体关键字，XXX为这条属性中声明的变量名，在cg/hlsl中可以通过#​if #elif #endif分别做判断|
|​`[Enum (UnityEngine.Rendering.CullMode)]`​|内置枚举，可在Enum()内直接调用Unity内部的枚举|
|​`[NoScaleOffset]`​|只能加在纹理属性前，使其隐藏材质面板中的Tiling和Offset|
|​`[Normal]`​|只能加在纹理属性前，标记此纹理是用来接收法线贴图的，当用户指定了非法线的纹理时会在材质面板上进行警告提示|
|​`[Gamma]`​|Float和Vector属性默认情况下不会进行颜色空间转换，可以通过添加[Gamma]来指明此属性为sRGB值|
|​`[PerRendererData]`​|标记当前属性将以材质属性块的形式来自于每个渲染器数据|
|​`[MainTexture]`​|标记当前纹理为主纹理，便于C#通过Material.mainTexture调用|
|​`[MainColor]`​|标记当前颜色为主颜色，便于C#通过Material.color调用|

---

## Math（数学运算）

### 基础数学函数

|函数|描述|
| ------| --------------------------------------------------------------------------------|
|​`abs(x)`​|取绝对值，即正值返回正值，负值返回的还是正值，x值也可以为向量|
|​`acos(x)`​|反余弦函数，输入参数范围为[-1, 1]，返回[0, π] 区间的弧度值|
|​`asin(x)`​|返回x的反正弦值，范围为(-π/2,π/2)|
|​`atan(x)`​|返回x的反正切值，范围为(-π/2,π/2)，表示弧度|
|​`atan2(y,x)`​|返回y/x的反正切值|
|​`ceil(x)`​|对x进行向上取整，即x=0.1返回1，x=1.5返回2，x=-0.3返回0|
|​`clamp(x,a,b)`​|如果x值小于a，则返回a；如果x值大于b，返回b；否则返回x|
|​`cos(x)`​|返回弧度x的余弦值，范围在[-1,1]之间|
|​`cosh(x)`​|双曲余弦函数|
|​`degrees(x)`​|将x从弧度转换成角度|
|​`exp(x)`​|计算e的x次方，e = 2.71828182845904523536|
|​`exp2(x)`​|计算2的x次方|
|​`floor(x)`​|对x值进行向下取整(去尾求整)|
|​`fmod(x,y)`​|返回x/y的余数。如果y为0，结果不可预料，注意！如果x为负值，返回的结果也是负值！|
|​`frac(x)`​|返回x的小数部分|
|​`log(x)`​|返回x的自然对数|
|​`log2(x)`​|返回以2为底的自然对数|
|​`log10(x)`​|返回以10为底的自然对数|
|​`max(a,b)`​|比较两个标量或等长向量元素，返回最大值|
|​`min(a,b)`​|比较两个标量或等长向量元素，返回最小值|
|​`pow(x,y)`​|返回x的y次方|
|​`round(x)`​|返回x四舍五入的值|
|​`saturate(x)`​|如果x<0返回0，如果x>1返回1，否则返回x|
|​`sin(x)`​|返回弧度x的正弦值，范围在[-1,1]之间|
|​`sinh(x)`​|双曲正弦函数|
|​`sqrt(x)`​|返回x的平方根|
|​`tan(x)`​|返回x的正切值|

### 向量运算

|函数|描述|
| ------| ----------------------------------------------------------------------------------------------------------------|
|​`all(a)`​|当a或a的所有分量都为true或者非0时返回1(true)，否则返回0(false)|
|​`any(a)`​|如果a=0或者a中的所有分量为0，则返回0(false)；否则返回1(true)|
|​`cross(a,b)`​|返回两个三维向量a与b的叉积|
|​`dot(a,b)`​|点乘，a和b可以为标量也可以为向量，其计算结果是两个向量夹角的余弦值，相当于\|a\| *|b|* cos(θ)或者a.x*b.x+a.y*b.y+a.z*b.z|
|​`distance(a,b)`​|返回a,b间的距离|
|​`length(v)`​|返回一个向量的模，即 sqrt(dot(v,v))|
|​`normalize(v)`​|返回一个向量的归一化版本(方向一样，但是模为1)|
|​`reflect(I, N)`​|根据入射光方向向量I，和顶点法向量N，计算反射光方向向量|
|​`refract(I,N,eta)`​|计算折射向量，I为入射光线，N为法向量，eta为折射系数|

### 插值和步进函数

|函数|描述|
| ------| ---------------------------------------------------------------------------------------------------|
|​`lerp(A,B,alpha)`​|线性插值。如果alpha=0，则返回A；如果alpha=1，则返回B；否则返回A与B的混合值|
|​`smoothstep(min,max,x)`​|平滑插值，如果x比min小，返回0；如果x比max大，返回1；如果x处于范围[min，max]中，则返回0和1之间的值|
|​`step(a,b)`​|如果a<=b返回1，否则返回0|

### 导数函数

|函数|描述|
| ------| -------------------------------------------------------------|
|​`ddx(v)`​|当前像素右边的v值-当前像素的v值(水平方向的差值)|
|​`ddy(v)`​|当前像素下边的v值-当前像素的v值(垂直方向的差值)|
|​`fwidth(v)`​|当前像素与它右边及下边像素的差值，abs(ddx(v)) + abs(ddy(v))|

### 纹理采样

#### 内置渲染管线

|函数|描述|
| ------| --------------------------------------------------------------|
|​`tex1D(samper1D tex,float s)`​|一维纹理采样|
|​`tex2D(samper2D tex,float2 s)`​|二维纹理采样|
|​`tex2Dlod(samper2D tex,float4 s)`​|二维纹理采样，s.w表示采样的是mipmap的几级，仅在ES3.0以上支持|
|​`tex2DProj(samper2D tex,float4 s)`​|二维投影纹理采样，uv使用s.xy/s.w|
|​`texCUBE(samperCUBE tex,float3 s)`​|立方体纹理采样|
|​`tex3D(samper3D tex,float3 s)`​|3D纹理采样，uv使用xyz|

#### URP渲染管线

|函数|描述|
| ------| ----------------------------------------|
|​`TEXTURE2D(textureName);`​|二维纹理的定义(纹理与采样器分离定义)|
|​`TEXTURECUBE(textureName);`​|立方体纹理的定义(纹理与采样器分离定义)|
|​`SAMPLER(samplerName);`​|采样器的定义(纹理与采样器分离定义)|
|​`SAMPLE_TEXTURE2D(textureName, samplerName, coord);`​|进行二维纹理采样操作|
|​`SAMPLE_TEXTURE2D_LOD(textureName, samplerName, coord,lod);`​|进行二维纹理采样操作|
|​`SAMPLE_TEXTURE2D_BIAS(textureName, samplerName, coord,bias);`​|对mipmap进行偏移后再采样纹理|
|​`SAMPLE_TEXTURECUBE(textureName, samplerName, coord);`​|进行立方体纹理采样操作|
|​`SAMPLE_TEXTURECUBE_LOD(textureName, samplerName, coord,lod);`​|进行立方体纹理采样操作|
|​`SAMPLE_TEXTURE3D(textureName, samplerName, coord);`​|进行3D纹理采样操作|

### 纹理数组采样（URP）

|函数|描述|
| ------| ----------------------|
|​`TEXTURE2D_ARRAY(textureName);`​|纹理数组的定义|
|​`SAMPLER(sampler_samplerName);`​|纹理数组的采样器定义|
|​`SAMPLE_TEXTURE2D_ARRAY(textureName, samplerName, coord2, index);`​|纹理数组采样|

### 常用指令

|指令|描述|
| ------| -------------------------------------------------------------------------------|
|​`mov dest, src1`​|dest = src1|
|​`add dest, src1, src2`​|dest = src1 + src2|
|​`mul dest, src1, src2`​|dest = src1 * src2|
|​`div dest, src1, src2`​|dest = src1 / src2|
|​`mad dest, src1, src2, src3`​|dest = src1 * src2 + src3|
|​`dp2 dest, src1, src2`​|(src1.x * src2.x) + (src1.y * src2.y)|
|​`dp3 dest, src1, src2`​|(src1.x * src2.x) + (src1.y * src2.y) + (src1.z * src2.z)|
|​`dp4 dest, src1, src2`​|(src1.x * src2.x) + (src1.y * src2.y) + (src1.z * src2.z) + (src1.w * src2.w)|
|​`rsq dest, src1`​|rsqrt(src1) 对src1进行平方根的倒数|
