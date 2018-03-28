---
title: 繫結 Objective C 程式庫
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
ms.technology: xamarin-cross-platform
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: 3b1894555c27e368fc80cc4ebaa1f6b40446e2aa
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="binding-objective-c-libraries"></a>繫結 Objective C 程式庫

當使用 Xamarin.iOS 或 Xamarin.Mac，您可能會遇到情況下，您要使用協力廠商 Objective C 程式庫。 在這些情況下，您可以使用 Xamarin 繫結專案建立的 C# 繫結至原生 Objective C 程式庫。 專案會使用相同的工具，我們使用 C#，使 iOS 和 Mac 應用程式開發介面。

本文件說明如何將繫結 Objective C 的 Api，如果您要繫結只 C 應用程式開發介面，您應該使用標準.NET 機制， [P/Invoke framework](http://www.mono-project.com/docs/advanced/pinvoke/)。
如何以靜態方式連結 C 程式庫的詳細資料位於[連結的原生程式庫](~/ios/platform/native-interop.md)頁面。

請參閱我們的隨附[繫結的型別參考指南](~/cross-platform/macios/binding/binding-types-reference.md)。
此外，如果您想要深入了解幕後發生的事，請檢查我們[繫結概觀](~/cross-platform/macios/binding/overview.md)頁面。

繫結可以建置適用於 iOS 和 Mac 文件庫。
此頁面會描述如何在 iOS 繫結，然而 Mac 繫結就非常類似。

**適用於 iOS 的程式碼範例**

您可以使用[iOS 繫結範例](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample)試驗繫結的專案。

<a name="Getting_Started" />

## <a name="getting-started"></a>使用者入門

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

建立繫結的最簡單方式是建立 Xamarin.iOS 繫結專案。
您可以從 Visual Studio for Mac 所選取的專案類型， **iOS > 文件庫 > 繫結的程式庫**:

[![](objective-c-libraries-images/00-sml.png "執行這項操作從 Visual Studio for Mac 所選取的專案類型時，iOS 程式庫的繫結的程式庫")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

建立繫結的最簡單方式是建立 Xamarin.iOS 繫結專案。
您可以從 Visual Studio，在 Windows 上所選取的專案類型， **Visual C# > iOS > 繫結的程式庫 (iOS)**:

[![](objective-c-libraries-images/00vs-sml.png "iOS 繫結的程式庫的 iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> 注意： 繫結專案**Xamarin.Mac**只能在 Visual Studio for mac。

-----

產生的專案包含您可以編輯的小型範本，因為它含有兩個檔案：`ApiDefinition.cs`和`StructsAndEnums.cs`。

`ApiDefinition.cs`可讓您將定義 API 合約，這是描述基礎 OBJECTIVE-C API 投影至 C# 的方式的檔案。 語法與此檔案的內容討論這份文件的主要主題，而它的內容限制為 C# 介面和 C# 委派宣告。 `StructsAndEnums.cs`檔案是您將在其中輸入的介面和委派所需的任何定義的檔案。 這包括列舉值，以及可能會使用您的程式碼的結構。

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>繫結應用程式開發介面

若要執行完整的繫結，您會想要了解 OBJECTIVE-C API 定義，並熟悉的.NET Framework 設計方針。

繫結您的程式庫通常一開始利用 API 定義檔案。 API 定義的檔案是只是 C# 原始程式檔，其中包含 C# 介面已標註少數幾個說明磁碟機的屬性繫結。  這個檔案是所定義的 C# 和 Objective C 之間的合約是什麼。

比方說，這是簡單式的應用程式開發介面檔案之程式庫：

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

上述範例會定義一種類別稱為`Cocos2D.Camera`衍生自`NSObject`基底類型 (此型別來自`Foundation.NSObject`) 和其定義的靜態屬性 (`ZEye`)，採用任何引數和方法的兩個方法會採用三個引數。

API 檔案和屬性，您可以使用格式的深入討論在講述[API 定義檔](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)下一節。

若要產生完整的繫結，您通常會處理與四個元件：

-  API 定義的檔案 (`ApiDefinition.cs`範本中)。
-  選擇性： 任何列舉類型、 應用程式開發介面定義檔所需的結構 (`StructsAndEnums.cs`範本中)。
-  選擇性： 額外來源可能會展開產生的繫結，或提供多個 C# 易記 API （任何 C# 檔案加入至專案）。
-  您要繫結原生程式庫。

這個圖表可顯示檔案之間的關聯性：

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "這個圖表可顯示檔案之間的關聯性")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

API 定義檔案只會包含命名空間和介面的定義 （使用任何介面可以包含成員），且不能包含類別、 列舉型別、 委派或結構。 API 定義的檔案是只將用來產生應用程式開發介面的合約。

任何額外的程式碼，您需要喜歡列舉型別，或支援的類別應該裝載在個別的檔案，在上面 「 CameraMode 」 範例是 CS 檔案中不存在，而且應該在個別的檔案，例如裝載的列舉值`StructsAndEnums.cs`:

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

`APIDefinition.cs`檔案就會結合`StructsAndEnum`類別，並會用來產生的程式庫的核心繫結。 您可以使用產生的程式庫做為，但一般而言，您會想要微調結果的程式庫，加入者您的使用者，而某些 C# 功能。 一些範例包括實作`ToString()`方法，提供 C# 索引子、 新增隱含轉換，某些原生類型的或提供的一些方法的強型別版本。 這些增強功能會儲存額外的 C# 檔案中。 只將 C# 檔案新增至您的專案，將會包含這個建置流程中。

這會顯示如何您也會實作中的程式碼程式`Extra.cs`檔案。 請注意，您將會使用部分類別為這些加強的部分類別的組合所產生的`ApiDefinition.cs`和`StructsAndEnums.cs`核心繫結：

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

建置程式庫，將會產生原生繫結。

若要完成此繫結，您應該將原生程式庫加入至專案。  您可以透過原生程式庫加入您的專案，或是拖曳到原生程式庫從尋找工具拖曳至方案總管 中的專案，用滑鼠右鍵按一下專案，然後選擇**新增** > **加入檔案**選取原生程式庫。
原生程式庫慣例開頭"lib"這個字開頭和結尾".a 」 的延伸模組。 當您這樣做時，Visual Studio for Mac 將會新增兩個檔案：.a 檔案並自動填入 C# 檔，其中包含原生程式庫包含哪些內容的相關資訊：

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "依照慣例的原生程式庫與 word lib 開頭和結尾延伸.a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

內容`libMagicChord.linkwith.cs`檔可以如何使用此程式庫相關資訊，並且會指示您的 IDE 這個二進位檔封裝至產生的 DLL 檔案：

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

如何使用詳細資料的完整[ `[LinkWith]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)屬性都記錄於[繫結的型別參考指南](~/cross-platform/macios/binding/binding-types-reference.md)。

現在當您建置專案時您會得到`MagicChords.dll`檔案，其中包含繫結和原生程式庫。 您可以將發佈此專案，或產生的 DLL，以便他們自己的其他開發人員使用。

有時候您可能會發現您需要幾個列舉值、 委派定義或其他類型。 請勿將放置在應用程式開發介面的定義檔，因為這是只是合約

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>API 定義的檔案

API 定義的檔案數目的介面所組成。 API 定義的介面會變成類別宣告，必須以裝飾[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)屬性來指定類別的基底類別。

您可能會想知道為什麼我們未使用的類別而不是介面合約定義。 我們挑選介面，因為它允許我們就不必提供 API 定義檔中的方法主體，或需要提供主體必須擲回例外狀況或傳回有意義的值寫入方法的合約。

但是，因為我們要做為基本架構使用介面，以產生的類別，我們必須求助於裝飾的磁碟機繫結屬性與合約的各個部分。

<a name="Binding_Methods" />

### <a name="binding-methods"></a>繫結方法

您可以最簡單繫結會使用繫結方法。 只宣告中的 C# 命名慣例的介面的方法和裝飾的方法與[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性。 [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性是連結 C# nombre Objective C 中的名稱 Xamarin.iOS 執行階段。 參數[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性是 Objective C 選取器的名稱。 一些範例如下：

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

上述範例會示範如何繫結執行個體方法。 若要將繫結的靜態方法，您必須使用[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)屬性，就像這樣：

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

這是必要的因為合約是介面的一部分，而且介面並不了解靜態與執行個體宣告，因此需要再次求助於屬性。 如果您想要隱藏特定的方法從繫結，您可以裝飾的方法與[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性。

`btouch-native`命令時，會加入為參考參數不可為 null 的檢查。 如果您想要讓特定參數的 null 值，使用[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)屬性參數，就像這樣：

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

參考類型，與在匯出時[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)關鍵字，您也可以指定配置語意。 這是為了確保任何資料外洩。

<a name="Binding_Properties" />

### <a name="binding-properties"></a>繫結屬性

如同方法 OBJECTIVE-C 屬性要繫結使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性和 C# 屬性直接對應。 可以使用裝飾的方法，就像屬性[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)和[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性。

當您使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)底下涵蓋 btouch 原生屬性上的屬性會繫結兩個方法： getter 和 setter。 您提供匯出的名稱**basename**和 setter 的計算方式是 「 組 」，開啟的第一個字母的字前面加上**basename**成大寫並進行需要的選取器引數。 這表示`[Export ("label")]`上套用屬性實際上會繫結的"label"和"setLabel:"Objective C 的方法。

有時候 OBJECTIVE-C 屬性未遵循前述的模式，而且名稱會以手動方式覆寫。 在這些情況下，您可以控制的方式使用所產生的繫結[ `[Bind]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute)屬性 getter 或 setter，例如：

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

此內容會接著繫結 」 isMenuVisible"和"setMenuVisible:"。 （選擇性） 屬性可以繫結使用下列語法：

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

其中的 getter 和 setter 中所明確定義為`name`和`setName`上述的繫結。

除了使用的靜態屬性的支援[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)，您可以使用執行緒靜態屬性來裝飾[ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute)，例如：

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

如同方法，可讓某些參數標示與[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)，您可以套用[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)至屬性以表示該 null 是有效的值屬性，例如：

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)參數也可以直接在 setter 上指定：

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>自訂控制項繫結的注意事項

設定的自訂控制項繫結時，應注意下列事項：

1. **繫結屬性必須是靜態**-定義的屬性，繫結時[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)屬性必須一起使用。
 2. **屬性名稱必須完全符合**-用來繫結屬性的名稱必須完全符合的自訂控制項的屬性名稱。
3. **屬性類型必須完全符合**-用來將屬性繫結的變數類型必須完全符合的自訂控制項的屬性類型。
4. **中斷點和 getter/setter** -中斷點放在 getter 或 setter 方法的屬性會永遠不會被叫用。
5. **觀察回呼**-您必須使用觀察回呼的自訂控制項的屬性值中的變更通知。

要觀察任何上述所列的警告可能導致在執行階段以無訊息模式失敗的繫結。

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Objective C 的可變動模式和屬性

Objective C 架構使用的慣用語，其中某些類別是不變，與可變動的子類別。 例如`NSString`是不可變的版本，而`NSMutableString`是可變動的子類別。

這些類別中很常見，若要查看不可變的基底類別包含具有 getter，但沒有 setter 的屬性。 與引入 setter 可變動的版本。 由於這不是真的可能使用 C#，我們必須將這個慣用語對應至 C# 會使用慣用句。

這會對應至 C# 的方式是加入 getter 和 setter 基底類別，但加上旗標與 setter [ `[NotImplemented]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)屬性。

然後，在可變動的子類別，您使用[ `[Override]` ](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute)屬性，以確保屬性實際上覆寫父代的行為上的屬性。

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

### <a name="binding-constructors"></a>繫結的建構函式

`btouch-native`工具會自動產生慵建構函式，在類別中，指定類別`Foo`，它會產生：

-  `Foo ()`： 預設建構函式 （對應至 Objective C 的 「 初始化 「 建構函式）
-  `Foo (NSCoder)`: NIB 檔案的還原序列化期間使用的建構函式 (對應至 Objective C 的"initWithCoder: 「 建構函式)。
-  `Foo (IntPtr handle)`： 建構函式的控制代碼為基礎建立，這時叫用由執行階段執行階段需要公開受管理的物件，從 unmanaged 物件。
-  `Foo (NSEmptyFlag)`： 這用來防止 double 初始化由衍生類別。

建構函式定義，它們必須使用介面定義內的下列簽章宣告： 它們必須傳回`IntPtr`值和方法的名稱應該是建構函式。 例如若要繫結`initWithFrame:`建構函式，這是您可以使用：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>繫結通訊協定

一節中的應用程式開發介面設計文件中所述[討論模型和通訊協定](~/ios/internals/api-design/index.md#Models)，Xamarin.iOS 將 OBJECTIVE-C 通訊協定對應至已標幟的類別[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)屬性。 這通常用於實作 OBJECTIVE-C 委派類別。

規則繫結的類別和委派類別的大差別在於委派類別，可能會有一或多個選用的方法。

如需範例，請考慮`UIKit`類別`UIAccelerometerDelegate`，這是如何結合 Xamarin.iOS 中：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

由於這是選擇性的方法上的定義`UIAccelerometerDelegate`沒有執行其他的項目。 但是如果通訊協定上沒有所需的方法，您應該增加[ `[Abstract]` ](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)屬性加入方法。 這會強制使用者實作的實際方法中提供的主體。

一般情況下，回應訊息的類別會使用通訊協定。 這通常是 Objective C 中指派給 「 委派 」 屬性中的通訊協定的方法會回應物件的執行個體。

在 Xamarin.iOS 慣例是任何支援這兩個 OBJECTIVE-C 鬆散偶合樣式的執行個體`NSObject`可以指派給委派，而也公開它的強型別版本。 基於這個理由，我們通常兩者都提供`Delegate`強類型的屬性和`WeakDelegate`鬆散輸入。 我們通常繫結具有的鬆散型別版本[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)，我們使用[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)屬性提供強型別版本。

這會顯示我們的繫結`UIAccelerometer`類別：

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

已加入從 MonoTouch 7.0 開始新的和改進的通訊協定，繫結功能。  這個新的支援可讓您更容易使用 OBJECTIVE-C 慣用語採用指定的類別中的一個或多個通訊協定。

每個通訊協定定義`MyProtocol`Objective C，在沒有現在`IMyProtocol`介面，其中列出所有需要的方法，從通訊協定，以及提供選擇性的所有方法的擴充功能類別。  上述項目，結合在 Xamarin Studio 編輯器可讓開發人員實作通訊協定方法，而不需要使用個別的子類別，先前的抽象模型類別的新支援。

包含任何定義[ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute)屬性實際上會產生三個支援的類別來大幅改善您使用的通訊協定的方式：

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

**類別實作**提供完整的抽象類別，您可以用來覆寫個別的方法，並取得完整的型別安全。  但由於 C# 中不支援多重繼承，所以有很的案例，您可能需要有不同的基底類別，但您仍然想要實作的介面，where。

產生**介面定義**傳入。  它是具有所有必要的方法，從通訊協定的介面。  這可讓開發人員用來實作您的通訊協定只是實作介面。  執行階段會自動登錄型別以採用之通訊協定。

請注意，此介面只列出需要的方法，且沒有公開選擇性的方法。  這表示，採用之通訊協定的類別會收到需要的方法，檢查完整的型別，但就必須求助於弱式類型 (手動使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性和比對簽章) 為選擇性通訊協定方法。

為了更方便使用的通訊協定會使用的 API，繫結工具也會產生延伸模組方法類別公開的所有選用的方法。  這表示，只要您使用應用程式開發介面，您將能夠處理通訊協定為具有所有方法。

如果您想要在您的 API 中使用的通訊協定定義，您必須在您的應用程式開發介面定義中撰寫基本架構空的介面。  如果您想要使用 MyProtocol API 中，您需要執行這項操作：

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

上述需要處理，因為在繫結時`IMyProtocol`就不存在，也就是為什麼您需要提供空的介面。

#### <a name="adopting-protocol-generated-interfaces"></a>採用通訊協定產生的介面

每當您實作其中一個介面產生的通訊協定，像這樣：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

介面方法的實作自動取得匯出具有適當的名稱，讓它相當於這個：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

並不重要隱含或明確地實作介面。

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>繫結類別的擴充功能

Objective C 中很可能延伸類別的新方法，在夠用類似於 C# 擴充方法。 當存在其中一種方法時，您可以使用[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)加上旗標為 OBJECTIVE-C 訊息的接收者方法的屬性。

例如，在 Xamarin.iOS 我們繫結所定義的擴充方法`NSString`時`UIKit`匯入做為方法中`NSStringDrawingExtensions`，如下所示：

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>繫結 Objective C 的引數清單

Objective C 支援 variadic 引數。 例如: 

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

叫用此方法，從 C#，您會想要建立的簽章，像這樣：

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

這會宣告方法，為內部，隱藏使用者，從上述的 API，但是將它公開至文件庫。 然後您可以撰寫方法如下：

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

### <a name="binding-fields"></a>繫結欄位

有時候您會想要存取文件庫中所宣告的公用欄位。

通常這些欄位會包含字串或整數值的參考。 它們通常用於為字串，代表特定通知，做為字典中索引鍵。

繫結欄位，將屬性加入至介面定義檔案，和具有的屬性來裝飾[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)屬性。 這個屬性接受一個參數： 要查閱之符號的 C 名稱。 例如: 

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

如果您想要包裝不是衍生自靜態類別中的各種欄位`NSObject`，您可以使用[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class)屬性上的類別，像這樣：

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

將會產生上述`LonelyClass`這不是衍生自`NSObject`並在其中包含的繫結`NSSomeEventNotification` 
 `NSString`公開為`NSString`。

[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)屬性可以套用至下列資料類型：

-  `NSString` 參考 （只有唯讀屬性）
-  `NSArray` 參考 （只有唯讀屬性）
-  32 位元 int (`System.Int32`)
-  64 位元 int (`System.Int64`)
-  32 位元浮點數 (`System.Single`)
-  64 位元浮點數 (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

除了原生的欄位名稱 中，您可以指定欄位位置，藉由傳遞至程式庫名稱的程式庫名稱：

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

如果您要以靜態方式連結，沒有程式庫來繫結，因此您必須使用`__Internal`名稱：

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>繫結列舉

您可以加入`enum`直接在您的繫結檔案，以輕鬆地使用這些 API 定義-內使用之不同原始程式檔 （需要重新編譯的繫結和最後一個專案中）。

範例：

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

它也可建立取代您自己列舉`NSString`常數。 在此情況下產生器將**自動**建立方法，以將列舉值和 NSString 常數轉換為您。

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

在上述範例中，您可以決定裝飾`void Perform (NSString mode);`與[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性。 這將會**隱藏**常數為基礎的 API 與您的繫結取用者。

不過這會限制子類別的類型化沒用 API 替代使用[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)屬性。 這些產生的方法不是`virtual`，也就是您不可以覆寫它們的或不可能的是很不錯的選擇。

替代方式是將原始`NSString`-基礎，定義為`[Protected]`。 這可讓子類別化，才能執行，在必要時，wrap'ed 版本仍將工作和呼叫覆寫的方法。

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>繫結`NSValue`， `NSNumber`，和`NSString`更好的型別

[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)屬性允許繫結`NSNumber`，`NSValue`和`NSString`（列舉） 到更精確的 C# 類型。 屬性可以用來建立更好、 更正確透過原生 API 的.NET API。

您可以將方法 （在傳回值）、 參數和屬性與裝飾[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)。 唯一的限制是，您的成員**必須不**內[ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute)或[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)介面。

例如: 

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

會輸出：

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

我們會在內部執行`bool?`  <->  `NSNumber`和`CGRect`  <->  `NSValue`轉換。

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) 也支援陣列`NSNumber``NSValue`和`NSString`（列舉）。

例如: 

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

會輸出：

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` 是`NSString`備份列舉，我們將會提取右邊`NSString`值，並處理型別轉換。

請參閱[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)文件，請參閱支援的轉換類型。

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>繫結通知

通知是訊息張貼至`NSNotificationCenter.DefaultCenter`，可作為一項機制來廣播訊息到另一個應用程式的一個部分。 開發人員通常會使用通知訂閱[NSNotificationCenter](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/)的[AddObserver](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/M/AddObserver/)方法。 當應用程式會將訊息張貼至通知中心時，它通常包含儲存在裝載[NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/)字典。 這個字典弱型別，並取得資訊不使用為不易有錯誤，使用者通常需要讀取文件的索引鍵是字典和可以儲存在字典中的值類型上使用中。 存在的索引鍵有時會做為布林值。

Xamarin.iOS 繫結產生器提供繫結通知的開發人員的支援。 若要這樣做，您將[ `[Notification]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)也已在屬性的屬性加上[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)屬性 （可以是公用或私用）。

這個屬性可以使用不含引數不包含任何承載的通知，或者您可以指定`System.Type`參考另一個介面，在應用程式開發介面定義中，通常名稱結尾"EventArgs"。 產生器會變成介面類別的子類別`EventArgs`，而且將包含所有列出的屬性。 [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性應該 EventArgs 類別中用來列出用來查閱 OBJECTIVE-C 字典擷取值的索引鍵的名稱。

例如: 

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

上述程式碼會產生巢狀的類別`MyClass.Notifications`使用下列方法：

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

您的程式碼的使用者便可輕鬆地訂閱通知張貼至[NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/)使用如下的程式碼：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

傳回的值`ObserveDidStart`可用來輕鬆地停止接收通知，就像這樣：

```csharp
token.Dispose ();
```

您可以呼叫[NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject/)和將語彙基元。 如果您的通知中包含參數，您應該指定 helper`EventArgs`介面，像這樣：

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

將會產生上述`MyScreenChangedEventArgs`類別`ScreenX`和`ScreenY`屬性，將會提取的資料從[NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) "ScreenXKey"和"ScreenYKey"的索引鍵的字典分別並套用適當的轉換。 `[ProbePresence]`探查如果索引鍵在設定使用屬性產生器`UserInfo`，而不是嘗試擷取值。 這用的情況，索引鍵的目前狀態的值 （通常為布林值）。

這可讓您撰寫程式碼如下：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>繫結類別

類別是用來擴充的方法和類別中可用的屬性集 OBJECTIVE-C 機制。   在實務上，它們可用來擴充功能的基底類別 (例如`NSObject`) 的特定架構中的連結時 (例如`UIKit`)，可用，但前提是已連結的新架構，以使它們的方法。   在某些其他情況下，它們會依功能用來組織類別中的功能。   它們是在夠用類似於 C# 擴充方法。這是類別在目標 c： 會尋找

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上述範例中如果上找到文件庫會擴充的執行個體`UIView`方法`makeBackgroundRed`。

若要將那些繫結，您可以使用[ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)介面定義上的屬性。  當使用[ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)屬性的意義[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)從用來指定擴充，而是要延伸的類型的基底類別的屬性變更。

下列示範如何`UIView`繫結和轉換成 C# 擴充方法擴充功能：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

將會建立上述`MyUIViewExtension`類別，其中包含`MakeBackgroundRed`擴充方法。  這表示您現在可以呼叫 「 MakeBackgroundRed"任何`UIView`子類別，讓您得到目標 c 相同的功能 在某些其他情況下，不是用來擴充系統類別，但組織只提供裝飾用途的功能，使用類別。  與下列類似：

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

雖然您可以使用[ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)屬性也為此裝飾樣式的宣告中，您可能也只將它們全部加入類別定義。  這兩種會達到相同結果：

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

在這些情況下，若要合併的類別只還短時：

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

### <a name="binding-blocks"></a>繫結區塊

區塊會將功能相當於 C# 匿名方法帶到目標 C.Apple 所導入新的建構 例如，`NSSet`類別現在會公開這個方法：

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

上述的描述會宣告方法，稱為`enumerateObjectsUsingBlock:`採用一個引數，名為`block`。 這個區塊是類似於 C# 匿名方法，在於，它可支援擷取目前的環境 （"this"指標，存取區域變數和參數）。 在上述的方法`NSSet`叫用具有兩個參數區塊`NSObject`(`id obj`組件) 和布林值的指標 ( `BOOL *stop`) 部分。

要繫結與 btouch 這類的應用程式開發介面時，您必須先宣告的區塊類型簽章，以及 C# 委派然後參考該應用程式開發介面進入點，就像這樣：

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

而且現在您的程式碼時，可以從 C# 呼叫您的函式：

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

如果您想要的話，您也可以使用 lambda:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>非同步方法

繫結產生器可以變成方便的非同步方法的特定類別的方法 (方法的傳回 Task&lt;T&gt;)。

您可以使用[ `[Async]` ](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) ，傳回 void，而且其最後一個引數為回呼的方法上的屬性。  當您將此套用至方法時，繫結產生器會產生後置詞，該方法的版本`Async`。  如果回呼會不採用任何參數，傳回值將是`Task`，如果回呼採用參數，結果會是`Task<T>`。  如果回呼採用多個參數，您應該設定`ResultType`或`ResultTypeName`來指定所要產生的型別會保留所有屬性的名稱。

範例：

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

上述程式碼會產生兩個 LoadFile 方法，以及：

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>面對強式類型的弱式 NSDictionary 參數

在 OBJECTIVE-C API 中的許多地方，參數會傳遞為弱型別`NSDictionary`具有特定索引鍵和值，但這些 Api 的錯誤 （您可以傳遞無效的索引鍵，並不取得任何警告; 您可以傳遞無效的值，並取得沒有警告），很容易出錯而令人沮喪若要使用，因為它們需要查閱可能索引鍵的名稱和值的文件的多個往返。

解決方案是提供強類型版本所提供的 api，並在幕後的強型別版本對應索引鍵和各種基礎值。

因此，例如，如果 OBJECTIVE-C API 接受`NSDictionary`記載為索引鍵和`XyzVolumeKey`這個方法會接受`NSNumber`與磁碟區介於 0.0 到 1.0 和`XyzCaptionKey`，接受字串，您會想要使用者會有很棒的應用程式開發介面這看起來像這樣：

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

`Volume`屬性定義為可為 null 的浮點數，如 Objective C 中的慣例不需要有值，因此可能不會設定值的情況下這些字典。

若要這樣做，您必須進行幾件事：

* 建立強型別類別，該子類別[DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/)並提供每個屬性的各種 getter 和 setter。
* 宣告方法多載`NSDictionary`採取新版強型別。

您可以手動，或是建立強型別類別，或使用產生器，為您執行工作。  我們第一次瀏覽如何執行這項操作以手動方式讓您了解如何運作，然後按一下 自動的方法。

您需要建立此項支援的檔案，不進入您應用程式開發介面的合約。  這是您必須撰寫建立 XyzOptions 類別：

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

然後應該提供包裝函式的方法，可呈現高階的 API，在低階 API 之上。

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

如果您的 API 不需要覆寫中，您可以安全地隱藏 NSDictionary 為基礎的 API 使用[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性。

您可以看到，我們使用[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)屬性來呈現新的應用程式開發介面進入點，而我們介面它使用我們強型別`XyzOptions`類別。  包裝函式方法也允許傳遞 null。

現在，我們不未提及的一件事是 where`XyzOptionsKeys`值的來源。  您通常會組成群組的應用程式開發介面會提供諸如之類的靜態類別中的索引鍵`XyzOptionsKeys`，如下所示：

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

讓我們看看自動建立這些強型別字典支援。  這可避免未定案、 大量，您可以直接在您的 API 合約，而不是使用外部檔案中定義的字典。

若要建立強型別字典，造成您的 API 中的介面，而且裝飾它與[StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)屬性。  這會告訴程式產生器，它應該具有相同名稱做為您衍生的介面建立類別`DictionaryContainer`並會為它提供強式型別的存取子。

[ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)屬性只採用一個參數，也就是靜態類別，其中包含您的字典索引鍵的名稱。  然後每個屬性的介面就會成為強型別存取子。  根據預設，程式碼會使用屬性的名稱後置詞 「 金鑰 」，在靜態類別中建立存取子。

這表示外部檔案，也不需要手動建立的 getter 和 setter 的每個屬性，也不必以手動方式查閱索引鍵，會建立您的強型別存取子不會再要求您自己。

這是您的整個繫結的樣子：

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

萬一您需要在參考您`XyzOption`成員不同的欄位 (也就是不是名稱的後置詞屬性`Key`)，您可以宣告這個屬性與[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性名稱，您想要使用。

<a name="Type_mappings" />

## <a name="type-mappings"></a>型別對應

本章節涵蓋 Objective C 類型如何對應到 C# 類型。

<a name="Simple_Types" />

### <a name="simple-types"></a>簡單類型

下表顯示如何您應將對應從 Objective C 和 CocoaTouch 世界 Xamarin.iOS 世界的型別：

|Objective C 類型名稱|型別-統一的 API Xamarin.iOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([更詳細的繫結 NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (請參閱： [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
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

Xamarin.iOS 執行階段會自動處理的轉換 C# 陣列給`NSArrays`並執行轉換，例如虛數 OBJECTIVE-C 方法傳回`NSArray`的`UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

繫結像這樣：

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

做法是使用強型別陣列 C#，因為這可讓 IDE，才能提供適當的程式碼完成功能，與實際的型別，而不會強制使用者猜測，或查閱文件以了解實際的型別陣列中所包含的物件。

在其中您可以不追蹤陣列中包含實際的最多衍生類型的情況下，您可以使用`NSObject []`當做傳回值。

<a name="Selectors" />

### <a name="selectors"></a>選取器

選取器會出現在做為特殊類型的 OBJECTIVE-C API `SEL`。 繫結時的選取器，您會將對應的型別`ObjCRuntime.Selector`。  通常是選取器會公開在物件、 目標物件，和選取器 API，以叫用目標物件。 基本上提供這兩種對應至 C# 委派： 封裝要叫用方法以及物件來叫用的方法中的項目。

這是繫結的外觀：

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

這是如何通常會使用此方法的應用程式中：

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

若要讓 C# 開發人員沒用繫結，您通常會提供的方法會接受`NSAction`參數，而不是使用 C# 委派和 lambda，可讓`Target+Selector`。 若要這樣做通常會隱藏`SetTarget`方法加上旗標與[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性，然後會公開新的協助程式方法，就像這樣：

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

現在您的使用者程式碼可以撰寫如下：

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

當您要繫結的方法會接受`NSString`，您可以取代，以 C# 字串型別，同時在部署上傳回型別和參數。

當您可能想要使用的唯一情況`NSString`直接時，這個字串當做語彙基元。 如需有關字串和`NSString`，請參閱[NSString API 設計](~/ios/internals/api-design/nsstring.md)文件。

在某些罕見的情況下，應用程式開發介面可能會公開類似 C 字串 (`char *`) 而不是 Objective C 字串 (`NSString *`)。 在這些情況下，您可以標註的參數[ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)屬性。

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out / ref 參數

某些應用程式開發介面的參數，傳回值或傳址方式傳遞參數。

通常簽章看起來像這樣：

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

第一個範例會顯示一般的 OBJECTIVE-C 慣用語，傳回的錯誤碼，指標`NSError`傳遞指標，並傳回時設定值。   第二個方法顯示如何 OBJECTIVE-C 方法可能需要的物件，並修改其內容。   這是傳遞參考，而不是單純的輸出值。

您的繫結會看起來像這樣：

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>記憶體管理屬性

當您使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性，並傳遞所呼叫的方法將保留的資料，您可以藉由傳遞做為第二個參數，例如指定的引數語意：

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

上述會加上旗標的值為具有 「 保留 」 語意。 可用的語意如下：

-  指派
-  複製
-  保留

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>樣式指導方針

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>使用 [內部]

您可以使用[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性來隱藏公用 API 中的方法。 您可以在其中公開的 API 是太低層級，而您想要提供在個別的檔案，這個方法為基礎的高層級實作的情況下執行這項操作。

您也可以使用這時遇到繫結產生器中的限制，例如某些進階的案例可能會公開未繫結的型別和您想要繫結您自己的方式，和您想要自行在自己的方法來包裝這些型別。

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>事件處理常式和回呼

Objective C 類別通常廣播通知，或要求上委派類別 （OBJECTIVE-C 委派） 傳送訊息的資訊。

此模型中，雖然全面支援以及由 Xamarin.iOS 顯示有時候很麻煩。 Xamarin.iOS 公開 C# 事件模式和方法回呼上的系統可以在這些情況下使用的類別。 這可讓程式碼執行如下：

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

繫結產生器能夠減少輸入要對應至 C# 模式的 Objective C 模式所需的數量。

啟動 Xamarin.iOS 1.4 與它有可能也會指示產生器來產生特定的 OBJECTIVE-C 委派繫結和公開為 C# 事件和屬性上的主機類型的委派。

有兩種類別參與此程序，將的裝載類別是指目前會發出事件，並將傳送至這些`Delegate`或`WeakDelegate`和實際委派類別。

請考慮下列安裝程式：

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

若要包裝類別，您必須：

-  在主應用程式類別中，加入您 [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   宣告做為其委派和您所公開的 C# 名稱的類型。 在上述範例中是`typeof (MyClassDelegate)`和`WeakDelegate`分別。
-  在委派類別中，有兩個以上的參數，每個方法上，您需要指定您想要用於自動產生的 EventArgs 類別的類型。

繫結產生器並不限於單一事件目的地文繞圖，進行該委派發出到多個訊息部分 Objective C 類別，因此您必須提供陣列，以便支援此安裝程式。 大部分的安裝不需要它，但產生器已準備好支援這種情況。

將產生的程式碼：

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

`EventArgs`用來指定名稱`EventArgs`要產生的類別。 您應該使用其中一個簽章 (在此範例中，`EventArgs`將包含`With`屬性的型別 nint)。

使用上述的定義，產生器會產生產生 MyClass 中的下列事件：

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

因此您現在可以使用程式碼如下：

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

就像事件叫用的回呼，其差異在於，而不需要多個可能的訂閱者 (例如，多個方法可以連結至`Clicked`事件或`DownloadFinished`事件) 回呼只能有單一訂閱者。

處理程序是完全相同，唯一的差別，而不是公開的名稱`EventArgs`類別將產生的 EventArgs 實際上用來產生 C# 委派名稱命名。

如果委派類別中的方法的傳回值，繫結產生器會將這對應到在父類別，而不是事件的委派方法。 在這些情況下，您需要提供應傳回方法如果使用者沒有不會連結至委派的預設值。 您使用[ `[DefaultValue]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)或[ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)屬性。

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) 傳回值，將硬時[ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)用來指定將傳回哪一個輸入引數。

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>列舉型別和基底類型

您也可以參考列舉型別或不直接支援 btouch 介面定義系統的基底類型。 若要這樣做，請將您列舉型別和核心類型放入個別檔案並包含這其中一個額外的檔案提供給 btouch 的一部分。

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>連結的相依性

如果您要繫結不屬於您的應用程式的 Api，您需要確定您可執行檔會針對這些程式庫連結。

您需要通知 Xamarin.iOS 如何連結您的程式庫，作法是藉由改變您的組建組態，來叫用`mtouch`命令與某些額外建置引數，指定如何使用新的程式庫連結 」-gcc_flags 」 選項，後面加上引號的字串，包含所有額外的程式庫所需的程式，就像這樣：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

上述範例中將會連結`libMyLibrary.a`，`libSystemLibrary.dylib`和`CFNetwork`framework 程式庫，到最後一個可執行檔。

您可以利用組件層級或者[ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)，讓您嵌入合約檔案 (例如`AssemblyInfo.cs`)。
當您使用[ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)，您必須將您繫結，這樣會內嵌原生程式庫與應用程式時您原生程式庫。 例如: 

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

您可能會懷疑，為何需要`-force_load`命令，以及的原因是，-ObjC 旗標，雖然在編譯中的程式碼時，不會保留支援的類別 （連結器/編譯器無作用程式碼刪除去除它） 所需的中繼資料的需要在執行階段 Xamarin.iOS。

<a name="Assisted_References" />

## <a name="assisted-references"></a>輔助的參考

有些暫時性的物件，例如動作的工作表和警示的方塊會追蹤適用於開發人員的麻煩，而且繫結產生器有助於有點這裡。

例如，如果您的類別，顯示一則訊息，然後產生`Done`事件，傳統方式處理這將會：

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

在上述案例中，開發人員必須保留物件參考和任一遺漏，或主動清除方塊上自己的參考。  繫結程式碼產生器支援追蹤參考，並清除它叫用特殊的方法時，上述程式碼就會成為：

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

請注意如何它不再需要請記住變數執行個體，可搭配本機變數，並不需要物件無作用時，請清除該參考。

若要利用這一點，您的類別應該具備中設定事件屬性[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)宣告以及`KeepUntilRef`變數設定為當物件已完成其工作，例如叫用方法的名稱這個：

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>繼承的通訊協定

Xamarin.iOS v3.2 為準，我們支援繼承自已標示的通訊協定[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)屬性。 這適用於特定應用程式開發介面的模式，例如 in`MapKit`其中`MKOverlay`通訊協定，請繼承自`MKAnnotation`通訊協定，以及採用的數個類別繼承自`NSObject`。

在過去我們需要將通訊協定複製到每個實作中，但在這些情況下現在我們可以有`MKShape`類別繼承自`MKOverlay`通訊協定，它將所有需要的方法自動產生。

## <a name="related-links"></a>相關的連結

- [繫結範例](https://developer.xamarin.com/samples/BindingSample/)
