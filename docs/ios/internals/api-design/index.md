---
title: Xamarin iOS API 設計
description: 用來設計 Xamarin Api 的指導準則，以及它們與目標 C 的關聯性。
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: ec17e22069b1389549effe7f6f4f05c56a835afb
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602316"
---
# <a name="xamarinios-api-design"></a>Xamarin iOS API 設計

除了屬於 Mono 的核心基類庫之外， [Xamarin](~/ios/index.yml) 還隨附各種 iOS api 的系結，可讓開發人員使用 Mono 建立原生 iOS 應用程式。

在 Xamarin 的核心中，有一個 interop 引擎可將 c # 世界與 CoreGraphics 和 OpenGL ES 等 iOS C 型 Api （例如，和 [OpenGL ES](#opengles)）進行橋接。

與目標 C 程式碼通訊的低層級執行時間位於 [monotouch.dll. ObjCRuntime](#objcruntime)。 此外，也會提供 [Foundation](#foundation)、CoreFoundation 和 [UIKit](#uikit) 的系結。

## <a name="design-principles"></a>設計原則

本節將詳細說明一些適用于 Xamarin 的設計原則 (它們也適用于 Xamarin，也就是 macOS 上目標 C 的 Mono 系結) ：

- 遵循 [架構設計指導方針](/dotnet/standard/design-guidelines)
- 讓開發人員可以子類別化目標-C 類別：

  - 衍生自現有類別
  - 呼叫基底函式以進行鏈
  - 覆寫方法應該使用 c # 的覆寫系統來完成
  - 子類別化應該搭配 c # 標準結構使用

- 不要將開發人員公開至目標-C 選取器
- 提供一種機制來呼叫任意的目標-C 程式庫
- 讓常見的目標-C 工作變得簡單且難以進行
- 將目標 C 屬性公開為 c # 屬性
- 公開強型別 API：

  - 提高型別安全
  - 將執行階段錯誤降至最低
  - 在傳回類型上取得 IDE IntelliSense
  - 允許 IDE 快顯視窗檔

- 鼓勵以 IDE 探索 Api：

  - 例如，您不會公開弱型別陣列，如下所示：

    ```objc
    NSArray *getViews
    ```

    公開強型別，如下所示：

    ```csharp
    NSView [] Views { get; set; }
    ```

    使用強型別可讓 Visual Studio for Mac 在流覽 API 時執行自動完成的功能，讓所有 `System.Array` 作業可在傳回的值上使用，並允許傳回值參與 LINQ。

- 原生 c # 類型：

  - [`NSString` 成為 `string`](~/ios/internals/api-design/nsstring.md)
  - 應該已經列舉 `int` `uint` 為 c # 列舉的參數和具有屬性的 c # `[Flags]` 列舉
  - 不是型別中立的物件，而是將 `NSArray` 陣列公開為強型別陣列。
  - 針對事件和通知，讓使用者選擇：

    - 預設為強型別版本
    - 適用于先進使用案例的弱類型版本

- 支援目標-C 委派模式：

  - C # 事件系統
  - 公開 c # 委派 (lambda、匿名方法，以及 `System.Delegate` 將目標 c api) 為區塊

### <a name="assemblies"></a>組件

Xamarin 包含許多構成 *Xamarin IOS 設定檔* 的元件。 [ [元件](~/cross-platform/internals/available-assemblies.md) ] 頁面有詳細資訊。

### <a name="major-namespaces"></a>主要命名空間

#### <a name="objcruntime"></a>ObjCRuntime

[ObjCRuntime](xref:ObjCRuntime)命名空間可讓開發人員在 c # 與目標 c 之間橋接世界。
這是根據 Cocoa # 和 Gtk # 的體驗，特別針對 iOS 設計的新系結。

#### <a name="foundation"></a>Foundation

[基礎](xref:Foundation)命名空間提供了基本的資料類型，其設計目的是要與屬於 IOS 的目標 C Foundation framework 交互操作，而且是以目標為導向的物件導向程式設計基礎。

Xamarin： c # 中的類別階層是來自目標 C 的類別階層架構。 例如，c # 的目標 C 基類 NSObject 可透過 [NSObject](xref:Foundation.NSObject)來使用。

雖然 Foundation 命名空間提供基礎目標 C 基礎類型的系結，但在少數情況下，我們已將基礎類型對應至 .NET 類型。 例如：

- 執行時間不會處理 NSString 和 [NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html)，而是會將它們公開為 API 中的 c # [字串](xref:System.String)和強型別 [陣列](xref:System.Array)。

- 這裡公開各種協助程式 Api，可讓開發人員系結協力廠商的目標 C Api、其他 iOS Api，或目前未受 Xamarin 所系結的 Api。

For more information on binding APIs, see the [Xamarin.iOS Binding Generator](~/cross-platform/macios/binding/binding-types-reference.md) section.

##### <a name="nsobject"></a>NSObject

[NSObject](xref:Foundation.NSObject)型別是所有目標-C 系結的基礎。 Xamarin 類型會從 iOS CocoaTouch Api 鏡像兩種類型的類別： C 類型 (通常稱為 CoreFoundation 類型) 和 NSObject 類別都衍生自類別) 的目標 C 類型 (。

針對每個鏡像非受控型別的型別，都可以透過 [Handle](xref:Foundation.NSObject.Handle) 屬性取得原生物件。

雖然 Mono 將提供所有物件的垃圾收集，但會實 `Foundation.NSObject` [IDisposable](xref:System.IDisposable) 介面。 您可以明確釋放任何指定 NSObject 的資源，而不必等待垃圾收集行程開始進行。 當您使用繁重的 NSObjects （例如，可能持有大型資料區塊指標的 UIImages）時，明確釋放資源相當重要。

如果您的類型需要執行決定性的完成，請覆寫 [NSObject (bool) 方法。](xref:Foundation.NSObject.Dispose(System.Boolean)) dispose 的參數會是「bool 處置」，如果設定為 true，則表示因為使用者明確呼叫了物件上的 dispose () ，所以會呼叫 dispose 方法。 False 值表示您的 Dispose (bool 處置) 方法是從完成項執行緒上的完成項呼叫。

##### <a name="categories"></a>類別

從 Xamarin. iOS 8.10 開始，可以從 c # 建立目標 C 類別。

這是使用屬性來完成 `Category` ，指定要擴充為屬性之引數的型別。 下列範例會擴充 NSString。

```csharp
[Category (typeof (NSString))]
```

每個類別方法都使用一般機制，將方法匯出至使用屬性的目標 C `Export` ：

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

所有 managed 擴充方法都必須是靜態的，但您可以使用 c # 中的擴充方法標準語法來建立目標 C 實例方法：

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

擴充方法的第一個引數將是叫用方法的實例。

完整範例：

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
}
```

這個範例會將原生 toUpper 實例方法加入至 NSString 類別，這個類別可以從目標 C 叫用。

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

其中一個實用的案例是將方法新增至程式碼基底中的整個類別集合，例如，這會讓所有 `UIViewController` 實例報告可以旋轉：

```csharp
[Category (typeof (UINavigationController))]
class Rotation_IOS6 {
      [Export ("shouldAutorotate:")]
      static bool ShouldAutoRotate (this UINavigationController self)
      {
          return true;
      }
}
```

##### <a name="preserveattribute"></a>PreserveAttribute

PreserveAttribute 是一個自訂屬性，用來在處理應用程式以縮減其大小時，用來指示 mtouch （即）在階段中保留類型或成員類型的成員。

未由應用程式以靜態方式連結的每個成員都可能會移除。 因此，這個屬性會用來標記未以靜態方式參考的成員，但您的應用程式仍需要此屬性。

例如，若您是以動態方式來具現化類型，您可以保留您類型的預設建構函式。 若您使用的是 XML 序列化，則您可能會想要保留您類型的屬性。

您可以將此屬性套用到某個類型的每個成員，或是套用到類型本身。 如果您想要保留整個類型，可以在類型上使用 [保留 (AllMembers = true) ] 語法。

#### <a name="uikit"></a>UIKit

[UIKit](xref:UIKit)命名空間包含以 c # 類別形式組成 CocoaTouch 的所有 UI 元件的一對一對應。 API 已修改為遵循 c # 語言中使用的慣例。

C # 委派提供給一般作業。 如需詳細資訊，請參閱 [委派](#delegates) 一節。

#### <a name="opengles"></a>OpenGLES

針對 OpenGLES，我們會將 [修改](xref:OpenTK) 過的 [opentk 簡介](https://opentk.net/) API 版本（物件導向系結）散發至已修改為使用 CoreGraphics 資料類型和結構的 OpenGL，並只公開 iOS 上可用的功能。

OpenGLES 1.1 功能可透過 [ES11.GL 類型](xref:OpenTK.Graphics.ES11.GL)使用。

OpenGLES 2.0 功能可透過 [ES20.GL 類型](xref:OpenTK.Graphics.ES20.GL)使用。

OpenGLES 3.0 功能可透過 [ES30.GL 類型](xref:OpenTK.Graphics.ES30.GL)使用。

### <a name="binding-design"></a>系結設計

Xamarin 不只是基礎目標 C 平臺的系結。 它會擴充 .NET 型別系統並分派系統，以提供更好的 blend c # 和目標 C。

就像 P/Invoke 是在 Windows 和 Linux 上叫用原生程式庫的公用程式，或可用於 Windows 上 COM interop 的 IJW 支援一樣，Xamarin 也擴充了執行時間，以支援將 c # 物件系結至目標 C 物件。

在接下來的幾節中，您不需要針對建立 Xamarin iOS 應用程式的使用者進行討論，但可協助開發人員瞭解完成專案的方式，並在建立更複雜的應用程式時協助他們。

#### <a name="types"></a>類型

無論是哪種情況，c # 型別都會公開給 c # universe，而非低層級的基礎類型。  這表示 [API 會使用 c # "string" 類型而不是 NSString](~/ios/internals/api-design/nsstring.md) ，而且它會使用強型別的 c # 陣列，而不是公開 NSArray。

一般而言，在 Xamarin 和 Xamarin 的設計中， `NSArray` 不會公開基礎物件。 相反地，執行時間會自動將 `NSArray` s 轉換成某個類別的強型別陣列 `NSObject` 。 因此，Xamarin 不會公開弱型別方法（例如 GetViews）來傳回 NSArray：

```csharp
NSArray GetViews ();
```

相反地，系結會公開強型別傳回值，如下所示：

```csharp
UIView [] GetViews ();
```

有幾個方法會在中公開 `NSArray` ，在您可能想要直接使用的角落案例中 `NSArray` ，但不建議在 API 系結中使用它們。

此外，在 **傳統 api** （而不是公開 `CGRect` 、 `CGPoint` 和 `CGSize` 自 CoreGraphics api）中，我們會將它們取代為執行 `System.Drawing` `RectangleF` 、 `PointF` 和， `SizeF` 因為它們可協助開發人員保留使用 opentk 簡介的現有 OpenGL 程式碼。 使用新的64位 **整合 API** 時，應使用 CoreGraphics API。

#### <a name="inheritance"></a>繼承

Xamarin API 設計可讓開發人員使用「覆寫」關鍵字在衍生類別上擴充原生目標 C 型別，以擴充 c # 型別，並使用 "base" c # 關鍵字連結到基底實作為基底。

這項設計可讓開發人員避免在其開發過程中處理目標 C 選取器，因為整個目標-C 系統已經包裝在 Xamarin. iOS 程式庫中。

#### <a name="types-and-interface-builder"></a>類型和介面產生器

當您建立的 .NET 類別是介面產生器所建立之類型的實例時，您必須提供接受單一參數的函式 `IntPtr` 。
這是將 managed 物件實例系結至非受控物件的必要項。
程式碼是由一行組成，如下所示：

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

#### <a name="delegates"></a>委派

目標 C 和 c # 對於每種語言中的單字委派都有不同的意義。

在目標 C 世界中，以及您可以在線上找到 CocoaTouch 的檔中，委派通常是將會回應一組方法的類別實例。 這類似于 c # 介面，差別在於方法不一定是強制性的。

這些委派在 UIKit 和其他 CocoaTouch Api 中扮演著重要的角色。 它們可用來完成各種工作：

- 若要為您的程式碼提供通知 (類似于 c # 或 Gtk +) 中的事件傳遞。
- 為數據視覺效果控制項執行模型。
- 來驅動控制項的行為。

程式設計模式的設計目的是要將衍生類別的建立最小化，以改變控制項的行為。 這個解決方案與其他 GUI 工具組在多年來完成的精神類似： Gtk 的信號、Qt 位置、Winforms 事件、WPF/Silverlight 事件等等。 為了避免有數百個介面 (每個動作都有一個) 或要求開發人員執行不需要的許多方法，目標 C 支援選擇性的方法定義。 這與需要執行所有方法的 c # 介面不同。

在 [目標-C] 類別中，您會看到使用此程式設計模式的類別會公開一個稱為的屬性， `delegate` 此屬性是必要的，以執行介面的必要部分以及零或多個選擇性部分。

在 Xamarin 中，提供三種互斥機制來系結至這些委派：

1. [Via 事件](#via-events)。
2. [透過屬性的強型別 `Delegate`](#strongly-typed-via-a-delegate-property)
3. [透過屬性的鬆散類型 `WeakDelegate`](#loosely-typed-via-the-weakdelegate-property)

例如，請考慮 UIWebView 類別。 這會分派給指派給委派屬性的 UIWebViewDelegate 實例。

##### <a name="via-events"></a>Via 事件

針對許多類型，Xamarin 會自動建立適當的委派，以將 `UIWebViewDelegate` 呼叫轉送至 c # 事件。 針對 `UIWebView`：

- WebViewDidStartLoad 方法會對應至  [UIWebView. LoadStarted](xref:UIKit.UIWebView.LoadStarted) 事件。
- WebViewDidFinishLoad 方法會對應至  [UIWebView. LoadFinished](xref:UIKit.UIWebView.LoadFinished) 事件。
- Web 程式： didFailLoadWithError 方法會對應至  [UIWebView. LoadError](xref:UIKit.UIWebView.LoadError) 事件。

例如，這個簡單的程式會記錄載入網頁視圖時的開始和結束時間：

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

##### <a name="via-properties"></a>Via 屬性

當事件可能有一個以上的訂閱者時，事件會很有用。 此外，事件僅限於程式碼沒有傳回值的情況。

在程式碼預期會傳回值的情況下，我們選擇改為屬性。 這表示在指定的時間內，只有一個方法可以設定為物件。

例如，您可以使用這種機制來關閉的處理常式上螢幕上的鍵盤 `UITextField` ：

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

`UITextField` `ShouldReturn` 在此情況下，的屬性會將傳回 bool 值的委派當作引數，並決定是否應該在按下 [返回] 按鈕時，進行某些動作。 在我們的方法中，我們會將 *true* 傳回給呼叫者，但我們也會從螢幕中移除鍵盤 (當欄位呼叫) 時，就會發生這種情況 `ResignFirstResponder` 。

##### <a name="strongly-typed-via-a-delegate-property"></a>透過委派屬性的強型別

如果您不想使用事件，您可以提供自己的 [UIWebViewDelegate](xref:UIKit.UIWebViewDelegate) 子類別，並將它指派給 [UIWebView。 Delegate](xref:UIKit.UIWebView.Delegate) 屬性。 一旦 UIWebView 之後，UIWebView 事件分派機制將無法再運作，而且會在對應的事件發生時叫用 UIWebViewDelegate 方法。

例如，這種簡單類型會記錄載入 web view 所花費的時間：

```csharp
class Notifier : UIWebViewDelegate  {
    DateTime startTime, endTime;

