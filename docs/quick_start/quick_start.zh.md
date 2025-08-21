# 快速开始

在这一章里，通过代码快速介绍和神津精机的基本交互功能，这些交互中有一部分实现了异步和同步两种交互方式。

!!! danger
    在使用同步方式同时实现多轴的交互时，请确保轴之间在移动时不会互相接触也不会于工件碰撞。


## 创建平台对象实例

通过读取平台的配置文件或者使用内嵌的初始配置信息来获取平台配置的对象实例。
在获取配置信息的对象实例之后，通过调用`GetStage()`:material-api:来获取平台的对象实例。

!!! tip
    在本开发包里，通常使用配置文件来定义平台的多轴构成和所需要的相关信息。
    然后根据配置文件的内容，来生成一个相对于的平台对象实例。

### 使用默认配置的方式

使用默认的配置信息来创建平台对象实例的示例代码如下。

```csharp
IStageConfig config = KohzuStageConfig.DefaultConfig;
IStage stage = config.GetStage();
```

### 使用配置文件的方式

在实际的开发当中，一个平台的轴的构成和所使用的平台的一些特有的参数可以过配置文件进行提前的设置,示例代码如下。

```csharp
string jsonContent = File.ReadAllText("config.json");
IStageConfig config = new KohzuStageConfig().FromJson(jsonContent);
IStage stage = config.GetStage();
```

!!! tip
    用提前设置好的配置文件来创建平台对象实例会更符合实际的使用场景。

!!! warning
    配置文件的格式仅支持`.json`格式。


## 连接平台

在获得一个平台对象实例之后，需要对平台的实例对象进行初始化并且和平台设备进行连接操作。

```csharp
stage.Open();
```


!!! note
    本开发包中使用以太网通信的方式实现和神津精机的交互。
    以太网地址和端口号存储与配置文件中。

!!! tip
    可以使用以下命令测试于神津精机的通信状态。
    ```powershell
    ping 192.168.1.120
    ```


## 原点复位

异步或同步的方式对单轴或者所有轴进行原点复位。

```csharp
// 同步方式对所有轴进行原点复位
stage.OriginMove();

// 同步的方式指定单轴进行原点复位
stage.OriginMove(0);
tage.OriginMoveAsync();

// 同异步的方式指定单轴进行原点复位
await stage.OriginMoveAsync(0);
```

!!! tip
    在实际场景中，如过需要在轴完成原点复位之后执行后续的轴的交互，可以使用异步的方式。

## 点动

同步的方式对单轴进行点动。

```csharp
stage.JogMove(0, 10.0d);
```

!!! note
    点动交互只会将点动命令传达给神津精机之后，点动的处理即视为执行结束。所以并没有异步交互。

## 停止移动

通过主动交互的方式来停止神津精机的单轴或所有轴移动,也有同步和异步两种方式。

```csharp
// 同步的方式停止单轴移动
stage.Stop(0);

// 同步的方式停止所有轴移动
stage.StopAll();

// 异步的方式停止单轴移动
await stage.WaitUntilStopAsync(0);

// 异步的方式停止所有轴移动
await stage.WaitUntilStopAsync();
```

!!! note
    同步方式的停止移动，只是将命令传达给神津精机之后，处理即视为执行结束，
    此时的神津精机的轴还可能正在执行减速移动直到完全静止的过程。
    所以，如果想要确保在发出停止移动命令之后等待轴完全静止的情况，
    推荐使用异步的方式。

## 指定坐标位置移动

异步或同步的方式指定单轴以一定的速度移动到指定的位置。

```csharp
// 同步的方式指定单轴移动
stage.MoveAbs(0, 100.0, 10.0);

// 异步的方式指定单轴移动
await stage.MoveAbsAsync(0, 100.0, 10.0);
```

!!! tip
    在实际场景中，如过需要在轴完成移动后执行后续的轴的交互，可以使用异步的方式。

## 相对移动

异步或同步的方式指定单轴以一定的速度在当前的位置基础上偏移移动。

```csharp
// 同步的方式指定单轴偏移移动
stage.MoveRel(0, 10.0, 10.0);

// 异步的方式指定单轴偏移移动
await stage.MoveRelAsync(0, 10.0, 10.0);
```

!!! tip
    在实际场景中，如过需要在轴完成移动后执行后续的轴的交互，可以使用异步的方式。

## 获取轴的当前位置

获取单轴或者所有轴的当前位置信息。

```csharp
// 更新神津精机平台状态
stage.UpdateAxisStatus();

// 通过索引来获取单轴的当前位置信息
double currentPosition = stage.Positions[0];
```


!!! note
    从`Positions`属性访问位置信息之前，请执行一次`UpdateAxisStatus()`:material-api:。


## 通过轮询的方式来定期更新平台状态

通过开启一个工作线程在后台执行轮询来定期更新平台的状态，
方便监控平台的各个轴的当前状态。

```csharp
CancellationTokenSource cancelTokenSource = new CancellationTokenSource();
CancellationToken token = cancelTokenSource.Token;
Task task = Task.Run(async () =>
{
    while (!token.IsCancellationRequested)
    {
        stage.UpdateAxisStatus();
        await Task.Delay(100, token); // 每100毫秒更新一次状态
    }
}, token);
```


## 解除紧急停止状态

```csharp
try
{
    stage.ReleaseEmergency();
}
catch(Exception e)
{
    // do something
}
```

!!! note
    如果紧急停止解除失败（例如物理急停按钮未复位，或者平台自身的急停原因没有排除），
    则会抛出异常。建议在解除命令执行逻辑中使用`try-catch`来捕捉失败原因。
