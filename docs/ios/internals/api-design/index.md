---
title: Xamarin iOS API 設計
description: 本檔說明一些用來架構 Xamarin. iOS Api 的指導方針, 以及這些原則與目標-C 的關係。
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 11d9c3d27fa9cf9ba830648d95ef8af9ed386afb
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526480"
---
# <a name="xamarinios-api-design"></a>Xamarin iOS API 設計

除了屬於 Mono 的核心基類庫, [Xamarin](http://www.xamarin.com/iOS)也隨附各種 ios api 的系結, 可讓開發人員使用 Mono 建立原生 iOS 應用程式。

在 Xamarin 的核心中, 有一個 interop 引擎可將C#世界與目標-c 的世界銜接, 以及以 iOS C 為基礎的 api 系結 (例如 CoreGraphics 和[OpenGL ES](#OpenGLES))。

要與目標 C 程式碼通訊的低層級執行時間位於[MonoTouch. ObjCRuntime](#MonoTouch.ObjCRuntime)。 在此之上, 會提供[Foundation](#MonoTouch.Foundation)、CoreFoundation 和[UIKit](#MonoTouch.UIKit)的系結。

## <a name="design-principles"></a>設計原則

這些是我們一些適用于 Xamarin. iOS 系結的設計原則 (也適用于 macOS 上的目標-C 的 Mono 系結):

- 遵循[架構設計方針](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- 允許開發人員子類別化目標-C 類別:

  - 衍生自現有的類別
  - 呼叫基底函式以進行連鎖
  - 覆寫方法應使用C#的覆寫系統來完成
  - 子類別化應C#使用標準結構

- 不要向目標-C 選取器公開開發人員
- 提供呼叫任意目標 C 程式庫的機制
- 將常見的目標 C 工作簡化和硬性目標-C 的作業
- 將目標 C 屬性公開為C#屬性
- 公開強型別 API:

  - 增加型別安全
  - 將執行階段錯誤降至最低
  - 取得傳回類型的 IDE IntelliSense
  - 允許 IDE 快顯視窗檔

- 鼓勵在 IDE 中探索 Api:

  - 例如, 而不是公開弱類型陣列, 如下所示:
    
    ```objc
    NSArray *getViews
    ```
    公開強型別, 如下所示:
    
    ```csharp
    NSView [] Views { get; set; }
    ```
    
    這可讓 Visual Studio for Mac 在流覽 API 時執行自動完成的功能, 讓所有`System.Array`作業都能在傳回的值上使用, 並允許傳回值參與 LINQ。

- 原C#生類型:

  - [`NSString`滿`string`](~/ios/internals/api-design/nsstring.md)
  - `uint` C# `[Flags]`將應該已列舉的C# 和參數,轉換為具有屬性的列舉`int`和列舉
  - 不是類型中立`NSArray`的物件, 會將陣列公開為強型別陣列。
  - 針對事件和通知, 讓使用者有下列選擇:

    - 預設的強型別版本
    - 適用于 advanced 使用案例的弱型別版本

- 支援目標-C 委派模式:

    - C#事件系統
    - 將C#委派 (lambda、匿名方法和`System.Delegate`) 公開為目標-C api 做為區塊

### <a name="assemblies"></a>組件

Xamarin 包含許多構成*Xamarin 設定檔*的元件。 [[元件](~/cross-platform/internals/available-assemblies.md)] 頁面包含詳細資訊。

### <a name="major-namespaces"></a>主要命名空間 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

[ObjCRuntime](xref:ObjCRuntime)命名空間可讓開發人員橋接與C#目標-C 之間的世界。
這是根據 Cocoa # 和 Gtk # 的經驗, 特別針對 iOS 所設計的新系結。

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>打下

[基礎](xref:Foundation)命名空間提供的基本資料類型, 是設計用來與屬於 iOS 一部分的目標 c 基礎架構互通, 而這是在目標-c 中物件導向程式設計的基礎。

來自目標-C 的C#類別階層中的 Xamarin. iOS 鏡像。 例如, 可透過[NSObject](xref:Foundation.NSObject)使用C# [NSObject](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html)的目標 C 基類。

雖然此命名空間提供基礎目標-C 基礎類型的系結, 但在少數情況下, 我們已將基礎類型對應至 .NET 類型。 例如：

- 執行時間不會[處理 NSString](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html)和[NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html), 而是會在C#整個 API 中公開這些做為字串的和強[型](xref:System.String)別[陣列](xref:System.Array)。

- 這裡會公開各種協助程式 Api, 讓開發人員能夠系結協力廠商目標-C Api、其他 iOS Api 或目前不受 Xamarin 約束的 Api。

如需有關系結 Api 的詳細資訊, 請參閱 < [Xamarin](~/cross-platform/macios/binding/binding-types-reference.md)系結產生器一節。


##### <a name="nsobject"></a>NSObject

[NSObject](xref:Foundation.NSObject)類型是所有目標 C 系結的基礎。 Xamarin iOS 類型會從 iOS CocoaTouch Api 鏡像兩種類型的類別: C 類型 (通常稱為 CoreFoundation 類型) 和目標-C 類型 (這些全都衍生自 NSObject 類別)。

針對每個鏡像非受控類型的類型, 可以透過[Handle](xref:Foundation.NSObject.Handle)屬性取得原生物件。

雖然 Mono 會提供所有物件的垃圾收集, `Foundation.NSObject`但會執行[IDisposable](xref:System.IDisposable)介面。 這表示您可以明確釋放任何指定 NSObject 的資源, 而不需要等候垃圾收集行程啟動。 當您使用繁重的 NSObjects (例如, UIImages 可能會保存大型資料區塊的指標) 時, 這是很重要的。

如果您的型別需要執行決定性的結束, 請覆寫[NSObject](xref:Foundation.NSObject.Dispose(System.Boolean))的參數為「bool 處置」, 而如果設定為 true, 則表示會呼叫您的 dispose 方法, 因為使用者已明確呼叫物件上的 Dispose ()。 如果值為 false, 則表示您的 Dispose (bool 處置) 方法是從完成項執行緒上的 finalizer 呼叫。


##### <a name="categories"></a>Categories

從 Xamarin. iOS 8.10 開始, 您可以從C#建立目標 C 類別。

這是使用`Category`屬性來完成, 並指定要擴充為屬性引數的類型。 下列範例會擴充 NSString。

```csharp
[Category (typeof (NSString))]
```

每個類別方法都會使用一般機制, 將方法匯出至使用`Export`屬性的目標-C:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

所有 managed 擴充方法都必須是靜態的, 但在中C#, 您可以使用擴充方法的標準語法來建立目標-C 實例方法:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

而擴充方法的第一個引數將是叫用方法所在的實例。

完整範例:

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

這個範例會將原生 toUpper 實例方法新增至 NSString 類別, 而這個類別可以從目標-C 叫用。

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAudoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

這種情況很有用的一個案例是將方法新增至程式碼基底中的整組類別, 例如, 這會使`UIViewController`所有實例報告可以旋轉:

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

PreserveAttribute 是一個自訂屬性, 用來告訴 mtouch –在處理應用程式以減少其大小時, 會在階段中保留類型或類型的成員。

未由應用程式以靜態方式連結的每個成員都可能會移除。 因此, 這個屬性是用來標示未以靜態方式參考, 但應用程式仍需要的成員。

例如，若您是以動態方式來具現化類型，您可以保留您類型的預設建構函式。 若您使用的是 XML 序列化，則您可能會想要保留您類型的屬性。

您可以將此屬性套用到某個類型的每個成員，或是套用到類型本身。 如果您想要保留整個類型, 可以在類型上使用語法 [保留 (AllMembers = true)]。

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

[UIKit](xref:UIKit)命名空間包含以C#類別的形式組成 CocoaTouch 之所有 UI 元件的一對一對應。 API 已修改為遵循C#語言中使用的慣例。

C#委派是針對一般作業提供的。 如需詳細資訊, 請參閱[委派](#Delegates)一節。

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGLES

針對 OpenGLES, 我們會將[修改過](xref:OpenTK)的[opentk 簡介](http://www.opentk.com/)API 版本散發給已修改為使用 CoreGraphics 資料類型和結構的 OpenGL 的物件導向系結, 以及僅公開 iOS 上可用的功能。

OpenGLES 1.1 功能可透過 ES11.GL 類型取得, 記載于[此處](xref:OpenTK.Graphics.ES11.GL)的類型。

OpenGLES 2.0 功能可透過 ES20.GL 類型取得, 記載于[此處](xref:OpenTK.Graphics.ES20.GL)的類型。

OpenGLES 3.0 功能可透過 ES30.GL 類型取得, 記載于[此處](xref:OpenTK.Graphics.ES30.GL)的類型。


### <a name="binding-design"></a>系結設計

而不只是基礎目標-C 平臺的系結。 它將 .NET 類型系統和分派系統擴充為更好C#的 Blend 與目標-C。

就像 P/Invoke 是在 Windows 和 Linux 上叫用原生程式庫一樣有用的工具, 或者因為 IJW 支援可用於 Windows 上的 COM Interop, 但 Xamarin 則會擴充運行C#時間, 以支援將物件系結至目標 C 物件。

建立 Xamarin iOS 應用程式的使用者不需要接下來幾節中的討論, 但可協助開發人員瞭解如何完成工作, 以及在建立更複雜的應用程式時提供協助。



#### <a name="types"></a>型別

在合理的情況下C# , 會將類型公開而不是低層級的基礎C#類型。  這表示[API 會C#使用 "string" 類型而不是 NSString](~/ios/internals/api-design/nsstring.md) , 而且它會使用強C#類型陣列, 而不是公開 NSArray。

一般而言, 在 xamarin. iOS 和 xamarin. Mac 設計中, 不會公開`NSArray`基礎物件。 相反地, 執行時間會`NSArray`自動將轉換成某種`NSObject`類別的強型別陣列。 因此, Xamarin 不會公開弱類型的方法, 例如 GetViews 來傳回 NSArray:

```csharp
NSArray GetViews ();
```

相反地, 系結會公開強型別傳回值, 如下所示:

```csharp
UIView [] GetViews ();
```

在中`NSArray`, 有幾個公開的方法, 在您可能想要直接使用的`NSArray`角落案例中, 但在 API 系結中不鼓勵使用它們。

此外, 在**Classic API** , 而不是`CGRect`公開`CGPoint`和`CGSize` CoreGraphics API, 我們會將`System.Drawing` `RectangleF`它們取代為執行, `PointF`並`SizeF`因為他們可以協助開發人員保留使用 opentk 簡介的現有 OpenGL 程式碼。 使用新的64位**Unified API**時, 應該使用 CoreGraphics API。

<a name="Inheritance" />

#### <a name="inheritance"></a>繼承

Xamarin iOS API 的設計可讓開發人員以擴充C#類型的相同方式擴充原生的目標 C 類型、在衍生類別上使用 "override" 關鍵字, 以及使用 "base" C#連結到基底執行關鍵字.

這項設計可讓開發人員避免在開發過程中處理目標 C 選取器, 因為整個目標 C 系統已包裝在 Xamarin 程式庫內。


#### <a name="types-and-interface-builder"></a>類型和 Interface Builder

當您建立的 .net 類別是 Interface Builder 所建立之類型的實例時, 您需要提供採用單一`IntPtr`參數的函式。
這是用來系結 managed 物件實例與非受控物件的必要參數。
程式碼是由一行組成, 如下所示:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

<a name="Delegates" />


#### <a name="delegates"></a>委派

目標-C, C#並對每種語言的單字委派有不同的意義。

在目標-C 的世界中, 以及您會在線上找到的 CocoaTouch 檔中, 委派通常是會回應一組方法之類別的實例。 這與C#介面非常類似, 差別在於方法不一定是必要的。

這些委派在 UIKit 和其他 CocoaTouch Api 中扮演著重要的角色。 它們可用來完成各種工作:

- 將通知提供給您的程式碼 (類似于或C# Gtk + 中的事件傳遞)。
- 為數據視覺效果控制項執行模型。
- 以驅動控制項的行為。


程式設計模式的設計, 是為了將衍生類別的建立最小化, 以改變控制項的行為。 這個解決方案與其他 GUI 工具組在多年來進行的精神很類似:Gtk 的信號、Qt 位置、Winforms 事件、WPF/Silverlight 事件等等。 為了避免有數百個介面 (每個動作各一個), 或要求開發人員執行不需要的方法太多, 目標-C 支援選擇性的方法定義。 這不同于C#需要實作為所有方法的介面。

在 [目標-C] 類別中, 您會看到使用此程式設計模式的類別會公開一個`delegate`屬性 (通常稱為), 這是執行介面的必要部分以及零個或更多的選擇性元件所需的。

在 Xamarin 中, 會提供系結至這些委派的三種互斥機制:

1. [Via 事件](#Via_Events)。
2. [透過屬性的`Delegate`強型別](#StrongDelegate)
3. [透過`WeakDelegate`屬性鬆散輸入](#WeakDelegate)

例如, 請考慮[UIWebView](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html)類別。 這會分派給指派給[委派](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate)屬性的[UIWebViewDelegate](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html)實例。

<a name="Via_Events" />

##### <a name="via-events"></a>Via 事件

針對許多類型, Xamarin 會自動建立適當的委派, 以將`UIWebViewDelegate`呼叫轉送至C#事件。 針對 `UIWebView`：

- [WebViewDidStartLoad](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:)方法會對應到[UIWebView. LoadStarted](xref:UIKit.UIWebView.LoadStarted)事件。
- [WebViewDidFinishLoad](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:)方法會對應到[UIWebView. LoadFinished](xref:UIKit.UIWebView.LoadFinished)事件。
- [Web 工作: didFailLoadWithError](https://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:)方法會對應到[UIWebView. LoadError](xref:UIKit.UIWebView.LoadError)事件。

例如, 這個簡單的程式會記錄載入 web 視圖時的開始和結束時間:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>Via 屬性

當事件可能有多個訂閱者時, 事件會很有用。 此外, 事件僅限於程式碼中沒有傳回值的情況。

在程式碼預期會傳回值的情況下, 我們選擇改為 [屬性]。 這表示在給定的時間內, 只能在物件中設定一個方法。

例如, 您可以使用這種機制, 在的處理常式`UITextField`上關閉螢幕上的鍵盤:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

在此情況下, `UITextField`的屬性會採用傳回bool值的委派做為引數,並判斷此欄位是否應該在按下[返回]按鈕時執行某些動作。`ShouldReturn` 在我們的方法中, 我們會將*true*傳回給呼叫者, 但我們也會從螢幕中移除鍵盤 (當欄位`ResignFirstResponder`呼叫時就會發生這種情況)。

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>透過委派屬性的強型別

如果您不想要使用事件, 您可以提供自己的[UIWebViewDelegate](xref:UIKit.UIWebViewDelegate)子類別, 並將它指派給[UIWebView 委派](xref:UIKit.UIWebView.Delegate)屬性。 一旦指派 UIWebView 之後, UIWebView 事件分派機制將無法再運作, 而且會在對應的事件發生時叫用 UIWebViewDelegate 方法。

例如, 這個簡單的類型會記錄載入 web 視圖所需的時間:

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

上述程式碼會用於:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

上述程式會建立 UIWebViewer, 並指示它將訊息傳送至「通知」的實例, 這是我們為了回應訊息而建立的類別。

此模式也可用來控制某些控制項的行為, 例如在 UIWebView 案例中, [UIWebView. ShouldStartLoad](xref:UIKit.UIWebView.ShouldStartLoad)屬性允許`UIWebView`實例控制是否`UIWebView`要載入頁面。

此模式也會用來提供幾個控制項的隨選資料。 例如, [UITableView](xref:UIKit.UITableView)控制項是功能強大的資料表轉譯控制項–而且外觀和內容都是由[UITableViewDataSource](xref:UIKit.UITableViewDataSource)的實例所驅動

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>透過 WeakDelegate 屬性鬆散輸入

除了強型別屬性之外, 還有一個弱型別委派, 可讓開發人員在需要時以不同的方式系結專案。
在 Xamarin 的系`Delegate`結中公開強型別屬性的任何地方, 也`WeakDelegate`會公開對應的屬性。

使用`WeakDelegate`時, 您必須負責使用[Export](xref:Foundation.ExportAttribute)屬性來指定選取器, 以適當裝飾您的類別。 例如：

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

請注意, 一旦`WeakDelegate`指派屬性之後`Delegate` , 就不會使用屬性。 此外, 如果您在想要 [匯出] 的繼承基類中執行方法, 您必須將它設為公用方法。


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>將目標-C 委派模式對應到 C&#35;

當您看到如下所示的目標-C 範例:

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

這會指示語言建立並建立 "SomethingDelegate" 類別的實例, 並將值指派給 foo 變數上的委派屬性。 這項機制受到 Xamarin 的支援, C#語法如下:

```csharp
foo.Delegate = new SomethingDelegate ();
```

在 Xamarin 中, 我們已提供對應至目標-C 委派類別的強型別類別。 若要使用這些專案, 您將會子類別化並覆寫由 Xamarin. iOS 的實作為定義的方法。 如需其使用方式的詳細資訊, 請參閱下面的「模型」一節。


##### <a name="mapping-delegates-to-c35"></a>將委派對應至 C&#35;

UIKit 一般會使用兩種形式的目標-C 委派。

第一個表單會提供元件模型的介面。 例如, 做為視需要提供資料的機制, 例如清單視圖的資料儲存設備。  在這些情況下, 您應該一律建立適當類別的實例, 並指派變數。

在下列範例中, 我們為使用`UIPickerView`字串的模型提供了執行:

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

第二種形式是提供事件的通知。 在這些情況下, 雖然我們仍然以上面所述的形式公開 API, 我們也C#會提供事件, 這應該更容易用於快速作業, 並與中C#的匿名委派和 lambda 運算式整合。

例如, 您可以訂閱`UIAccelerometer`事件:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

有兩個選項可供使用, 但身為程式設計人員, 您必須挑選其中一個。 如果您建立自己的強型別回應器/委派實例並加以指派, C#事件將無法正常運作。 如果您使用C#事件, 則永遠不會呼叫回應者/委派類別中的方法。

先前使用`UIWebView`的範例可以使用C# 3.0 lambda 來撰寫, 如下所示:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>回應事件

在目標 C 程式碼中, 有時候多個控制項的多個控制項和資訊提供者的事件處理常式, 將裝載于相同的類別中。 這是可能的, 因為類別會回應訊息, 而且只要類別回應訊息, 就可以將物件連結在一起。

如先前所述, Xamarin 支援以事件為C#基礎的程式設計模型和目標-C 委派模式, 您可以在其中建立新的類別來執行委派並覆寫所需的方法。

也可以支援目標-C 的模式, 其中多個不同作業的回應程式全都裝載于相同類別的實例中。 不過, 若要這樣做, 您必須使用 Xamarin. iOS 系結的低層級功能。

例如, 如果您想要讓類別在類別的相同實例`UITextFieldDelegate.textFieldShouldClear`中回應: 訊息`UIWebViewDelegate.webViewDidStartLoad`和:, 您就必須使用 [Export] 屬性宣告:

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

方法C#的名稱並不重要;重要的是傳遞至 [Export] 屬性的字串。

使用這種程式設計樣式時, 請確定C#參數符合執行時間引擎將傳遞的實際類型。

<a name="Models" />

#### <a name="models"></a>模型

在 UIKit 儲存設施中, 或在使用協助程式類別來執行的回應中, 這些通常稱為「委派」 (delegate), 並實作為通訊協定。

目標-C 通訊協定就像介面, 但支援選擇性的方法, 也就是說, 並非所有方法都必須執行, 才能讓通訊協定正常操作。

有兩種方式可以執行模型。 您可以手動執行, 或使用現有的強型別定義。


當您嘗試執行的類別尚未受到 Xamarin 的系結時, 需要手動機制。 這麼簡單:

- 標示您的類別以向執行時間註冊
- 在您想要覆寫的每個方法上, 將 [Export] 屬性與實際的選取器名稱一起套用
- 具現化類別, 並將其傳遞。

例如, 下列程式只會執行 UIApplicationDelegate 通訊協定定義中的其中一個選擇性方法:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

目標-C 選取器名稱 ("applicationDidFinishLaunching:") 是以 Export 屬性宣告, 而類別則是以`[Register]`屬性註冊。

Xamarin 提供強型別宣告 (可供使用), 而不需要手動系結。 為了支援此程式設計模型, Xamarin. iOS 執行時間支援類別宣告的 [Model] 屬性。 這會通知執行時間, 除非明確地執行方法, 否則它不應該連線類別中的所有方法。

這表示在 UIKit 中, 代表具有選擇性方法之通訊協定的類別會以下面方式撰寫:

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

當您想要執行只會實作為部分方法的模型時, 您只需要覆寫您感興趣的方法, 並忽略其他方法。 執行時間只會連結覆寫的方法, 而不會連結至目標-C 世界的原始方法。

等同于先前的手動範例為:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

其優點是不需要深入探索目標 C 標頭檔來尋找選取器、引數的類型, 或與的對應C#, 以及從 Visual Studio for Mac 取得 intellisense 以及強式類型


#### <a name="xib-outlets-and-c35"></a>XIB 輸出和 C&#35;

> [!IMPORTANT]
> 本節說明使用 XIB 檔時的 IDE 與輸出口的整合。 使用 Xamarin Designer for iOS 時, 會在 IDE 的 [屬性] 區段中, 于 [**識別 > 名稱**] 下輸入名稱來取代此項, 如下所示:
>
> [![](images/designeroutlet.png "在 iOS 設計工具中輸入專案名稱")](images/designeroutlet.png#lightbox)
>
>如需 iOS 設計工具的詳細資訊, 請參閱[Ios 設計工具簡介](~/ios/user-interface/designer/introduction.md#how-it-works)檔。

這是輸出與C#的整合方式的低層級描述, 為 Xamarin 的先進使用者提供。 使用 Visual Studio for Mac 時, 對應會在幕後使用產生的程式碼為您自動完成。

當您使用 Interface Builder 設計使用者介面時, 您只會設計應用程式的外觀, 並會建立一些預設連接。 如果您想要以程式設計方式提取資訊、在執行時間改變控制項的行為, 或在執行時間修改控制項, 則必須將一些控制項系結至您的 managed 程式碼。

這只需要幾個步驟就可以完成:

1. 將 [**輸出**] 宣告新增至您**的檔案擁有**者。
1. 將您的控制項連接到檔案**的擁有**者。
1. 將 UI 和連接儲存到您的 XIB/筆尖檔案。
1. 在執行時間載入筆尖檔案。
1. 存取插座變數。


使用 Interface Builder 建立介面的 Apple 檔中會涵蓋步驟 (1) 到 (3)。

使用 Xamarin 時, 您的應用程式必須建立衍生自 UIViewController 的類別。 它會實作為如下所示:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

然後, 若要從筆尖檔案載入您的 ViewController, 請執行下列動作:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

這會從筆尖載入使用者介面。 接下來, 若要存取輸出, 必須通知執行時間我們想要存取它們。 若要這麼做, `UIViewController`子類別必須宣告屬性, 並使用 [Connect] 屬性為其加上批註。 與下列類似：

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

屬性的實作為實際提取並儲存實際原生類型值的。

使用 Visual Studio for Mac 和 InterfaceBuilder 時, 您不需要擔心這一點。 Visual Studio for Mac 會在編譯為專案一部分的部分類別中, 自動將所有宣告的輸出與程式碼鏡像。

#### <a name="selectors"></a>選取器

目標-C 程式設計的核心概念是選取器。 您通常會跨越需要傳遞選取器的 Api, 或預期您的程式碼會回應選取器。

在中C#建立新的選取器非常簡單, 您只需建立`ObjCRuntime.Selector`類別的新實例, 並在 API 中的任何位置使用結果 (需要它)。 例如：

```csharp
var selector_add = new Selector ("add:plus:");
```

針對C#方法回應選取器呼叫, 它必須繼承自`NSObject`型別, 而且C#方法必須使用`[Export]`屬性 (attribute) 以選取器名稱裝飾。 例如：

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

請注意, 選取器名稱**必須**完全相符, 包括所有中間和尾端冒號 (":") (如果有的話)。

#### <a name="nsobject-constructors"></a>NSObject 的構造函式

在 Xamarin. iOS 中, 衍生自`NSObject`的大部分類別都會公開物件功能特有的函式, 但它們也會公開不會立即明顯的各種不同的函式。

使用的函式如下所示:

```csharp
public Foo (IntPtr handle)
```

當執行時間需要將您的類別對應至未受管理的類別時, 此函式會用來具現化您的類別。 當您載入 XIB/筆尖檔案時, 就會發生這種情況。  此時, 目標-C 執行時間會在非受控世界中建立物件, 而且會呼叫這個函式來初始化 managed 端。

一般來說, 您只需要使用 handle 參數呼叫基底函式, 並在主體中執行任何必要的初始化。

```csharp
public Foo ()
```

這是類別的預設的函式, 而在 Xamarin 中提供的類別中, 這會初始化 NSObject 類別, 而在結尾的所有類別會將其連結至類別上的目標-C `init`方法。

```csharp
public Foo (NSObjectFlag x)
```

此函式是用來初始化實例, 但會防止程式碼在結尾呼叫目標-C "init" 方法。 當您已註冊初始化 (當您在您的函式上使用`[Export]`時), 或已透過另一個平均值完成初始化時, 通常會使用此方式。

```csharp
public Foo (NSCoder coder)
```

在從 NSCoding 實例初始化物件的情況下, 會提供這個函式。 如需詳細資訊, 請參閱 Apple 的封存[和序列化程式設計指南。](https://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>例外狀況

「Xamarin iOS API」設計並不會將目標 C 例外狀況C#引發為例外狀況。 此設計會強制不會在一開始就將任何垃圾郵件傳送給目標-C 世界, 而且必須產生的任何例外狀況都是由系結本身所產生, 然後才會將無效資料傳遞給目標-C 世界。

#### <a name="notifications"></a>通知

在 iOS 和 OS X 中, 開發人員都可以訂閱由基礎平臺廣播的通知。 這是使用`NSNotificationCenter.DefaultCenter.AddObserver`方法來完成。 `AddObserver`方法接受兩個參數, 一個是您想要訂閱的通知, 另一個則是在引發通知時所要叫用的方法。

在 [Xamarin] 和 [Xamarin] 中, 各種通知的金鑰會裝載于觸發通知的類別上。 例如, 所引發`UIMenuController`的通知會裝載為`static NSString` `UIMenuController`類別中以「通知」名稱結尾的屬性。

### <a name="memory-management"></a>記憶體管理

Xamarin 具有垃圾收集行程, 會在您不再使用資源時, 負責為您釋放它們。 除了垃圾收集行程以外, 所有衍生自`NSObject`的物件都會`System.IDisposable`執行介面。

#### <a name="nsobject-and-idisposable"></a>NSObject 和 IDisposable

公開介面是協助開發人員釋放可能封裝大型記憶體區塊之物件的便利方式 (例如`UIImage` , 可能看起來只像無害指標, 但可能指向 2 mb 的影像)。 `IDisposable`) 和其他重要和有限的資源 (例如影片解碼緩衝區)。

NSObject 會實作為 IDisposable 介面, 也會執行[.Net Dispose 模式](https://msdn.microsoft.com/library/fs2xkftw.aspx)。 這可讓子類別 NSObject 的開發人員覆寫處置行為, 並視需要發行自己的資源。 例如, 請考慮使用此視圖控制器來保留一堆映射:

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

當受管理物件被處置時, 就不再有用了。 您可能還是會有物件的參考, 但物件是用於所有意圖, 而目的是在此時無效。 如果您嘗試存取已處置物件上的任何方法, 某些 .NET Api 會藉由擲回 ObjectDisposedException 來確保此情況, 例如:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

即使您仍然可以存取變數「映射」, 它其實是不正確參考, 而且不會再指向保留映射的目標-C 物件。

但是, 在中C#處置物件並不表示物件一定會終結。 您所做的就是釋放對C#物件的參考。 Cocoa 環境可能會保留參考以供自己使用。 例如, 如果您將 UIImageView 的影像屬性設定為影像, 然後處置該影像, 則基礎 UIImageView 已取得其本身的參考, 並會保留這個物件的參考, 直到它完成使用為止。

#### <a name="when-to-call-dispose"></a>呼叫 Dispose 的時機

當您需要 Mono 來去除物件時, 應該呼叫 Dispose。 可能的使用案例是當 Mono 不知道您的 NSObject 實際上是否持有對重要資源 (例如記憶體或資訊集區) 的參考時。 在這些情況下, 您應該呼叫 Dispose 來立即釋放記憶體的參考, 而不是等待 Mono 執行垃圾收集迴圈。

就內部而言, 當 Mono[從C#字串建立 NSString 參考](~/ios/internals/api-design/nsstring.md)時, 它會立即處置它們, 以減少垃圾收集行程必須執行的工作量。 要處理的物件越少, GC 執行的速度就愈快。

#### <a name="when-to-keep-references-to-objects"></a>保留物件參考的時機

自動記憶體管理的一個副作用是, 只要沒有任何參考, GC 就會清除未使用的物件。 這有時候可能會產生令人驚訝的副作用, 例如, 如果您建立了一個本機變數來保存最上層視圖控制器, 或最上層視窗, 然後讓它們消失在後方。

如果您未在物件中保留靜態或執行個體變數的參考, Mono 會很高興地對其呼叫 Dispose () 方法, 而且它們會釋放物件的參考。 由於這可能是唯一未處理的參考, 因此, 目標-C 執行時間會為您終結物件。

## <a name="related-links"></a>相關連結

- [系結欄位](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
