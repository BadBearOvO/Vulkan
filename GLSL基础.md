# GLSL基础

## 基本类型：

|     **类型**      | **说明**                    |
| :---------------: | --------------------------- |
|       void        | 空类型                      |
|       bool        | true or false               |
|        int        | 整数                        |
|       float       | 浮点数                      |
|    vec2, 3, 4     | n维浮点数向量               |
|    bvec2, 3, 4    | n维布尔向量                 |
| ivec2, 3, 4(uvec) | n维整数向量(无符号整数向量) |
|     sampler2D     | 二维纹理句柄                |
|    samplerCube    | cube map纹理句柄            |
|     sampler3D     | 三维纹理句柄                |

## 其他类型：

### 1.结构体：

```
struct fogStruct { 

vec4 color; 

float start; 

float end; 

vec3 points[3]; // 固定大小的数组是合法的* 

} fogVar;
```

### 2.数组：

只能用一维数组，数组内置length()函数。

### 3.矩阵类型：

用mat来定义矩阵。

| 类型        | 描述              |
| ----------- | ----------------- |
| mat2        | 2x2的浮点矩阵类型 |
| mat3 / mat4 | 类似              |
| mat2x3      | 2x3的浮点矩阵     |

### 4.内置变量：

GLSL的内置变量是图形管线和着色器之间的桥梁，它们提供了一个标准化的方式，用于与OpenGL固定功能模块交互。虽然它们在早期OpenGL中非常重要，但在现代OpenGL中，推荐使用自定义输入输出变量和Uniform块来代替这些内置变量以获得更大的灵活性和控制能力。

**顶点着色器内置变量：**

| 名称                     | 类型    | 描述                                                         |
| ------------------------ | ------- | ------------------------------------------------------------ |
| `gl_Color`               | `vec4`  | 输入属性 - 表示顶点的主颜色                                  |
| `gl_SecondaryColor`      | `vec4`  | 输入属性 - 表示顶点的辅助颜色                                |
| `gl_Normal`              | `vec3`  | 输入属性 - 表示顶点的法线值                                  |
| `gl_Vertex`              | `vec4`  | 输入属性 - 表示物体空间的顶点位置                            |
| `gl_MultiTexCoordn`      | `vec4`  | 输入属性 - 表示顶点的第n个纹理坐标                           |
| `gl_FogCoord`            | `float` | 输入属性 - 表示顶点的雾坐标                                  |
| `gl_Position`            | `vec4`  | 输出属性 - 变换后的顶点的位置，用于后面的固定的裁剪等操作。所有的顶点着色器都必须写这个值。 |
| `gl_ClipVertex`          | `vec4`  | 输出坐标，用于用户裁剪平面的裁剪                             |
| `gl_PointSize`           | `float` | 点的大小                                                     |
| `gl_FrontColor`          | `vec4`  | 正面的主颜色的varying输出                                    |
| `gl_BackColor`           | `vec4`  | 背面的主颜色的varying输出                                    |
| `gl_FrontSecondaryColor` | `vec4`  | 正面的辅助颜色的varying输出                                  |
| `gl_BackSecondaryColor`  | `vec4`  | 背面的辅助颜色的varying输出                                  |
| `gl_TexCoord[]`          | `vec4`  | 纹理坐标的数组varying输出                                    |
| `gl_FogFragCoord`        | `float` | 雾坐标的varying输出                                          |

**片段着色器变量：**

| 名称                | 类型    | 描述                                                         |
| ------------------- | ------- | ------------------------------------------------------------ |
| `gl_Color`          | `vec4`  | 包含主颜色的插值只读输入                                     |
| `gl_SecondaryColor` | `vec4`  | 包含辅助颜色的插值只读输入                                   |
| `gl_TexCoord[]`     | `vec4`  | 包含纹理坐标数组的插值只读输入                               |
| `gl_FogFragCoord`   | `float` | 包含雾坐标的插值只读输入                                     |
| `gl_FragCoord`      | `vec4`  | 只读输入，窗口的 x, y, z 和 1/w                              |
| `gl_FrontFacing`    | `bool`  | 只读输入，如果是窗口正面图元的一部分，则这个值为 true        |
| `gl_PointCoord`     | `vec2`  | 点精灵的二维空间坐标范围在 (0.0, 0.0) 到 (1.0, 1.0) 之间，仅用于点图元和点精灵开启的情况下。 |
| `gl_FragData[]`     | `vec4`  | 使用 `glDrawBuffers` 输出的数据数组。不能与 `gl_FragColor` 结合使用。 |
| `gl_FragColor`      | `vec4`  | 输出的颜色用于随后的像素操作                                 |
| `gl_FragDepth`      | `float` | 输出的深度用于随后的像素操作，如果这个值没有被写，则使用固定功能管线的深度值代替。 |

