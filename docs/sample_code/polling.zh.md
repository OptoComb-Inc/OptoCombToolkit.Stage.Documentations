# 获取平台轴的状态

## 可以通过手动更新来获取轴的最新状态

这一部分代码介绍了通过手动方式来更新并且获取轴的最新状态的示例代码。

```csharp
using OptocombToolkit.Libs.Embedded.Core.Primitives;
using OptocombToolkit.Libs.Embedded.Stages.Core.Apis;
using OptocombToolkit.Libs.Embedded.Stages.Kohzu;

KohzuStageConfig defaultConfig = KohzuStageConfig.DefaultConfig;
IStage stage = defaultConfig.GetStage();

// 手动更新平台状态
stage.UpdateAxisStatus();

// 获取轴的个数
int length = stage.AxesReady.Length;

// 获取所有轴的当前位置,并且打印
for (int i = 0; i < length; i++)
{
    string axisName = stage.StageConfig.Axes.ElementAt(i).Name;
    double currentPosition = stage.Positions[i];
    Console.WriteLine($"{axisName}轴的当前位置为{currentPosition}");
}
```


## 通过轮询的方式来实时监控平台各个轴的状态

这一部分的代码介绍了，如何从过轮询的方式来实时的监控神津精机平台所连接的各个轴的基本信息和状态。

!!! note
    这部分的代码，使用了默认的配置来简化前期所需要的处理。请根据实际使用的平台来更换部分代码。

```csharp
using OptocombToolkit.Libs.Embedded.Core.Primitives;
using OptocombToolkit.Libs.Embedded.Stages.Core.Apis;
using OptocombToolkit.Libs.Embedded.Stages.Kohzu;

KohzuStageConfig defaultConfig = KohzuStageConfig.DefaultConfig;
IStage stage = defaultConfig.GetStage();

// 通过开启一个工作线程在后台轮询平台状态更新 (根据实际情况进行实装)
CancelTokenSource cancelTokenSource = new CancellationTokenSource();
CanellationToken token = cancelTokenSource.Token;
Task task = Task.Run(async () =>
{
    while (!token.IsCancellationRequested)
    {
        stage.UpdateAxisStatus();
        await Task.Delay(100, token); // 每100毫秒更新一次状态
    }
}, token);

// 使用同步的方式进行平台的原点复位
stage.OriginMove();

// 模拟监视
while (true)
{
    int axisNo = 0; // 假设只读取第一个轴的信息
    // 获取轴的当前位置信息
    double currentPosition = stage.Positions[axisNo];
    // 获取轴是否正在移动
    bool isMoving = stage.AxesReady[axisNo];
    // 获取轴是否完成了原点复位
    bool isOriginDone = stage.AxesOriginReady[axisNo];
    // 获取轴是否发生了紧急停止异常
    bool isEmergencyStopped = stage.AxesEmergencyStatus[axisNo];
    // 获取轴是否发生了软限位异常
    ESoftLimitStatus softLimitStatus = stage.AxesSoftLimitStatus[axisNo];
    // 获取轴是否发生了控制器异常
    bool isControllerError = stage.AxesControllerError[axisNo];
    // 如果全部轴都完成了原点复位且已经停止移动，则退出循环
    if (!stage.AxesReady.All(x=> x))
        break;
    // 这里可以使用事件委托或者别的方式将轴的信息传递给上层
}
```
