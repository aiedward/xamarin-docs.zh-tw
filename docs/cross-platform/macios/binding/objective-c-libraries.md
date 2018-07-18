---
title: 繫結 Objective C 程式庫
description: 本文件提供如何建立 C# 的繫結 OBJECTIVE-C 程式碼，描述如何將繫結事件、 方法、 自訂控制項，還有更多的高階概觀。
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: 4c414e0e863f44045473a248576a3612b1719559
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854827"
---
# <a name="binding-objective-c-libraries"></a>繫結 Objective C 程式庫

當使用 Xamarin.iOS 或 Xamarin.Mac，您可能會遇到您要使用第三方 Objective C 程式庫的情況。 在這些情況下，您可以使用 Xamarin 繫結專案建立 C# 繫結至原生的 Objective C 程式庫。 專案會使用相同的工具，我們使用 C#，使 iOS 和 Mac Api。

本文件說明如何將繫結 Objective C Api，如果您要繫結只 C Api，您應該這麼做，使用標準的.NET 機制[P/Invoke framework](http://www.mono-project.com/docs/advanced/pinvoke/)。
如何以靜態方式連結的 C 程式庫的詳細資訊位於[連結的原生程式庫](~/ios/platform/native-interop.md)頁面。

請參閱我們的小幫手[繫結的型別參考指南](~/cross-platform/macios/binding/binding-types-reference.md)。
此外，如果您想要深入了解發生的實際運作，請檢查我們[繫結概觀](~/cross-platform/macios/binding/overview.md)頁面。

繫結可以建置適用於 iOS 和 Mac 程式庫。
此頁面說明如何在 iOS 繫結，然而 Mac 繫結非常類似。

**適用於 iOS 的範例程式碼**

您可以使用[iOS 繫結範例](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample)嘗試繫結的專案。

<a name="Getting_Started" />

## <a name="getting-started"></a>使用者入門

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

建立繫結的最簡單方式是建立在 Xamarin.iOS 繫結專案。
您可以從 Visual Studio for Mac 中選取專案類型中**iOS > 文件庫 > 繫結程式庫**:

[![](objective-c-libraries-images/00-sml.png "可以從 Visual Studio for Mac 中，選取 專案類型，iOS 程式庫的繫結程式庫")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

建立繫結的最簡單方式是建立在 Xamarin.iOS 繫結專案。
您可以從 Windows 上的 Visual Studio 藉由選取專案類型中**Visual C# > iOS > 繫結程式庫 (iOS)**:

[![](objective-c-libraries-images/00vs-sml.png "iOS 繫結程式庫的 iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> 注意： 繫結專案**Xamarin.Mac**只支援在 Visual Studio for mac。

-----

產生的專案包含的小的範本，您可以編輯，因為它含有兩個檔案：`ApiDefinition.cs`和`StructsAndEnums.cs`。

`ApiDefinition.cs`可讓您將在其中定義 API 合約，這是描述基礎的 Objective C API 如何投影到 C# 檔案。 語法與此檔案的內容討論的這份文件的主題，而且它的內容僅限於 C# 介面和 C# 委派宣告。 `StructsAndEnums.cs`檔案是讓您輸入所需的任何定義的介面和委派的檔案。 這包括列舉值和您的程式碼可能會使用的結構。

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>繫結 API

若要執行完整的繫結，您會想要了解的 Objective C API 定義，並熟悉的.NET Framework 設計方針。

若要繫結程式庫將通常開始的 API 定義檔。 API 定義檔是只是 C# 原始程式檔，其中包含繫結的 C# 介面已標註少數幾個幫助的屬性。  這個檔案是定義 C# 與 OBJECTIVE-C 之間的合約是什麼。

比方說，這是程式庫的一般 api 檔：

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

上述範例會定義一個叫做類別`Cocos2D.Camera`衍生自`NSObject`基底型別 (此型別來自`Foundation.NSObject`) 和其定義的靜態屬性 (`ZEye`)，採用任何引數和方法的兩種方法會採用三個引數。

API 檔案和屬性，您可以使用格式的深入討論涵蓋[API 定義檔](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file)下一節。

若要產生完整的繫結，您通常將會處理的四個元件：

-  API 定義檔 (`ApiDefinition.cs`範本中)。
-  選擇性： 任何列舉類型、 所需的 API 定義檔的結構 (`StructsAndEnums.cs`範本中)。
-  選擇性︰ 額外來源可能會展開所產生的繫結，或提供更多 C# 易記的 API （任何 C# 檔案加入專案）。
-  您要繫結原生程式庫。

此圖表會顯示檔案之間的關聯性：

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "此圖表顯示檔案之間的關聯性")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

在 API 定義檔只會包含命名空間和介面的定義 （含任何成員的介面可以包含），而且不應該包含類別、 列舉、 委派或結構。 API 定義檔是只會用來產生 API 的合約。

任何額外的程式碼，您需要列舉或像支援的類別應該裝載在不同的檔案，在上述 「 CameraMode 」 範例是列舉值，不存在 CS 檔案中，而且應該裝載在不同的檔案，例如`StructsAndEnums.cs`:

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

`APIDefinition.cs`檔案會結合`StructsAndEnum`類別，並會用來產生的程式庫的核心繫結。 您可以使用做為產生的程式庫-，但一般而言，您會想要調整產生的程式庫來新增為了使用者方便一些 C# 功能。 部分範例包括實作`ToString()`方法，提供 C# 索引子、 新增隱含轉換，與一些原生型別，或提供強型別版本的一些方法。 這些增強功能會儲存在額外的 C# 檔案。 只將 C# 檔案新增至您的專案，並會納入此建置程序。

這會顯示如何實作中的程式碼程式`Extra.cs`檔案。 請注意，您將使用部分類別，這些增強的組合，從產生的部分類別`ApiDefinition.cs`而`StructsAndEnums.cs`核心繫結：

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

若要完成此繫結，您應該在專案中新增原生程式庫。  您可以藉由將您的專案，或將拖放原生程式庫從尋找工具拖曳至方案總管中的專案中的原生程式庫，以滑鼠右鍵按一下專案，然後選擇**新增** > **將檔案新增**選取原生程式庫。
依照慣例的原生程式庫以文字"lib"開頭，並以副檔名".a"結尾。 當您這樣做時，Visual Studio for Mac 將會新增兩個檔案：.a 檔案和自動填入的 C# 檔案，其中包含有關原生程式庫所包含的資訊：

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "依照慣例的原生程式庫 word 程式庫以開頭和結尾延伸.a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

內容`libMagicChord.linkwith.cs`檔可以如何使用此程式庫的相關資訊，並且會指示 IDE，以封裝這個二進位檔產生的 DLL 檔案：

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

完整的使用方式的詳細[ `[LinkWith]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)屬性都記載於[繫結的型別參考指南](~/cross-platform/macios/binding/binding-types-reference.md)。

現在當您建置專案時您會得到`MagicChords.dll`檔案，其中包含繫結和原生程式庫。 您可以將此專案，或使用他們自己的其他開發人員產生的 DLL。

有時候您可能會發現您需要幾個列舉值、 委派定義或其他類型。 請勿將放在 API 定義檔中，因為這是只是合約

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>API 定義檔

API 定義檔所組成的一些介面。 API 定義中的介面則會轉換為類別宣告，並必須以裝飾[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)屬性來指定類別的基底類別。

您可能會好奇為什麼我們未使用的類別而不是介面合約定義。 我們會挑選介面，因為它可讓我們撰寫方法合約，而無需提供 API 定義檔中的方法主體，或需要提供主體必須擲回例外狀況或傳回有意義的值。

但因為我們要做為基本架構使用介面，以產生的類別，我們必須求助於裝飾具有屬性的合約，將磁碟機繫結的各個部分。

<a name="Binding_Methods" />

### <a name="binding-methods"></a>繫結方法

您可以執行簡單繫結是繫結方法。 只是宣告 C# 命名慣例的介面中的方法和裝飾的方法[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性。 [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性是連結 Objective C 中的名稱，Xamarin.iOS 執行階段您 C# 名稱。 參數[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性是 OBJECTIVE-C 選取器的名稱。 一些範例如下：

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

上述範例會示範如何繫結執行個體方法。 若要繫結的靜態方法，您必須使用[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)屬性，像這樣：

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

這是必要的因為合約是介面的一部分，而且介面並不了解靜態與執行個體的宣告，因此就必須再次求助於屬性。 如果您想要隱藏特定的方法，從繫結，您可以裝飾具有方法[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性。

`btouch-native`命令將介紹檢查參考參數不可為 null。 如果您想要允許特定參數的 null 值，使用[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)屬性上的參數，就像這樣：

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

使用匯出參考型別時[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)關鍵字，您也可以指定配置語意。 這是為了確保沒有資料會外洩。

<a name="Binding_Properties" />

### <a name="binding-properties"></a>繫結屬性

就像方法，OBJECTIVE-C 屬性都會使用繫結[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性和直接對應至 C# 屬性。 就像方法，屬性可以使用裝飾[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)並[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性。

當您使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)涵蓋 btouch 原生 之下的屬性上屬性實際上會繫結的兩個方法： getter 和 setter。 您提供匯出的名稱**basename**和 setter 的計算方式前面加上 「 set 」，開啟的第一個字母的單字**basename**成大寫，並讓需要的選取器引數。 這表示`[Export ("label")]`上套用屬性實際上會繫結的 「 標籤 」 和 「 setLabel:"OBJECTIVE-C 方法。

有時候 OBJECTIVE-C 屬性未遵循前述的模式，且名稱是以手動方式覆寫。 在這些情況下，您可以控制的方式使用所產生的繫結[ `[Bind]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute)屬性 getter 或 setter，例如：

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

這會接著繫結 」 isMenuVisible"和"setMenuVisible:"。 （選擇性） 屬性可以繫結使用下列語法：

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

其中的 getter 和 setter 中明確定義為`name`和`setName`上述的繫結。

除了使用的靜態屬性的支援[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)，您可以裝飾具有執行緒靜態屬性[ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute)，例如：

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

就像方法可讓某些參數，會標示[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)，您可以套用[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)要屬性來表示該 null 是有效的值屬性，例如：

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

[ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)也可以直接在 set 存取子上指定參數：

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>自訂控制項繫結的注意事項

設定自訂控制項的繫結時，應該考慮下列事項：

1. **繫結屬性必須是靜態**-當定義的屬性，繫結[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)都必須使用屬性。
 2. **屬性名稱必須完全符合**-用來將屬性繫結的名稱必須完全相符的自訂控制項中的屬性名稱。
3. **屬性類型必須完全符合**-用來將屬性繫結的變數類型必須完全相符的自訂控制項中的屬性類型。
4. **中斷點和 getter/setter** -中斷點放在 getter 或 setter 方法的屬性會永遠不會被叫用。
5. **觀察回呼**-您必須使用的自訂控制項的屬性值中的變更通知的觀察回呼。

若要觀察任何上述列出需要注意的事項可能導致無訊息方式在執行階段失敗的繫結。

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>OBJECTIVE-C 可變動的模式和屬性

Objective C 架構使用的慣用語，其中某些類別是不可變，與可變動的子類別。 比方說`NSString`是不可變的版本中，雖然`NSMutableString`是可變動的子類別。

這些類別中很常見，若要查看不可變的基底類別，包含一個 getter，但沒有 setter 的屬性。 與可變動版本導入 setter。 因為這真的不可能使用 C#，我們必須將這個慣用語對應至可使用 C# 慣用語。

這會對應到 C# 的方式是新增 getter 和 setter 的基底類別，但加上旗標與 setter [ `[NotImplemented]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)屬性。

您所使用的可變動的子類別，然後[ `[Override]` ](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute)屬性上的屬性，以確認屬性實際上覆寫父項的行為。

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

`btouch-native`工具會自動產生以四位數的建構函式，在類別中，指定類別`Foo`，它會產生：

-  `Foo ()`： 預設建構函式 （對應到 「 init 」 Objective C 的建構函式）
-  `Foo (NSCoder)`： 的 NIB 檔案的還原序列化期間使用的建構函式 (對應至 Objective C 的"initWithCoder: 「 建構函式)。
-  `Foo (IntPtr handle)`： 建構函式控制代碼為基礎建立，這時叫用由執行階段執行階段必須公開從 unmanaged 物件的 managed 的物件。
-  `Foo (NSEmptyFlag)`： 這用由衍生類別，來避免雙重的初始化。

如果建構函式定義，他們需要使用下列簽章的介面定義中宣告： 它們必須傳回`IntPtr`值和方法的名稱應該是建構函式。 例如若要繫結`initWithFrame:`建構函式，這是您會使用：

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>繫結通訊協定

一節中的 API 設計文件中所述[討論模型和通訊協定](~/ios/internals/api-design/index.md#Models)，Xamarin.iOS 將 OBJECTIVE-C 通訊協定對應至具有已標示的類別[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)屬性。 這通常用於實作 OBJECTIVE-C 委派類別。

規則繫結的類別和委派類別最大的差別是委派類別可能會有一或多個選用的方法。

例如，請考慮`UIKit`類別`UIAccelerometerDelegate`，這是它如何結合在 Xamarin.iOS 中：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

由於這是選擇性的方法上定義`UIAccelerometerDelegate`任何執行其他操作。 但是如果通訊協定上所需的方法，您應該增加[ `[Abstract]` ](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)屬性加入方法。 這會強制使用者實作的實際方法中提供的內文。

一般情況下，回應訊息的類別會使用通訊協定。 這通常是在 OBJECTIVE-C 中指派給 「 delegate 」 屬性中的通訊協定的方法回應物件的執行個體。

在 Xamarin.iOS 中的慣例是為了支援這兩個的 Objective C 鬆散偶合樣式，其中任何執行個體`NSObject`可以指派給委派，並也會公開它的強型別版本。 基於這個理由，我們通常會提供這兩`Delegate`強類型的屬性和`WeakDelegate`，鬆散型別。 我們通常會繫結具有的 「 鬆散型別 」 版本[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)，而我們會使用[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)提供強型別版本的屬性。

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

已合併從 MonoTouch 7.0 開始全新和改進的通訊協定，繫結功能。  這個新的支援可讓您更容易採用指定的類別中的一或多個通訊協定時，用於 OBJECTIVE-C 的習慣用語。

如需每個通訊協定定義`MyProtocol`Objective C 中目前沒有`IMyProtocol`會列出所有必要的方法，從通訊協定的介面，以及提供選擇性的所有方法的延伸模組類別。  上述項目，結合編輯器可讓開發人員實作通訊協定方法，而不需要使用不同的子類別的上一個抽象的模型類別在 Xamarin Studio 中的新支援。

包含任何定義[ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute)屬性實際上會產生三個支援的類別，來大幅改善，您會使用通訊協定的方式：

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

**類別實作**提供完整的抽象類別，您可以用來覆寫個別方法，並取得完整的型別安全。  但由於 C# 不支援多重繼承中，有的案例，您可能需要有不同的基底類別，但您仍想要實作的介面，是要在哪裡

產生**介面定義**傳入。  它是具有所有必要的方法，從 通訊協定的介面。  這可讓開發人員用來實作您的通訊協定，只是實作介面。  執行階段會自動將註冊類型為採用之通訊協定。

請注意，此介面只會列出所需的方法，且並未公開 （expose） 的選擇性方法。  這表示，採用的通訊協定的類別會檢查所需的方法的完整型別，但必須求助於弱式類型 (手動使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性和符合簽章) 為選擇性通訊協定方法。

為了更方便取用的 API，會使用通訊協定，繫結工具也會產生公開 （expose） 所有選擇性方法的擴充方法類別。  這表示，只要您使用 API，您將能夠處理通訊協定為具有所有方法。

如果您想要使用的通訊協定定義在您的 API，您必須撰寫您的 API 定義基本架構空的介面。  如果您想要使用 MyProtocol API 中，您會需要執行這項操作：

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

上述需要因為在繫結時間`IMyProtocol`就不存在，也就是為什麼您需要提供空的介面。

#### <a name="adopting-protocol-generated-interfaces"></a>採用的通訊協定產生介面

每當您實作一個介面產生通訊協定，像這樣：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

介面方法的實作會自動取得匯出具有適當的名稱，讓它相當於這個：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

它並不重要如果隱含或明確實作介面。

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>繫結類別的延伸模組

在 OBJECTIVE-C 中就可以擴充以新方法，在精神與 C# 的擴充方法類似的類別。 當其中一種方法時，您可以使用[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)屬性加上旗標為訊息接收者的 Objective C 的方法。

例如，在 Xamarin.iOS 中我們繫結所定義的擴充方法`NSString`時`UIKit`中的方法為匯入`NSStringDrawingExtensions`，如下所示：

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>繫結 Objective C 的引數清單

OBJECTIVE-C 支援 variadic 引數。 例如: 

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

若要叫用這個方法，從 C# 您會想要建立的簽章，就像這樣：

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

這會宣告方法，為內部，隱藏上述 API 的使用者，但將其公開到程式庫。 然後您可以撰寫如下的方法：

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

通常這些欄位會包含必須參考的字串或整數值。 它們通常用為字串，代表特定的通知，並做為字典中的索引鍵。

若要將欄位繫結，將屬性加入至您的介面定義檔和裝飾的屬性[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)屬性。 這個屬性會採用一個參數： 要查閱的符號 C 名稱。 例如: 

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

如果您想要將未衍生自靜態類別中的各種欄位`NSObject`，您可以使用[ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class)屬性的類別，像這樣：

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

將會產生上述`LonelyClass`這不是衍生自`NSObject`，且會包含的繫結`NSSomeEventNotification` 
 `NSString`公開為`NSString`。

[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)屬性可以套用至下列資料類型：

-  `NSString` 參考 （僅限唯讀屬性）
-  `NSArray` 參考 （僅限唯讀屬性）
-  32 位元 int (`System.Int32`)
-  64 位元 int (`System.Int64`)
-  32 位元浮點數 (`System.Single`)
-  64 位元浮點數 (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

除了原生的欄位名稱，您可以指定程式庫名稱欄位所在的位置，藉由傳遞至程式庫名稱：

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

如果您要以靜態方式連結，沒有任何程式庫來繫結，因此您必須使用`__Internal`名稱：

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>繫結列舉

您可以新增`enum`直接在您的繫結檔案更容易而不需使用不同原始程式檔 （也就需要重新編譯的繫結和最終的專案中） 的 API 定義為內部使用它們。

範例：

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

您也可建立您自己的列舉，來取代`NSString`常數。 在此情況下將會產生器**自動**建立列舉值和 NSString 常數轉換為您的方法。

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

在上述範例中，您可以決定裝飾`void Perform (NSString mode);`具有[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性。 這將會**隱藏**常數為基礎的 API，從您的繫結取用者。

不過這會限制為到目前的 API 替代的使用，類型子類別化[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)屬性。 這些產生的方法不是`virtual`，也就是您將能夠覆寫這些-或不可能，是不錯的選擇。

替代方法是將原始`NSString`為基礎，定義來作為`[Protected]`。 這可讓運作，必要時，子類別化及 wrap'ed 版本仍會運作，並呼叫覆寫方法。

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>繫結`NSValue`， `NSNumber`，和`NSString`更好的型別

[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)屬性可讓繫結`NSNumber`，`NSValue`和`NSString`（列舉） 到更精確的 C# 型別。 屬性可用來建立更好、 更精確，在原生 api 的.NET API。

您可以將方法 （在傳回的值）、 參數和屬性與裝飾[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)。 唯一的限制是，您的成員**不得**內被[ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute)或[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)介面。

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

就內部而言，我們會執行`bool?`  <->  `NSNumber`並`CGRect`  <->  `NSValue`轉換。

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

`CAScroll` 已`NSString`支援列舉中，我們將會擷取右邊`NSString`值，並處理型別轉換。

請參閱[ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)文件，請參閱支援的轉換型別。

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>繫結通知

通知是訊息張貼至`NSNotificationCenter.DefaultCenter`並做為機制來廣播訊息到另一個應用程式的一個部分。 開發人員通常會使用通知訂閱[NSNotificationCenter](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/)的[AddObserver](https://developer.xamarin.com/api/type/Foundation.NSNotificationCenter/M/AddObserver/)方法。 當應用程式張貼訊息至通知中心時，通常包含儲存在裝載[NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/)字典。 這個字典弱型別，以及取得問題的資訊是容易發生錯誤，因為使用者通常需要讀取文件索引鍵位於字典和可儲存在字典中值的型別中。 存在的索引鍵有時會使用做為布林值。

Xamarin.iOS 繫結產生器提供繫結通知的開發人員的支援。 若要這樣做，您設定[ `[Notification]` ](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)也是在屬性上的屬性加上[ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) （可以是公用或私用） 的屬性。

這個屬性可以用於不含引數執行沒有承載的通知，或者您可以指定`System.Type`參考另一個介面，在 API 定義中，通常與名稱結尾"EventArgs"。 產生器會將介面變成類別子類別化`EventArgs`，且會包含所有列出的屬性。 [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性應該用在的 EventArgs 類別，列出用來查閱 OBJECTIVE-C 字典擷取值的索引鍵的名稱。

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

您的程式碼的使用者可以接著輕鬆訂閱通知張貼至[NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/)使用如下的程式碼：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

傳回的值從`ObserveDidStart`可用來輕鬆地停止接收通知，像這樣：

```csharp
token.Dispose ();
```

您也可以呼叫[NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject/)並傳遞 token。 如果您的通知中包含參數，您應該指定 helper`EventArgs`介面，就像這樣：

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

將會產生上述`MyScreenChangedEventArgs`類別`ScreenX`並`ScreenY`屬性，可將擷取的資料[NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/) "ScreenXKey"和"ScreenYKey 」 鍵的字典分別並套用適當的轉換。 `[ProbePresence]`屬性產生器用來探查，如果已設定金鑰`UserInfo`，而不是嘗試擷取值。 這用於的情況下，索引鍵的目前狀態 （通常適用於布林值） 的值。

這可讓您撰寫如下的程式碼：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>繫結類別

類別是以 OBJECTIVE-C 機制用來擴充一組方法和類別中可用的屬性。   在實務上，它們用來擴充基底類別的功能 (例如`NSObject`) 以特定架構中的連結時 (例如`UIKit`)，讓它們的方法，可供使用，但只有在 連結新的架構。   在某些其他情況下，它們會依功能用來組織類別中的功能。   也就是在精神與 C# 擴充方法類似。這是一個類別會如下所示在目標 c:

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上述範例中如果上找到的程式庫會擴充的執行個體`UIView`方法使用`makeBackgroundRed`。

若要將這些繫結，您可以使用[ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)介面定義上的屬性。  使用時[ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)屬性的意義[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)屬性被用來指定基底類別延伸，而是要擴充的型別會變更。

下列顯示如何`UIView`擴充功能會繫結和轉換成 C# 擴充方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

將建立上述`MyUIViewExtension`類別，其中包含`MakeBackgroundRed`擴充方法。  這表示您現在可以呼叫 「 MakeBackgroundRed"任何`UIView`子類別，讓您在 OBJECTIVE-C 相同的功能 在某些其他情況下，不要擴充系統類別，而組織功能，只提供裝飾用途使用類別。  與下列類似：

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

雖然您可以使用[ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)屬性也為此裝飾樣式的宣告中，您可能也只是將它們全部加入類別定義。  這兩種會達成相同目的：

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

在這些情況下，若要合併的類別，只要還短時：

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

區塊是由 Apple，以將 C# 匿名方法的對等功能帶到 OBJECTIVE-C 導入新的建構 比方說，`NSSet`類別現在會公開這個方法：

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

上述描述中宣告呼叫的方法`enumerateObjectsUsingBlock:`採用一個引數，名為`block`。 在於，它可讓您擷取目前的環境 （"this"指標，存取區域變數和參數），此區塊會類似於 C# 匿名方法。 在上述的方法`NSSet`叫用具有兩個參數的區塊`NSObject`(`id obj`一部分) 以及布林值的指標 ( `BOOL *stop`) 部分。

要繫結與 btouch 這類的 API，您需要先宣告區塊型別簽章，如 C# 委派，並接著將其參考來自 API 進入點，就像這樣：

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

此外，現在您的程式碼時，可以從 C# 呼叫您的函式：

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

如果您想，您也可以使用 lambda:

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>非同步方法

繫結產生器可以將特定類別的方法轉換成非同步友善方法 (方法會傳回工作或任務&lt;T&gt;)。

您可以使用[ `[Async]` ](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute)方法會傳回 void，而且其最後一個引數為回呼的屬性。  當您將此套用至方法時，繫結產生器會產生該方法具有尾碼的版本`Async`。  如果回呼不接受任何參數，將會傳回值`Task`，如果回呼會採用參數，結果會是`Task<T>`。  如果回呼不接受多個參數，您應該設定`ResultType`或`ResultTypeName`來指定所要產生的型別會保留所有屬性的名稱。

範例：

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

上述程式碼會產生這兩個 LoadFile 方法，以及：

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>呈現的弱式 NSDictionary 參數的強式型別

在 Objective C API 中的許多地方，參數會傳遞為弱型別`NSDictionary`具有特定索引鍵和值，但這些 Api 會出錯 （您可以傳遞無效的索引鍵，並不取得任何警告; 您可以傳遞無效的值，並取得無警告） 和令人洩氣若要使用視需要來查閱的可能索引鍵的名稱和值的文件的多個來回行程。

解決方法是提供強型別版本所提供的 api，並在幕後的強型別版本對應各種基礎索引鍵和值。

因此，例如，如果 Objective C API 接受`NSDictionary`和記載為索引鍵`XyzVolumeKey`這個方法會接受`NSNumber`磁碟區值從 0.0 到 1.0，`XyzCaptionKey`採用字串，您會想使用者會有一個不錯的 API看起來像這樣：

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

`Volume`屬性定義為可為 null 的浮點數，因為在 OBJECTIVE-C 中的慣例不需要有值，因此值可能不會設定位置的情況下這些字典。

若要這樣做，您需要執行一些作業：

* 建立強型別類別，子類別化[DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/)並提供每個屬性的各種不同的 getter 和 setter。
* 宣告的方法都會多載`NSDictionary`才會在新的強型別版本。

您可以建立的強型別類別方法是以手動的方式，或使用產生器來為您執行的工作。  我們先探討如何執行這項操作以手動方式讓您了解發生什麼情況，然後按一下 自動的方法。

您必須建立此項支援的檔案，不進入您 API 的合約。  這是您必須撰寫建立 XyzOptions 類別：

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

您接著應該提供呈現高階 API，低層級 API 之上的包裝函式方法。

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

如果您的 API 不需要覆寫中，您可以使用安全地隱藏 NSDictionary 為基礎的 API [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性。

如您所見，我們會使用[ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)呈現新的 API 進入點，此屬性，以及我們呈現它使用我們強型別`XyzOptions`類別。  包裝函式方法也可讓要傳遞的 null。

現在，我們忘記提到的一件事是 where`XyzOptionsKeys`值的來源。  您通常會群組之類的靜態類別中的 API 介面的索引鍵`XyzOptionsKeys`，如下所示：

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

讓我們看看建立這些強型別字典的自動支援。  這可避免未定案、 大量，您可以直接在您的 API 合約，而不是使用外部檔案中定義的字典。

若要建立強型別字典，介紹在您的 API 介面和裝飾與其[StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)屬性。  這會告訴程式產生器，它應該建立的類別與您的介面會衍生自同名`DictionaryContainer`，並將它提供強式型別的存取子。

[ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary)屬性會採用一個參數，這是靜態類別，其中包含您的字典索引鍵的名稱。  然後介面的每個屬性就會成為強型別存取子。  根據預設，程式碼將使用的屬性名稱後置字元"Key"靜態類別中建立存取子。

這表示外部檔案，也不需要手動建立 getter 和 setter 的每個屬性，也不必以手動方式查閱索引鍵，將建立您的強型別存取子不會再需要您自己。

這是您的整個繫結會如下所示：

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

萬一您需要在參考您`XyzOption`成員不同的欄位 (也就是不是名稱具有後置詞的屬性`Key`)，您可以裝飾具有屬性[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性名稱，您想要使用。

<a name="Type_mappings" />

## <a name="type-mappings"></a>型別對應

本章節涵蓋如何將 OBJECTIVE-C 類型對應至 C# 類型。

<a name="Simple_Types" />

### <a name="simple-types"></a>簡單類型

下表顯示您從 OBJECTIVE-C 與產品 CocoaTouch 世界 Xamarin.iOS 世界的類型應該對應方式：

|OBJECTIVE-C 型別名稱|Xamarin.iOS 統一 API 類型|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([詳細資料繫結 NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (另請參閱： [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
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

Xamarin.iOS 執行階段會自動轉換到 C# 陣列`NSArrays`，並執行的轉換，例如的虛數的 OBJECTIVE-C 方法，傳回`NSArray`的`UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

繫結就像這樣：

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

其概念是要使用強型別 C# 陣列，因為這可讓 IDE 提供適當的程式碼完成功能的實際類型，而不會強制使用者猜到，或查閱文件，以找出實際的型別陣列中所包含的物件。

在其中您可以不追蹤實際的最具衍生性型別陣列中所含的情況下，您可以使用`NSObject []`當做傳回值。

<a name="Selectors" />

### <a name="selectors"></a>選取器

選取器出現在特殊類型的 Objective C API `SEL`。 繫結時的選取器，您會將對應的型別`ObjCRuntime.Selector`。  通常是選取器會公開在使用物件、 目標物件和選取器 API，以叫用目標物件中。 基本上提供這兩種都對應至 C# 委派： 叫用的方法以及要叫用方法的物件會封裝的項目。

這是繫結的樣子：

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

而這是如何通常會使用此方法的應用程式中：

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

若要讓 C# 開發人員到目前的繫結，您通常會提供採用的方法`NSAction`參數，可讓 C# 委派和 lambda，而不是使用`Target+Selector`。 若要這樣做您通常會隱藏`SetTarget`方法加上旗標與其[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性，然後會公開新的協助程式方法，就像這樣：

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

因此，現在的使用者程式碼可以撰寫如下：

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

當您要繫結採用的方法`NSString`，您可以取代，與 C# 字串類型，同時在傳回的型別和參數。

您可能想要使用的唯一情況`NSString`直接時，則會使用字串做為權杖。 如需字串的詳細資訊和`NSString`，請閱讀[NSString 上的 API 設計](~/ios/internals/api-design/nsstring.md)文件。

在某些罕見的情況下，API 可能會公開類似 C 的字串 (`char *`) 而非 Objective C 字串 (`NSString *`)。 在這些情況下，您可以標註的參數[ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)屬性。

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out / ref 參數

某些 Api 的參數，傳回值或傳址方式傳遞參數。

通常簽章看起來像這樣：

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

第一個範例顯示一般的 Objective C 慣用句，傳回錯誤碼，指標`NSError`指標傳遞，並在傳回的值設定。   第二個方法顯示如何 OBJECTIVE-C 方法可能會接受物件，並修改其內容。   這是參考，而不是單純的輸出值的階段。

您的繫結會看起來像這樣：

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>記憶體管理屬性

當您使用[ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)屬性，您會傳遞呼叫的方法將保留的資料，您可以藉由傳遞做為第二個參數，例如指定的引數語意：

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

您可以使用[ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)屬性以隱藏來自公用 API 的方法。 您可能想要在其中公開的 API 是太低層級，而您想要提供在不同的檔案，這個方法為基礎的高層級實作的情況下執行這項操作。

您也可以使用這當遇到繫結產生器中的限制，例如一些進階的案例可能會公開未繫結的類型和您想要繫結您自己的方式，而您想要自行包裝這些型別，在您自己的方式。

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>事件處理常式和回呼

OBJECTIVE-C 類別通常會廣播通知，或要求上委派類別 （Objective C 委派） 傳送訊息的資訊。

此模型中，雖然完全支援和呈現 Xamarin.iOS 有時候很麻煩。 C# 事件模式，可以在這些情況下使用的類別上的系統在方法回呼，就會顯示 Xamarin.iOS。 這可讓程式碼，就像這樣執行：

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

繫結產生器可以減少打字，將 OBJECTIVE-C 模式對應至 C# 模式所需的量。

與 Xamarin.iOS 1.4 啟動將能夠也指示產生器以產生特定的 Objective C 委派繫結，且將公開為 C# 事件和屬性上的主機類型的委派。

有兩種類別參與此程序，將主應用程式類別是指目前會發出事件，並將這些應用程式傳送`Delegate`或`WeakDelegate`和實際委派類別。

請考慮下列設定：

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

若要包裝的類別中，您必須：

-  在您的主機類別，將新增至您 [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   宣告做為其委派和您所公開的 C# 名稱的型別。 在上述範例中所`typeof (MyClassDelegate)`和`WeakDelegate`分別。
-  委派類別，有兩個以上的參數，每個方法中，您需要指定您想要用於自動產生的 EventArgs 類別的型別。

繫結產生器不是限於文繞圖只有單一事件的目的地，進行，將委派發出多個訊息的某些 OBJECTIVE-C 類別，因此您必須提供陣列，以便支援這項設定。 大部分的配置不需要它，但產生器已準備好支援這種情況。

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

`EventArgs`用來指定名稱`EventArgs`要產生的類別。 您應該使用其中每個簽章 (在此範例中，`EventArgs`將包含`With`屬性的型別 nint)。

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

因此您現在可以使用如下的程式碼：

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

就像事件叫用的回呼，其差異在於，而不是讓多個可能的訂閱者 (例如，多個方法可以將連結到`Clicked`事件或`DownloadFinished`事件) 回呼只能有單一訂閱者。

此程序完全相同，唯一的差別在於，而不是公開的名稱`EventArgs`類別，將會產生 EventArgs 實際上用來產生 C# 委派名稱命名。

如果委派類別中的方法的傳回值，繫結產生器會將這對應到在父類別，而不是事件的委派方法。 在這些情況下，您需要提供應傳回方法使用者不會不掛至委派的預設值。 做法是使用[ `[DefaultValue]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)或是[ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)屬性。

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) 將硬式編碼傳回值，雖然[ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)用來指定將傳回哪一個輸入引數。

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>列舉型別和基底類型

您也可以參考列舉型別或不直接支援 btouch 介面定義系統的基底類型。 若要這樣做，請將您的列舉型別和核心類型放入個別的檔案並包含這其中一個額外的檔案提供給 btouch 的一部分。

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>連結的相依性

如果您要繫結並不屬於您的應用程式的 Api，您需要確定可執行檔針對這些程式庫連結。

您需要通知 Xamarin.iOS 如何連結您的程式庫，這可以藉由改變您的組建組態，以叫用`mtouch`具有一些額外的命令會建置引數，指定如何使用新的程式庫連結 」-gcc_flags 」 選項，後面加上引號的字串，包含所有額外的程式庫所需的程式，像這樣：

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

上述範例會將連結`libMyLibrary.a`，`libSystemLibrary.dylib`而`CFNetwork`framework 程式庫，到最後一個可執行檔。

或利用組件層級[ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)，，您可以內嵌在您的合約檔案 (例如`AssemblyInfo.cs`)。
當您使用[ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)，您必須擁有您原生程式庫可在您做您繫結，這會將原生程式庫內嵌您的應用程式的時間。 例如: 

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

您可能會好奇，為什麼需要`-force_load`命令，以及原因是，雖然它會編譯中的程式碼的旗標-ObjC，它不會保留支援的類別 (連結器/編譯器無作用程式碼刪除 whr) 所需的中繼資料您適用於 Xamarin.iOS 需要在執行階段。

<a name="Assisted_References" />

## <a name="assisted-references"></a>輔助的參考

某些暫時性的物件，例如動作試算表和警示方塊是難以追蹤的開發人員，繫結產生器可以協助這裡稍微投機取巧。

例如，如果您的類別，顯示一則訊息，並產生`Done`事件，傳統的方式來處理這會是：

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

在上述案例中，開發人員必須以自己和任一流失保留物件參考，或主動清除方塊上他自己的參考。  雖然繫結程式碼，產生器支援追蹤參考，並清除它叫用特殊的方法時，上述程式碼就會成為：

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

請注意如何才不會再將它搭配本機變數，並不需要物件無作用時，請清除參考，該變數保留執行個體，在。

若要利用這一點，您的類別應該具備設定的事件屬性[ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)宣告以及`KeepUntilRef`變數設定為當物件已完成其工作，例如叫用方法的名稱這樣：

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>繼承的通訊協定

自 Xamarin.iOS v3.2 起我們支援繼承已標示的通訊協定[ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)屬性。 這是用於特定 API 的模式，例如在`MapKit`何處`MKOverlay`通訊協定，請繼承自`MKAnnotation`通訊協定，以及採用的幾個類別繼承自`NSObject`。

過去我們需要將通訊協定複製到每個實作中，但是在這些情況下我們現在可以有`MKShape`類別繼承自`MKOverlay`通訊協定，並將所有必要的方法可自動產生。

## <a name="related-links"></a>相關連結

- [繫結範例](https://developer.xamarin.com/samples/BindingSample/)
- [Xamarin University 課程： 建置 OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University 課程： 建置目標 Sharpie OBJECTIVE-C 繫結程式庫](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