## 取分量：

可通过xyzw，rgba，stpq来分别取坐标、颜色和纹理分量。

例子：

```
vec4 v=vec4(1.0,2.0,3.0,1.0);
float x = v.x; //1.0
float x1 = v.r; //1.0
float x2 = v[0]; //1.0

vec3 xyz = v.xyz; //vec3(1.0,2.0,3.0)
vec3 xyz1 = vec(v[0],v[1],v[2]); //vec3(1.0,2.0,3.0)
vec3 rgb = v.rgb; //vec3(1.0,2.0,3.0)

vec2 xyzw = v.xyzw; //vec4(1.0,2.0,3.0,1.0);
vec2 rgba = v.rgba; //vec4(1.0,2.0,3.0,1.0);
```

## 类型之间的运算：

GLSL中没有隐式类型转换，原则上要求任何表达式左右两侧的类型必须一致，以下是错误例子：

```
int a =2.0; //错误,r-value为float 而 lvalue 为int.
int a =1.0+2;
float a =2;
float a =2.0+1;
bool a = 0; 
vec3 a = vec3(1.0, 2.0, 3.0) * 2;
```

## 修饰符

### 1.变量存储限定符

| 限定符        | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| **none**      | **默认类型**，可读可写。                                     |
| **const**     | **只读**类型，不可修改。                                     |
| **attribute** | 全局只读，一般存放顶点或法线数据。                           |
| **uniform**   | 全局只读，一般存放外部程序传递来的环境数据（光照、材质参数等）。 |
| **varying**   | vertex shader（可修改）与fragment shader（不能在这里修改）之间传递数据。 |

**varying示例：**

```
//顶点着色器
varying vec4 v_Color;
void main(){ 
    ...
    v_Color = vec4(1.,1.,1.,1);
}

//片元着色器
...
varying vec4 v_Color;
void main() {
  gl_FragColor = v_Color;
}
...
```

### 2.函数参数限定符：

函数的参数默认情况下是拷贝形式传递，即值传递而传递引用，故修改并不会影响实参变量本身，可通过参数限定符修改传递方式。

| **限定符**    | 说明                                                   |
| ------------- | ------------------------------------------------------ |
| none: default | 默认情况使用in限定符                                   |
| in            | 传入输入参数，**可读不可写**，修改形参不会影响实参。   |
| out           | 传入输出参数，**可写不可读**，值会被修改但不能被使用。 |
| inout         | 也传入输入输出参数，**可读也可写**。                   |

**out示例：**

```
void updateValue(out float x) {
    x = 10.0;  // 修改 x 的值
}

void main() {
    float a = 5.0;
    updateValue(a);  // a 现在变为 10.0
}
```

**inout示例：**

```
void modifyValue(inout float x) {
    x = x * 2.0;  // 修改 x 的值
}

void main() {
    float a = 5.0;
    modifyValue(a);  // a 现在变为 10.0
}
```

## 函数限定：

GLSL中函数不能递归调用。

## 内置函数库

glsl提供了非常丰富的函数库,供我们使用,这些功能都是非常有用且会经常用到的. 这些函数按功能区分大改可以分成7类:

**通用函数**：

下文中的 类型 T可以是 float, vec2, vec3, vec4,且可以逐分量操作.

| 方法                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| T abs(T x)                                                   | 返回x的绝对值                                                |
| T sign(T x)                                                  | 比较x与0的值,大于,等于,小于 分别返回 1.0 ,0.0,-1.0           |
| T floor(T x)                                                 | 返回<=x的最大整数                                            |
| T ceil(T x)                                                  | 返回>=等于x的最小整数                                        |
| T fract(T x)                                                 | 获取x的小数部分                                              |
| T mod(T x, T y) T mod(T x, float y)                          | 取x,y的余数                                                  |
| T min(T x, T y) T min(T x, float y)                          | 取x,y的最小值                                                |
| T max(T x, T y) T max(T x, float y)                          | 取x,y的最大值                                                |
| T clamp(T x, T minVal, T maxVal) T clamp(T x, float minVal,float maxVal) | min(max(x, minVal), maxVal),返回值被限定在 minVal,maxVal之间 |
| T mix(T x, T y, T a) T mix(T x, T y, float a)                | 取x,y的线性混合,x*(1-a)+y*a                                  |
| T step(T edge, T x) T step(float edge, T x)                  | 如果 x<edge 返回 0.0 否则返回1.0                             |
| T smoothstep(T edge0, T edge1, T x) T smoothstep(float edge0,float edge1, T x) | 如果x<edge0 返回 0.0 如果x>edge1返回1.0, 否则返回Hermite插值 |

