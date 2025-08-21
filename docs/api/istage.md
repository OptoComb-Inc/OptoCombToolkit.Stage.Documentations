# IStageインターフェース

名前空間: OptoCombToolkit.Libs.Embedded.Stages.Core.Apis

アセンブリ: OptoCombToolkit.Libs.Embedded.Stages.Core.dll

ステージの基本制御機能を提供するインターフェースです。

```csharp
public interface IStage
```

## プロパティ

### StageConfig

ステージの構成情報。

```csharp
IStageConfig StageConfig{get;}

```

**戻り値**

[IStageConfig](../istage_config/)インターフェースタイプのステージ構成情報のオブジェクトインスタンスをリターンします。

### Positions

ステージの全軸の現在位置。

```csharp
double[] Positions {get;}
```

**戻り値**

全軸の現在位置を格納する配列をリターンします。

### AxesReady

ステージの全軸移動中かどうか。

```csharp
bool[] AxesReady{get;}
```

**戻り値**

ステージの全軸移動中かどうか状態を格納する配列をリターンします。

`true`の場合は、該当軸が静止で移動指令受付可能状態です。

`false`の場合は、該当軸が移動中です。

### AxesOriginReady

ステージの全軸が原点復帰済みかどうか。

```csharp
bool[] AxesOriginReady{get;}
```

**戻り値**


ステージの全軸が原点復帰済みかどうかを格納する配列をリターンします。。

`true`の場合は、該当軸が原点復帰済み状態です。

`false`の場合は、該当軸が原点復帰未実施状態です。

### AxesEmergencyStatus

ステージの全軸が非常停止かどうか。

```csharp
bool[] AxesEmergencyStatus{get;}
```

**戻り値**

ステージの全軸が非常停止かどうかを格納する配列をリターンします。

`true`の場合は、該当軸が非常停止中です。

`false`の場合は、該当軸が非常停止されていないこと。

**備考**

神津ステージの装置の仕様で、非常停止された場合は全軸が全部非常停止状態になります。
よって、以下のコードで神津ステージが非常停止かどうかが感知可能です。
```csharp
bool isStageEmergency = AxesEmergencyStatus.Any(x=> x == true);
```

### AxesControllerError

ステージの全軸がコントローラーエラーかどうか。

```csharp
bool[] AxesControllerError{get;}
```

**戻り値**

ステージの全軸がコントローラーエラーかどうかを格納する配列をリターンします。

**備考**

神津ステージの場合、コントローラーと軸が一対多のことがあるため、
軸ごとにコントローラーエラーかどうかを判断する必要があります。

### AxesSoftLimitStatus

ステージの全軸がソフトリミットエラーかどうか。

```csharp
AxesSoftLimitStatus[] AxesSoftLimitStatus{get;}
```

**戻り値**

ステージの全軸がソフトリミットエラーかどうかを格納する配列をリターンします。

`WithinLimit`の場合は、該当軸がフトリミットエラーになっていないこと。

`OverPlusLimit`の場合は、該当軸が＋方向にソフトリミットエラーが発生していること。

`OverMinusLimit`の場合は、該当軸が-方向にソフトリミットエラーが発生していること。


## メソッド

### :material-api: JogMove(int,double)

指定軸にジョグ運転。

```csharp
void JogMove(int axisNo, double speed)
```

**パラメータ**

`axisNo`:軸のインデックス番号です。

`speed`:ジョグ移動速度。リニア軸の場合、単位はmm/secです。回転軸の場合、単位はdeg/secです。

**サンプルコード**

```csharp
// ステージのインスタンスを取得します
IStage stage = KohzuStageConfig.DefaultConfig.GetStage();

// 軸構成配列のインデックス０番の軸で10mm(deg)/sec速度でジョグ運転します
stage.JogMove(0, 10.0);

// 同期でジョグ移動を停止します
stage.Stop(0);

// 非同期でジョグ移動を停止します
EStopStatus result = await stage.WaitUntilStopAsync(0);

// 非同期で移動停止した場合、戻り値で異常判断をします
if(result == EStopStatus.ErrorStop)
{
    // 異常で移動停止された場合の処理をここに実装します
}
```

### :material-api: MoveAbs(int,double,double)

同期で軸を一定の速度で指定された位置に移動します。

```csharp
void MoveAbs(int axisNo, double targetPosition, double speed)
```

**パラメータ**

`axisNo`:軸のインデックス番号です。

`targetPosition`: 軸の移動先の目標位置です。リニア軸の場合、単位はmmです。回転軸の場合、単位はdegです。

`speed`:移動速度。リニア軸の場合、単位はmm/secです。回転軸の場合、単位はdeg/secです。

!!! warning
    同期方式のため、この関数実行完了後軸が既に静止状態とは限りません。

**サンプルコード**

