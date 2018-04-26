---
title: 應用程式開發介面設計
description: API Xamarin.iOS 設計的檢視方塊
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: da827c5bdd25bf841f5396b10d1eeb70e132dfeb
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="api-design"></a>應用程式開發介面設計

除了核心 Mono，一部分的基底類別庫[Xamarin.iOS](http://www.xamarin.com/iOS)隨附的各種 iOS 應用程式開發介面可讓開發人員建立單聲道的原生 iOS 應用程式繫結。

Xamarin.iOS 核心，沒有橋接器的 C# 世界 Objective C 的世界中，以及繫結，適用於 iOS C 為基礎的 Api，像是 CoreGraphics interop 引擎和[OpenGL ES](#OpenGLES)。

Objective C 程式碼通訊的低層級的執行階段處於[MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime)。 在此，繫結之上[Foundation](#MonoTouch.Foundation)，CoreFoundation，和[UIKit](#MonoTouch.UIKit)所提供。

## <a name="design-principles"></a>設計原則

這些是一些 （它們也套用到 Xamarin.Mac，macOS 上取得 Objective C 的單聲道繫結） 我們設計原則，將 Xamarin.iOS 繫結：

- 請遵循[Framework 設計方針](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- 可讓開發人員子類別 Objective C 類別：

  - 衍生自現有的類別
  - 呼叫鏈結的基底建構函式
  - 覆寫方法應該使用 C# 的覆寫系統
  - 子類別化應該使用 C# 標準建構

- 不會公開 Objective C 的選取器的開發人員
- 提供機制來呼叫任意 Objective C 程式庫
- 簡單和固定 Objective C 的工作可能使 Objective C 的一般工作
- 將 OBJECTIVE-C 屬性公開為 C# 屬性
- 公開強類型的 API:

  - 提升類型安全
  - 最小化執行階段錯誤
  - 傳回型別可以使用 IDE IntelliSense
  - 允許快顯 IDE 的文件

- 鼓勵 IDE 中瀏覽的 Api:

  - 例如，而不是公開的弱式類型的陣列像這樣：
    
    ```objc
    NSArray *getViews
    ```
    會公開為強式型別，就像這樣：
    
    ```csharp
    NSView [] Views { get; set; }
    ```
    
    這可讓 Visual Studio for Mac 瀏覽 API 時不要自動完成，但是所有`System.Array`上傳回的值，可用的作業，並允許參與 LINQ 將傳回值。

- 原生 C# 類型：

  - [`NSString` 會變成 `string`](~/ios/internals/api-design/nsstring.md)
  - 開啟`int`和`uint`參數應該已列舉成 C# 列舉型別和 C# 列舉型別與`[Flags]`屬性
  - 而不是型別中性`NSArray`物件，公開為強型別陣列的陣列。
  - 事件與通知，讓使用者可以選擇：

    - 預設為強型別版本
    - 弱型別版本進階的使用案例

- 支援 OBJECTIVE-C 委派模式：

    - C# 事件系統
    - 公開 （expose) C# 委派 (lambda、 匿名方法，和`System.Delegate`) 為區塊 OBJECTIVE-C api

### <a name="assemblies"></a>組件

Xamarin.iOS 包含組成組件數*Xamarin.iOS 設定檔*。 [組件](~/cross-platform/internals/available-assemblies.md)頁面具有的詳細資訊。

### <a name="major-namespaces"></a>主要命名空間 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

[ObjCRuntime](https://developer.xamarin.com/api/namespace/ObjCRuntime/)命名空間可讓開發人員世界 C# 和目標 c 之間的橋接
這是新的繫結，特別是針對 iOS、 根據 Cocoa # 及 Gtk # 經驗而設計。

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>Foundation

[Foundation](https://developer.xamarin.com/api/namespace/Foundation/)命名空間提供的基本資料型別設計成屬於 iOS OBJECTIVE-C 基礎架構與交互操作，而且它是基底物件導向程式設計目標 c

Xamarin.iOS 反映在 C# 中的目標 c 中的類別階層架構 例如，OBJECTIVE-C 基底類別[NSObject](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html)可以從 C# 透過[Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)。

雖然此命名空間提供的基礎 Objective C 的基礎類型的繫結，在少數情況下我們有對應的基礎類型.NET 類型。 例如: 

- 而不是處理[NSString](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html)和[NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html)，執行階段會公開為 C#[字串](https://developer.xamarin.com/api/type/System.String/)s 和強型別[陣列](https://developer.xamarin.com/api/type/System.Array/)整個 s應用程式開發介面。

- 各種協助程式 Api 會公開以下，可讓開發人員將第三方 Objective C 的 Api，其他的 iOS 應用程式開發介面或應用程式開發介面中目前未連結 Xamarin.iOS 所繫結。

如需有關繫結應用程式開發介面的詳細資訊，請參閱[Xamarin.iOS 繫結產生器](~/cross-platform/macios/binding/binding-types-reference.md)> 一節。


##### <a name="nsobject"></a>NSObject

[NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)類型是所有 OBJECTIVE-C 繫結的基礎。 Xamarin.iOS 類型鏡像從 iOS CocoaTouch Api 類型的兩個類別： C 類型 （通常來參考以做為 CoreFoundation 類型） 和 Objective C 類型 （這些都是衍生自 NSObject 類別）。

針對每個鏡像處理為 unmanaged 的類型的類型，便可取得的原生物件透過[處理](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/)屬性。

雖然單聲道會提供對您物件的回收`Foundation.NSObject`實作[System.IDisposable](https://developer.xamarin.com/api/type/System.IDisposable/)介面。 這表示，您可以明確而不必等候開始在記憶體回收行程釋放任何給定 NSObject 的資源。 當您使用大量 NSObjects，比方說，UIImages 可能會保存到大型資料區塊的指標，這很重要。

如果您的型別，就需要執行具決定性最終處理，會覆寫[NSObject.Dispose(bool) 方法](https://developer.xamarin.com/api/type/Foundation.NSObject/%2fM%2fDispose)要處置的參數"bool disposing"，而如果設定為 true，它表示的 Dispose 方法呼叫因為使用者在物件上明確呼叫的 Dispose （)。 如果值為 false，這表示，Dispose (bool disposing) 方法呼叫來自完成項所在的完成項執行緒。 []()


##### <a name="categories"></a>分類

從 Xamarin.iOS 8.10 它是可以從 C# 建立 Objective C 的類別。

這是使用`Category`屬性，指定要做為引數的屬性延伸的類型。 下列範例將執行個體延伸 NSString。

    [Category (typeof (NSString))]

每個類別的方法使用標準機制來方法使用匯出到 Objective C`Export`屬性：

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

所有受管理的擴充方法必須是靜態，但您可建立 Objective C 的執行個體方法使用標準語法的 C# 中的擴充方法：

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

與擴充方法的第一個引數將叫用方法時所在的執行個體。

完整的範例：

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

這個範例會將原生 toUpper 執行個體方法加入至 NSString 類別，可從目標 C.可以叫用

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

這非常有用的一個案例將方法加入至整個組中的程式碼基底類別，例如，這會使所有`UIViewController`可以旋轉的執行個體報告：

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

PreserveAttribute 應用程式，以減少其大小的處理時的階段時，仍可用來告知 mtouch – Xamarin.iOS 部署工具 – 要保留型別或型別的成員，為自訂屬性。

未由應用程式以靜態方式連結的每個成員都可能會移除。 因此，此屬性用來將成員不以靜態方式參考，但是您的應用程式仍然需要的標記。

例如，若您是以動態方式來具現化類型，您可以保留您類型的預設建構函式。 若您使用的是 XML 序列化，則您可能會想要保留您類型的屬性。

您可以將此屬性套用到某個類型的每個成員，或是套用到類型本身。 如果您想要保留整個類型，您可以使用語法 [保留 (AllMembers = true)] 類型。

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

[UIKit](https://developer.xamarin.com/api/namespace/UIKit/)命名空間包含的所有 UI 元件的 C# 類別形式 CocoaTouch 構成的一對一對應。 應用程式開發介面已修改成遵循 C# 語言中使用的慣例。

C# 委派會提供一般作業。 請參閱[委派](#Delegates)節的詳細資訊。

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGLES

OpenGLES，我們將發佈[版本修改](https://developer.xamarin.com/api/namespace/OpenTK/)的[OpenTK](http://www.opentk.com/)已修改成使用 CoreGraphics 資料類型和結構的 OpenGL 的物件導向繫結，以及只公開的 API在 iOS 使用的功能。

OpenGLES 1.1 功能是透過 ES11.GL 型別，記載[這裡](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES11.GL/)型別。

OpenGLES 2.0 的功能是透過 ES20.GL 型別，記載[這裡](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES20.GL/)型別。

OpenGLES 3.0 功能是透過 ES30.GL 型別，記載[這裡](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES30.GL/)型別。


### <a name="binding-design"></a>繫結設計

Xamarin.iOS 不只是基礎 OBJECTIVE-C 平台的繫結。 它會擴充.NET 類型系統和分派系統，以更好的 blend C# 和目標。

就像 P/Invoke 會叫用在 Windows 和 Linux 上的原生程式庫有用的工具，或為 IJW 支援可用於在 Windows 上的 COM interop，Xamarin.iOS 擴充執行階段支援繫結 C# 物件 Objective C 的物件。

項目會完成的方式，而且有助於他們建立更複雜的應用程式時，了解在接下來的幾個章節不需要的使用者，要建立 Xamarin.iOS 應用程式，但可協助開發人員的討論。



#### <a name="types"></a>類型

其中它沒有任何意義，而不是低層級基礎類型，而 C# universe 公開 C# 類型。  這表示[API 使用 C# 「 字串 」 類型，而不是 NSString](~/ios/internals/api-design/nsstring.md)也會使用 C# 強型別的陣列，而不是公開 NSArray。

一般情況下，在 Xamarin.iOS 和 Xamarin.Mac 設計中，基礎`NSArray`物件不會公開。 相反地，執行階段會自動將轉換`NSArray`強型別陣列的部分 s`NSObject`類別。 因此，Xamarin.iOS 未公開弱型別方法來傳回 NSArray GetViews 像：

```csharp
NSArray GetViews ();
```

相反地，繫結會公開強類型的傳回值，就像這樣：

```csharp
UIView [] GetViews ();
```

有幾個方法中公開`NSArray`，極端案例，您可能想要的`NSArray`直接，但其使用的 API 繫結中不建議使用。

此外，在**傳統 API**而不是公開`CGRect`，`CGPoint`和`CGSize`從 CoreGraphics API 中，我們取代具有`System.Drawing`實作`RectangleF`， `PointF`和`SizeF`因為它們會協助開發人員會保留現有的 OpenGL 程式碼會使用 OpenTK。 當使用新的 64 位元**統一的 API**，應使用 CoreGraphics API。

<a name="Inheritance" />

#### <a name="inheritance"></a>繼承

API Xamarin.iOS 設計可讓開發人員擴充 Objective C 的原生類型，它們會擴充 C# 類型，在衍生類別上使用 「 覆寫"關鍵字，以及鏈結至使用 「 基底 」 的 C# 關鍵字的基底實作的方式相同。

此設計可讓開發人員，以避免其開發程序中，處理 OBJECTIVE-C 選取器，因為整個 OBJECTIVE-C 系統已包裝的 Xamarin.iOS 程式庫內。


#### <a name="types-and-interface-builder"></a>類型和介面產生器

當您建立.NET 類別執行個體的介面產生器所建立的類型時，您需要提供的建構函式會採用單一`IntPtr`參數。
需要此繫結與未受管理物件的受管理的物件執行個體。
程式碼包含單一行，就像這樣：

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

<a name="Delegates" />


#### <a name="delegates"></a>委派

Objective C 和 C# 有不同的意義，word 委派中每一種語言。

Objective C 的世界中，在和中的文件，您會發現有關 CocoaTouch 線上，委派通常是類別的一組方法將回應的執行個體。 方法不一定是必要的這是非常類似於 C# 介面，兩者的差異。

這些委派扮演重要角色 UIKit 和其他 CocoaTouch Api。 它們可用來完成各種工作：

-  提供您的程式碼 （類似於事件傳遞的 C# 或 Gtk +） 的通知。
-  若要實作模型的資料視覺效果控制項。
-  磁碟機控制項的行為。


程式設計模式是設計用來建立衍生的類別，以變更控制項的行為的最小化。 這個解決方案相當夠用類似於其他 GUI 工具組已完成多年： Gtk 的訊號，Qt 插槽、 Winforms 事件、 WPF/Silverlight 事件，依此類推。 若要避免發生數百個介面 （一個用於每個動作），或需要開發人員實作不需要太多的方法，Objective C 支援選擇性的方法定義。 這是不同於 C# 需要所有的方法實作的介面。

在 Objective C 的類別，您會看到使用這種程式設計模式的類別公開的屬性，通常稱為`delegate`，即必須實作必要的部分的介面及零或多個選擇性部分。

Xamarin.iOS 提供三個互斥的機制將繫結至這些委派：

1.  [透過事件](#Via_Events)。
2.  [透過強型別`Delegate`屬性](#StrongDelegate)
3.  [透過鬆散輸入`WeakDelegate`屬性](#WeakDelegate)

例如，請考慮[UIWebView](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html)類別。 這個分派到以[UIWebViewDelegate](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html)執行個體，這指派給[委派](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate)屬性。

<a name="Via_Events" />

##### <a name="via-events"></a>透過事件

對於許多類型，Xamarin.iOS 會自動建立適當的委派會轉送`UIWebViewDelegate`呼叫到 C# 事件。 針對 `UIWebView`：

-  [WebViewDidStartLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:)方法對應到[UIWebView.LoadStarted](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadStarted/)事件。
-  [WebViewDidFinishLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:)方法對應到[UIWebView.LoadFinished](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadFinished/)事件。
-  [WebView:didFailLoadWithError](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:)方法對應到[UIWebView.LoadError](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadError/)事件。

例如，這個簡單的程式會記錄載入網頁的檢視時的開始和結束時間：

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>透過屬性

可能有多個事件訂閱者時，事件相當實用。 此外，事件會限制的情況下為從程式碼沒有傳回值的地方。

程式碼會預期傳回值的情況下，我們選擇改為屬性。 這表示只有一個方法，可以設定在物件中指定的時間。

比方說，您可以使用這項機制，若要關閉的處理常式上的螢幕上的鍵盤`UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

`UITextField`的`ShouldReturn`屬性在此情況下會做為引數會傳回布林值，並決定是否 TextField 應進行使用傳回的按鈕被按下的委派。 在方法中，我們決定傳回*true*至呼叫端，但我們從螢幕中也移除鍵盤 (textfield 呼叫時會發生這個問題`ResignFirstResponder`)。

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>強型別透過委派屬性

如果您不想使用事件，您可以提供您自己[UIWebViewDelegate](https://developer.xamarin.com/api/type/UIKit.UIWebViewDelegate/)子類別並將它指派給[UIWebView.Delegate](https://developer.xamarin.com/api/property/UIKit.UIWebView.Delegate/)屬性。 一旦 UIWebView.Delegate 已被指派，將無法再運作 UIWebView 事件分派機制，並對應的事件發生時，會叫用 UIWebViewDelegate 方法。

例如，這個簡單的類型記錄載入 web 檢視所花費的時間：

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

上述用於如下的程式碼：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.Delegate = new Notifier ();
```

上述將會建立 UIWebViewer，而且它會指示它將訊息傳送至的通知，我們建立回應訊息的類別執行個體。

此模式也用來控制針對特定控制項，例如在 UIWebView 案例中，行為[UIWebView.ShouldStartLoad](https://developer.xamarin.com/api/property/UIKit.UIWebView.ShouldStartLoad/)屬性可讓`UIWebView`至控制項的執行個體是否`UIWebView`會載入不論頁面。

此模式也用於視幾個控制項提供的資料。 例如， [UITableView](https://developer.xamarin.com/api/type/UIKit.UITableView/)控制項是一個功能強大的表格呈現控制項 – 和外觀和內容會驅動執行個體所[UITableViewDataSource](https://developer.xamarin.com/api/type/UIKit.UITableView/DataSource)

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>鬆散型別透過 WeakDelegate 屬性

除了強型別屬性，還有弱式的具型別的的委派，讓開發人員想要時以不同的方式繫結項目。
每個地方的強型別`Delegate`Xamarin.iOS 的繫結，對應中公開屬性`WeakDelegate`也會公開屬性。

當使用`WeakDelegate`，您有責任正確而將您的類別使用[匯出](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)屬性來指定選取器。 例如: 

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

請注意，一次`WeakDelegate`屬性已被指派，`Delegate`屬性將不會使用。 此外，如果您想要 [匯出] 繼承基底類別中實作的方法，您必須讓它的公用方法。


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>對應到 C 的 OBJECTIVE-C 委派模式&#35;

當您看到 Objective C 的範例，看起來像這樣：

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

這會指示要建立和建構類別 」 SomethingDelegate"的執行個體，並將值指派給委派屬性 foo 變數上的語言。 這項機制會受到 Xamarin.iOS 和 C# 語法是：

```csharp
foo.Delegate = new SomethingDelegate ();
```

在 Xamarin.iOS 中有提供對應至 Objective C 的強型別類別委派類別。 若要使用它們，您會子類別化並覆寫 Xamarin.iOS 的實作所定義的方法。 如需有關其運作方式的詳細資訊，請參閱 > 一節 < 模型 > 底下。


##### <a name="mapping-delegates-to-c35"></a>對應到 C 的委派&#35;

UIKit 一般情況下會使用兩種形式的 Objective C 的委派。

第一種形式提供介面的元件模型。 例如，以提供資料隨檢視，例如，針對清單檢視的資料儲存設備的機制。  在這些情況下，您應該一律建立適當的類別執行個體，並將變數指派。

在下列範例中，我們提供`UIPickerView`的模型，會使用字串的實作：

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

第二種形式是要提供事件的通知。 在這些情況下，雖然仍會公開的 API，以上所述的格式，但是我們也提供 C# 事件，它應該要用於快速的作業更簡單且整合式匿名委派與 C# 中的 lambda 運算式。

例如，您可以訂閱`UIAccelerometer`事件：

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

兩個選項可用，where 他們有意義，但身為程式設計人員，您必須選取其中一個。 如果您建立您自己的強型別回應/委派的執行個體，並將它指派，C# 事件將無法正常運作。 如果您使用 C# 事件，則絕不會呼叫您的回應者/委派類別中的方法。

之前的範例中使用`UIWebView`可以寫入使用 C# 3.0 lambda 像這樣：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>回應事件

Objective C 程式碼中，有時候多個控制項和提供者的資訊為多個控制項，事件處理常式會裝載於相同類別。 這是可能的因為類別回應訊息，而且只要類別回應訊息，便可將物件連結在一起。

先前有詳細說明，Xamarin.iOS 支援這兩個 C# 事件為基礎的程式設計模型，而且 OBJECTIVE-C 委派模式，其中您可以建立新的類別，用的實作委派，並覆寫所需的方法。

它也可支援 Objective C 的模式，其中多個不同作業的回應所有裝載於相同類別的執行個體。 若要透過這樣，您必須使用 Xamarin.iOS 繫結的低層級功能。

例如，如果您想要您的類別，來回應同時`UITextFieldDelegate.textFieldShouldClear`： 訊息和`UIWebViewDelegate.webViewDidStartLoad`： 類別位於相同執行個體，您必須使用 [匯出] 屬性宣告：

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

方法的 C# 名稱並不重要;所有會影響結果會傳遞給 [匯出] 屬性的字串。

當使用這種程式設計樣式，請確定 C# 參數相符，執行階段引擎會將傳遞的實際型別。

<a name="Models" />

#### <a name="models"></a>模型

在 UIKit 儲存設備，或回應程式會使用協助程式類別實作中，這些通常稱為 Objective C 程式碼中的委派，以及它們被實作為通訊協定。

Objective C 的通訊協定會像介面，但它們支援選擇性方法 (也就是所有方法必須實作此通訊協定才能運作）。

有兩種實作模型。 您可以手動實作，或使用現有的強型別的定義。


當您嘗試實作尚未由 Xamarin.iOS 繫結的類別時，才能使用手動機制。 它是很容易：

-  您的類別與執行階段註冊的旗標
-  您想要覆寫每個方法上套用 [匯出] 屬性，以實際的選取器名稱
-  具現化類別，並將它傳遞。

例如，下列實作選擇性方法的其中之一 UIApplicationDelegate 通訊協定定義中：

```csharp
public class MyAppController : NSObject {
        [Export ("applicationDidFinishLaunching:")]
        public void FinishedLaunching (UIApplication app)
        {
                SetupWindow ();
        }
}
```

Objective C 選取器名稱 ("applicationDidFinishLaunching:") 以匯出屬性宣告和類別已向`[Register]`屬性。

Xamarin.iOS 提供強型別的宣告，可供使用，不需要手動繫結。 若要支援這個程式設計模型，Xamarin.iOS 執行階段會支援類別宣告上的 [模型] 屬性。 這會通知執行階段，它不應該網路類別中的所有方法除非方法都是會明確實作。

這表示在 UIKit，表示通訊協定與選擇性方法的類別撰寫如下：

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

您想要實作只會實作部分方法的模型，您只需要時覆寫您感興趣，並忽略其他方法的方法。 執行階段只會將覆寫的方法，不 OBJECTIVE-C 世界的原始方法連結。

相當於先前的手動範例是：

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

優點是，不是需要深入了解 OBJECTIVE-C 標頭檔，尋找選取器中，引數或加入 C# 中，對應的型別，而且 intellisense 從 Visual Studio for Mac，連同強式型別


#### <a name="xib-outlets-and-c35"></a>XIB 插座和 C&#35;

> [!IMPORTANT]
> 使用 XIB 檔案時，本節會說明與插座 IDE 整合。 使用 Xamarin 設計工具為 iOS，當這所有取代輸入的名稱下的**識別 > 名稱**屬性區段中的 IDE，如下所示：
>
> [![](images/designeroutlet.png "輸入 iOS 設計工具中的項目名稱")](images/designeroutlet.png#lightbox)
>
>如需 iOS 設計工具的詳細資訊，請檢閱[iOS 設計工具簡介](~/ios/user-interface/designer/introduction.md#how-it-works)文件。

這是低階描述如何與 C# 整合插座，並提供 Xamarin.iOS 的進階使用者。 使用 Visual Studio for Mac 對應完成時自動使用在幕後為您產生班機的程式碼。

當您設計您的使用者介面與介面產生器時，您只設計應用程式的外觀，並將會建立一些預設的連線。 如果您想要以程式設計方式擷取資訊、 變更控制項在執行階段的行為或修改在執行階段控制項，則需要將控制項繫結至 managed 程式碼。

這是幾個步驟：

1.  新增**插座宣告**到您**檔案的擁有者**。
1.  連接您的控制項**檔案的擁有者**。
1.  儲存 UI 加上連接到您 XIB/NIB 檔案。
1.  載入 NIB 檔案在執行階段。
1.  存取輸出變數。


建立使用介面產生器介面的 Apple 文件中涵蓋了透過 (3) 的步驟 (1)。

當使用 Xamarin.iOS，您的應用程式必須建立衍生自 UIViewController 類別。 它實作就像這樣：

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

然後從 NIB 檔案載入您 ViewController，您執行下列作業：

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

這會從 NIB 載入使用者介面。 現在，若要存取插座，則需要以通知執行階段所要存取它們。 若要這樣做，`UIViewController`子類別必須宣告屬性，並使用 [連線] 屬性來標註。 與下列類似：

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

屬性實作是實際擷取和儲存的實際原生類型的值。

您不需要擔心這用於 Mac 和 InterfaceBuilder 使用 Visual Studio 時。 適用於 Mac 的 visual Studio 會自動反映與程式碼會編譯為專案中的部分類別中的所有宣告的插座。

#### <a name="selectors"></a>選取器

Objective C 程式設計的核心概念是選取器。 您通常會遇到需要您將選取器，或您的程式碼來回應選取器必須要有應用程式開發介面。

在 C# 中建立新的選取器是很容易，因為您剛才建立的新執行個體`ObjCRuntime.Selector`類別，並且在需要它的 API 中的任何地方使用結果。 例如: 

```csharp
var selector_add = new Selector ("add:plus:");
```

針對 C# 方法回應選取器呼叫，它必須是繼承自`NSObject`型別和 C# 方法必須以選取器名稱使用裝飾`[Export]`屬性。 例如: 

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

請注意該選取器名稱**必須**完全符合，包括所有中繼和尾端的冒號 (":")，如果有的話。

#### <a name="nsobject-constructors"></a>NSObject 建構函式

大部分的類別中衍生自 Xamarin.iOS`NSObject`會公開特有的功能，該物件的建構函式，但它們也會公開各種建構函式不馬上看得出來。

使用建構函式，如下所示：

```csharp
public Foo (IntPtr handle)
```

這個建構函式用來執行階段必須將您的類別對應至 unmanaged 類別具現化您的類別。 會發生這種情況是當您載入 XIB/NIB 檔案。  此時，Objective C 執行階段將已建立物件在 unmanaged 世界中，並將呼叫這個建構函式，來初始化在 managed 的端。

一般而言，您只需要是呼叫基底建構函式的控制代碼參數，並在主體中，進行任何所需的初始化。

```csharp
public Foo ()
```

這是預設建構函式類別，並在 Xamarin.iOS 提供類別，這會初始化 Foundation.NSObject 類別及其所有的類別之間，並在結束時，鏈結這 Objective C`init`類別上的方法。

```csharp
public Foo (NSObjectFlag x)
```

這個建構函式用來初始化執行個體，但呼叫 Objective C 」 初始化 」 方法在結束時，防止程式碼。 這一般用於當您已經註冊進行初始化 (當您使用`[Export]`您建構函式上) 或當您已完成您初始化透過另一個的平均值。

```csharp
public Foo (NSCoder coder)
```

這個建構函式提供的案例，其中物件在從 NSCoding 執行個體初始化。 如需詳細資訊，請參閱 Apple[封存與序列化程式設計指南 》。](http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>例外狀況

API Xamarin.iOS 設計不會當做 C# 例外狀況引發 Objective C 例外狀況。 沒有記憶體回收在第一次傳送到 OBJECTIVE-C 世界，會強制執行設計與之前曾經是無效的資料，必須產生任何例外狀況所產生的繫結本身傳遞至 OBJECTIVE-C 世界。

#### <a name="notifications"></a>通知

在 iOS 和 OS X 開發人員可以訂閱基礎平台廣播的通知。 這是使用`NSNotificationCenter.DefaultCenter.AddObserver`方法。 `AddObserver`方法會採用兩個參數是您想要訂閱的通知; 另一個方法是在引發通知時要叫用方法。

在 Xamarin.iOS 和 Xamarin.Mac 的各種告知按鍵裝載觸發通知的類別上。 通知所產生的例如`UIMenuController`裝載為`static NSString`中的屬性`UIMenuController`結尾的名稱 「 通知 」 的類別。

### <a name="memory-management"></a>記憶體管理

Xamarin.iOS 有會負責您釋放資源，當它們已經不再使用的記憶體回收行程。 除了記憶體回收行程中，所有物件都衍生自`NSObject`實作`System.IDisposable`介面。

#### <a name="nsobject-and-idisposable"></a>NSObject 和 IDisposable

公開`IDisposable`介面是協助開發人員在釋放物件，可能會將封裝的大型記憶體區塊的一個便利方式 (例如，`UIImage`看起來會像只無害的指標，但無法指向 2 mb 的映像) 和其他重要且有限的資源 （例如視訊解碼緩衝處理）。

NSObject 實作 IDisposable 介面以及[.NET Dispose 模式](http://msdn.microsoft.com/library/fs2xkftw.aspx)。 這可讓開發人員 NSObject 來覆寫 Dispose 行為，並釋放其自己的資源，視該子類別。 例如，請考慮一堆映像會保持此檢視控制站：

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

處置 managed 的物件時，就不再有用。 您仍可能會有物件的參考，但物件無效，不管您的目的在此時。 某些.NET Api 會確保這藉由擲回 ObjectDisposedException，如果您嘗試存取已處置的物件上的任何方法，例如：

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

即使您仍然可以存取的變數 「 映像 」，實際上是無效的參考，而不再指向保留影像 Objective C 物件。

但處置 C# 中的物件並不表示一定會終結物件。 您的版本 C# 在物件的參考。 很可能 Cocoa 環境可能已保留周圍的參考，供其使用。 例如，如果您將 UIImageView 影像 屬性設定為映像，然後處置映像，基礎 UIImageView 必須採取其本身的參考和完成之前，將此物件的參考將會保留使用它。

#### <a name="when-to-call-dispose"></a>當呼叫 Dispose

當您需要單聲道中移除的物件時，您應該呼叫 Dispose。 可能的使用案例是當單聲道不了解您 NSObject 實際保有重要的資源，例如記憶體或資訊集區的參考。 在這些情況下，您應該呼叫 Dispose 以立即釋放的記憶體，而不是等待 Mono 執行記憶體回收循環參考。

就內部而言，當單聲道建立[NSString 參考從 C# 字串](~/ios/internals/api-design/nsstring.md)，它將處置它們立即以降低記憶體回收行程必須進行的工作數量。 大約來處理，速度 GC 較少的物件將會執行。

#### <a name="when-to-keep-references-to-objects"></a>當保留物件參考

一個副作用是具有自動記憶體管理是的 GC 會移除未使用的物件，只要不有任何參考。 這有時會很令人意外的副作用，例如，如果您建立的本機變數來保存您的最上層檢視控制站，或最上層視窗中，，然後讓那些消失背後備份。

如果您不要為您的物件保留的參考，在您的靜態或執行個體變數，Mono 愉快地保存他們將其上，呼叫 dispose （） 方法，它們就會在釋放物件的參考。 因為這可能是只有未處理的參考，Objective C 執行階段會為您終結物件。

## <a name="related-links"></a>相關連結

- [繫結欄位](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
