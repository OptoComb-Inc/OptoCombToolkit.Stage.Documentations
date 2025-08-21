# クイックスタート

この章では、パッケージを用いた神津ステージを制御するについて説明し、一部の制御方式には同期または非同期の二通りがあります。

!!! danger
    同期でステージ制御を行う際に、ステージの各軸が動作中に物との衝突しないことを確認してください。


## ステージのインスタンス作成

設定ファイルまたは組み込まれている初期設定でステージのインスタンス作成をします。
ステージ構成の情報が正しく読み込んだ場合、`GetStage()`:material-api:でステージのインスタンス作成ができます。

!!! tip
    事前に設定ファイルにステージの軸構成やその他の基本情報を記述し、ファイルからステージインスタンス作成するのが一般的です。

### 初期設定を用いた方法

パッケージに組み込まれている初期設定を用いたステージのインスタンス作成方法は以下のようです。

```csharp
IStageConfig config = KohzuStageConfig.DefaultConfig;
IStage stage = config.GetStage();
```

### 設定ファイルを用いた方法

実環境においては、神津ステージの軸構成やその他の基本情報を記述したファイルからのインスタンス作成する方法は以下のようです。

```csharp
string jsonContent = File.ReadAllText("config.json");
IStageConfig config = new KohzuStageConfig().FromJson(jsonContent);
IStage stage = config.GetStage();
```

!!! tip
    事前に情報をファイルに記述することでステージのインスタンス作成することより一般的です。

!!! warning
    設定ファイルの形式は`.json`ファイルとなっています。


## オープン

ステージのインスタンス作成した後、装置に対し初期化と接続などオープン処理を行う必要があります。

```csharp
stage.Open();
```


!!! note
    神津ステージの通信方式はイーサネット経由のソケット通信です。
    ソケット通信するのにIPとポート番号などの情報は設定ファイルに記述されています。

!!! tip
    コマンドプロンプトでピングすること神津ステージのコントローラーとの通信テストは可能です。
    ```powershell
    ping 192.168.1.120
    ```


## 原点復帰

同期・非同期で単軸または全軸に対し原点復帰を行います。

```csharp
// 同期で全軸の原点復帰
stage.OriginMove();

// 同期で単軸の原点復帰
stage.OriginMove(0);

// 非同期で全軸の原点復帰
tage.OriginMoveAsync();

// 非同期で単軸の原点復帰
await stage.OriginMoveAsync(0);
```

!!! tip
    実開発の場面において、原点復帰完了後何か軸に対して制御を行う場合は非同期実行を推薦します。

## ジョグ運転

同期で単軸に対しジョグ運転を行います。

```csharp
stage.JogMove(0, 10.0d);
```

!!! note
    ジョグ運転には非同期実行が用意されていません。

## 移動停止

同期・非同期で単軸または全軸に対し明示的に移動停止指令をします。

```csharp
// 同期で単軸の移動停止
stage.Stop(0);

// 同期で全軸の移動停止
stage.StopAll();

// 非同期で単軸の移動停止
await stage.WaitUntilStopAsync(0);

// 非同期で全軸の移動停止
await stage.WaitUntilStopAsync();
```

!!! note
    同期方式で軸の移動停止の場合、コントローラーに指令を出すことまで関数の実行
    が完了となるため、該当軸がまだ完全静止な状態とは限りません。
    移動停止指令を出して、軸が完成静止後制御をする場合は、非同期実行を推薦します。

## アブソリュート座標位置に移動

同期・非同期で単軸を一定の速度で指定された位置に移動します。

```csharp
// 同期で軸を指定位置に移動
stage.MoveAbs(0, 100.0, 10.0);

// 非同期で軸を指定位置に移動
await stage.MoveAbsAsync(0, 100.0, 10.0);
```

!!! tip
    実開発の場面において、移動完了後他の制御を行う場合、非同期実行を推薦します。

## 相対位置に移動

同期・非同期で単軸を一定の速度で現在の位置から指定されたオフセット値で移動します。

```csharp
// 同期で軸の相対移動
stage.MoveRel(0, 10.0, 10.0);

// 非同期で軸の相対移動
await stage.MoveRelAsync(0, 10.0, 10.0);
```

!!! tip
    実開発の場面において、移動完了後他の制御を行う場合、非同期実行を推薦します。

## 軸の現在位置取得

単軸または全軸の現在位置を取得します。

```csharp
// ステージの情報更新
stage.UpdateAxisStatus();

// インデックス０番の軸の現在位置の取得
double currentPosition = stage.Positions[0];
```


!!! note
    現在位置を取得する前に、必ず`UpdateAxisStatus()`:material-api:を実行してください。


## ポーリングでステージの情報の更新

別のスレッドまたはタスクで、ステージの情報を定期的に更新し、
ステージの状態監視を実現する。

```csharp
CancellationTokenSource cancelTokenSource = new CancellationTokenSource();
CancellationToken token = cancelTokenSource.Token;
Task task = Task.Run(async () =>
{
    while (!token.IsCancellationRequested)
    {
        stage.UpdateAxisStatus();
        await Task.Delay(100, token); // 100ms間隔でステージ状態を更新する
    }
}, token);
```


## 非常停止状態の解除

非常停止が起きた場合、非常停止を解除する。

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
    非常停止解除が失敗した場合（例えば物理非常停止スイッチが解除されていないことまたは
    ステージの非常停止原因が解消されていないこと）、非常停止解除すると例外が吐き出されるため、
    この関数実行する際`try-catch`で例外をキャッチすることを推薦します。