    public override LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    public override LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}
```

上述程式碼中使用的程式碼如下所示：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

上述程式會建立 UIWebViewer，並指示它將訊息傳送給通知者的實例，這是我們為了回應訊息而建立的類別。

這個模式也可用來控制特定控制項的行為，例如在 UIWebView 案例中， [UIWebView ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad) 屬性允許 `UIWebView` 實例控制是否要 `UIWebView` 載入頁面。

此模式也可用來提供一些控制項的隨選資料。 例如， [ios uitableview 範例](xref:UIKit.UITableView)控制項是功能強大的資料表轉譯控制項，而且外觀和內容都是由[UITableViewDataSource](xref:UIKit.UITableViewDataSource)的實例所驅動。

### <a name="loosely-typed-via-the-weakdelegate-property"></a>透過 WeakDelegate 屬性的鬆散類型

除了強型別屬性之外，還有一個弱式型別委派，可讓開發人員在需要時，以不同的方式系結專案。
在所有的強型別 `Delegate` 屬性都在 Xamarin 的系結中公開時， `WeakDelegate` 也會公開對應的屬性。

使用時 `WeakDelegate` ，您必須負責使用 [Export](xref:Foundation.ExportAttribute) 屬性來指定選取器，以正確裝飾您的類別。 例如：

```csharp
class Notifier : NSObject  {
    DateTime startTime, endTime;

