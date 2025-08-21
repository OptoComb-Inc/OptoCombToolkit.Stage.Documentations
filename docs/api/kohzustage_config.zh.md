# KohzuStageConfig类

神津精机的配置文件对象类型。

```csharp
public struct KohzuStageConfig
```

**继承**

[IStageConfig](../istage_config/)接口类型。

## 属性

### InitializeInfo

神津精机控制器内部的配置内容对象。

```csharp
public InitializeInfo InitializeInfo{get; internal set;}
```

**返回值**

返回一个包含了内部日志相关的配置内容。

!!! note
    默认设置无需更改。

### ConnectInfo

神津精机平台用以太网交互的基本信息。

```csharp
public ReadableConnectionInfo ConnectInfo{get;set;}
```

**返回值**

返回一个包含了以太网通信的IP和端口号的基本信息。

!!! danger
    神津精机平台的控制主机的出厂IP为**192.168.1.120**，使用端的IP根据控制主机的出厂设置进行调整。
    如需变更控制主机的IP地址，请与[我们](https://optocomb.com/contact-1-0-0-0)联系。
    端口号默认值为**12321**，无需进行更改。

!!! tip
    可以用一下命令行来测试以太网的通信情况。

    ```bash
    ping 192.168.1.120
    ```

### Axes

`KohzuAxisInfo`类型的神津精机轴的信息列表。

```csharp
public KohzuAxisInfo[] Axes{get;set;}
```

**返回值**

返回一个`KohzuAxisInfo`类型的神津精机轴的信息列表。

!!! note
    [IStageConfig](../istage_config/)接口类型中的`Axes`属性的实现在该对象中通过接口的隐式方式进行了实装。

### DefaultConfig

神津精机平台的默认配置。

```csharp
public static KohzuStageConfig DefaultConfig{get;}
```

**返回值**

返回一个由五个轴构成的默认神津精机平台的配置文件。

!!! tip
    该属性为静态实现。可以通过调用该方法来快速的生成一个默认配置的对象，并且以`.json`的类型输出。

    ```csharp
    KohzuStageConfig.DefaultConfig.ToJsonFile(@"C:\default_config.json");
    ```

## 方法

### :material-api: ToJson

将神津精机平台的配置对象实例转换成`.json`字符串。

```csharp
public string ToJson()
```

**返回值**

返回一个配置内容相对应的`.json`字符串

**示例代码**

```csharp
string json = KohzuStageConfig.DefaultConfig.ToJson();
```

### :material-api: ToJsonFile

将神津精机平台的配置对象实例转换成`.json`字符串，并且保存。

```csharp
public void ToJsonFile(string jsonFile)
```

**参数**

`jsonFile`: Json文件的保存路径

**示例代码**

```csharp
KohzuStageConfig.DefaultConfig.ToJsonFile(@"C:\default_config.json");
```

### :material-api: FromJson

将JSON字符串转换成`KohzuStageConfig`对象实例。

```csharp
public KohzuStageConfig FromJson(string json)
```

**参数**

`json`: JSON字符串

**返回值**

神津精机平台的配置对象实例。

!!! warning
    如果无法将JSON字符串转换成对象类型，则会抛出异常。

**示例代码**

```csharp
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

KohzuStageConfig config = new KohzuStageConfig().FromJson(json);
'''
```

### :material-api: FromJsonFile

将JSON文件的内容转换成神津精机平台配置实例对象。

```csharp
public void FromJsonFile(string jsonFile)
```

**参数**

`jsonFile`: Json文件的保存路径

**示例代码**

```csharp
// 获取一个默认的配置
KohzuStageConfig config = KohzuStageConfig.DefaultConfig;

// 读取JSON
config.FromJsonFile(@"config.json")

// 这个时候，默认的五轴配置被重写为JSON文件中的配置信息
config.Open();
```

!!! note
    因为`KohzuStageConfig`是值类型的构造体所以在使用这个函数时并不会返回一个新的实例，而是将现有的实例中的值进行了重写。

