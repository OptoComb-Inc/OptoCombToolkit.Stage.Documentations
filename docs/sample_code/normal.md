# 基本功能的示例代码

このサンプルコードには、神津精機ステージの設定ファイルの読み込みからステージの基本制御までについて説明します。

```csharp
using OptocombToolkit.Libs.Embedded.Core.Primitives;
using OptocombToolkit.Libs.Embedded.Stages.Core.Apis;
using OptocombToolkit.Libs.Embedded.Stages.Kohzu;

// 三軸構成を例とします
string json = '''
{
  "$type": "OptocombToolkit.Libs.Embedded.Stages.Kohzu.KohzuStageConfig, OptocombToolkit.Libs.Embedded.Stages.Kohzu",
  "StageName": "神津ステージ３軸構成",
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

// IStageConfigのAPIでステージのインスタンスを取得する
// ステージのインスタンスはIStageインターフェースタイプのオブジェクトです
IStage stage = config.GetStage();

// IStageインターフェースにはステージの基本制御APIが提供しています

// ステージをオープンする
stage.Open();

// ある軸に対しジョグ運転をさせます
// +方向へジョグ移動の場合は、速度は正数を指定し、-方向へのジョグ移動の場合は、速度には負数を指定します
stage.JogMove(0, 10.0);

// 移動停止（ジョグ運転、アブソリュート座標移動まはは相対移動中に使用します）
// 同期で単軸の移動を停止します
stage.Stop(0);
// 同期で全軸の移動を停止します
stage.StopAll();
// 非同期で指定軸の移動を停止します
await stage.WaitUntilStopAsync(0);
// 非同期で全軸の移動を停止します
await stage.WaitUntilStopAsync();

// 同期で単軸を一定の速度で指定位置に移動します（リニア軸の場合は、移動位置の単位はmm、速度の単位はmm/sec）
stage.MoveAbs(0, 100.0, 10.0);

// 非同期で単軸を一定の速度で指定位置に移動します。戻り値は軸移動中に異常発生されたかどうかの判断に使用します
EStopStatus absMoveResult = await stage.MoveAbsAsync(0, 100.0, 10.0);

// 同期で単軸を現在の位置から相対移動します
// 上記の100mmの位置から、+方向に10.0㎜を移動します
stage.MoveRel(0, 10.0, 10.0);

// 同期で110mm位置から、-方向に10.0mmを移動します
stage.MoveRel(0, -10.0, 10.0);


// 非同期で単軸を現在の位置から相対移動します
// 100.0mmの位置から、非同期で+方向に10.0mmを移動します。 戻り値は軸移動中に異常発生されたかどうかの判断に使用します
EStopStatus relMoveAbs = await stage.MoveRelAsync(0, 10.0, 10.0);


// 同期で単軸の原点復帰
stage.OriginMove(0);
// 同期で全軸の原点復帰
stage.OriginMove();

// 非同期で単軸の原点復帰。戻り値は軸移動中に異常発生されたかどうかの判断に使用します
EStopStatus originMoveResult = await stage.OriginMoveAsync(0);
await stage.OriginMoveAsync();

```


!!! tip
    JSONファイルパス指定でステージのインスタンスを取得する場合は以下のコードを使用します。
    ```csharp
    KohzuStageConfig config = new KohzuStageConfig();
    config.FromJsonFile("config.json");
    ```
