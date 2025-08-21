# IAxisInfoインターフェース

名前空間: OptoCombToolkit.Libs.Embedded.Stages.Core.Apis

アセンブリ: OptoCombToolkit.Libs.Embedded.Stages.Core.dll

軸の基本情報。

```csharp
public interface IAxisInfo
```

## プロパティ

### Type

軸の種類。

```csharp
EAxisType Type{get;}
```

**戻り値**

`EAxisType`タイプの軸種類をリターンします。

`Linear`: リニア軸

`Rotary`: 回転軸

### Id

軸に割り当てた物理論理局番です。

```csharp
int Id{get;}
```

**戻り値**

軸に割り当てた物理論理局番をリターンします。

!!! danger
    物理論理局番は装置設置完了後変更することはありません。

### Name

軸の名前。

```csharp
string Name{get;}
```

**戻り値**

軸の名前を表す文字列をリターンします。例：X軸。

!!! tip
    軸の名前は任意です。

### NegativeString

マイナス移動方向の表示文字。

```csharp
string NegativeString{get;}
```

**戻り値**

マイナス移動方向の表示文字をリターンします。

!!! tip
    軸の種類によって、マイナス方向の表示文字が異なる場合があります。
    リニア軸の場合は、「-」と表示し、回転軸は「CCW」と表示するのが一般です。
    任意指定可能です。

### PositiveString

プラス移動方向の表示文字。

```csharp
string PositiveString{get;}
```

**戻り値**

プラス移動方向の表示文字をリターンします。


!!! tip
    軸の種類によって、マイナス方向の表示文字が異なる場合があります。
    リニア軸の場合は、「+」と表示し、回転軸は「CW」と表示するのが一般です。
    任意指定可能です。


### ReverseDirection

軸の移動方向を逆にするかどうか。

```csharp
bool ReverseDirection{get;}
```

**戻り値**

軸の移動方向を逆にするかどうかの値をリターンします。

`true`の場合は、軸の移動方向を逆にします。
`false`の場合は、軸の既定の移動方向に準じます。

!!! danger
    この設定の初期値は`false`です。
    ハードウェア側で軸の移動方法の逆設定ができない場合、ソフト上で移動方向の反転制御を行います。
    基本は修正しません。

