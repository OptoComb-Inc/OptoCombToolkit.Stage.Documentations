# IStage接口

名称空间: OptoCombToolkit.Libs.Embedded.Stages.Core.Apis

程序集: OptoCombToolkit.Libs.Embedded.Stages.Core.dll

平台的交互基本接口。

```csharp
public interface IStage
```

## 属性

### StageConfig

平台的配置信息。

```csharp
IStageConfig StageConfig{get;}

```

**返回值**

返回一个[IStageConfig](../istage_config/)接口类型的平台的配置文件的对象实例。

### Positions

平台连接的所有轴的位置信息。

```csharp
double[] Positions {get;}
```

**返回值**

返回一个包含所有轴的位置信息数组。

### AxesReady

平台连接的所有轴的移动状态。

```csharp
bool[] AxesReady{get;}
```

**返回值**

返回一个包含所有轴的移动状态。
`true`表示轴处于静止状态可以接受移动命令。
`false`表示轴正在移动中。

### AxesOriginReady

平台连接的所有轴的原点复位信息。

```csharp
bool[] AxesOriginReady{get;}
```

**返回值**

返回一个包含所有轴的是否执行了原点复位操作的数组。

`true`表示轴已经完成了原点复位。

`false`表示轴未执行原点复位。

### AxesEmergencyStatus

平台连接的所有轴的急停信息。

```csharp
bool[] AxesEmergencyStatus{get;}
```

**返回值**

返回一个包含所有轴的是否处于急停状态的数组。

`true`表示轴急停中。

`false`表示轴未急停。

**备注**

神津精机平台在紧急停止时，所有轴都会处理急停状态，
如果想要判断平台是否急停则可以使用
```csharp
bool isStageEmergency = AxesEmergencyStatus.Any(x=> x == true);
```

### AxesControllerError

平台连接的所有轴所对应的控制器是否错误信息。

```csharp
bool[] AxesControllerError{get;}
```

**返回值**

返回一个包含所有轴对应的控制器是否发生异常。

**备注**

神津精机平台的多轴所对应的控制器会有一次或者多个，
根据实际情况进行使用。

### AxesSoftLimitStatus

平台连接的所有轴是否触发了软限位报错。

```csharp
AxesSoftLimitStatus[] AxesSoftLimitStatus{get;}
```

**返回值**

返回一个包含所有轴是否触发了软限位信息的枚举类型的数组。

`WithinLimit`表示并未触发软限位。

`OverPlusLimit`表示触发了正方向的软限位。

`OverMinusLimit`表示触发了负方向的软限位。


## 方法

### :material-api: JogMove(int,double)

控制指定轴进行一定速度的连续点动。

```csharp
void JogMove(int axisNo, double speed)
```

**参数**

`axisNo`:轴的索引编号。

`speed`:点动的移动速度。如果轴是线性移动的类型，单位为**毫米/秒**。如果是回转轴类型，单位为**度/秒**。

**示例代码**

```csharp
// 获取移动平台对象示例
IStage stage = KohzuStageConfig.DefaultConfig.GetStage();

// 指定配置文件中索引值为0的轴以10毫米(度)/秒的速度进行点动
stage.JogMove(0, 10.0);

// 同步的方式控制指定轴停止移动
stage.Stop(0);

// 异步的方式控制指定轴停止移动
EStopStatus result = await stage.WaitUntilStopAsync(0);

// 使用异步的方式可以进行后续的异常断言
if(result == EStopStatus.ErrorStop)
{
    // 如果发生异常则进行相关的处理
}
```

### :material-api: MoveAbs(int,double,double)

同步的方式控制指定轴以一定的速度移动到目标位置。

```csharp
void MoveAbs(int axisNo, double targetPosition, double speed)
```

**参数**

`axisNo`:轴的索引编号。

`targetPosition`: 轴移动的目标位置。如果轴是线性移动的类型，单位为**毫米**。如果是回转轴类型，单位为**度**。

`speed`:移动速度。如果轴是线性移动的类型，单位为**毫米/秒**。如果是回转轴类型，单位为**度/秒**。

!!! warning
    因为是同步方式，所以调用执行结束之后并不能保证指定的轴已经结束移动进入等待状态。

**示例代码**

