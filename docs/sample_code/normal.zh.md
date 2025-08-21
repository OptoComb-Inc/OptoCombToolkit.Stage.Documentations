# 基本功能的示例代码

这一部分代码简单的介绍了如何从配置文件获取神津精机平台对象示例并且实现了基本的平台控制。

```csharp
using OptocombToolkit.Libs.Embedded.Core.Primitives;
using OptocombToolkit.Libs.Embedded.Stages.Core.Apis;
using OptocombToolkit.Libs.Embedded.Stages.Kohzu;

// 以三轴构成的配置为例
string json = '''
{
  "$type": "OptocombToolkit.Libs.Embedded.Stages.Kohzu.KohzuStageConfig, OptocombToolkit.Libs.Embedded.Stages.Kohzu",
  "StageName": "神津精机3轴平台",
  "UpdateRefreshInterval": 200,
  "InitializeInfo": {
    "$type": "OptocombToolkit.Libs.Embedded.Stages.Kohzu.KohzuDLL+InitializeInfo, OptocombToolkit.Libs.Embedded.Stages.Kohzu",
    "LogFilter": 6,
    "LogFolderName": null
  },
  "ConnectInfo": {
    "$type": "OptocombToolkit.Libs.Embedded.Stages.Kohzu.ReadableConnectInfo, OptocombToolkit.Libs.Embedded.Stages.Kohzu",
    "IpAddress": "192.168.1.120",
    "PortNo": 12321,
    "Timeout": 1000
  },
  "Axes": {
    "$type": "OptocombToolkit.Libs.Embedded.Stages.Kohzu.KohzuAxisInfo[], OptocombToolkit.Libs.Embedded.Stages.Kohzu",
    "$values": [
      {
        "$type": "OptocombToolkit.Libs.Embedded.Stages.Kohzu.KohzuAxisInfo, OptocombToolkit.Libs.Embedded.Stages.Kohzu",
        "Type": "Linear",
        "Name": "X",
        "Id": 1,
        "Resolution": 0.001,
        "StartSpeedUpperLimit": 1,
        "Acceleration": 100.0,
        "NegativeString": "-",
        "PositiveString": "+",
        "ReverseDirection": false
      },
      {
        "$type": "OptocombToolkit.Libs.Embedded.Stages.Kohzu.KohzuAxisInfo, OptocombToolkit.Libs.Embedded.Stages.Kohzu",
        "Type": "Linear",
        "Name": "Y",
        "Id": 2,
        "Resolution": 0.001,
        "StartSpeedUpperLimit": 1,
        "Acceleration": 100.0,
        "NegativeString": "-",
        "PositiveString": "+",
        "ReverseDirection": false
      },
      {
        "$type": "OptocombToolkit.Libs.Embedded.Stages.Kohzu.KohzuAxisInfo, OptocombToolkit.Libs.Embedded.Stages.Kohzu",
        "Type": "Linear",
        "Name": "Z",
        "Id": 3,
        "Resolution": 0.001,
        "StartSpeedUpperLimit": 1,
        "Acceleration": 100.0,
        "NegativeString": "-",
        "PositiveString": "+",
        "ReverseDirection": false
      }
    ]
  }
}
'''

KohzuStageConfig config = new KohzuStageConfig().FromJson(json);

// 通过调用IStageConfig的API来获取实际的运动平台实例对象
// 实例对象的类型是IStage接口类型
IStage stage = config.GetStage();

// IStage接口提供了基本的运动平台控制API

// 打开运动平台
stage.Open();

// 指定某一个轴进行点动
// +方向进行点动的话，速度为正数，-方向进行点动的话，速度为负数
stage.JogMove(0, 10.0);

// 停止移动（可以用于点动，绝对位子移动和相对位置移动中）
// 停止某一个轴（非异步）
stage.Stop(0);
// 停止所有轴（非异步）
stage.StopAll();
// 异步停止某一个轴
await stage.WaitUntilStopAsync(0);
// 异步停止所有轴
await stage.WaitUntilStopAsync();

// 指定某一个轴移动到指定坐标位置(非异步) 坐标单位如果是线性轴则为毫米， 速度单位为毫米每秒
stage.MoveAbs(0, 100.0, 10.0);

// 异步指定某一个轴移动到指定坐标位置 返回值可以用于判断是否发生了异常停止
EStopStatus absMoveResult = await stage.MoveAbsAsync(0, 100.0, 10.0);

// 指定某一个轴在当前位置基础上进行相对移动
// 在100.0毫米的位子，往+方向移动10.0毫米 (非异步)
stage.MoveRel(0, 10.0, 10.0);

// 在110.0毫米的位子，往-方向移动10.0毫米 (非异步)
stage.MoveRel(0, -10.0, 10.0);
// 异步指定某一个轴在当前位置基础上进行相对移动
// 在100.0毫米的位子，往+方向移动10.0毫米 返回值可以用于判断是否发生了异常停止
EStopStatus relMoveAbs = await stage.MoveRelAsync(0, 10.0, 10.0);


// 指定某一个轴进行原点复位 (非异步)
stage.OriginMove(0);
// 所有轴原点复位 (非异步)
stage.OriginMove();

// 异步指定某一个轴进行原点复位 返回值可以用于判断是否发生了异常停止
EStopStatus originMoveResult = await stage.OriginMoveAsync(0);
await stage.OriginMoveAsync();

```


!!! tip
    如果从JSON文件里读取平台的配置，则可以使用下一段代码来替换获取平台配置对象实例的部分。
    ```csharp
    KohzuStageConfig config = new KohzuStageConfig();
    config.FromJsonFile("config.json");
    ```
