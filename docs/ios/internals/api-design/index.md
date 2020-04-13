---
title: Xamarin.iOS API 設計
description: 用於構建 Xamarin.iOS API 的指導原則,以及這些原則與目標 C 的關係。
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: a2435b30b7d5b468fca6c55d295c87b9a0d20652
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304797"
---
# <a name="xamarinios-api-design"></a>Xamarin.iOS API 設計

除了作為單聲道一部分的核心基類庫外[,Xamarin.iOS](~/ios/index.yml)還附帶各種 iOS API 的綁定,允許開發人員使用 Mono 創建本機 iOS 應用程式。

在 Xamarin.iOS 的核心,有一個互操作引擎,將 C# 世界與目標 C 世界以及基於 iOS C 的 API(如 CoreGraphics 和[OpenGL ES](#opengles))綁定。"

與 Objective-C 程式碼通訊的低級運行時位於[MonoTouch.ObjCRuntime](#objcruntime)中。 除此之外,還提供了[基礎](#foundation)、CoreFoundation 和[UIKit](#uikit)的綁定。

## <a name="design-principles"></a>設計原則

以下是我們對 Xamarin.iOS 綁定的一些設計原則(它們也適用於 Xamarin.Mac,mac 上用於目標 C 的單聲道綁定):

- 遵循[框架設計指南](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- 允許開發人員子類別 Objective-C 類別:

  - 派生自現有類
  - 將基本建構函數呼叫鍊
  - 重寫方法應使用 C# 的重寫系統完成
  - 子類應使用 C# 標準建構

- 不要將開發人員暴露給目標 C 選擇器
- 提供呼叫任意目標 C 函式庫機制
- 使共同的目標 C 任務變得簡單和困難的目標 C 任務成為可能
- 將目標 C 屬性公開為 C# 屬性
- 公開強類型 API:

  - 提高類型安全性
  - 最小化執行時發生錯誤
  - 取得傳回類型的 IDE 感應
  - 允許 IDE 彈出式文件

- 鼓勵 IDE 中對 API 的探索:

  - 例如,而不是公開如下所示的弱類型陣列:

    ```objc
    NSArray *getViews
    ```

    公開強類型,如下所示:

    ```csharp
    NSView [] Views { get; set; }
    ```

    這使 Visual Studio Mac 能夠在流覽 API 時自動`System.Array`完成,使返回值上的所有 操作都可用,並允許返回值參與 LINQ。

- 本機 C# 類型:

  - [`NSString`成為`string`](~/ios/internals/api-design/nsstring.md)
  - 將`int``uint`本應 有參數轉換為 C# 枚舉和 C# 枚舉屬性`[Flags]`
  - 而不是類型中性`NSArray`物件,將數組公開為強類型陣列。
  - 對於事件和通知,請為使用者提供在:

    - 預設情況下,強類型版本
    - 進階用類別的弱類型版本

- 支援目標 C 委託模式:

  - C# 事件系統
  - 將 C# 委託 (lambdas、`System.Delegate`匿名方法與 ) 公開為目標 C API 作為區塊

### <a name="assemblies"></a>組件

Xamarin.iOS 包括構成*Xamarin.iOS 配置檔*的多個程式集。 [程式集「](~/cross-platform/internals/available-assemblies.md)頁包含詳細資訊」。

### <a name="major-namespaces"></a>主要命名空間

#### <a name="objcruntime"></a>ObjC潤時間

[ObjCRuntime](xref:ObjCRuntime)命名空間允許開發人員在 C# 和 Objective-C 之間架橋世界。
這是一個新的綁定,專為 iOS 設計,基於 Cocoa# 和 Gtk+ 的體驗。

#### <a name="foundation"></a>Foundation

[基礎](xref:Foundation)命名空間提供基本資料類型,旨在與作為 iOS 一部分的目標 C 基礎框架進行互通,它是目標 C 中面向物件程式設計的基礎。

Xamarin.iOS 在 C# 中鏡像來自目標 C 的類的層次結構。 例如,目標 C 基類 NSObject 可透過[Foundation.NSObject](xref:Foundation.NSObject)從 C# 可用。

儘管此命名空間為基礎 Objective-C 基礎類型提供了綁定,但在某些情況下,我們已經將基礎類型映射到 .NET 類型。 例如：

- 執行時不處理 NSString 與[NSArray,](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html)而是在整個 API 中公開這些作為 C#[字串](xref:System.String)與強類型[陣列](xref:System.Array)。

- 此處公開了各種説明程式 API,允許開發人員綁定第三方 Objective-C API、當前不受 Xamarin.iOS 約束的其他 iOS API 或 API。

有關綁定 API 的更多詳細資訊,請參閱[Xamarin.iOS 綁定生成器](~/cross-platform/macios/binding/binding-types-reference.md)部分。

##### <a name="nsobject"></a>NSObject

[NSObject](xref:Foundation.NSObject)類型是所有目標 C 綁定的基礎。 Xamarin.iOS 類型反映了 iOS CocoaTouch API 中的兩類類型:C 類型(通常稱為 CoreFoundation 類型)和目標 C 類型(這些類型都派生自 NSObject 類)。

對於鏡像非託管類型的每種類型,可以通過[Handle](xref:Foundation.NSObject.Handle)屬性獲取本機物件。

雖然 Mono 將為所有物件提供垃圾回收,`Foundation.NSObject`但實現[System.I 可處理介面](xref:System.IDisposable)。 這意味著您可以顯式釋放任何給定 NSObject 的資源,而無需等待垃圾回收器啟動。 當您使用重的 NSObjects 時,這一點很重要,例如,可能保存指向大數據塊的指標的 UIImage。

如果類型需要執行確定性定稿,請重寫[NSObject.Dispose(bool) 方法](xref:Foundation.NSObject.Dispose(System.Boolean))要處置的參數是"布爾處置",如果設置為 true,則意味著將調用 Dispose 方法,因為使用者在物件上顯式調用 Dispose ()。 如果該值為 false,則意味著從終結器線程上的終結器調用 Dispose(布林處置)方法。

##### <a name="categories"></a>類別

從 Xamarin.iOS 8.10 開始,可以從 C# 創建目標 C 類別。

這是使用`Category`屬性完成的,指定要作為參數擴展到屬性的類型。 例如,以下示例將擴展 NSString。

```csharp
[Category (typeof (NSString))]
```

每個類別方法都使用正常機制使用`Export`屬性將方法匯出到 Objective-C:

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

所有託管延伸方法都必須是靜態的,但可以使用 C# 中的擴充方法的標準語法建立 Objective-C 實例方法:

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

對擴展方法的第一個參數將是調用該方法的實例。

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

此示例將本機到上部實例方法添加到 NSString 類,可以從 Objective-C 調用。

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

其中一種情況非常有用,即將方法添加到代碼庫中的一整組類,例如,這將使所有`UIViewController`實例都報告它們可以旋轉:

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

##### <a name="preserveattribute"></a>保留屬性

PreserveAttribute 是一個自定義屬性,用於告訴 mtouch ( Xamarin.iOS 部署工具 ) 在處理應用程式以減小其大小的階段保留類型或類型的成員。

未由應用程式以靜態方式連結的每個成員都可能會移除。 因此,此屬性用於標記不是靜態引用但應用程式仍然需要的成員。

例如，若您是以動態方式來具現化類型，您可以保留您類型的預設建構函式。 若您使用的是 XML 序列化，則您可能會想要保留您類型的屬性。

您可以將此屬性套用到某個類型的每個成員，或是套用到類型本身。 如果要保留整個類型,可以在類型上使用語法 [保留 (All 成員 = true)。]

#### <a name="uikit"></a>UIKit

[UIKit](xref:UIKit)命名空間包含一對一映射,以 C# 類的形式組成 CocoaTouch 的所有 UI 元件。 API 已修改,以遵循 C# 語言中使用的約定。

為常見操作提供 C# 委託。 有關詳細資訊,請參閱[委託](#delegates)部分。

#### <a name="opengles"></a>開放

對於 OpenGLES,我們分發[OpenTK](https://opentk.net/) API[的修改版本](xref:OpenTK),這是一個面向物件的綁定,已修改為使用 CoreGraphics 數據類型和結構,並且僅公開 iOS 上可用的功能。

OpenGLES 1.1 功能可通過[ES11.GL類型](xref:OpenTK.Graphics.ES11.GL)獲得。

OpenGLES 2.0 功能可通過[ES20.GL 類型](xref:OpenTK.Graphics.ES20.GL)獲得。

OpenGLES 3.0 功能可通過[ES30.GL類型](xref:OpenTK.Graphics.ES30.GL)獲得。

### <a name="binding-design"></a>繫結設計

Xamarin.iOS 不僅僅是對底層 Objective-C 平台的綁定。 它擴展了 .NET 類型系統和調度系統,以更好地混合 C# 和目標 C。

正如 P/Invoke 是呼叫 Windows 和 Linux 上的本機庫的有用工具一樣,或者 IJW 支援可用於 Windows 上的 COM 互通,Xamarin.iOS 擴展執行時,以支援將 C# 物件綁定到 Objective-C 物件。

對於創建 Xamarin.iOS 應用程式的用戶來說,接下來的幾個部分中的討論並不需要,但將説明開發人員瞭解操作方式,並在創建更複雜的應用程式時幫助他們。

#### <a name="types"></a>型別

在有意義的地方,C# 類型會公開而不是低級基礎類型,並暴露給 C# 宇宙。  這意味著[API 使用 C# "字串" 類型而不是 NSString,](~/ios/internals/api-design/nsstring.md)並且它使用強類型 C# 陣列,而不是公開 NSArray。

通常,在 Xamarin.iOS 和 Xamarin.Mac 設計中`NSArray`,底層物件不會公開。 相反,運行時會自動將`NSArray`轉換為`NSObject`某些 類的強類型陣組。 因此,Xamarin.iOS 不會公開弱類型方法(如 GetViews)來返回 NSArray:

```csharp
NSArray GetViews ();
```

相反,綁定公開強類型返回值,如下所示:

```csharp
UIView [] GetViews ();
```

對於可能希望直接使用`NSArray``NSArray`的角情況,會公開一些方法,但在 API 綁定中不鼓勵使用它們。

此外,在**經典 API**`CGRect`中`CGPoint`,`CGSize`而不是公開 ,並從 CoreGraphics API 中`System.Drawing`, 我們用`RectangleF`實現`PointF`替換`SizeF`了這些,因為它們將幫助開發人員保留使用 OpenTK 的現有 OpenGL 代碼。 使用新的 64 位**元統一 API**時,應使用核心圖形 API。

#### <a name="inheritance"></a>繼承

Xamarin.iOS API 設計允許開發人員擴展本機 Objective-C 類型的方式與擴展 C# 類型的方式相同,使用派生類上的"覆蓋"關鍵字,並使用"base"C# 關鍵字連結到基本實現。

此設計允許開發人員避免將目標 C 選擇器作為開發過程的一部分來處理,因為整個 Objective-C 系統已包裝在 Xamarin.iOS 庫中。

#### <a name="types-and-interface-builder"></a>類型與介面產生器

創建 .NET 類時,它們是介面生成器創建的類型實例,需要提供一個採用`IntPtr`單個 參數的構造函數。
這將需要將託管物件實例與非託管物件綁定。
代碼由一行組成,如下所示:

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

#### <a name="delegates"></a>委派

目標 C 和 C# 對於每種語言中的單詞委託具有不同的含義。

在 Objective-C 世界中,以及您將線上找到的有關 CocoaTouch 的文檔中,委託通常是將回應一組方法的類的實例。 這與 C# 介面非常相似,區別在於這些方法並不總是必填的。

這些代表在 UIKit 和其他 CocoaTouch API 中發揮著重要作用。 它們用於完成各種工作:

- 向程式碼提供通知(類似於 C# 或 Gtk+中的事件傳遞)。
- 實現數據可視化控件的模型。
- 驅動控件的行為。

程式設計模式旨在最大限度地減少派生類的創建,以更改控制項的行為。 此解決方案在精神上與其他 GUI 工具套件多年來所做的類似:Gtk 的訊號、Qt 插槽、Winforms 事件、WPF/Silverlight 事件等。 為了避免有數百個介面(每個操作一個)或要求開發人員實現不需要的方法太多,Objective-C 支援可選方法定義。 這與需要實現所有方法的 C# 介面不同。

在 Objective-C 類中,您將看到使用此程式設計模式的類公開一個屬性,`delegate`通常稱為,該屬性是實現介面的必需部分和可選部分的零個或多個。

在 Xamarin.iOS 中,提供了三個相互排斥的機制,以綁定到這些委託:

1. [通過事件](#via-events)。
2. [使用`Delegate`屬性進行強型態](#strongly-typed-via-a-delegate-property)
3. [使用`WeakDelegate`屬性鬆結鍵](#loosely-typed-via-the-weakdelegate-property)

例如,請考慮 UIWebView 類。 此調度到 UIWebView 委託實例,該實例分配給委託屬性。

##### <a name="via-events"></a>通過活動

對於許多類型,Xamarin.iOS將自動創建一個適當的委託,將`UIWebViewDelegate`調用轉發到 C# 事件。 針對 `UIWebView`：

- WebViewDidStartLoad 方法映射到[UIWebView.LoadStart](xref:UIKit.UIWebView.LoadStarted)事件。
- WebViewDidFinishLoad 方法映射到[UIWebView.Load 完成](xref:UIKit.UIWebView.LoadFinished)事件。
- WebView:didFailLoad與錯誤方法映射到[UIWebView.LoadError](xref:UIKit.UIWebView.LoadError)事件。

例如,此簡單程式記錄載入 Web 檢視時的開始和結束時間:

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```

##### <a name="via-properties"></a>通過屬性

當事件可能有多個訂閱者時,事件很有用。 此外,事件僅限於代碼中沒有返回值的情況。

對於代碼應返回值的情況,我們選擇了屬性。 這意味著在給定時間只能在物件中設置一個方法。

例如,可以使用此機制關閉處理程式上的鍵盤: `UITextField`

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

本`UITextField`例`ShouldReturn`中的 屬性將返回 bool 值並確定 TextField 是否應在按下 Return 按鈕的情況下執行某些操作的委託作為參數。 在我們的方法中,我們返回*true*到調用方,但我們也會從螢幕中刪除鍵盤(當文本欄位`ResignFirstResponder`調用 時,就會發生這種情況)。

##### <a name="strongly-typed-via-a-delegate-property"></a>通過委託屬性進行強類型

如果您不希望使用事件,則可以提供自己的[UIWebView 委託](xref:UIKit.UIWebViewDelegate)子類並將其分配給[UIWebView.委託](xref:UIKit.UIWebView.Delegate)屬性。 分配 UIWebView.委託後,UIWebView 事件調度機制將不再起作用,並且在發生相應事件時將調用 UIWebView 委派方法。

例如,此簡單類型記錄載入 Web 檢視所需的時間:

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

上述代碼用於以下代碼中:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

上述內容將創建一個 UIWebViewer,它將指示它將消息發送到通知器的實例,該類是我們為回應消息而創建的類。

此模式還用於控制某些控制項的行為,例如在 UIWebView 案例中[,UIWebView.應時載入](xref:UIKit.UIWebView.ShouldStartLoad)屬性`UIWebView`允許`UIWebView`實例控制 是否會載入頁面。

該模式還用於提供幾個控制件的按需數據。 例如[,UITableView](xref:UIKit.UITableView)控制項是一個強大的表呈現控制, 並且外觀和內容都由[UITableViewDataSource](xref:UIKit.UITableViewDataSource)的實體驅動程式

### <a name="loosely-typed-via-the-weakdelegate-property"></a>通過弱委託屬性鬆散鍵入

除了強類型屬性之外,還有一個弱類型化委託,允許開發人員根據需要以不同的方式綁定內容。
在 Xamarin.iOS 綁定中公開`Delegate`強類型 屬性的任何地方,`WeakDelegate`也會公開相應的 屬性。

使用`WeakDelegate`時 ,您負責使用[Export](xref:Foundation.ExportAttribute)屬性正確修飾類以指定選擇器。 例如：

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

請注意,`WeakDelegate`一旦分配了該屬性,將`Delegate`不會 使用該屬性。 此外,如果在要 [export] 的繼承基類中實現該方法,則必須將其作為公共方法。

## <a name="mapping-of-the-objective-c-delegate-pattern-to-c"></a>將目標 C 委託模式映射到 C\#

當您看到如下所示的目標 C 範例時:

```objc
foo.delegate = [[SomethingDelegate] alloc] init]
```

這指示語言創建和構造類「某物委託」的實例,並將值分配給 foo 變數上的委託屬性。 Xamarin.iOS 和 C# 支援此機制,其語法是:

```csharp
foo.Delegate = new SomethingDelegate ();
```

在 Xamarin.iOS 中,我們提供了映射到 Objective-C 委託類的強類型類。 要使用它們,您將對 Xamarin.iOS 的實現定義的方法進行子類化並重寫。 有關它們工作方式的詳細資訊,請參閱下面的"模型"一節。

### <a name="mapping-delegates-to-c"></a>將委託映射到 C\#

UIKit 通常以兩種形式使用目標 C 委託。

第一個表單提供元件模型的介面。 例如,作為一種根據檢視按需提供數據的機制,例如 List 檢視的數據存儲設施。  在這些情況下,應始終創建適當類的實例並分配變數。

在下面的範例中,我們為使用字串的`UIPickerView`模型提供了實現:

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

第二個表單是提供事件的通知。 在這些情況下,儘管我們仍以上述形式公開 API,但我們還提供 C# 事件,這些事件應該更簡單地用於快速操作,並與 C# 中的匿名委託和 lambda 運算式集成。

例如,您可以訂閱`UIAccelerometer`事件:

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

這兩個選項在它們有意義的地方可用,但作為程式師,您必須選擇其中一個。 如果創建自己的強類型回應器/委託實例並分配它,則 C# 事件將不起作用。 如果使用 C# 事件,則永遠不會呼叫回應器/委託類中的方法。

可以使用`UIWebView`的前面的範例可以使用 C# 3.0 lambdas 撰寫,如下所示:

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```

#### <a name="responding-to-events"></a>回應事件

在 Objective-C 代碼中,有時多個控制項的事件處理程式和多個控制項的資訊提供程式將託管在同一個類中。 這是可能的,因為類回應消息,並且只要類回應消息,就可以將對象連結在一起。

如之前詳述的,Xamarin.iOS 既支援基於 C# 事件的程式設計模型,又支援 Objective-C 委託模式,您可以在其中創建實現委託並重寫所需方法的新類。

還可以支援 Objective-C 的模式,其中多個不同操作的回應者都託管在類的同一實例中。 但是,要執行此操作,您必須使用 Xamarin.iOS 綁定的低級功能。

例如,如果希望類別同時回應`UITextFieldDelegate.textFieldShouldClear`:`UIWebViewDelegate.webViewDidStartLoad`訊息與 : 在類別的同一個實體中,您必須使用 [export] 屬性聲明:

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

方法的 C# 名稱並不重要;因此,這些方法的 C# 名稱並不重要。重要的是傳遞給 [export] 屬性的字串。

使用此程式設計樣式時,請確保 C# 參數與運行時引擎將傳遞的實際類型匹配。

#### <a name="models"></a>模型

在 UIKit 儲存設施中或使用幫助器類實現的回應器中,這些通常在 Objective-C 代碼中稱為委託,它們作為協議實現。

Objective-C 協定類似於介面,但它們支援可選方法,也就是說,並非所有方法都需要實現,以便協定才能正常工作。

實現模型有兩種方法。 您可以手動實現它或使用現有的強類型定義。

當您嘗試實現未受 Xamarin.iOS 約束的類時,手動機制是必需的。 這是很容易做到的:

- 將類別標記為與執行時註冊
- 在要覆寫的每個方法上應用具有實際選擇器名稱的 [export] 屬性
- 實例化類,並傳遞它。

例如,以下方法僅實現 UIApplicationDelegate 協定定義中的一種可選方法:

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

使用 Export 屬性聲明 Objective-C 選擇器名稱("應用程式完成啟動:"),該類`[Register]`將註冊到該 屬性。

Xamarin.iOS 提供可使用的強烈類型聲明,不需要手動綁定。 為了支援此程式設計模型,Xamarin.iOS 運行時支援類聲明上的 [模型] 屬性。 這通知運行時,除非顯式實現這些方法,否則它不應將類中的所有方法連接起來。

這意味著在 UIKit 中,使用可選方法表示協定的類編寫如下:

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

當要實現僅實現某些方法的模型時,只需重寫感興趣的方法,並忽略其他方法。 執行時只會將覆蓋的方法掛鉤,而將原始方法連接到 Objective-C 世界。

與前面的手動示例等效的是:

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

優點是無需挖掘 Objective-C 標頭檔來尋找選擇器、參數類型或映射到 C#,並且從 Mac 的 Visual Studio 以及強類型中取得對感覺

#### <a name="xib-outlets-and-c"></a>XIB 插座與 C\#

> [!IMPORTANT]
> 本節介紹使用 XIB 檔時與插座的 IDE 集成。 在 iOS 中使用 Xamarin 設計器時,所有內容都透過在 IDE 的「屬性」部分的 **「身份>名稱**」下輸入名稱來取代,如下所示:
>
> [![](images/designeroutlet.png "Entering an item Name in the iOS Designer")](images/designeroutlet.png#lightbox)
>
>有關 iOS 設計器的詳細資訊,請檢視[iOS 設計器文件簡介](~/ios/user-interface/designer/introduction.md#how-it-works)。

這是一個低級的描述:Outlets 如何與 C# 集成,並提供給 Xamarin.iOS 的高級使用者。 在 Mac 中使用 Visual Studio 時,使用您航班上生成的代碼自動在後台進行映射。

當您使用介面生成器設計使用者介面時,您只會設計應用程式的外觀,並將建立一些預設連接。 如果要以程式設計方式獲取資訊、更改控制項在執行時的行為或在執行時修改控制項,則需要將某些控制項綁定到託管代碼。

這分幾步完成:

1. 將**匯出宣告**加入**檔案的擁有者**。
1. 將控制項連線到**檔案的擁有者**。
1. 將 UI 以及連接儲存在 XIB/NIB 檔案中。
1. 在運行時載入 NIB 檔。
1. 訪問插座變數。

步驟 (1) 到 (3) 包含在 Apple 與介面構建器構建器構建介面的文檔中。

使用 Xamarin.iOS 時,應用程式將需要創建派生自 UIViewController 的類。 它像這樣實現:

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

然後,要從 NIB 檔載入 ViewController,請執行這些操作:

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

這將從 NIB 載入使用者介面。 現在,要訪問插座,必須通知運行時我們希望訪問它們。 為此,`UIViewController`子類需要聲明屬性,並使用 [Connect] 屬性對其進行給它們下一些用。 例如：

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

屬性實現是實際提取和存儲實際本機類型的值的實現。

使用 Visual Studio 進行 Mac 和介面建構器時,您無需擔心這一點。 Mac 的 Visual Studio 會自動鏡像所有聲明的分點,這些分段中的代碼都是作為專案的一部分編譯的。

#### <a name="selectors"></a>選取器

目標C程式設計的核心概念是選擇器。 您經常遇到需要透過選擇器或期望代碼回應選擇器的 API。

在 C# 中建立新選擇器非常簡單`ObjCRuntime.Selector`— 只需建立 類的新實例,並在 API 中需要該結果的任何位置使用結果即可。 例如：

```csharp
var selector_add = new Selector ("add:plus:");
```

對於 C# 方法對選擇器調用的回應,它`NSObject`必須從 類型繼承,並且 C# 方法必須使用`[Export]`屬性用選擇器名稱進行修飾。 例如：

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

請注意,選擇器名稱**必須**完全匹配,包括所有中間和尾隨冒號(":"),如果存在。

#### <a name="nsobject-constructors"></a>NSObject 建構函式

Xamarin.iOS 中派生`NSObject`自的大多數類將公開特定於物件功能的構造函數,但它們也會公開不立即明顯的各種構造函數。

建構函數的使用方式如下:

```csharp
public Foo (IntPtr handle)
```

當運行時需要將類映射到非託管類時,此構造函數用於實例化類。 當您載入 XIB/NIB 檔時,將發生這種情況。  此時,Objective-C 運行時將在非託管世界中創建一個物件,並將調用此構造函數來初始化託管端。

通常,您只需使用句柄參數調用基本構造函數,並在正文中執行任何必要的初始化。

```csharp
public Foo ()
```

這是類的預設構造函數,在 Xamarin.iOS 提供的類中,這將初始化 Foundation.NSObject 類以及之間和末尾的所有類,將其與類上的 Objective-C`init`方法鏈。

```csharp
public Foo (NSObjectFlag x)
```

此建構函數用於初始化實例,但阻止代碼在末尾調用 Objective-C"init"方法。 通常,當您已註冊初始化(當您在構造函數上使用`[Export]`時)或您已經通過另一個平均值完成初始化時,可以使用此方法。

```csharp
public Foo (NSCoder coder)
```

此構造函數用於從 NSCoding 實例初始化物件的情況。

#### <a name="exceptions"></a>例外狀況

Xamarin.iOS API 設計不會將目標 C 異常作為 C# 異常提出。 該設計強制首先不向 Objective-C 世界發送任何垃圾,並且必須生成的任何異常由綁定本身生成,然後再將無效數據傳遞到 Objective-C 世界。

#### <a name="notifications"></a>通知

在 iOS 和 OS X 中,開發人員可以訂閱基礎平台廣播的通知。 這是通過使用`NSNotificationCenter.DefaultCenter.AddObserver`方法完成的。 該方法`AddObserver`採用兩個參數;一個是您要訂閱的通知;另一種是在引發通知時調用的方法。

在 Xamarin.iOS 和 Xamarin.Mac 中,各種通知的鍵託管在觸發通知的類上。 例如,由引發的通知`UIMenuController`將作為`static NSString`以名稱"通知"結尾`UIMenuController`的類中的屬性進行託管。

### <a name="memory-management"></a>記憶體管理

Xamarin.iOS 有一個垃圾回收器,在資源不再使用時,它會負責為您釋放資源。 除了垃圾回收器之外,派生自`NSObject`實現`System.IDisposable`介面的所有物件。

#### <a name="nsobject-and-idisposable"></a>NSObject 與 I 一次性

公開`IDisposable`介面是説明開發人員釋放可能封裝大量記憶體塊的物件(例如,看似`UIImage`純真的指標,但可能指向 2 MB 圖像)和其他重要且有限的資源(如視頻解碼緩衝區)的便捷方法。

NSObject 實現了 I 可支配介面與[.NET 處置模式](https://msdn.microsoft.com/library/fs2xkftw.aspx)。 這允許子類 NSObject 的開發人員重寫 Dispose 行為並按需釋放自己的資源。 例如,請考慮此檢視控制器,該控制器保留一堆圖像:

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

釋放託管物件時,它不再有用。 您可能仍然具有對物件的引用,但此時該對象對於所有意圖和目的都無效。 如果嘗試存取已釋放物件上的任何方法,某些 .NET API 通過引發 ObjectDisposeException 來確保這一點,例如:

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

即使您仍然可以造訪變數"image",它實際上還是一個無效的引用,不再指向持有圖像的 Objective-C 物件。

但是,在 C# 中釋放物件並不意味著該物件必須被銷毀。 您所做的只是釋放 C# 對物件的引用。 可哥環境可能一直留有供自己使用的參考。 例如,如果將 UIImageView 的 Image 屬性設置為圖像,然後釋放圖像,則基礎 UIImageView 已獲取其自己的引用,並將保留對此物件的引用,直到它完成使用它。

#### <a name="when-to-call-dispose"></a>何時呼叫 Dispose

在刪除物件時,應呼叫Dispose。 一個可能的用例是,當 Mono 不知道您的 NSObject 實際上持有對記憶體或資訊池等重要資源的引用時。 在這些情況下,應調用 Dispose 以立即釋放對記憶體的引用,而不是等待 Mono 執行垃圾回收週期。

在內部,當 Mono[從 C# 字串創建 NSString 引用](~/ios/internals/api-design/nsstring.md)時,它會立即釋放它們以減少垃圾回收器必須完成的工作量。 處理物件越少,GC 的運行速度就越快。

#### <a name="when-to-keep-references-to-objects"></a>何時保留對物件的參考

自動記憶體管理有一個副作用是,只要沒有對未使用的物件,GC 就會刪除它們。 這有時會有令人驚訝的副作用,例如,如果您創建一個局部變數來保存頂級視圖控制器或頂層視窗,然後讓這些變數消失在你的背後。

如果不在靜態變數或實例變數中保留對物件的引用,Mono 將愉快地調用物件上的 Dispose() 方法,並且它們將釋放對物件的引用。 由於這可能是唯一未完成的引用,因此 Objective-C 執行時將為您銷毀物件。

## <a name="related-links"></a>相關連結

- [繫結欄位](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