```csharp
// 该API的使用场景为，同时控制多个轴进行移动，然后异步等待多个轴的移动结束。

// 获取移动平台对象示例
IStage stage = KohzuStageConfig.DefaultConfig.GetStage();

// 同时控制配置文件中索引值为0, 1, 2的三轴进行移动
stage.MoveAbs(0, 10.0, 10.0);
stage.MoveAbs(1, 10.0, 5.0);
stage.MoveAbs(2, 10.0, 1.0);

// 异步等待所有轴移动结束
IEnumerable<EStopStatus> results = await stage.WaitUntilStopAsync();

// 根据返回值进行断言
if(results.Any(x => x == EStopStatus.ErrorStop))
{
    // 如果其中某一轴发生异常则进行相关处理
}
```

!!! danger
    多轴同时进行移动控制时，请确保轴在移动过程中不会发生物理碰撞。

### :material-api: MoveRel(int,double,double)

同步的方式控制轴从当前位置以一定的速度进行一定量的偏移。

```csharp
void MoveRel(int axisNo, double distance, double speed)
```

**参数**

`axisNo`:轴的索引编号。

`distance`: 偏移量。如果轴是线性移动的类型，单位为**毫米**。如果是回转轴类型，单位为**度**。偏移量中支持`+-`符号，若为`-`的偏移量，则进行反方向的移动。

`speed`:移动速度。如果轴是线性移动的类型，单位为**毫米/秒**。如果是回转轴类型，单位为**度/秒**。

!!! warning
    因为是同步方式，所以调用执行结束之后并不能保证指定的轴已经结束移动进入等待状态。

**示例代码**