    [Export ("webViewDidStartLoad:")]
    public void LoadStarted (UIWebView webview)
    {
        startTime = DateTime.Now;
    }

    [Export ("webViewDidFinishLoad:")]
    public void LoadingFinished (UIWebView webView)
    {
        endTime= DateTime.Now;
    }
}

[...]

var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.WeakDelegate = new Notifier ();
```

一旦 `WeakDelegate` 指派屬性， `Delegate` 就不會使用屬性。 此外，如果您在想要 [匯出] 的繼承基類中執行方法，就必須將它設為公用方法。

## <a name="mapping-of-the-objective-c-delegate-pattern-to-c"></a>將目標 C 委派模式對應到 C\#

當您看到如下所示的目標 C 範例：

```objc
foo.delegate = [[SomethingDelegate] alloc] init]
```

這會指示語言建立並建立類別 "SomethingDelegate" 的實例，並將值指派給 foo 變數上的委派屬性。 這項機制受到 Xamarin 和 c # 的支援，語法如下：

```csharp
foo.Delegate = new SomethingDelegate ();
```

在 Xamarin 中，我們提供了對應至目標 C 委派類別的強型別類別。 若要使用它們，您將會子類別化並覆寫由 Xamarin 的實作為定義的方法。 如需其運作方式的詳細資訊，請參閱下面的「模型」一節。

### <a name="mapping-delegates-to-c"></a>將委派對應到 C\#

UIKit 一般會使用兩種形式的目標 C 委派。

第一個表單會提供元件模型的介面。 例如，做為可依需求提供資料的機制，例如清單視圖的資料儲存設備。  在這些情況下，您應該一律建立適當類別的實例，並指派變數。

在下列範例中，我們會為 `UIPickerView` 使用字串的模型提供一個實作為：

```csharp
public class SampleTitleModel : UIPickerViewTitleModel {