```csharp
// このAPIの使用場面は、多軸を同時に移動させ、その後多軸の移動完了まで待つことを想定しています。

// ステージのインスタンスを取得します
IStage stage = KohzuStageConfig.DefaultConfig.GetStage();

// インデックス番号0, 1, 2の三軸を同時に移動します
stage.MoveAbs(0, 10.0, 10.0);
stage.MoveAbs(1, 10.0, 5.0);
stage.MoveAbs(2, 10.0, 1.0);

// 非同期で三軸の移動完了まで待ちます
IEnumerable<EStopStatus> results = await stage.WaitUntilStopAsync();

// 非同期で移動停止した場合、戻り値で異常判断をします
if(results.Any(x => x == EStopStatus.ErrorStop))
{
  // 一軸でも異常が起きた場合のエラーハンドリングをここで実装します
}
```

!!! danger
    多軸を同時に移動する前に、各軸が衝突しないことを確認してください。

### :material-api: MoveRel(int,double,double)

同期で単軸を一定の速度で現在の位置から指定されたオフセット値で移動します。

```csharp
void MoveRel(int axisNo, double distance, double speed)
```

**パラメータ**

`axisNo`:軸のインデックス番号です。

`distance`: オフセット値。リニア軸の場合、単位はmmです。回転軸の場合、単位はdegです。オフセット値には符号付き可能です、
軸の移動方向に一致します。

`speed`:移動速度。リニア軸の場合、単位はmm/secです。回転軸の場合、単位はdeg/secです。

!!! warning
    同期方式のため、この関数実行完了後軸が既に静止状態とは限りません。

**サンプルコード**

