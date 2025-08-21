# IStageConfigインターフェース

名前空間: OptoCombToolkit.Libs.Embedded.Stages.Core.Apis

アセンブリ: OptoCombToolkit.Libs.Embedded.Stages.Core.dll

ステージの基本構成情報。

```csharp
public interface IStageConfig
```

## プロパティ

### StageName

ステージの名前。

```csharp
string StageName{get;}
```

**戻り値**

ステージの名前をリターンします。

### Axes

ステージの軸構成情報。

```csharp
IEnumerable<IAxisInfo> Axes{get;}
```

**戻り値**

[IAxisInfo](../iaxisinfo/)タイプの軸構成配列をリターンします。

!!! danger
    この配列のインデックス番号が[IStage](../istage/)インターフェース各APIの`axisNo`パラメータとなっています。


### UpdateRefreshInterval

内部更新時間間隔です。

```csharp
int UpdateRefreshInterval{get;}
```

**戻り値**

内部更新時間間隔をリターンします。単位はmsecです。

## メソッド

### :material-api: GetStage()

[IStage](../istage/)インターフェースタイプのステージインスタンスを取得します。

```csharp
IStage GetStage();
```


**戻り値**

[IStage](../istage/)インターフェースタイプのステージインスタンスをリターンします。

### :material-api: GetInfo(int,int,char)

ステージ構成情報を取得します。

```csharp
string GetInfo(int indentLevel = 0, int indentSpace = 2, char indentChar = ',')
```

**戻り値**

ステージ構成情報の文字列をリターンします。