    public override string TitleForRow (UIPickerView picker, nint row, nint component)
    {
        return String.Format ("At {0} {1}", row, component);
    }
}

[...]

pickerView.Model = new MyPickerModel ();
```

第二種形式是提供事件的通知。 在這些情況下，雖然我們仍會以上述形式公開 API，但我們也會提供 c # 事件，這應該更容易用於快速作業，並與 c # 中的匿名委派和 lambda 運算式整合。

例如，您可以訂閱 `UIAccelerometer` 事件：

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

有兩個選項可供使用，但以程式設計人員而言，您必須挑選一個。 如果您建立自己的強型別回應程式/委派實例，並將它指派給它，則 c # 事件將無法運作。 如果您使用 c # 事件，將永遠不會呼叫回應者/委派類別中的方法。

先前使用的範例 `UIWebView` 可以使用 c # 3.0 lambda 來撰寫，如下所示：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```

#### <a name="responding-to-events"></a>回應事件

在目標 C 程式碼中，有時多個控制項的多個控制項和提供者的事件處理常式，將會裝載在相同的類別中。 這是可能的，因為類別會回應訊息，而且只要類別回應訊息，就可以將物件連結在一起。

如先前所述，Xamarin 支援以 c # 事件為基礎的程式設計模型，以及目標 C 委派模式，您可以在其中建立新的類別來執行委派，並覆寫所需的方法。

