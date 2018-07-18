---
title: Xamarin.iOS API 設計
description: 本文件說明的一些指導原則，用來建構 Xamarin.iOS Api 與何這些目標。
ms.prod: xamarin
ms.assetid: 322D2724-AF27-6FFE-BD21-AA1CFE8C0545
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 275db96435639a60be89e0e3ddb7fa120a30de1c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996408"
---
# <a name="xamarinios-api-design"></a>Xamarin.iOS API 設計

除了基底類別庫屬於 Mono 的核心[Xamarin.iOS](http://www.xamarin.com/iOS)隨附於適用於各種不同的 iOS Api 可讓開發人員建立與 Mono 的原生 iOS 應用程式的繫結。

Xamarin.iOS 的核心，沒有橋接的 Objective C 的世界中，以及適用於 iOS C 為基礎的 Api，例如 CoreGraphics 繫結使用 C# 世界 interop 引擎並[OpenGL ES](#OpenGLES)。

OBJECTIVE-C 程式碼通訊的低層級的執行階段處於[MonoTouch.ObjCRuntime](#MonoTouch.ObjCRuntime)。 在此，繫結之上[Foundation](#MonoTouch.Foundation)，CoreFoundation，及[UIKit](#MonoTouch.UIKit)提供。

## <a name="design-principles"></a>設計原則

以下是一些我們的設計原則 Xamarin.iOS 繫結 （它們也適用於 Xamarin.Mac、 適用於 OBJECTIVE-C 在 macOS 上的 Mono 繫結）：

- 請依照下列[Framework 設計方針](https://docs.microsoft.com/dotnet/standard/design-guidelines)
- 可讓開發人員子類別 Objective C 類別：

  - 從現有的類別衍生
  - 呼叫鏈結的基底建構函式
  - 覆寫方法應該使用 C# 的覆寫系統
  - 子類別應該使用 C# 標準建構

- 不會公開以 OBJECTIVE-C 選取器的開發人員
- 提供機制來呼叫任意的 Objective C 程式庫
- 簡單且永久的 Objective C 工作盡量讓 Objective C 的一般工作
- 將 OBJECTIVE-C 屬性公開為 C# 屬性
- 公開強類型的 API:

  - 提升類型安全
  - 執行階段錯誤降至最低
  - 傳回型別取得 IDE IntelliSense
  - 允許 IDE 快顯視窗的文件

- 建議 Api 的 IDE 中瀏的覽：

  - 比方說，而不是公開這類弱型別陣列：
    
    ```objc
    NSArray *getViews
    ```
    公開為強式型別，就像這樣：
    
    ```csharp
    NSView [] Views { get; set; }
    ```
    
    這可讓 Visual Studio for Mac 中進行瀏覽 API 時的自動完成，讓所有`System.Array`上傳回的值，可用的作業，並允許加入 LINQ 中的傳回值。

- 原生的 C# 型別：

  - [`NSString` 會變成 `string`](~/ios/internals/api-design/nsstring.md)
  - 開啟`int`並`uint`應該已列舉成 C# 列舉型別和 C# 列舉型別與參數`[Flags]`屬性
  - 而不是型別中性`NSArray`物件，公開為強型別陣列的陣列。
  - 如需事件和通知，讓使用者選擇：

    - 預設的強型別版本
    - 進階的使用案例弱型別版本

- 支援的 Objective C 的委派模式：

    - C# 事件系統
    - 公開 （expose) C# 委派 (lambda、 匿名方法，和`System.Delegate`) 為區塊的 Objective C api

### <a name="assemblies"></a>組件

Xamarin.iOS 包含組成組件數*Xamarin.iOS 設定檔*。 [組件](~/cross-platform/internals/available-assemblies.md)頁面具有的詳細資訊。

### <a name="major-namespaces"></a>主要命名空間 

<a name="MonoTouch.ObjCRuntime" />

#### <a name="objcruntime"></a>ObjCRuntime

[ObjCRuntime](https://developer.xamarin.com/api/namespace/ObjCRuntime/)命名空間可讓開發人員世界連接在 C# 與 OBJECTIVE-C 之間
這是新的繫結，專為 iOS，根據與 Cocoa # Gtk # 經驗而設計。

<a name="MonoTouch.Foundation" />

#### <a name="foundation"></a>Foundation

[Foundation](https://developer.xamarin.com/api/namespace/Foundation/)命名空間提供與屬於 iOS OBJECTIVE-C Foundation 架構交互操作而設計的基本資料類型，而且是物件導向程式設計在 OBJECTIVE-C 中的基底

Xamarin.iOS 鏡像在 C# 中的從 OBJECTIVE-C 類別階層 比方說，OBJECTIVE-C 基底類別[NSObject](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/Reference/Reference.html)可以從 C# 透過[Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)。

雖然此命名空間提供基礎的 Objective C 的基礎類型的繫結，在少數情況下已對應的基礎類型到.NET 類型。 例如: 

- 而不是處理[NSString](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html)並[NSArray](https://developer.apple.com/library/ios/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html)，執行階段會公開這些 C#[字串](xref:System.String)s 和強型別[陣列](xref:System.Array)整個 sAPI。

- 各種協助程式 Api 會公開以下，讓開發人員可以繫結 Objective C Api、 其他 iOS Api 或目前未連結的 Xamarin.iOS 的 Api 的協力廠商。

如需繫結 Api 的詳細資訊，請參閱 < [Xamarin.iOS 繫結產生器](~/cross-platform/macios/binding/binding-types-reference.md)一節。


##### <a name="nsobject"></a>NSObject

[NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)類型是所有 OBJECTIVE-C 繫結的基礎。 Xamarin.iOS 類型反映的類型從 iOS CocoaTouch Api 的兩個類別： C 類型 （通常稱為 CoreFoundation 類型） 和 Objective C 類型 （這些都是衍生自 NSObject 類別）。

每個類型對映 unmanaged 型別，就可以取得的原生物件透過[處理](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/)屬性。

而 Mono 會提供所有的物件，記憶體回收`Foundation.NSObject`會實作[System.IDisposable](xref:System.IDisposable)介面。 這表示，您可以不必等到記憶體回收行程在安排中，明確釋放任何給定的 NSObject 的資源。 當您使用大量 NSObjects，比方說，可能會保存到大型資料區塊的指標的 UIImages，這很重要。

如果您的型別，就需要執行具決定性最終處理，覆寫[NSObject.Dispose(bool) 方法](https://developer.xamarin.com/api/type/Foundation.NSObject/%2fM%2fDispose)Dispose 的參數"bool disposing"，而如果設定為 true，它表示，會因為正在呼叫 Dispose 方法的使用者在物件上的明確呼叫的 Dispose （)。 如果值為 false，這表示，程式的 Dispose (bool disposing) 方法是從完成項執行緒上呼叫完成項。 []()


##### <a name="categories"></a>分類

從 Xamarin.iOS 8.10 它是可以從 C# 建立 OBJECTIVE-C 類別。

這是使用`Category`屬性，指定要做為引數的屬性延伸的型別。 下列範例將執行個體擴充 NSString。

    [Category (typeof (NSString))]

每個類別的方法使用一般的機制方法使用匯出到 OBJECTIVE-C`Export`屬性：

    [Export ("today")]
    public static string Today ()
    {
        return "Today";
    }

所有受管理的擴充方法必須是靜態的但您可建立 C# 中的擴充方法使用標準語法的 Objective C 執行個體方法：

    [Export ("toUpper")]
    public static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }

因此擴充方法的第一個引數叫用方法時所在的執行個體。

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

此範例會將原生 toUpper 執行個體方法加入到 NSString 類別，可從 OBJECTIVE-C 叫用

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

這會很實用的其中一個案例將方法加入至整個設定組中的程式碼基底類別，比方說，這會讓所有`UIViewController`可以旋轉的執行個體報表：

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

PreserveAttribute 是自訂屬性，以用來告知 mtouch – Xamarin.iOS 部署工具 – 要保留的型別或成員的型別，在應用程式，以減少其大小的處理時的階段。

未由應用程式以靜態方式連結的每個成員都可能會移除。 因此，這個屬性用來將成員的非靜態參考，但仍需要您的應用程式標記。

例如，若您是以動態方式來具現化類型，您可以保留您類型的預設建構函式。 若您使用的是 XML 序列化，則您可能會想要保留您類型的屬性。

您可以將此屬性套用到某個類型的每個成員，或是套用到類型本身。 如果您想要保留整個類型，您可以使用語法 [保留 (AllMembers = true)] 類型。

<a name="MonoTouch.UIKit" />

#### <a name="uikit"></a>UIKit

[UIKit](https://developer.xamarin.com/api/namespace/UIKit/)命名空間包含所有 C# 類別的形式的產品 CocoaTouch 構成 UI 元件的一對一對應。 API 已經過修改，以遵循 C# 語言中使用的慣例。

C# 委派會提供適用於一般作業。 請參閱[委派](#Delegates)節的詳細資訊。

<a name="OpenGLES" />

#### <a name="opengles"></a>OpenGLES

OpenGLES，對於我們發佈[修改版本](https://developer.xamarin.com/api/namespace/OpenTK/)的[OpenTK](http://www.opentk.com/)已修改成使用 CoreGraphics 資料類型和結構的 OpenGL 的物件導向繫結，以及只公開的 API在 iOS 上可用的功能。

OpenGLES 1.1 功能是透過 ES11.GL 類型，記載[此處](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES11.GL/)型別。

OpenGLES 2.0 功能是透過 ES20.GL 類型，記載[此處](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES20.GL/)型別。

OpenGLES 3.0 功能是透過 ES30.GL 類型，記載[此處](https://developer.xamarin.com/api/type/OpenTK.Graphics.ES30.GL/)型別。


### <a name="binding-design"></a>繫結設計

Xamarin.iOS 不只是基本的 Objective C 平台的繫結。 它會擴充的.NET 型別系統和更佳的混用 C# 與 OBJECTIVE-C 的分派系統

如同 P/Invoke 是實用的工具，來叫用在 Windows 和 Linux 上的原生程式庫，或為 IJW 支援可用於在 Windows 上的 COM interop，Xamarin.iOS 將擴充執行階段支援以 OBJECTIVE-C 物件的繫結 C# 物件。

在接下來的幾節並不需要使用者建立 Xamarin.iOS 應用程式，但可協助開發人員討論了解如何完成項目，以及幫助它們時建立更複雜的應用程式。



#### <a name="types"></a>類型

它用來進行有意義，而不是低層級的基礎類型，以 C# universe 公開 C# 型別。  這表示[API 使用 C# 「 字串 」 類型，而不是 NSString](~/ios/internals/api-design/nsstring.md)並使用強型別的 C# 陣列而不是公開 NSArray。

一般情況下，在 Xamarin.iOS 和 Xamarin.Mac 的設計，基礎`NSArray`物件不會公開。 相反地，執行階段會自動將轉換`NSArray`部分的強型別陣列以`NSObject`類別。 因此，Xamarin.iOS 不會公開像是 GetViews 傳回 NSArray 弱型別方法：

```csharp
NSArray GetViews ();
```

相反地，繫結會公開強類型的傳回值，就像這樣：

```csharp
UIView [] GetViews ();
```

有幾個方法中公開的`NSArray`，您可能要使用的邊角案例`NSArray`直接，但不建議他們使用 API 繫結中。

此外，在**傳統 API**而不是公開`CGRect`，`CGPoint`並`CGSize`CoreGraphics api，我們取代具有`System.Drawing`實作`RectangleF`， `PointF`和`SizeF`會讓開發人員會保留現有的 OpenGL 程式碼使用 OpenTK。 當使用新的 64 位元**統一 API**，應使用 CoreGraphics API。

<a name="Inheritance" />

#### <a name="inheritance"></a>繼承

Xamarin.iOS API 設計可讓開發人員擴充原生 OBJECTIVE-C 型別，它們會擴充 C# 類型，在衍生類別中，使用"override"關鍵字，以及將鏈結，以使用 「 基底 」 的 C# 關鍵字的基底實作的方式相同。

此設計可讓開發人員若要避免在其開發程序中，處理 OBJECTIVE-C 選取器，因為整個 OBJECTIVE-C 系統已包裝的 Xamarin.iOS 程式庫內。


#### <a name="types-and-interface-builder"></a>型別和介面產生器

當您建立.NET 類別的介面產生器所建立的類型的執行個體時，您必須提供的建構函式會採用單一`IntPtr`參數。
這是繫結所需的受管理的物件執行個體，與未受管理的物件。
程式碼包含單一行中，像這樣：

```csharp
public partial class void MyView : UIView {
   // This is the constructor that you need to add.
   public MyView (IntPtr handle) : base (handle) {}
}
```

<a name="Delegates" />


#### <a name="delegates"></a>委派

OBJECTIVE-C 與 C# 有不同的意義，word 委派兩種語言。

在 OBJECTIVE-C 世界中，和您會發現有關產品 CocoaTouch 線上文件中，委派通常是一組方法會回應類別的執行個體。 在於方法並非必要，這是非常類似於 C# 介面的差異。

這些委派扮演著重要的角色，UIKit 和其他產品 CocoaTouch Api。 它們用來完成各種工作：

-  提供通知給您的程式碼 （類似於 C# 或 Gtk + 中的事件傳遞）。
-  若要實作的資料視覺效果控制項的模型。
-  驅動控制項的行為。


程式設計模式被設計成最小化建立衍生的類別，以變更控制項的行為。 此解決方案是在精神與其他 GUI 工具組已完成多年類似： Gtk 的訊號，Qt 位置、 Winforms 事件、 WPF/Silverlight 事件，依此類推。 若要避免發生數百個 （一個用於每個動作） 的介面，或需要開發人員實作不需要太多的方法，OBJECTIVE-C 支援選擇性的方法定義。 這是不同於 C# 介面需要實作的所有方法。

在 OBJECTIVE-C 類別中，您會看到使用此程式設計模式的類別公開的屬性，通常稱為`delegate`，即必須實作介面的必要組件，以及零個或多個選擇性部分。

在 Xamarin.iOS 中提供三個互斥的機制，以繫結至這些委派：

1.  [透過事件](#Via_Events)。
2.  [透過強型別`Delegate`屬性](#StrongDelegate)
3.  [透過鬆散型別`WeakDelegate`屬性](#WeakDelegate)

例如，請考慮[UIWebView](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html)類別。 這會分派至[UIWebViewDelegate](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html)執行個體，這指派給[委派](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebView_Class/Reference/Reference.html#//apple_ref/occ/instp/UIWebView/delegate)屬性。

<a name="Via_Events" />

##### <a name="via-events"></a>透過事件

對於許多類型，Xamarin.iOS 將會自動建立適當的委派會將轉送`UIWebViewDelegate`到 C# 事件的呼叫。 針對 `UIWebView`：

-  [WebViewDidStartLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidStartLoad:)方法會對應至[UIWebView.LoadStarted](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadStarted/)事件。
-  [WebViewDidFinishLoad](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webViewDidFinishLoad:)方法會對應至[UIWebView.LoadFinished](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadFinished/)事件。
-  [WebView:didFailLoadWithError](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/UIWebViewDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIWebViewDelegate/webView:didFailLoadWithError:)方法會對應至[UIWebView.LoadError](https://developer.xamarin.com/api/event/UIKit.UIWebView.LoadError/)事件。

比方說，這個簡單的程式會記錄何時載入 web 檢視的開始和結束時間：

```csharp
DateTime startTime, endTime;
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += (o, e) => startTime = DateTime.Now;
web.LoadFinished += (o, e) => endTime = DateTime.Now;
```


##### <a name="via-properties"></a>透過屬性

可能有多個事件訂閱者時，則事件會很有用。 此外，事件會限制的情況下為從程式碼沒有傳回值的地方。

程式碼應該在其中傳回值的情況下，我們選擇改為屬性。 這表示，只有一個方法您可以設定在物件中指定的時間。

例如，您可以使用這項機制，可關閉鍵盤將畫面上的處理常式`UITextField`:

```csharp
void SetupTextField (UITextField tf)
{
    tf.ShouldReturn = delegate (textfield) {
        textfield.ResignFirstResponder ();
        return true;
    }
}
```

`UITextField`的`ShouldReturn`屬性在此情況下會做為引數的委派，會傳回 bool 值，並判斷是否 TextField 應進行使用按下 [傳回] 按鈕。 在我們的方法中，我們會傳回*真*呼叫者，但我們也會從螢幕移除鍵盤 (textfield 呼叫時，發生這種的情況`ResignFirstResponder`)。

<a name="StrongDelegate"/>

##### <a name="strongly-typed-via-a-delegate-property"></a>強型別透過委派屬性

如果您不希望使用事件，您可以提供您自己[UIWebViewDelegate](https://developer.xamarin.com/api/type/UIKit.UIWebViewDelegate/)子類別並將它指派給[UIWebView.Delegate](https://developer.xamarin.com/api/property/UIKit.UIWebView.Delegate/)屬性。 一旦 UIWebView.Delegate 已被指派，將無法再運作 UIWebView 事件分派機制，和對應的事件發生時，會叫用 UIWebViewDelegate 方法。

比方說，這個簡單的型別記錄載入網頁檢視所花費的時間：

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

上述會建立 UIWebViewer，而且它會指示它將訊息傳送至通知程式，我們建立回應訊息的類別的執行個體。

此模式也會用來控制特定控制項，例如在 UIWebView 案例中，行為[UIWebView.ShouldStartLoad](https://developer.xamarin.com/api/property/UIKit.UIWebView.ShouldStartLoad/)屬性允許`UIWebView`控制項的執行個體是否`UIWebView`會載入頁面與否。

此模式也會視幾個控制項提供的資料。 例如， [UITableView](https://developer.xamarin.com/api/type/UIKit.UITableView/)控制項是一個功能強大的資料表轉譯控制項 – 和外觀和內容由所驅使的執行個體[UITableViewDataSource](https://developer.xamarin.com/api/type/UIKit.UITableView/DataSource)

<a name="WeakDelegate"/>

### <a name="loosely-typed-via-the-weakdelegate-property"></a>鬆散型別透過 WeakDelegate 屬性

除了強型別屬性，還有弱式的具型別的的委派，讓開發人員所需時以不同的方式繫結項目。
強型別 everywhere`Delegate`屬性會公開在 Xamarin.iOS 的繫結，對應`WeakDelegate`也會公開屬性。

使用時`WeakDelegate`，您必須負責適當地裝飾類別使用[匯出](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/)屬性來指定選取器。 例如: 

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

請注意，一次`WeakDelegate`屬性已被指派，`Delegate`屬性將不會使用。 此外，如果您想要 [匯出] 繼承基底類別中實作方法，您必須讓它的公用方法。


## <a name="mapping-of-the-objective-c-delegate-pattern-to-c35"></a>對應到 C 的 Objective C 委派模式&#35;

當您看到 Objective C 範例，看起來像這樣：

```csharp
foo.delegate = [[SomethingDelegate] alloc] init]
```

這會指示要建立和建構類別 」 SomethingDelegate"的執行個體，並將值指派給 foo 變數上的 委派 屬性的語言。 這項機制使用 Xamarin.iOS 所支援，而 C# 語法是：

```csharp
foo.Delegate = new SomethingDelegate ();
```

在 Xamarin.iOS 中我們提供了強型別類別對應到 OBJECTIVE-C 委派類別。 若要使用它們，您將會是子類別化及覆寫 Xamarin.iOS 的實作所定義的方法。 如需有關其運作方式的詳細資訊，請參閱 < 一節 < 模型 > 下方。


##### <a name="mapping-delegates-to-c35"></a>對應到 C 的委派&#35;

UIKit 一般情況下會使用兩種形式的 Objective C 的委派。

第一種形式提供介面給元件的模型。 比方說，身為提供資料隨檢視的機制，例如資料儲存體設備，如需清單檢視。  在這些情況下，您應該一律建立適當的類別的執行個體，並指派的變數。

在下列範例中，我們提供`UIPickerView`模型會使用字串的實作：

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

第二種形式是要提供事件的通知。 在這些情況下，雖然我們仍會公開 API，以上所述的格式，但我們也提供 C# 事件，這應該是要用於快速的作業更簡單且整合式匿名委派與 C# 中的 lambda 運算式。

例如，您可以訂閱`UIAccelerometer`事件：

```csharp
UIAccelerometer.SharedAccelerometer.Acceleration += (sender, args) => {
   UIAcceleration acc = args.Acceleration;
   Console.WriteLine ("Time={0} at {1},{2},{3}", acc.Time, acc.X, acc.Y, acc.Z);
}
```

兩個選項可用，在他們有意義，但是身為程式設計人員，您必須挑選其中一種。 如果您建立您自己的強型別回應/委派執行個體，並將它指派，C# 事件將無法正常運作。 如果您使用 C# 事件，絕不會呼叫您的回應者/委派類別中的方法。

使用上述範例`UIWebView`可以使用 C# 3.0 lambda，像這樣撰寫：

```csharp
var web = new UIWebView (new CGRect (0, 0, 200, 200));
web.LoadStarted += () => { startTime = DateTime.Now; }
web.LoadFinished += () => { endTime = DateTime.Now; }
```


#### <a name="responding-to-events"></a>回應事件

在 OBJECTIVE-C 程式碼，有時候多個控制項和提供者的資訊為多個控制項，事件處理常式將裝載於相同的類別。 這是可行的因為類別回應訊息，而且只要回應訊息的類別，就可以將物件連結在一起。

先前詳細說明，Xamarin.iOS 支援這兩個 C# 事件為基礎的程式設計模型，而且 OBJECTIVE-C 委派模式，其中您可以建立新的類別，用的實作委派，並覆寫所需的方法。

它也可支援 Objective C 的模式，其中多個不同作業的回應均裝載在相同的執行個體的類別。 若要透過執行此作業，您必須使用 Xamarin.iOS 繫結的低層級的功能。

例如，如果您想要您的類別，以回應這兩`UITextFieldDelegate.textFieldShouldClear`： 訊息和`UIWebViewDelegate.webViewDidStartLoad`： 位於相同的執行個體的類別，您必須使用 [匯出] 屬性宣告：

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

方法的 C# 名稱並不重要;最重要的是傳遞給 [匯出] 屬性的字串。

當使用這種程式設計樣式，請確定 C# 參數符合執行階段引擎會傳遞的實際型別。

<a name="Models" />

#### <a name="models"></a>模型

在 UIKit 儲存設備，或回應程式會使用協助程式類別實作中，這些通常稱為 OBJECTIVE-C 程式碼中的委派，以及它們被實作為通訊協定。

OBJECTIVE-C 通訊協定就像是介面，但它們支援選擇性方法，也就是不是所有的方法需要實作此通訊協定才能運作。

有兩種實作模型。 您可以手動實作，或使用現有的強型別的定義。


當您嘗試實作尚未 Xamarin.iOS 所繫結的類別必須手動機制。 它是很容易進行：

-  您的類別與執行階段註冊的旗標
-  您想要覆寫每個方法上套用的實際的選取器名稱的 [匯出] 屬性
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

OBJECTIVE-C 選取器名稱 ("applicationDidFinishLaunching:") 宣告匯出的屬性和類別已向`[Register]`屬性。

Xamarin.iOS 提供強型別的宣告，可供使用，不需要手動繫結。 若要支援這個程式設計模型，Xamarin.iOS 執行階段會支援 [模型] 屬性，在類別宣告。 這會通知執行階段，除非方法時，它應該不在類別中的所有方法連接會明確實作。

這表示在 UIKit，代表一種通訊協定與選擇性方法的類別撰寫像這樣：

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

當您想要實作只會實作部分方法的模型時，您只需要為覆寫您感興趣，並忽略其他方法的方法。 執行階段只會將覆寫方法，也就是不在 OBJECTIVE-C 世界的原始方法連結。

相當於先前的手動範例是：

```csharp
public class AppController : UIApplicationDelegate {
    public override void FinishedLaunching (UIApplication uia)
    {
     ...
    }
}
```

優點是，不是需要深入了解 OBJECTIVE-C 標頭檔，若要尋找的選取器、 引數或以 C# 中，對應的類型，而且 intellisense 從 Visual Studio for Mac，連同強式型別


#### <a name="xib-outlets-and-c35"></a>XIB 輸出 」 和 「 C&#35;

> [!IMPORTANT]
> 使用 XIB 檔案時，本節會說明與輸出的 IDE 整合。 當使用適用於 iOS 的 Xamarin 設計工具，這所有取代輸入的名稱**身分識別 > 名稱**IDE，如下所示的 [屬性] 區段中：
>
> [![](images/designeroutlet.png "IOS 設計工具中輸入的項目名稱")](images/designeroutlet.png#lightbox)
>
>如需有關 iOS 設計工具的詳細資訊，請檢閱[iOS 設計工具簡介](~/ios/user-interface/designer/introduction.md#how-it-works)文件。

這是低層級的輸出與 C# 的整合方式描述，並提供適用於 Xamarin.iOS 的進階使用者。 使用 Visual Studio for Mac 對應完成時自動使用在幕後為您產生航班的程式碼。

當您設計您的使用者介面與介面產生器時，您只設計應用程式的外觀，並將建立一些預設的連線。 如果您想要以程式設計方式擷取資訊、 變更執行階段將控制項的行為或在執行階段將控制項修改，就必須將一些控制項繫結至 managed 程式碼。

這會在幾個步驟：

1.  新增**輸出宣告**至您**檔案的擁有者**。
1.  連接您的控制項**檔案的擁有者**。
1.  存放區 UI，再加上連線到您的 XIB/NIB 檔案。
1.  NIB 檔案在執行階段載入。
1.  輸出變數的存取。


在 Apple 的文件中建置的 Interface Builder 的介面係透過 (3) 的步驟 (1)。

當使用 Xamarin.iOS，您的應用程式必須建立衍生自 UIViewController 類別。 它實作它像這樣：

```csharp
public class MyViewController : UIViewController {
    public MyViewController (string nibName, NSBundle bundle) : base (nibName, bundle)
    {
        // You can have as many arguments as you want, but you need to call
        // the base constructor with the provided nibName and bundle.
    }
}
```

然後從 NIB 檔案載入您 ViewController，這麼做：

```csharp
var controller = new MyViewController ("HelloWorld", NSBundle.MainBundle, this);
```

這會從 NIB 載入使用者介面。 現在，若要存取輸出，就必須以通知執行階段，我們想要存取它們。 若要這樣做，`UIViewController`子類別必須宣告屬性，並以 [Connect] 屬性標註。 與下列類似：

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

屬性的實作是實際擷取並儲存實際的原生類型的值。

您不需要擔心這個使用 Visual Studio for Mac 和 InterfaceBuilder 時。 Visual Studio for Mac 會自動反映所有宣告的輸出，以編譯為專案中的部分類別中的程式碼。

#### <a name="selectors"></a>選取器

Objective C 程式設計的核心概念是選取器。 您通常會遇到需要您將選取器，或需要您的程式碼來回應選取器 Api。

在 C# 中建立新的選取器是很簡單 – 您剛建立的新執行個體`ObjCRuntime.Selector`類別，並在需要它的 API 中的任何位置使用的結果。 例如: 

```csharp
var selector_add = new Selector ("add:plus:");
```

針對 C# 方法回應的選取器呼叫，它必須繼承自`NSObject`以選取器名稱使用，則必須裝飾型別和 C# 方法`[Export]`屬性。 例如: 

```csharp
public class MyMath : NSObject {
    [Export ("add:plus:")]
    int Add (int first, int second)
    {
         return first + second;
    }
}
```

請注意該選取器名稱**必須**完全符合，包括所有中繼和結尾的冒號 (":")，如果有的話。

#### <a name="nsobject-constructors"></a>NSObject 建構函式

在衍生自的 Xamarin.iOS 中的大部分類別`NSObject`會公開 （expose） 的建構函式特有之物件的功能，但它們也會公開各種建構函式並非立即可見。

建構函式會使用，如下所示：

```csharp
public Foo (IntPtr handle)
```

這個建構函式用來執行階段必須將您的類別對應至未受管理的類別時，將您的類別具現化。 會發生這種情況是當您載入 XIB/NIB 檔案。  此時，OBJECTIVE-C 執行階段會在 unmanaged 世界中，建立物件，這個建構函式會呼叫以初始化 managed 的端。

一般而言，您只需要是呼叫基底建構函式的控制代碼參數，並在主體中，執行任何所需的初始化。

```csharp
public Foo ()
```

這是預設建構函式類別，並在 Xamarin.iOS 提供類別，這之間，初始化 Foundation.NSObject 類別及其所有的類別，並且在結束時，鏈結這 objective-c`init`類別上的方法。

```csharp
public Foo (NSObjectFlag x)
```

這個建構函式用來初始化執行個體，但防止程式碼結尾呼叫 OBJECTIVE-C 「 init 」 方法。 這一般用於當您已經註冊了初始設定時 (當您使用`[Export]`您建構函式上) 或當您已完成您的初始化透過另一個的平均值。

```csharp
public Foo (NSCoder coder)
```

這個建構函式可將物件從 NSCoding 執行個體正在初始化位置。 如需詳細資訊，請參閱 Apple 的[封存與序列化程式的指南。](http://developer.apple.com/mac/library/documentation/Cocoa/Conceptual/Archiving/index.html#//apple_ref/doc/uid/10000047i)

#### <a name="exceptions"></a>例外狀況

Xamarin.iOS API 設計不會引發 Objective C 例外狀況當做 C# 例外狀況。 設計會強制執行，一開始沒有廢棄項目傳送到 OBJECTIVE-C 世界和無效的資料曾經之前，必須產生任何例外狀況所產生的繫結本身傳遞至 OBJECTIVE-C 世界。

#### <a name="notifications"></a>通知

在 iOS 和 OS X 中，開發人員可以訂閱由基礎平台廣播的通知。 這是藉由使用`NSNotificationCenter.DefaultCenter.AddObserver`方法。 `AddObserver`方法採用兩個參數是您想要訂閱的通知; 另一個是在引發通知時要叫用方法。

在 Xamarin.iOS 和 Xamarin.Mac 中，各種通知的金鑰位於上觸發通知的類別。 通知所引發的例如`UIMenuController`做為裝載`static NSString`中的屬性`UIMenuController`類別結尾為 「 通知 」 的名稱。

### <a name="memory-management"></a>記憶體管理

Xamarin.iOS 則具有會負責為您正在釋放資源，它們不會再使用時的記憶體回收行程。 除了記憶體回收行程中，所有的物件衍生自`NSObject`實作`System.IDisposable`介面。

#### <a name="nsobject-and-idisposable"></a>NSObject 和 IDisposable

公開`IDisposable`介面是便利的方式，來協助開發人員在釋放物件可能封裝大型記憶體區塊的 (例如`UIImage`看起來會像只是無辜的指標，但無法指向 2 mb 的映像) 和其他重要且最有限的資源 （例如視訊解碼緩衝區）。

NSObject 實作 IDisposable 介面，也[.NET Dispose 模式](http://msdn.microsoft.com/library/fs2xkftw.aspx)。 這可讓開發人員覆寫 Dispose 行為和發行自己的資源，視 NSObject 的子類別。 例如，請考慮一大堆映像會保持此檢視控制器：

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

處置 managed 的物件時，它不再有用。 您可能還有參考物件，但該物件是針對與目的無效此時。 某些.NET Api 會確保這藉由擲回 ObjectDisposedException，如果您嘗試存取已處置的物件上的任何方法，例如：

```csharp
var image = UIImage.FromFile ("demo.png");
image.Dispose ();
image.XXX = false;  // this at this point is an invalid operation
```

即使您仍然可以存取該變數的 「 映像 」，其實是無效的參考，而不再保留映像 OBJECTIVE-C 物件的點。

但處置 C# 中的物件不代表一定會終結物件。 您所做的只是釋放 C# 有物件的參考。 可以 Cocoa 環境可能已保留供自身使用的參考周圍。 比方說，如果您 UIImageView 映像將屬性設定為映像，然後您處置映像時，基礎 UIImageView 屍體它自己的參考而將保留此物件的參考，直到完成為止使用它。

#### <a name="when-to-call-dispose"></a>何時要呼叫 Dispose

當您需要 Mono 中擺脫掉您的物件時，您應該呼叫 Dispose。 可能的使用案例時，Mono 一無所知您 NSObject 實際上會保存重要的資源，例如記憶體或資訊集區的參考。 在這些情況下，您應該呼叫 Dispose，以立即釋放的記憶體，而不是等待進行記憶體回收週期的 Mono 的參考。

就內部而言，當 Mono 建立[NSString 參考從 C# 字串](~/ios/internals/api-design/nsstring.md)，它將會處置它們立即以降低記憶體回收行程必須進行的工作數量。 將會執行約來處理，速度 GC 較少的物件。

#### <a name="when-to-keep-references-to-objects"></a>當保留物件的參考

一個副作用自動記憶體管理的是，GC 會移除未使用的物件，只要不有任何參考。 這有時可以有令人驚訝的副作用，例如，如果您建立一個本機變數來保存您的最上層檢視控制器，或最上層視窗中，並接著讓那些消失後您的後置。

如果您不要對物件保留在您的靜態參考或執行個體變數，Mono 會很高興地操作，呼叫 dispose （） 方法，它們會釋放物件的參考。 因為這可能是只有未完成的參考，OBJECTIVE-C 執行階段會為您終結物件。

## <a name="related-links"></a>相關連結

- [繫結欄位](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_Fields)