**角度&三角函数:**

下文中的 类型 T可以是 float, vec2, vec3, vec4,且可以逐分量操作.

| 方法                                | 说明                    |
| ----------------------------------- | ----------------------- |
| T radians(T degrees)                | 角度转弧度              |
| T degrees(T radians)                | 弧度转角度              |
| T sin(T angle)                      | 正弦函数,角度是弧度     |
| T cos(T angle)                      | 余弦函数,角度是弧度     |
| T tan(T angle)                      | 正切函数,角度是弧度     |
| T asin(T x)                         | 反正弦函数,返回值是弧度 |
| T acos(T x)                         | 反余弦函数,返回值是弧度 |
| T atan(T y, T x) T atan(T y_over_x) | 反正切函数,返回值是弧度 |

**指数函数:**

下文中的 类型 T可以是 float, vec2, vec3, vec4,且可以逐分量操作.

| 方法               | 说明                        |
| ------------------ | --------------------------- |
| T pow(T x, T y)    | 返回x的y次幂 xy             |
| T exp(T x)         | 返回x的自然指数幂 ex        |
| T log(T x)         | 返回x的自然对数 ln          |
| T exp2(T x)        | 返回2的x次幂 2x             |
| T log2(T x)        | 返回2为底的对数 log2        |
| T sqrt(T x)        | 开根号 √x                   |
| T inversesqrt(T x) | 先开根号,在取倒数,就是 1/√x |

**几何函数:**

下文中的 类型 T可以是 float, vec2, vec3, vec4,且可以逐分量操作.

| 方法                            | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| float length(T x)               | 返回矢量x的长度                                              |
| float distance(T p0, T p1)      | 返回p0 p1两点的距离                                          |
| float dot(T x, T y)             | 返回x y的点积                                                |
| vec3 cross(vec3 x, vec3 y)      | 返回x y的叉积                                                |
| T normalize(T x)                | 对x进行归一化,保持向量方向不变但长度变为1                    |
| T faceforward(T N, T I, T Nref) | 根据 矢量 N 与Nref 调整法向量                                |
| T reflect(T I, T N)             | 返回 I - 2 * dot(N,I) * N, 结果是入射矢量 I 关于法向量N的 镜面反射矢量 |
| T refract(T I, T N, float eta)  | 返回入射矢量I关于法向量N的折射矢量,折射率为eta               |

**矩阵函数:**

mat可以为任意类型矩阵.

| 方法                             | 说明                          |
| -------------------------------- | ----------------------------- |
| mat matrixCompMult(mat x, mat y) | 将矩阵 x 和 y的元素逐分量相乘 |

**向量函数:**

下文中的 类型 T可以是 vec2, vec3, vec4, 且可以逐分量操作.

bvec指的是由bool类型组成的一个向量:

```
vec3 v3= vec3(0.,0.,0.);
vec3 v3_1= vec3(1.,1.,1.);
bvec3 aa= lessThan(v3,v3_1); //bvec3(true,true,true)
```



| 方法                                                  | 说明                                     |
| ----------------------------------------------------- | ---------------------------------------- |
| bvec lessThan(T x, T y)                               | 逐分量比较x < y,将结果写入bvec对应位置   |
| bvec lessThanEqual(T x, T y)                          | 逐分量比较 x <= y,将结果写入bvec对应位置 |
| bvec greaterThan(T x, T y)                            | 逐分量比较 x > y,将结果写入bvec对应位置  |
| bvec greaterThanEqual(T x, T y)                       | 逐分量比较 x >= y,将结果写入bvec对应位置 |
| bvec equal(T x, T y) bvec equal(bvec x, bvec y)       | 逐分量比较 x == y,将结果写入bvec对应位置 |
| bvec notEqual(T x, T y) bvec notEqual(bvec x, bvec y) | 逐分量比较 x!= y,将结果写入bvec对应位置  |
| bool any(bvec x)                                      | 如果x的任意一个分量是true,则结果为true   |
| bool all(bvec x)                                      | 如果x的所有分量是true,则结果为true       |
| bvec not(bvec x)                                      | bool矢量的逐分量取反                     |