也可以支援目標 C 的模式，其中多個不同作業的回應程式都裝載在相同類別的實例中。 若要這樣做，您必須使用適用于 Xamarin 系結的低層級功能。

例如，如果您想要讓類別在 `UITextFieldDelegate.textFieldShouldClear` 相同類別的實例中同時回應：訊息和 `UIWebViewDelegate.webViewDidStartLoad` ：，您必須使用 [匯出] 屬性聲明：

```csharp
public class MyCallbacks : NSObject {
    [Export ("textFieldShouldClear:"]
    public bool should_we_clear (UITextField tf)
    {
        return true;
    }

    [Export ("webViewDidStartLoad:")]
    public void OnWebViewStart (UIWebView view)
    {
        Console.WriteLine ("Loading started");
    }
}
```

方法的 c # 名稱並不重要;重要的是傳遞至 [Export] 屬性的字串。

使用這種程式設計樣式時，請確定 c # 參數符合執行時間引擎將傳遞的實際類型。

#### <a name="models"></a>模型

在 UIKit 儲存設施中，或在使用 helper 類別所執行的回應程式中，這些會在目標 C 程式碼中參考為委派，而且會實作為通訊協定。

目標 C 通訊協定就像介面，但它們支援選擇性的方法，也就是，並非所有的方法都必須執行，通訊協定才能運作。

