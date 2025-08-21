# ステージ状態の更新と取得

## マニュアル方法

マニュアルでステージ情報を更新して、各軸の状態を取得するサンプルコードです。

```csharp
using OptocombToolkit.Libs.Embedded.Core.Primitives;
using OptocombToolkit.Libs.Embedded.Stages.Core.Apis;
using OptocombToolkit.Libs.Embedded.Stages.Kohzu;

KohzuStageConfig defaultConfig = KohzuStageConfig.DefaultConfig;
IStage stage = defaultConfig.GetStage();

// マニュアルでステージ状態を更新します
stage.UpdateAxisStatus();

// ステージの軸数を取得します
int length = stage.AxesReady.Length;

// 各軸の現在位置を取得し、プリントします
for (int i = 0; i < length; i++)
{
    string axisName = stage.StageConfig.Axes.ElementAt(i).Name;
    double currentPosition = stage.Positions[i];
    Console.WriteLine($"{axisName}軸の現在位置は{currentPosition}");
}
```


## ポーリングで自動更新方法

ポーリングでステージの状態を等間隔で更新し、ステージ及び各軸の状態を監視する方法のサンプルコードです。

!!! note
    ここには組み込まれている軸構成を例としています。実環境に応じて設定ファイルからステージのインスタンス作成してください。

```csharp
using OptocombToolkit.Libs.Embedded.Core.Primitives;
using OptocombToolkit.Libs.Embedded.Stages.Core.Apis;
using OptocombToolkit.Libs.Embedded.Stages.Kohzu;

KohzuStageConfig defaultConfig = KohzuStageConfig.DefaultConfig;
IStage stage = defaultConfig.GetStage();

// ワーカースレッドでポーリング監視を実装します。（実際の開発に合わせてポーリング処理を実装してください。）
CancelTokenSource cancelTokenSource = new CancellationTokenSource();
CanellationToken token = cancelTokenSource.Token;
Task task = Task.Run(async () =>
{
    while (!token.IsCancellationRequested)
    {
        stage.UpdateAxisStatus();
        await Task.Delay(100, token); // 100msec間隔で更新 
    }
}, token);

// ここには全軸の原点復帰を行います
stage.OriginMove();

// 模擬監視処理
while (true)
{
    int axisNo = 0; // 一軸の状態監視のみを仮実装します
    // 現在の位置を取得します
    double currentPosition = stage.Positions[axisNo];
    // 現在移動中かどうか
    bool isMoving = stage.AxesReady[axisNo];
    // 原点復帰完了かどうか
    bool isOriginDone = stage.AxesOriginReady[axisNo];
    // 非常停止されているかどうか
    bool isEmergencyStopped = stage.AxesEmergencyStatus[axisNo];
    // ソフトリミットエラーかどうか
    ESoftLimitStatus softLimitStatus = stage.AxesSoftLimitStatus[axisNo];
    // コントローラーエラーかどうか
    bool isControllerError = stage.AxesControllerError[axisNo];
    // 全軸が原点復帰完了し、全部静止の場合ループから抜ける
    if (!stage.AxesReady.All(x=> x))
        break;
    // ここにはカスタマイズ処理を組みます
}
```
