---
title: 系結目標-C 程式庫
description: 本檔提供如何建立C#目標 C 程式碼系結的高階總覽, 並說明如何系結事件、方法、自訂控制項等等。
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: df90bc764200434e8d546a1ebf61e039498517bb
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199404"
---
# <a name="binding-objective-c-libraries"></a>系結目標-C 程式庫

使用 Xamarin 或 Xamarin 時, 您可能會遇到需要使用協力廠商目標-C 程式庫的情況。 在這些情況下, 您可以使用 Xamarin 系結專案來C#建立原生目標 C 程式庫的系結。 專案會使用我們用來將 iOS 和 Mac Api 帶入的相同工具C#。

本檔說明如何系結目標-C Api, 如果您只系結 C Api, 您應該使用標準 .NET 機制來執行此操作, 也就是[P/Invoke 架構](https://www.mono-project.com/docs/advanced/pinvoke/)。
如需如何以靜態方式連結 C 程式庫的詳細資訊, 請前往[連結原生程式庫](~/ios/platform/native-interop.md)頁面。

請參閱我們隨附的系結[類型參考指南](~/cross-platform/macios/binding/binding-types-reference.md)。
此外, 如果您想要深入瞭解幕後發生的情況, 請查看系結的[[總覽](~/cross-platform/macios/binding/overview.md)] 頁面。

可以為 iOS 和 Mac 程式庫建立系結。
此頁面說明如何使用 iOS 系結, 但 Mac 系結非常類似。

**IOS 的範例程式碼**

您可以使用 IOS 系結[範例](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample)專案來試驗系結。

<a name="Getting_Started" />

## <a name="getting-started"></a>使用者入門

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

建立系結最簡單的方式, 就是建立一個 Xamarin. iOS 系結專案。
您可以從 Visual Studio for Mac 選取專案類型 [ **iOS > 程式庫] >** 系結程式庫來執行此動作:

[![](objective-c-libraries-images/00-sml.png "從 Visual Studio for Mac 選取專案類型 [iOS 程式庫] [程式庫], 以執行這項操作。")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

建立系結最簡單的方式, 就是建立一個 Xamarin. iOS 系結專案。
您可以從 Windows 上的 Visual Studio 選取專案類型 [ **Visual C# > ios > 系結程式庫 (ios)** ] 來執行這項操作:

[![](objective-c-libraries-images/00vs-sml.png "iOS 系結程式庫 iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> 注意:只有 Visual Studio for Mac 支援**Xamarin 的系**結專案。

-----

產生的專案包含您可以編輯的小型範本, 其中包含兩個檔案: `ApiDefinition.cs`和`StructsAndEnums.cs`。

是您將定義 API 合約的位置, 這是描述基礎目標-C api 如何投射到C#的檔案。 `ApiDefinition.cs` 此檔案的語法和內容是本檔討論的主要主題, 而其內容僅限於C#介面和C#委派宣告。 `StructsAndEnums.cs`檔案是您將在其中輸入介面和委派所需之任何定義的檔案。 這包括您的程式碼可能會使用的列舉值和結構。

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>系結 API

若要執行完整的系結, 您會想要瞭解目標-C API 定義, 並熟悉 .NET Framework 的設計方針。

若要系結您的程式庫, 您通常會從 API 定義檔開始。 API 定義檔只是一個C#原始程式檔, 其中C#包含已使用一些可協助驅動系結的屬性來標注的介面。  此檔案會定義與目標-C 之間C#的合約。

例如, 這是程式庫的簡單 api 檔案:

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

上述範例`Cocos2D.Camera`會定義一個衍生`NSObject`自基底類型`Foundation.NSObject`(此類型來自) 的類別, 並定義靜態屬性 (`ZEye`)、兩個不接受引數的方法, 以及採用三個的方法參量.

您可以在下面的[api 定義](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)檔案一節中, 深入討論 api 檔案的格式以及您可使用的屬性。

若要產生完整的系結, 您通常會處理四個元件:

- API 定義檔 (`ApiDefinition.cs`在範本中)。
- 選擇性: API 定義檔所需的任何列舉、類型、結構 (`StructsAndEnums.cs`在範本中)。
- 選擇性: 可能會展開所產生之系結的額外來源, 或C#提供更易記的C# API (任何您加入至專案的檔案)。
- 您要系結的原生程式庫。

此圖表顯示檔案之間的關聯性:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "此圖表顯示檔案之間的關聯性")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

API 定義檔只會包含命名空間和介面定義 (具有介面可以包含的任何成員), 而且不應該包含類別、列舉、委派或結構。 API 定義檔只是將用來產生 API 的合約。

您需要的任何額外程式碼 (例如列舉或支援類別) 都應該裝載在不同的檔案上, 在上述範例中, "CameraMode" 是不存在於 CS 檔案中且應裝載于個別檔案中的列舉值, 例如`StructsAndEnums.cs`:

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

`APIDefinition.cs`檔案會`StructsAndEnum`與類別結合, 並用於產生程式庫的核心系結。 您可以使用所產生的程式庫, 但一般來說, 您會想要調整產生的媒體櫃, 為您C#的使用者權益新增一些功能。 其中一些範例包括實`ToString()`作為方法、 C#提供索引子、加入與某些原生類型之間的隱含轉換, 或提供某些方法的強型別版本。 這些改良功能會儲存在C#額外的檔案中。 只要將檔案C#新增至您的專案, 這些檔案就會包含在此組建進程中。

這會顯示如何在您的檔案中執行`Extra.cs`程式碼。 請注意, 您將使用部分類別, 因為這些會增強從`ApiDefinition.cs` `StructsAndEnums.cs`和核心系結的組合產生的部分類別:

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

建立程式庫將會產生您的原生系結。

若要完成此系結, 您應該將原生程式庫加入至專案。  若要這麼做, 您可以將原生程式庫加入至專案, 方法是將原生程式庫從搜尋工具拖放至方案 explorer 中的專案, 或以滑鼠右鍵按一下專案, 然後選擇 [**新增** > ] [新增檔案至]選取原生程式庫。
依慣例的原生程式庫會以 "lib" 一字開頭, 並以副檔名 ". a" 結尾。 當您這麼做時, Visual Studio for Mac 將會加入兩個檔案: 檔案和自動填入C#的檔案, 其中包含原生程式庫所包含內容的相關資訊:

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "依慣例的原生程式庫會以 lib 開頭, 並以副檔名結尾。")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

檔案的內容`libMagicChord.linkwith.cs`包含如何使用此程式庫的相關資訊, 並指示您的 IDE 將此二進位檔封裝到產生的 DLL 檔案中:

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

如何使用的完整詳細資料[`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 
屬性記載于系結[類型參考指南](~/cross-platform/macios/binding/binding-types-reference.md)中。

現在當您建立專案時, 將會得到一個`MagicChords.dll`檔案, 其中包含系結和原生程式庫。 您可以將此專案或產生的 DLL 散發給其他開發人員, 以供自己使用。

有時候, 您可能會發現需要一些列舉值、委派定義或其他類型。 請勿將這些檔案放在 API 定義檔中, 因為這只是合約

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>API 定義檔

API 定義檔包含許多介面。 API 定義中的介面會轉換成類別宣告, 而且必須使用[`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)屬性來裝飾, 以指定類別的基類。

您可能會想知道為什麼我們不使用類別, 而不是合約定義的介面。 我們挑選了介面, 因為它可讓我們撰寫方法的合約, 而不需要在 API 定義檔中提供方法主體, 或必須提供必須擲回例外狀況或傳回有意義之值的主體。

但由於我們使用介面做為產生類別的基本架構, 因此我們必須利用屬性來裝飾合約的各個部分, 以驅動系結。

<a name="Binding_Methods" />

### <a name="binding-methods"></a>系結方法

您可以執行的最簡單系結是系結方法。 只要在介面中宣告具有C#命名慣例的方法, 並使用來裝飾方法[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
特性. 屬性會連結您C#的名稱與 Xamarin iOS 執行時間中的目標-C 名稱。 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 的參數。[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attribute 是目標-C 選取器的名稱。 一些範例如下：

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

上述範例會示範如何系結實例方法。 若要系結靜態方法, 您必須[`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)使用屬性, 如下所示:

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

這是必要的, 因為合約是介面的一部分, 而介面沒有靜態與實例宣告的概念, 因此必須再次使用屬性。 如果您想要隱藏系結中的特定方法, 您可以使用[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性來裝飾方法。

此`btouch-native`命令會將參考參數的檢查引入不是 null。 如果您想要針對特定參數允許 null 值, 請使用[`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
參數上的屬性, 如下所示:

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)關鍵字匯出參考型別時, 您也可以指定配置的語義。 這是確保不會遺漏任何資料的必要動作。

<a name="Binding_Properties" />

### <a name="binding-properties"></a>系結屬性

如同方法, 目標-C 屬性會使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
屬性, 並直接對應C#至屬性。 如同方法, 屬性可以使用[`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)
和[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
特性.

當您使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)涵蓋 btouch 原生之下的屬性上屬性實際上會繫結的兩個方法： getter 和 setter。 您提供給匯出的名稱是**basename** , 而 setter 則是藉由在前面加上 "set" 這個字來計算, 將**basename**的第一個字母轉換成大寫, 然後讓選取器接受引數。 這表示在`[Export ("label")]`屬性上套用的會實際系結「標籤」和「setLabel:」目標-C 方法。

有時, 目標-C 屬性不會遵循上述的模式, 而是手動覆寫名稱。 在這些情況下, 您可以使用來控制系結的產生方式。[`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 
getter 或 setter 上的屬性, 例如:

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

這會接著系結 "isMenuVisible" 和 "setMenuVisible:"。 或者, 您可以使用下列語法來系結屬性:

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

在上述的`name`和`setName`系結中, 會將 getter 和 setter 明確定義為。

除了使用[`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)的靜態屬性支援以外, 您還可以用[`[IsThreadStatic]`](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute)來裝飾執行緒靜態屬性, 例如:

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

就像方法允許某些參數加[`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)上旗標, 您可以套用[`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
屬性, 表示 null 是屬性的有效值, 例如:

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

[`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)參數也可以直接在 setter 上指定:

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>系結自訂控制項的警告

設定自訂控制項的系結時, 應該考慮下列事項:

1. 系結**屬性必須為靜態**-定義屬性的系結時, [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)必須使用屬性。
2. **屬性名稱必須完全相符**-用來系結屬性的名稱必須完全符合自訂控制項中的屬性名稱。
3. **屬性類型必須完全符合**-用來系結屬性的變數類型必須完全符合自訂控制項中的屬性類型。
4. 將永遠不會叫用**中斷點和 getter/setter** -位於屬性之 getter 或 setter 方法中的中斷點。
5. **觀察回撥**-您必須使用觀察回呼來通知自訂控制項屬性值的變更。

若無法觀察上述任何列出的警告, 可能會導致在執行時間以無訊息方式失敗。

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>目標-C 可變動模式和屬性

目標-C 架構會使用某些類別不會與可變子類別變的用法。 例如`NSString` , 是不可變的版本, `NSMutableString`而是允許變化的子類別。

在這些類別中, 通常會看到不可變的基類包含具有 getter 的屬性, 但沒有 setter。 和, 讓可變動的版本引進 setter。 因為這不太可能發生C#, 所以我們必須將此方法對應到可使用的用法。 C#

這種對應的方式C#是在基類上加入 getter 和 setter, 但會以下列程式標記 setter:[`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)
特性.

然後, 在可變的子類別上, 您可以使用[`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
屬性 (property) 上的屬性 (attribute), 以確保屬性確實會覆寫父系的行為。

範例：

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>系結函式

此`btouch-native`工具會在您的類別中自動產生 fours 的函式, `Foo`針對指定的類別, 它會產生:

- `Foo ()`: 預設的函式 (對應至目標-C 的 "init" 函數)
- `Foo (NSCoder)`: 在還原序列化筆尖檔案期間使用的函式 (對應至目標-C 的 "initWithCoder:" 程式化)。
- `Foo (IntPtr handle)`: 以控制碼為基礎之建立的函式, 執行時間需要從非受控物件公開 managed 物件時, 會叫用此函數。
- `Foo (NSEmptyFlag)`: 衍生類別會使用這個來避免雙重初始化。

針對您所定義的函式, 必須在介面定義內使用下列簽章來宣告這些函式`IntPtr` : 它們必須傳回值, 而且方法的名稱應該是「構造函式」。 例如, 若要`initWithFrame:`系結此函式, 這就是您要使用的:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>系結通訊協定

如 API 設計檔中所述, 在[討論模型和通訊協定](~/ios/internals/api-design/index.md#models)一節中, Xamarin 會將目標 C 通訊協定對應至已標記為的類別。[`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)
特性. 這通常是在執行目標-C 委派類別時使用。

一般系結類別和委派類別之間的最大差異在於, 委派類別可能會有一或多個選擇性方法。

例如, 請考慮`UIKit`類別`UIAccelerometerDelegate`, 這就是它在 Xamarin 中的系結方式:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

因為這是定義`UIAccelerometerDelegate`上的選擇性方法, 所以不需要執行其他動作。 但如果通訊協定上有必要的方法, 您應該新增[`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
方法的屬性。 這會強制執行程式的使用者實際提供方法的主體。

一般來說, 通訊協定是在回應訊息的類別中使用。 這通常是在目標-C 中完成, 方法是指派給 "delegate" 屬性物件的實例, 以回應通訊協定中的方法。

在 Xamarin 中的慣例是支援目標-C 鬆散結合的樣式, 其中的`NSObject`任何實例都可以指派給委派, 同時也會公開它的強型別版本。 基於這個理由, 我們通常會提供`Delegate`強型別的屬性`WeakDelegate` , 以及鬆散型別的。 我們通常會將鬆散類型的版本與[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)系結, 而我們[`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)會使用屬性來提供強型別版本。

這會顯示我們`UIAccelerometer`如何系結類別:

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

<a name="iOS7ProtocolSupport" />

**MonoTouch 7.0 的新功能**

從 MonoTouch 7.0 開始已納入全新且改良的通訊協定系結功能。  這項新的支援可讓您更輕鬆地使用慣用語, 以在指定類別中採用一或多個通訊協定。

針對目標-C `MyProtocol`中的每個通訊協定定義, 現在`IMyProtocol`有一個介面會列出來自通訊協定的所有必要方法, 以及提供所有選擇性方法的擴充類別。  上述的與 Xamarin Studio 編輯器中的新支援結合, 可讓開發人員不需要使用先前抽象模型類別的個別子類別, 就能執行通訊協定方法。

包含[`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute)屬性的任何定義實際上都會產生三個支援的類別, 以大幅改善您取用通訊協定的方式:

```csharp
// Full method implementation, contains all methods
class MyProtocol : IMyProtocol {
    public void Say (string msg);
    public void Listen (string msg);
}

// Interface that contains only the required methods
interface IMyProtocol: INativeObject, IDisposable {
    [Export ("say:")]
    void Say (string msg);
}

// Extension methods
static class IMyProtocol_Extensions {
    public static void Optional (this IMyProtocol this, string msg);
    }
}
```

**類別執行**會提供完整的抽象類別, 您可以覆寫個別的方法, 並取得完整的型別安全。  但由於C#不支援多重繼承, 因此在某些情況下, 您可能需要有不同的基類, 但您仍想要執行介面, 也就是

產生的**介面定義**出現在中。  它是一種介面, 具有來自通訊協定的所有必要方法。  這可讓想要執行通訊協定的開發人員只執行介面。  執行時間會自動將類型註冊為採用通訊協定。

請注意, 介面只會列出必要的方法, 並會公開選擇性的方法。  這表示採用通訊協定的類別會針對所需的方法取得完整的類型檢查, 但必須使用弱型別 (以手動方式輸入[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
選擇性通訊協定方法的屬性和相符的簽章)。

為了方便取用使用通訊協定的 API, 系結工具也會產生擴充方法類別, 以公開所有選用的方法。  這表示只要您使用 API, 您就能夠將通訊協定視為具有所有方法。

如果您想要在 API 中使用通訊協定定義, 就必須在 API 定義中撰寫基本架構空白介面。  如果您想要在 API 中使用 MyProtocol, 您必須執行下列動作:

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

因為在系結時`IMyProtocol`不存在, 所以需要上述的, 這就是為什麼您需要提供空的介面。

#### <a name="adopting-protocol-generated-interfaces"></a>採用通訊協定產生的介面

每當您執行為通訊協定產生的其中一個介面時, 如下所示:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

介面方法的執行會自動以適當的名稱匯出, 因此它相當於:

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

如果介面是隱含或明確地實作為, 就不會有任何影響。

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>系結類別延伸

在 [目標-C] 中, 您可以使用新C#的方法來擴充類別, 這在與的擴充方法相同。 當其中一個方法存在時, 您可以使用[`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
用來將方法標示為目標-C 訊息接收者的屬性。

例如, 在 Xamarin 中, 我們系結了在匯入做為中`NSString`的`UIKit` `NSStringDrawingExtensions`方法時所定義的擴充方法, 如下所示:

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>系結目標-C 引數清單

目標-C 支援 variadic 引數。 例如：

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

若要從C#叫用此方法, 您會想要建立如下的簽章:

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

這會將方法宣告為內部, 並隱藏使用者的上述 API, 但將其公開至程式庫。 接著, 您可以撰寫如下所示的方法:

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

<a name="Binding_Fields" />

### <a name="binding-fields"></a>系結欄位

有時候您會想要存取在程式庫中宣告的公用欄位。

這些欄位通常包含必須參考的字串或整數值。 它們通常用來做為代表特定通知的字串, 以及做為字典中的索引鍵。

若要系結欄位, 請將屬性加入至您的介面定義檔, 並使用[`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)屬性裝飾屬性。 這個屬性接受一個參數: 要查閱之符號的 C 名稱。 例如：

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

如果您想要將不同的欄位包裝在不是衍生自`NSObject`的靜態類別中, 您可以使用[`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
類別上的屬性, 如下所示:

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

上述會產生不是`LonelyClass`衍生自`NSObject`的, 而且會包含與公開為`NSString`的`NSSomeEventNotification` 
 `NSString`系結。

[`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)屬性可以套用至下列資料類型:

- `NSString`參考 (僅限唯讀屬性)
- `NSArray`參考 (僅限唯讀屬性)
- 32位整數 (`System.Int32`)
- 64位整數 (`System.Int64`)
- 32-位浮點數`System.Single`()
- 64-位浮點數`System.Double`()
- `System.Drawing.SizeF`
- `CGSize`

除了原生功能變數名稱以外, 您還可以藉由傳遞程式庫名稱來指定欄位所在的程式庫名稱:

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

如果您要以靜態方式連結, 則不需要系結至程式庫, 因此您必須`__Internal`使用名稱:

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>系結列舉

您可以直接`enum`將系結檔案新增至您的系結檔案, 以便更輕鬆地在 API 定義中使用它們, 而不需要使用不同的原始程式檔 (必須在系結和最終專案中編譯)。

範例：

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

您也可以建立自己的列舉來取代`NSString`常數。 在此情況下, 產生器會**自動**建立方法來為您轉換列舉值和 NSString 常數。

範例：

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

在上述範例中, 您可以決定`void Perform (NSString mode);` [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)使用屬性來裝飾。 這會從您的系結取用者**隱藏**以常數為基礎的 API。

不過, 這會限制類型的子類別化, 因為更好 API [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)的替代方法會使用屬性。 這些產生的方法不`virtual`是, 也就是您無法覆寫它們-這可能是不錯的選擇。

替代方式是將原始`NSString`以為基礎的定義標示為。 `[Protected]` 這會在必要時允許子類別化工作, 而 wrap'ed 版本仍然可以使用並呼叫覆寫的方法。

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>將`NSValue`、 `NSNumber`和`NSString`系結至較佳的類型

屬性[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) C#允許系`NSValue` 結`NSString`, 並將 (列舉) 轉換成更精確的類型。 `NSNumber` 屬性可以透過原生 API, 用來建立更好、更精確的 .NET API。

您可以使用[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)來裝飾方法 (在傳回值上)、參數和屬性。 唯一的限制是您的成員**不得**位於[`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
或[`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)介面。

例如：

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

會輸出:

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

在內部`NSNumber` , 我們將`bool?`進行`CGRect` 和 <-> 轉換。  <->  `NSValue`

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)也支援和`NSNumber` `NSValue` 的`NSString`陣列 (列舉)。

例如：

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

會輸出:

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll`是支援的列舉, 我們將會提取正確`NSString`的值, 並處理類型轉換。 `NSString`

請參閱[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)檔, 以查看支援的轉換類型。

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>系結通知

通知是指張貼至`NSNotificationCenter.DefaultCenter`和的訊息, 可用來做為將訊息從應用程式的某個部分廣播至另一個元件的機制。 開發人員通常會使用[NSNotificationCenter](xref:Foundation.NSNotificationCenter)的[AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification}))方法來訂閱通知。 當應用程式將訊息張貼至通知中心時, 通常會包含儲存在[NSNotification](xref:Foundation.NSNotification.UserInfo)中的承載字典。 這個字典是弱型別, 而從中取得資訊很容易發生錯誤, 因為使用者通常需要閱讀檔中的索引鍵可用於字典, 以及可以儲存在字典中的數值型別。 金鑰的存在有時候也會用來做為布林值。

「Xamarin」系結產生器會提供開發人員系結通知的支援。 若要這麼做, 請設定[`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
屬性的屬性 (attribute) 也已標記為[`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)
屬性 (可以是公用或私用)。

此屬性可用於不含任何承載之通知的引數, 或者您可以指定`System.Type`參考 API 定義中另一個介面的, 其名稱通常會以 "EventArgs" 結尾。 產生器會將介面轉換為子`EventArgs`類別, 且會包含此處列出的所有屬性。 [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性應該用於 EventArgs 類別中, 以列出用來查閱目標-C 字典以提取值的索引鍵名稱。

例如：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

上述程式碼會使用下列方法來`MyClass.Notifications`產生一個嵌套類別:

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

然後, 您程式碼的使用者就可以使用如下的程式碼, 輕鬆地訂閱張貼至[NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter)的通知:

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

從`ObserveDidStart`傳回的值可以用來輕鬆地停止接收通知, 如下所示:

```csharp
token.Dispose ();
```

或者, 您可以呼叫[NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject))並傳遞權杖。 如果您的通知包含參數, 您應該指定 helper `EventArgs`介面, 如下所示:

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

上述將`MyScreenChangedEventArgs`會產生`ScreenX`具有和屬性的類別`ScreenY` , 而這些屬性會分別使用索引鍵 "ScreenXKey" 和 "ScreenYKey" 來提取[NSNotification](xref:Foundation.NSNotification.UserInfo)中的資料, 並套用適當的全都. 如果在中設定索引鍵`UserInfo`, 則會使用屬性來進行探查,而不是嘗試`[ProbePresence]`將值解壓縮。 這適用于有索引鍵為值的情況 (通常用於布林值)。

這可讓您撰寫如下的程式碼:

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>系結類別

類別目錄是一個目標 C 機制, 用來擴充類別中可用的方法和屬性集合。   實際上, 它們是用來擴充基類的功能 ( `NSObject`例如), 當特定架構連結在中`UIKit`時 (例如), 讓其方法可用, 但只有在新架構連結時才會使用。   在某些其他情況下, 它們是用來依功能來組織類別中的功能。   它們與C#擴充方法的精神很類似。這是類別在目標-C 中的樣子:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

如果在程式庫上找到, 則上述範例會使用`UIView`方法`makeBackgroundRed`來擴充的實例。

若要系結, 您可以在[`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)介面定義上使用屬性。  使用[`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
屬性, 其意義[`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
屬性變更, 使其無法用來指定要擴充的基類, 做為要擴充的類型。

以下顯示擴充功能的`UIView`系結和轉換為C#擴充方法的方式:

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

上述會建立`MyUIViewExtension` `MakeBackgroundRed`包含擴充方法的類別。  這表示您現在可以在任何`UIView`子類別上呼叫 "MakeBackgroundRed", 提供您在目標上取得的相同功能。 在某些其他情況下, 則不會使用類別來擴充系統類別, 而是用來組織功能, 純粹用於裝飾用途。  與下列類似：

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

雖然您可以使用[`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
屬性也適用于這種裝飾樣式的宣告, 您也可以將它們全部加入至類別定義。  這兩種方法都能達到相同的效果:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

在這些情況下, 合併分類的情況只會較短:

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

<a name="Binding_Blocks" />

### <a name="binding-blocks"></a>系結區塊

區塊是 Apple 引進的新結構, 可將功能相當的C#匿名方法帶入目標-C。 例如, `NSSet`類別現在會公開這個方法:

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

上述描述會宣告名`enumerateObjectsUsingBlock:`為的方法, 其採用一個名為`block`的引數。 這個區塊類似于C#匿名方法, 因為它支援捕獲目前的環境 ("this" 指標、區域變數和參數的存取權)。 中`NSSet`的上述方法會叫用具有兩個`NSObject`參數 ( `id obj`元件) `BOOL *stop`和布林 () 部分指標的區塊。

若要使用 btouch 系結這類 API, 您必須先將區塊類型簽章宣告為C#委派, 然後從 API 進入點參考它, 如下所示:

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

現在, 您的程式碼可以從呼叫C#您的函式:

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

如果您想要的話, 也可以使用 lambda:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>非同步方法

系結產生器可以將特定類別的方法變成非同步易懂的方法 (傳回工作或&lt;工作 T&gt;的方法)。

您可以使用[`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
傳回 void 之方法的屬性, 其最後一個引數是回呼。  當您將此套用至方法時, 系結產生器會使用`Async`後置詞來產生該方法的版本。  如果回呼不接受任何參數, 則傳回值會是`Task`, 如果回呼接受參數, 則結果會`Task<T>`是。  如果回呼接受多個參數, 您應該設定`ResultType`或`ResultTypeName` , 以指定將保留所有屬性之產生類型的所需名稱。

範例：

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

上述程式碼會產生 LoadFile 方法, 以及:

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>呈現弱式 NSDictionary 參數的強式類型

在目標-C API 的許多位置中, 參數會以弱型`NSDictionary`別 api 的形式傳遞, 並具有特定的索引鍵和值, 但這些是容易出錯的 (您可以傳遞不正確索引鍵, 而且不會收到任何警告; 您可以傳遞不正確值, 而且不會出現警告), 而令人沮喪若要使用, 因為它們需要多個檔行程來查閱可能的索引鍵名稱和值。

解決方案是提供強型別的版本, 以提供 API 的強型別版本, 而幕後則會對應各種基礎索引鍵和值。

因此, 例如, 如果目標-C `NSDictionary` API 接受, 而它被記錄為採用`NSNumber`具有從 0.0 `XyzVolumeKey`到1.0 之`XyzCaptionKey`磁片區值的金鑰, 以及接受字串的, 則您會希望使用者擁有絕佳的 API如下所示:

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

`Volume`屬性會定義為可為 null 的 float, 因為目標-C 中的慣例不需要這些字典具有值, 因此在某些情況下可能不會設定此值。

若要這樣做, 您需要執行幾項工作:

- 建立強型別類別, 子類別會[DictionaryContainer](xref:Foundation.DictionaryContainer)並提供每個屬性的各種 getter 和 setter。
- 針對接受新的強型`NSDictionary`別版本的方法宣告多載。

您可以手動建立強型別類別, 或使用產生器為您執行這項工作。  我們會先探索如何手動執行此動作, 讓您瞭解發生了什麼事, 然後再進行自動方法。

您需要為此建立支援檔案, 而不會進入您的合約 API。  這就是您必須撰寫來建立 XyzOptions 類別的內容:

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

接著, 您應該提供可在低層級 API 之上呈現高階 API 的包裝函式方法。

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

如果您的 API 不需要覆寫, 您可以使用, 安全地隱藏以 NSDictionary 為基礎的 API[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
特性.

如您所見, 我們會使用[`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
屬性, 以呈現新的 API 進入點, 並使用我們的強型`XyzOptions`別類別來呈現。  包裝函式方法也允許傳遞 null。

現在, 我們並未提到的一件事就是這些`XyzOptionsKeys`值來自何處。  您通常會將 API 表面呈現在靜態類別`XyzOptionsKeys`中的金鑰分組, 如下所示:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

讓我們查看自動支援以建立這些強型別字典。  這可避免許多重複使用, 而且您可以直接在 API 合約中定義字典, 而不是使用外部檔案。

若要建立強型別字典, 請在您的 API 中引進介面, 並使用[StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)屬性裝飾。  這會告訴產生器, 它應該建立一個類別, 其名稱與將衍生自`DictionaryContainer`的介面相同, 而且將為其提供強型別存取子。

[`[StrongDictionary]`](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)屬性會採用一個參數, 也就是包含字典索引鍵的靜態類別名稱。  然後, 介面的每個屬性都會變成強型別存取子。  根據預設, 此程式碼會在靜態類別中使用具有後置詞 "Key" 的屬性名稱, 以建立存取子。

這表示建立您的強型別存取子不再需要外部檔案, 也不必為每個屬性手動建立 getter 和 setter, 也不必手動查閱索引鍵。

這就是整個系結的樣子:

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

如果您需要在`XyzOption`成員中參考不同的欄位 (不是具有尾碼`Key`的屬性名稱), 您可以使用來裝飾屬性[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
具有您想要使用之名稱的屬性。

<a name="Type_mappings" />

## <a name="type-mappings"></a>型別對應

本節涵蓋目標 C 類型如何對應到C#類型。

<a name="Simple_Types" />

### <a name="simple-types"></a>簡單型別

下表顯示如何將類型從目標-C 和 CocoaTouch 世界對應到 Xamarin. iOS world:

|目標-C 類型名稱|Unified API 類型的 Xamarin iOS|
|---|---|
|`BOOL`、 `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString`([關於系結 NSString 的詳細資訊](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string`(另請參閱[`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring):)|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`、 `GLfloat`|`nfloat`|
|CoreFoundation 類型 (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|基礎類型 (`NS*`)|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>陣列

Xamarin iOS 執行時間會自動將陣列C#轉換為`NSArrays`並執行轉換, 因此例如, 會傳回`NSArray`之的`UIViews`虛構目標-C 方法:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

系結如下:

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

其概念是使用強型別C#陣列, 因為這可讓 IDE 以實際的型別提供適當的程式碼完成, 而不會強制使用者猜到, 或查閱檔來找出陣列中所含物件的實際型別。

如果您無法追蹤包含在陣列中的實際衍生類型, 您可以使用`NSObject []`做為傳回值。

<a name="Selectors" />

### <a name="selectors"></a>選取器

選取器會顯示在目標-C API 上做為`SEL`特殊類型。 系結選取器時, 您會將型別`ObjCRuntime.Selector`對應至。  通常會在具有物件、目標物件和要在目標物件中叫用之選取器的 API 中公開選取器。 提供這兩個基本上會對應至C#委派: 一個專案會封裝要叫用的方法, 以及要在其中叫用方法的物件。

這就是系結的樣子:

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

這就是方法通常會在應用程式中使用的方式:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

若要將系結更好C#到開發人員, 您通常會提供方法來接受`NSAction`參數, `Target+Selector`以允許C#使用委派和 lambda, 而不是。 若要這麼做, 您通常`SetTarget`會將方法標記為[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
屬性, 然後您會公開新的 helper 方法, 如下所示:

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

現在, 您的使用者程式碼可以撰寫如下:

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

<a name="Strings" />

### <a name="strings"></a>字串

當您要系結採用`NSString`的方法時, 可以在傳回型別和參數上, 將它取代為C#字串型別。

當您想要`NSString`直接使用時, 唯一的情況就是使用字串做為標記。 如需有關字串和`NSString`的詳細資訊, 請參閱[NSString 上的 API 設計](~/ios/internals/api-design/nsstring.md)檔。

在某些罕見的情況下, API 可能會公開類似 C 的字串`char *`(), 而不是目標-c`NSString *`字串 ()。 在這些情況下, 您可以使用來標注參數[`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
特性.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out/ref 參數

某些 Api 會傳回其參數中的值, 或以傳址方式傳遞參數。

通常, 簽章如下所示:

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

第一個範例顯示一個常見的目標-C 程式碼, 用來傳回錯誤碼、 `NSError`指標指標已通過, 並在傳回時設定值。   第二個方法會顯示目標 C 方法如何採用物件並修改其內容。   這會是以傳址方式傳遞, 而不是單純的輸出值。

您的系結看起來會像這樣:

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>記憶體管理屬性

當您使用[`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性, 而且您要傳遞的資料將由被呼叫的方法保留時, 您可以將引數的語義當做第二個參數傳遞來指定, 例如:

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

上述會將值標示為具有「保留」的語法。 可用的語法如下:

- 指派
- 複製
- 保留

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>樣式方針

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>使用 [內部]

您可以使用[`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
用來隱藏公用 API 中方法的屬性。 當公開的 API 太低層級, 而且您想要根據這個方法在個別檔案中提供高階的執行時, 您可能會想要這麼做。

當您在系結產生器中遇到限制時, 也可以使用這個方法, 例如, 某些先進的案例可能會公開未系結的類型, 而您想要以自己的方式系結, 而且您想要以自己的方式包裝這些類型。

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>事件處理常式和回呼

目標 C 類別通常會藉由在委派類別 (目標-C 委派) 上傳送訊息, 來廣播通知或要求資訊。

這種模型, 雖然 Xamarin 完全支援並呈現, 但有時可能會很麻煩。 在這些情況下, C# Xamarin 會在類別上公開事件模式和方法回呼系統。 這可讓像這樣的程式碼執行:

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

系結產生器能夠減少將目標 C 模式對應到C#模式所需的輸入量。

從 Xamarin. iOS 1.4 開始, 您也可以指示產生器針對特定的目標 C 委派產生系結, 並將委派公開為C#主機類型上的事件和屬性。

此套裝程式含兩個類別, 也就是目前發出事件的主機類別, 並將其傳送至`Delegate`或`WeakDelegate`和實際的委派類別。

考慮下列設定:

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

若要包裝類別, 您必須:

- 在您的主機類別中, 將新增至您的[`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   宣告作為其委派的類型, 以及您所公開C#的名稱。 在上述範例中, 它們`typeof (MyClassDelegate)`分別`WeakDelegate`是和。
- 在您的委派類別中, 每個具有兩個以上參數的方法上, 您必須指定要用於自動產生的 EventArgs 類別的型別。

系結產生器不限於僅包裝單一事件目的地, 某些目標 C 類別可能會發出訊息給多個委派, 因此您必須提供陣列以支援此設定。 大部分的服務都不需要它, 但產生器已準備好支援這些案例。

產生的程式碼將會是:

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

是用來指定要產生之`EventArgs`類別的名稱。 `EventArgs` 您應該使用每個簽章一個 (在此範例`EventArgs`中, 將`With`包含 nint 類型的屬性)。

在上述定義中, 產生器會在產生的 MyClass 中產生下列事件:

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

因此, 您現在可以使用如下的程式碼:

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

回呼就像事件叫用一樣, 其差異在於, 它不會有多個潛在的訂閱者 (例如, 多個`Clicked`方法可以攔截`DownloadFinished`事件或事件) 回呼只能有一個訂閱者。

此程式完全相同, 唯一的差別是不會公開將產生之`EventArgs`類別的名稱, 而是實際使用 EventArgs 來命名產生C#的委派名稱。

如果委派類別中的方法傳回值, 系結產生器會將此對應至父類別中的委派方法, 而不是事件。 在這些情況下, 如果使用者未連結至委派, 則需要提供方法應該傳回的預設值。 您可以使用[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
或[`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)屬性。

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)會硬式編碼傳回值, 而[`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
用來指定將傳回的輸入引數。

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>列舉和基底類型

您也可以參考 btouch 介面定義系統不直接支援的列舉或基底類型。 若要這麼做, 請將您的列舉和核心類型放在另一個檔案中, 並將它包含在您提供給 btouch 的其中一個額外檔案中。

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>連結相依性

如果您要系結的 Api 不是應用程式的一部分, 您必須確定您的可執行檔已連結至這些程式庫。

您必須通知 Xamarin, 如何連結您的程式庫, 這可以藉由變更組建設定來`mtouch`叫用命令, 並使用一些額外的組建引數, 指定如何使用 "-gcc_flags" 選項來連結新的程式庫。後面接著一個加上引號的字串, 其中包含程式所需的所有額外的程式庫, 如下所示:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

上述範例會將`libMyLibrary.a` `libSystemLibrary.dylib`和`CFNetwork`架構程式庫連結至您的最終可執行檔。

或者, 您可以利用元件層級[`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), 您可以將它內嵌在您的合約檔案中 ( `AssemblyInfo.cs`例如)。
當您使用[`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)時, 您必須在進行系結時使用原生程式庫, 因為這會將原生程式庫內嵌至您的應用程式。 例如：

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

您可能想知道, 為什麼需要`-force_load`命令, 而原因是-ObjC 旗標雖然它會在中編譯器代碼, 但它不會保留支援分類所需的中繼資料 (連結器/編譯器無效程式碼去除它), 您可以需要在執行時間進行 Xamarin. iOS。

<a name="Assisted_References" />

## <a name="assisted-references"></a>輔助參考

某些暫時性物件 (例如動作表和警示方塊) 很難追蹤開發人員, 而且系結產生器在此有説明。

例如, 如果您有一個顯示訊息的類別, 然後產生一個`Done`事件, 則處理這種情況的傳統方式會是:

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

在上述案例中, 開發人員必須自行保留物件的參考, 並在自己的情況下洩漏或主動清除 box 的參考。  當系結程式碼時, 產生器支援追蹤您的參考, 並在叫用特殊方法時清除它, 上述程式碼就會變成:

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

請注意, 不再需要將變數保留在實例中, 它可以搭配區域變數使用, 而且當物件停止時, 不需要清除參考。

若要利用此功能, 您的[`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)類別應該在宣告中設定 Events 屬性, `KeepUntilRef`同時將變數設為在物件完成其工作時所叫用的方法名稱, 如下所示:

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>繼承通訊協定

從 Xamarin. iOS 3.2 版開始, 我們支援從已使用[`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)屬性標示的通訊協定繼承。 這在某些 API 模式中很有用, 例如, `MapKit`在中`MKOverlay` , `MKAnnotation`通訊協定會繼承自通訊協定, 而則是由繼承自`NSObject`的許多類別所採用。

在過去, 我們需要將通訊協定複製到每個執行, 但在這些情況下`MKShape` , 我們可以讓`MKOverlay`類別繼承自通訊協定, 而且它會自動產生所有必要的方法。

## <a name="related-links"></a>相關連結

- [系結範例](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