有兩種方式可執行模型。 您可以手動執行，或使用現有的強型別定義。

當您嘗試執行由 Xamarin 未系結的類別時，需要手動機制。 做法很簡單：

- 以旗標標示類別以註冊執行時間
- 在您想要覆寫的每個方法上，套用 [Export] 屬性與實際的選取器名稱
- 具現化類別，並將它傳遞。

例如，下列程式只會執行 Uiapplicationdelegate 以便通訊協定定義中的其中一個選擇性方法：

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

目標-C 選取器名稱 ( "applicationDidFinishLaunching：" ) 是使用 Export 屬性來宣告，而類別是以屬性（ `[Register]` attribute）註冊。

Xamarin 提供強型別宣告，可供使用，且不需要手動系結。 為了支援此程式設計模型，Xamarin 執行時間支援類別宣告上的 [Model] 屬性。 這會告知執行時間，除非明確地執行方法，否則它不應該連接類別中的所有方法。

這表示在 UIKit 中，以選擇性方法表示通訊協定的類別會以如下方式撰寫：

```csharp
[Model]
public class SomeViewModel : NSObject {
    [Export ("someMethod:")]
    public virtual int SomeMethod (TheView view) {
       throw new ModelNotImplementedException ();
    }
    ...
}
```

當您想要執行僅執行某些方法的模型時，您只需要覆寫您感興趣的方法，並忽略其他方法。 執行時間只會將覆寫的方法（而不是原始的方法）連結至目標 C 世界。

相當於先前的手動範例：

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

優點是不需要深入探索目標 C 標頭檔，即可找出選取器、引數類型或 c # 的對應，以及從 Visual Studio for Mac 取得 intellisense 以及強式類型

#### <a name="xib-outlets-and-c"></a>XIB 輸出口和 C\#

這是使用 c # 與 c # 整合的低層級描述，並為適用于 Xamarin 的 advanced 使用者提供。 使用 Visual Studio for Mac 時，會在幕後使用產生的程式碼，在幕後自動完成對應。

當您使用 Interface Builder 設計使用者介面時，您只會設計應用程式的外觀，而且會建立一些預設連接。 如果您想要以程式設計方式提取資訊、在執行時間變更控制項的行為，或在執行時間修改控制項，則必須將某些控制項系結至您的 managed 程式碼。