请参考[:material-api: MoveAbs(int,double,double)](#moveabsintdoubledouble)

### :material-api: OriginMove(int)

控制指定轴进行原点复位。

```csharp
void OriginMove(int axisNo)
```

**参数**

`axisNo`:轴的索引编号。

!!! warning
    因为是同步方式，所以调用执行结束之后并不能保证指定的轴已经结束原点复位进入等待状态。

**示例代码**

请参考[:material-api: MoveAbs(int,double,double)](#moveabsintdoubledouble)

### :material-api: OriginMove()

控制平台连接的所有轴同时进行原点复位。

```csharp
void OriginMove()
```

!!! warning
    因为是同步方式，所以调用执行结束之后并不能保证指定所有的轴均已经结束原点复位进入等待状态。

**示例代码**

请参考[:material-api: MoveAbs(int,double,double)](#moveabsintdoubledouble)

### :material-api: Stop(int)

控制指定轴停止移动。

```csharp
void Stop(int axisNo)
```

**参数**

`axisNo`:轴的索引编号。

!!! warning
    因为是同步方式，所以调用执行结束之后并不能保证所指定的轴均已处于静止状态。

### :material-api: StopAll()

控制平台连接的所有轴停止移动。

```csharp
void StopAll()
```

!!! warning
    因为是同步方式，所以调用执行结束之后并不能保证所有的轴均已处于静止状态。

### :material-api: ReleaseEmergency()

解除移动平台的紧急停止异常。

```csharp
void ReleaseEmergency()
```

!!! warning
    在实际使用场景中，在没有解决紧急停止的异常问题前执行该API会因为无法解除异常而抛出异常。

### :material-api: UpdateAxisStatus()

将移动平台连接的所有轴都更新至最新状态。

```csharp
void UpdateAxisStatus()
```

!!! note
    该API只是将平台的轴信息进行更新，并不会将最新的状态信息通知给上位程序，需要自行获取所需要的轴的信息。

### :material-api: GetExpectAccelerationTime(int,double)

计算指定轴从静止状态加速到指定速度为止所需要的加速时间。

```csharp
double GetExpectAccelerationTime(int axisNo, double speed)
```

**参数**

`axisNo`:轴的索引编号。

`speed`:移动速度。如果轴是线性移动的类型，单位为**毫米/秒**。如果是回转轴类型，单位为**度/秒**。

### :material-api: CreateCustomAxisPropertyItem(int)

获取各个厂商移动平台的特有的内部设置项目。

```csharp
object? CreateCustomAxisPropertyItem(int)
```

!!! danger
    该API为开发者调试时使用。在实际场景开发中不推荐使用。

### :material-api: MoveAbsAsync(int,double,double)

异步的方式控制指定轴以一定的速度移动到目标位置

```csharp
Task<EStopStatus> MoveAbsAsync(int axisNo, double targetPosition, double speed)
```

**参数**

`axisNo`:轴的索引编号。

`targetPosition`: 轴移动的目标位置。如果轴是线性移动的类型，单位为**毫米**。如果是回转轴类型，单位为**度**。

`speed`:移动速度。如果轴是线性移动的类型，单位为**毫米/秒**。如果是回转轴类型，单位为**度/秒**。

**返回值**

返回一个异步执行的Task，该Task中返回异步执行的结果。该结果是一个`EStopStatus`的枚举类型。

`NormalStop`: 移动结束并未发生异常。
`ErrorStop`: 移动过程中发生了异常导致中断移动。

!!! note
    `EStopStatus`这个枚举类型是在`OpotoCombToolkit.Libs.Embedded.Core.nupkg`这个依赖包中，请确保程序有安装该NuGet包。

**示例代码**

```csharp
// 使用同步方式的控制轴的移动并且的等待
stage.MoveAbs(0, 10.0, 10.0);
EStopStatus result = await stage.WaitUntilStopAsync(0);

// 使用异步的方式可以将上面的代码整合起来
EStopStatus result = await stage.MoveAbsAsync(0,10.0, 10.0);
```

### :material-api: MoveRelAsync(int,double,double)

异步的方式控制轴从当前位置以一定的速度进行一定量的偏移。

```csharp
Task<EStopStatus> MoveRelAsync(int axisNo, double distance, double speed)
```

**参数**

`axisNo`:轴的索引编号。

`distance`: 偏移量。如果轴是线性移动的类型，单位为**毫米**。如果是回转轴类型，单位为**度**。偏移量中支持`+-`符号，若为`-`的偏移量，则进行反方向的移动。

`speed`:移动速度。如果轴是线性移动的类型，单位为**毫米/秒**。如果是回转轴类型，单位为**度/秒**。

**返回值**

返回一个异步执行的Task，该Task中返回异步执行的结果。该结果是一个`EStopStatus`的枚举类型。

`NormalStop`: 移动结束并未发生异常。
`ErrorStop`: 移动过程中发生了异常导致中断移动。

!!! note
    `EStopStatus`这个枚举类型是在`OpotoCombToolkit.Libs.Embedded.Core.nupkg`这个依赖包中，请确保程序有安装该NuGet包。

**示例代码**

请参照[:material-api: MoveAbsAsync(int,double,double)](#moveabsasyncintdoubledouble)

### :material-api: OriginMoveAsync(int)

异步的方式控制指定轴进行原点复位。

```csharp
Task<EStopStatus> OriginMoveAsync(int axisNo)
```

**参数**

`axisNo`:轴的索引编号。

**返回值**

返回一个异步执行的Task，该Task中返回异步执行的结果。该结果是一个`EStopStatus`的枚举类型。

`NormalStop`: 移动结束并未发生异常。
`ErrorStop`: 移动过程中发生了异常导致中断移动。

!!! note
    `EStopStatus`这个枚举类型是在`OpotoCombToolkit.Libs.Embedded.Core.nupkg`这个依赖包中，请确保程序有安装该NuGet包。

**示例代码**

请参照[:material-api: MoveAbsAsync(int,double,double)](#moveabsasyncintdoubledouble)

### :material-api: OriginMoveAsync()

异步的方式控制平台连接的所有中进行原点复位。

```csharp
Task<IEnumerable<EStopStatus>> OriginMoveAsync()
```

**返回值**

返回一个异步执行的Task，该Task中返回异步执行的结果。该结果是一个包含了所有轴`EStopStatus`的枚举类型的可迭代数组。

!!! note
    该API会同时命令所有轴进行原点复位，并且异步等待所有轴的原点复位执行结束。

    可以使用`Linq`语句来判断移动平台的所有轴是否都已正常执行原点复位。

**示例代码**

请参照[:material-api: MoveAbsAsync(int,double,double)](#moveabsasyncintdoubledouble)

### :material-api: WaitUntilStopAsync(int)

异步等待指定轴移动结束。

```csharp
Task<EStopStatus> WaitUntilStopAsync(int axisNo)
```

**参数**

`axisNo`:轴的索引编号。

!!! note
    `EStopStatus`这个枚举类型是在`OpotoCombToolkit.Libs.Embedded.Core.nupkg`这个依赖包中，请确保程序有安装该NuGet包。

**示例代码**

```csharp
// 用同步的方式控制指定轴进行原点复位
stage.OriginMove(0);

// 异步中止移动操作并且等待轴回到静止状态
EStopStatus results = await stage.WaitUntilStopAsync(0);
```

### :material-api: WaitUntilStopAsync()

异步等待所有轴移动结束。

```csharp
Task<IEnumerable<EStopStatus>> WaitUntilStopAsync()
```

**返回值**

返回一个异步执行的Task，该Task中返回异步执行的结果。该结果是一个包含了所有轴`EStopStatus`的枚举类型的可迭代数组。

**示例代码**

请参照[:material-api: WaitUntilStopAsync(int)](#waituntilstopasyncint)
