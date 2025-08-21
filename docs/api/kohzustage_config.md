# KohzuStageConfigクラス

神津ステージの構成情報クラス。

```csharp
public struct KohzuStageConfig
```

**継承**

[IStageConfig](../istage_config/)インターフェースを継承しています。

## プロパティ

### InitializeInfo

神津ステージコントローラー内部の設定情報。

```csharp
public InitializeInfo InitializeInfo{get; internal set;}
```

**戻り値**

神津ステージコントローラー内部の設定情報をリターンします。

!!! note
    初期設定のままで変更する必要はありません。

### ConnectInfo

神津ステージのソケット通信の設定情報。

```csharp
public ReadableConnectionInfo ConnectInfo{get;set;}
```

**戻り値**

ソケット通信の基本情報をリターンします。

!!! danger
    神津ステージのマスターコントローラーのIP出荷設定は192.168.1.120です。
    これに合わせてPC側のIPを調整してください。
    神津ステージのマスターコントローラーのIP変更する場合は、[こちら](https://optocomb.com/contact-1-0-0-0)へ問い合わせください。
    ポート番号の初期値は**12321**です、基本は変更しません。

!!! tip
    コマンドプロンプトで通信テストが可能です。

    ```bash
    ping 192.168.1.120
    ```

### Axes

`KohzuAxisInfo`タイプの神津精機軸の設定情報。

```csharp
public KohzuAxisInfo[] Axes{get;set;}
```

**戻り値**

`KohzuAxisInfo`タイプの神津精機軸設定情報の配列をリターンします。

!!! note
    [IStageConfig](../istage_config/)インターフェースの`Axes`プロパティはインプリシットで実装されています。

### DefaultConfig

神津精機軸ステージのディフォルト設定情報。

```csharp
public static KohzuStageConfig DefaultConfig{get;}
```

**戻り値**

五軸構成の神津精機ステージの設定情報をリターンします。

!!! tip
    このプロパティはスタティックとなっているため、
    このプロパティを利用して、ディフォルトの構成を`.json`ファイルに出力ことに使用されます。

    ```csharp
    KohzuStageConfig.DefaultConfig.ToJsonFile(@"C:\default_config.json");
    ```

## メソッド

### :material-api: ToJson

神津精機ステージの設定情報を`.json`文字列に変換します。

```csharp
public string ToJson()
```

**戻り値**

神津精機ステージの設定情報の`.json`文字列をリターンします。

**サンプルコード**

```csharp
string json = KohzuStageConfig.DefaultConfig.ToJson();
```

### :material-api: ToJsonFile

神津精機ステージの設定情報を`.json`文字列に変換し、ファイルに保存します。


```csharp
public void ToJsonFile(string jsonFile)
```

**パラメータ**

`jsonFile`:  ファイルの保存パス

**サンプルコード**

```csharp
KohzuStageConfig.DefaultConfig.ToJsonFile(@"C:\default_config.json");
```

### :material-api: FromJson

JSON文字列から`KohzuStageConfig`クラスに変換します。

```csharp
public KohzuStageConfig FromJson(string json)
```

**パラメータ**

`json`: JSON文字列

**戻り値**

変換した`KohzuStageConfig`タイプの設定情報インスタンスをリターンします。

!!! warning
    変換に失敗した場合は、例外が吐き出されます。

**サンプルコード**

```csharp
//  三軸構成を例とします。
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

KohzuStageConfig config = new KohzuStageConfig().FromJson(json);
'''
```

### :material-api: FromJsonFile

JSONファイルから神津精機ステージの設定情報を読み込みます。

```csharp
public void FromJsonFile(string jsonFile)
```

**パラメータ**

`jsonFile`:  JSONファイルのパス。

**サンプルコード**

```csharp
//  ディフォルトの設定情報インスタンスを用意します。
KohzuStageConfig config = KohzuStageConfig.DefaultConfig;

//  JSONファイルから神津精機ステージの設定情報を読み込みます
config.FromJsonFile(@"config.json")

//  ステージオープンする
config.Open();
```

!!! note
    `KohzuStageConfig`は値型となっているため、この関数実行する際、従来の内容を上書きと形でファイルの内容を読み込みます。