這會透過幾個步驟來完成：

1. 將 **輸出** 的宣告新增至檔案 **的擁有** 者。
1. 將您的控制項連接至檔案 **的擁有** 者。
1. 將 UI 和連接儲存在 XIB/筆尖檔案中。
1. 在執行時間載入筆尖檔。
1. 存取輸出口變數。

 (1) 到 (3) 的步驟包含在 Apple 的檔中，以使用 Interface Builder 建立介面。

使用的是您的應用程式時，您的應用程式將需要建立衍生自 UIViewController 的類別。 它會實作為：

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

然後，若要從筆尖檔案載入您的 ViewController，請執行下列動作：

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

這會從筆尖載入使用者介面。 現在，若要存取輸出，必須通知執行時間我們想要進行存取。 若要這樣做，子 `UIViewController` 類別需要宣告屬性，並使用 [連接] 屬性來標注它們。 例如：

```csharp
[Connect]
UITextField UserName {
    get {
        return (UITextField) GetNativeField ("UserName");
    }
    set {
        SetNativeField ("UserName", value);
    }
}
```

屬性實作為實際提取和儲存實際原生型別的值。

使用 Visual Studio for Mac 和 InterfaceBuilder 時，您不需要擔心這一點。 Visual Studio for Mac 會使用編譯為專案一部分的部分類別中的程式碼，自動鏡像所有宣告的輸出。

#### <a name="selectors"></a>選取器

目標 C 程式設計的核心概念是選取器。 您通常會遇到需要您傳遞選取器的 Api，或預期您的程式碼會回應選取器。

在 c # 中建立新的選取器很簡單–您只要建立類別的新實例 `ObjCRuntime.Selector` ，並在需要它的 API 中的任何位置使用結果。 例如：

```csharp
var selector_add = new Selector ("add:plus:");
```

針對 c # 方法回應選取器呼叫，它必須繼承自型別， `NSObject` 而 c # 方法則必須使用屬性以選取器名稱裝飾 `[Export]` 。 例如：

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

選取器名稱 **必須** 完全相符，包括所有的中間和尾端冒號 ( "：" ) （如果有的話）。

#### <a name="nsobject-constructors"></a>NSObject 函式

在所有衍生自的 Xamarin 中，大部分類別 `NSObject` 都會公開物件功能專屬的函式，但它們也會公開不同的非立即察覺的函式。

這些函式的使用方式如下：

```csharp
public Foo (IntPtr handle)
```

當執行時間需要將您的類別對應到未受管理的類別時，就會使用這個函式來具現化您的類別。 當您載入 XIB/筆尖檔案時，就會發生這種情況。  目前，C 執行時間會在非受控世界中建立物件，並呼叫這個函式以初始化 managed 端。

一般而言，您只需要使用控制碼參數呼叫基底函式，然後在主體中執行任何必要的初始化。

```csharp
public Foo ()
```

這是類別的預設的函式，而在 Xamarin 提供的類別中，這會初始化 NSObject 類別，而且兩端的所有類別都會將此方法連結至類別上的目標 C `init` 方法。

```csharp
public Foo (NSObjectFlag x)
```