[:material-api: MoveAbs(int,double,double)](#moveabsintdoubledouble)に参照してください。

### :material-api: OriginMove(int)

同期で単軸に対し原点復帰を行います。

```csharp
void OriginMove(int axisNo)
```

**パラメータ**

`axisNo`:軸のインデックス番号です。

!!! warning
    同期方式のため、この関数実行完了後軸が既に静止状態とは限りません。

**サンプルコード**

[:material-api: MoveAbs(int,double,double)](#moveabsintdoubledouble)に参照してください。

### :material-api: OriginMove()

同期で全軸に対し原点復帰を行います。

```csharp
void OriginMove()
```

!!! warning
    同期方式のため、この関数実行完了後軸が既に静止状態とは限りません。

**サンプルコード**

[:material-api: MoveAbs(int,double,double)](#moveabsintdoubledouble)に参照してください。

### :material-api: Stop(int)

同期で単軸に対し明示的に移動停止指令をします。

```csharp
void Stop(int axisNo)
```

**パラメータ**

`axisNo`:軸のインデックス番号です。

!!! warning
    同期方式のため、この関数実行完了後軸が既に静止状態とは限りません。

### :material-api: StopAll()

同期で全軸に対し明示的に移動停止指令をします。

```csharp
void StopAll()
```

!!! warning
    同期方式のため、この関数実行完了後軸が既に静止状態とは限りません。

### :material-api: ReleaseEmergency()

非常停止が起きた場合、非常停止を解除する。

```csharp
void ReleaseEmergency()
```

!!! warning
    非常停止解除が失敗した場合（例えば物理非常停止スイッチが解除されていないことまたは
    ステージの非常停止原因が解消されていないこと）、非常停止解除すると例外が吐き出されることがあります。

### :material-api: UpdateAxisStatus()

ステージの情報更新します。

```csharp
void UpdateAxisStatus()
```

!!! note
    このAPIはステージの情報を更新するのみ機能します。更新後、必要な情報取得するのに明示的に各プロパティをアクセスする必要があります。

### :material-api: GetExpectAccelerationTime(int,double)

該当軸が静止状態から指定の速度までの加速時間を計算します。

```csharp
double GetExpectAccelerationTime(int axisNo, double speed)
```

**パラメータ**

`axisNo`:軸のインデックス番号です。

`speed`:移動速度。リニア軸の場合、単位はmm/secです。回転軸の場合、単位はdeg/secです。

### :material-api: CreateCustomAxisPropertyItem(int)

軸ごとの内部パラメータを取得します。

```csharp
object? CreateCustomAxisPropertyItem(int)
```

!!! danger
    このAPIは開発調整向けで、基本は使用することはありません。

### :material-api: MoveAbsAsync(int,double,double)

非同期で軸を一定の速度で指定された位置に移動します。

```csharp
Task<EStopStatus> MoveAbsAsync(int axisNo, double targetPosition, double speed)
```

**パラメータ**

`axisNo`:軸のインデックス番号です。

`targetPosition`: 軸の移動先の目標位置です。リニア軸の場合、単位はmmです。回転軸の場合、単位はdegです。

`speed`:移動速度。リニア軸の場合、単位はmm/secです。回転軸の場合、単位はdeg/secです。

**戻り値**

非同期実行タスクをリターンします。非同期実行結果は`EStopStatus`のデータが内包されています。

`NormalStop`: 正しく移動完了したことを表します。

`ErrorStop`: 移動中に軸にエラーが発生し、途中停止されたことを表します。

!!! note
    `EStopStatus`データタイプは`OpotoCombToolkit.Libs.Embedded.Core.nupkg`のパッケージにあるため，依頼関係パッケージとしてプロジェクトに
    インストールされていることをご確認ください。

**サンプルコード**

```csharp
// 同期で軸の移動して、その軸の移動完了まで待ちます
stage.MoveAbs(0, 10.0, 10.0);
EStopStatus result = await stage.WaitUntilStopAsync(0);

// 上記の二行コードを非同期でまとめることができます
EStopStatus result = await stage.MoveAbsAsync(0,10.0, 10.0);
```

### :material-api: MoveRelAsync(int,double,double)

非同期で単軸を一定の速度で現在の位置から指定されたオフセット値で移動します。

```csharp
Task<EStopStatus> MoveRelAsync(int axisNo, double distance, double speed)
```

**パラメータ**

`axisNo`:軸のインデックス番号です。

`distance`: オフセット値。リニア軸の場合、単位はmmです。回転軸の場合、単位はdegです。オフセット値には符号付き可能です、 軸の移動方向に一致します。

`speed`:移動速度。リニア軸の場合、単位はmm/secです。回転軸の場合、単位はdeg/secです。

**戻り値**

非同期実行タスクをリターンします。非同期実行結果は`EStopStatus`のデータが内包されています。

`NormalStop`: 正しく移動完了したことを表します。

`ErrorStop`: 移動中に軸にエラーが発生し、途中停止されたことを表します。

!!! note
    `EStopStatus`データタイプは`OpotoCombToolkit.Libs.Embedded.Core.nupkg`のパッケージにあるため，依頼関係パッケージとしてプロジェクトに
    インストールされていることをご確認ください。

**サンプルコード**

[:material-api: MoveAbsAsync(int,double,double)](#moveabsasyncintdoubledouble)に参照してください。

### :material-api: OriginMoveAsync(int)

非同期で単軸に対し原点復帰を行います。

```csharp
Task<EStopStatus> OriginMoveAsync(int axisNo)
```

**パラメータ**

`axisNo`:軸のインデックス番号です。

**戻り値**

非同期実行タスクをリターンします。非同期実行結果は`EStopStatus`のデータが内包されています。

`NormalStop`: 正しく移動完了したことを表します。

`ErrorStop`: 移動中に軸にエラーが発生し、途中停止されたことを表します。

!!! note
    `EStopStatus`データタイプは`OpotoCombToolkit.Libs.Embedded.Core.nupkg`のパッケージにあるため，依頼関係パッケージとしてプロジェクトに
    インストールされていることをご確認ください。**サンプルコード**

[:material-api: MoveAbsAsync(int,double,double)](#moveabsasyncintdoubledouble)に参照してください。

### :material-api: OriginMoveAsync()

非同期で全軸に対し原点復帰を行います。

```csharp
Task<IEnumerable<EStopStatus>> OriginMoveAsync()
```

**戻り値**

非同期実行タスクをリターンします。非同期実行結果は`EStopStatus`のデータが内包されています。

`NormalStop`: 正しく移動完了したことを表します。

`ErrorStop`: 移動中に軸にエラーが発生し、途中停止されたことを表します。

!!! note
    `EStopStatus`データタイプは`OpotoCombToolkit.Libs.Embedded.Core.nupkg`のパッケージにあるため，依頼関係パッケージとしてプロジェクトに
    インストールされていることをご確認ください。**サンプルコード**

**サンプルコード**

[:material-api: MoveAbsAsync(int,double,double)](#moveabsasyncintdoubledouble)に参照してください。

### :material-api: WaitUntilStopAsync(int)

非同期で軸の移動完了まで待ちます。

```csharp
Task<EStopStatus> WaitUntilStopAsync(int axisNo)
```

**パラメータ**

`axisNo`:軸のインデックス番号です。

**戻り値**

非同期実行タスクをリターンします。非同期実行結果は`EStopStatus`のデータが内包されています。

`NormalStop`: 正しく移動完了したことを表します。

`ErrorStop`: 移動中に軸にエラーが発生し、途中停止されたことを表します。

!!! note
    `EStopStatus`データタイプは`OpotoCombToolkit.Libs.Embedded.Core.nupkg`のパッケージにあるため，依頼関係パッケージとしてプロジェクトに
    インストールされていることをご確認ください。**サンプルコード**

**サンプルコード**

```csharp
// 同期で単軸の原点復帰を行います
stage.OriginMove(0);

// 非同期で原点復帰完了まで待ちます
EStopStatus results = await stage.WaitUntilStopAsync(0);
```

### :material-api: WaitUntilStopAsync()

非同期で全軸の移動完了まで待ちます。

```csharp
Task<IEnumerable<EStopStatus>> WaitUntilStopAsync()
```

**戻り値**

非同期実行タスクをリターンします。非同期実行結果は各軸の`EStopStatus`データを格納する配列が内包されています。

**サンプルコード**

[:material-api: WaitUntilStopAsync(int)](#waituntilstopasyncint)に参照してください。
