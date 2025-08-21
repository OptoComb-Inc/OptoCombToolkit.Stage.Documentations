# IAxisInfo接口

名称空间: OptoCombToolkit.Libs.Embedded.Stages.Core.Apis

程序集: OptoCombToolkit.Libs.Embedded.Stages.Core.dll

轴的基本信息。

```csharp
public interface IAxisInfo
```

## 属性

### Type

轴的类型。

```csharp
EAxisType Type{get;}
```

**返回值**

返回一个`EAxisType`枚举类型的值。

`Linear`: 线性运动轴

`Rotary`: 回转运动轴

### Id

轴连接到平台控制器的物理ID。

```csharp
int Id{get;}
```

**返回值**

轴连接到平台控制器的物理ID。

!!! danger
    根据实际设备的环境来设定这个值，设置完成之后就不会进行更改。

### Name

轴的名称。

```csharp
string Name{get;}
```

**返回值**

返回该轴的名称。例如：X轴。

!!! tip
    轴的名称可以自定义。

### NegativeString

负方向运动的标识符。

```csharp
string NegativeString{get;}
```

**返回值**

返回一个表示负方向运动的标识符。

!!! tip
    根据轴类型的不同，线性运动类型的轴通常用`-`来表示。回转轴则会用`CCW`来表示。也可以根据实际使用场景进行自定义。

### PositiveString

正方向运动的标识符。

```csharp
string PositiveString{get;}
```

**返回值**

返回一个表示负正向运动的标识符。

!!! tip
    根据轴类型的不同，线性运动类型的轴通常用`+`来表示。回转轴则会用`CW`来表示。也可以根据实际使用场景进行自定义。


### ReverseDirection

是否将反转轴的运动方向。

```csharp
bool ReverseDirection{get;}
```

**返回值**

返回一个布尔值。`true`：则表示将反转轴的运动方向。`false`:则表示遵守既定的运动方向。

!!! danger
    这个设定默认为`false`。如果在硬件层面无法实现轴运动方向的管理控制，可以设置这个值通过软件层面进行反转。
    不推荐修改。