這個函式是用來初始化實例，但防止程式碼在結尾呼叫目標 C "init" 方法。 當您在您的函式 `[Export]` 上使用) 或已經透過另一個平均值完成初始化時，您通常會使用 (此方式。

```csharp
public Foo (NSCoder coder)
```

當物件是從 NSCoding 實例初始化時，就會提供此函數。

#### <a name="exceptions"></a>例外狀況

在 c # 例外狀況中，Xamarin API 設計不會引發目標 C 例外狀況。 此設計會強制不會在一開始就將垃圾郵件傳送給目標-C，而且必須產生的任何例外狀況都會由系結本身產生，然後才會將不正確資料傳遞至目標 C 世界。

#### <a name="notifications"></a>通知

在 iOS 和 OS X 中，開發人員都可以訂閱由基礎平臺廣播的通知。 這是藉由使用方法來完成 `NSNotificationCenter.DefaultCenter.AddObserver` 。 `AddObserver`方法採用兩個參數，一個是您想要訂閱的通知，另一個則是引發通知時要叫用的方法。

在 Xamarin 和 Xamarin 中，各種通知的金鑰都裝載在觸發通知的類別上。 例如，所引發的通知會以 `UIMenuController` `static NSString` `UIMenuController` 名稱為 "Notification" 的類別，以屬性的形式裝載。

### <a name="memory-management"></a>記憶體管理

Xamarin 具有垃圾收集行程，可在不再使用資源時，為您釋放資源。 除了垃圾收集行程之外，衍生自的所有物件都會 `NSObject` 執行 `System.IDisposable` 介面。

#### <a name="nsobject-and-idisposable"></a>NSObject 和 IDisposable

公開 `IDisposable` 介面是協助開發人員釋放可能封裝大型記憶體區塊的物件 (例如， `UIImage` 可能看起來只是無害指標，但可能指向 2 mb 的影像) 和其他重要和有限的資源 (例如影片解碼緩衝區) 。

NSObject 會實 IDisposable 介面，也會實作為 [.Net 處置模式](/dotnet/standard/garbage-collection/implementing-dispose)。 這可讓 NSObject 子類別的開發人員覆寫 Dispose 行為，並視需要發行自己的資源。 例如，請考慮使用會保留一堆影像的此 view 控制器：

```csharp
class MenuViewController : UIViewController {
    UIImage breakfast, lunch, dinner;
    [...]
    public override void Dispose (bool disposing)
    {
        if (disposing){
             if (breakfast != null) breakfast.Dispose (); breakfast = null;
             if (lunch != null) lunch.Dispose (); lunch = null;
             if (dinner != null) dinner.Dispose (); dinner = null;
        }
        base.Dispose (disposing)
    }
}
```

處置 managed 物件時，它不再有用。 您仍然可能會有物件的參考，但此物件適用于目前所有意圖和用途不正確物件。 某些 .NET Api 會在您嘗試存取已處置物件上的任何方法時擲回 ObjectDisposedException 來確保這一點，例如：

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

即使您仍然可以存取變數 "image"，它其實是不正確參考，而且不再指向持有影像的目標 C 物件。

但是，以 c # 處置物件並不表示物件一定會被終結。 您只需要釋放 c # 對物件的參考。 Cocoa 環境可能已保留參考，以供自己使用。 例如，如果您將 UIImageView 的影像屬性設定為影像，然後再處置影像，則基礎 UIImageView 已取得自己的參考，而且會保留這個物件的參考，直到它完成使用為止。

#### <a name="when-to-call-dispose"></a>呼叫 Dispose 的時機

當您需要 Mono 以清除您的物件時，請呼叫 Dispose。 可能的使用案例是當 Mono 無法得知您的 NSObject 實際上持有重要資源（例如記憶體或資訊集區）的參考時。 在這些情況下，您應該呼叫 Dispose 來立即釋放記憶體的參考，而不是等候 Mono 執行垃圾收集迴圈。

在內部，當 Mono [從 c # 字串建立 NSString 的參考](~/ios/internals/api-design/nsstring.md)時，它會立即處置它們，以減少垃圾收集行程必須執行的工作量。 要處理的物件越少，GC 的執行速度就愈快。

#### <a name="when-to-keep-references-to-objects"></a>保留物件參考的時機

自動記憶體管理有一個副作用，就是 GC 將會去除未使用的物件，只要沒有它們的參考。 有時候可能會有驚訝的副作用，例如，如果您建立一個區域變數來保存最上層的視圖控制器或最上層的視窗，然後讓這些變數消失在後方。

如果您未將靜態或執行個體變數中的參考保留給物件，Mono 會很高興地在其上呼叫 Dispose () 方法，而且會釋放物件的參考。 因為這可能是唯一未完成的參考，所以目標 C 執行時間會為您終結物件。

## <a name="related-links"></a>相關連結

- [系結欄位](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)