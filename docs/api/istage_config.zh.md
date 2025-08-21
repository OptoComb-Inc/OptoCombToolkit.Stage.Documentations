# IStageConfig接口

名称空间: OptoCombToolkit.Libs.Embedded.Stages.Core.Apis

程序集: OptoCombToolkit.Libs.Embedded.Stages.Core.dll

平台配置的基本接口。

```csharp
public interface IStageConfig
```

## 属性

### StageName

平台名称。

```csharp
string StageName{get;}
```

**返回值**

返回一个字符串类型的数值表示平台名称。

### Axes

平台所连接的轴的信息列表。

```csharp
IEnumerable<IAxisInfo> Axes{get;}
```

**返回值**

返回一个包含所有[IAxisInfo](../iaxisinfo/)类型的轴配置信息的列表。

!!! danger
    这里定义的轴的列表的索引值，即为[IStage](../istage/)的基本控制接口中的`axisNo`参数值。


### UpdateRefreshInterval

内部更新平台信息的更新速率。

```csharp
int UpdateRefreshInterval{get;}
```

**返回值**

返回一个整数值，单位为毫秒。

## 方法

### :material-api: GetStage()

通过平台的配置来获取一个平台对象实例。

```csharp
IStage GetStage();
```


**返回值**

返回一个[IStage](../istage/)接口类型的实例对象。

### :material-api: GetInfo(int,int,char)

返回一个包含信息内容的字符串。

```csharp
string GetInfo(int indentLevel = 0, int indentSpace = 2, char indentChar = ',')
```

**返回值**

返回平台的具体配置内容信息。
