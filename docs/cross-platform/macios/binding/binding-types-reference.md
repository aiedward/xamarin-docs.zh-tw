---
title: "繫結型別參考指南"
ms.topic: article
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
ms.technology: xamarin-cross-platform
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: 568650a850b9db1fa22deef55eebb6a437e7e0b7
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="binding-types-reference-guide"></a>繫結型別參考指南

本文件說明屬性，您可以用來加上註解您的 API 合約檔案到磁碟機繫結，以及產生的程式碼的清單

Xamarin.iOS 和 Xamarin.Mac API 合約是在 C# 中大部分寫入為定義的方式的介面定義 Objective C 程式碼會顯示為 C#。 程序包含混合的介面宣告再加上 API 合約可能需要一些基本的類型定義。 如需繫結類型的簡介，請參閱我們的附屬指南[繫結 Objective C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)。

## <a name="type-definitions"></a>型別定義

語法：

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

在您的合約定義的每個介面`[BaseType]`宣告產生物件的基底類型的屬性。 在上述宣告中`MyType`類別 C# 類型將會產生成 Objective C 類型的繫結呼叫**MyType**。

如果您的類型名稱之後指定任何型別 (在上述範例`Protocol1`和`Protocol2`) 使用介面繼承語法這些介面的內容都將予以內嵌好像它們一直為合約的一部分`MyType`。
Xamarin.iOS 介面型別會採用一種通訊協定是透過的方式內嵌的所有方法和通訊協定中宣告成類型本身的屬性。

下列示範如何 Objective C 宣告`UITextField`Xamarin.iOS 合約中，都會定義：

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

寫入像這樣做為 C# API 合約：

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

您可以將其他屬性套用至介面，以及設定 BaseType 屬性來控制其他許多方面的程式碼產生。


### <a name="generating-events"></a>產生事件

Xamarin.iOS 和 Xamarin.Mac API 設計一項功能是我們將 OBJECTIVE-C 委派類別對應為 C# 事件和回撥。 使用者可以選擇每個執行個體為基礎他們是否想要採用 Objective C 程式設計模式，藉由指定屬性，例如**委派**實作的各種方法的類別的執行個體，Objective C執行階段會呼叫，或是選擇 C#-樣式事件和屬性。

讓我們看到如何使用 OBJECTIVE-C 模型的其中一個範例：

```csharp
bool MakeDecision ()
{
    return true;
}

void Setup ()
{
     var scrollView = new UIScrollView (myRect);
     scrollView.Delegate = new MyScrollViewDelegate ();
     ...
}

class MyScrollViewDelegate : UIScrollViewDelegate {
    public override void Scrolled (UIScrollView scrollView)
    {
        Console.WriteLine ("Scrolled");
    }

    public override bool ShouldScrollToTop (UIScrollView scrollView)
    {
        return MakeDecision ();
    }
}
```

在上述範例中，您可以看到，我們已選擇覆寫兩個方法，其中一個通知該捲動事件花費，而且第二個，就應會傳回布林值，指示是否應該捲動到頂端 scrollView 的回呼或不是。

C# 模型可讓您的程式庫聆聽使用 C# 事件語法或屬性語法來連結都應傳回值的回呼通知使用者。

這是相同功能的 C# 程式碼看起來像使用 lambda 的方式：

```csharp
void Setup ()
{
    var scrollview = new UIScrollView (myRect);
    // Event connection, use += and multiple events can be connected
    scrollView.Scrolled += (sender, eventArgs) { Console.WriteLine ("Scrolled"); }

    // Property connection, use = only a single callback can be used
    scrollView.ShouldScrollToTop = (sv) => MakeDecision ();
}
```

因為事件不會傳回的值 （具有 void 傳回型別），您可以連接多個複本。 `ShouldScrollToTop`不是事件，它是改為與型別屬性`UIScrollViewCondition`具有此簽章：

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

它會傳回布林值，在此情況下 lambda 語法可讓我們只傳回從數值`MakeDecision`函式。

繫結產生器支援產生的事件和連結之類的類別的屬性`UIScrollView`具有其`UIScrollViewDelegate`（也會呼叫這些模型類別），這是加上附註您`BaseType`具有定義`Events`和`Delegates`參數 （如下所述）。 除了標註`BaseType`使用這些參數是為了通知的產生器的一些多項元件。

接受一個以上參數的事件 （Objective C 中的慣例是委派類別中的第一個參數是寄件者物件的執行個體） 您必須提供您想要產生的 EventArgs 類別需要名稱。 做法是使用`EventArgs`模型類別中的方法宣告上的屬性。 例如: 

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述宣告會產生`UIImagePickerImagePickedEventArgs`類別衍生自`EventArgs`和組件兩個參數，`UIImage`和`NSDictionary`。 產生器產生這個錯誤：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然後，它會公開下列 UIImagePickerController 類別中：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

傳回值的模型方法會以不同的方式繫結。 那些需要產生的 C# 的委派 （方法的簽章） 以及預設值傳回以防使用者並不會提供自己實作這兩個名稱。 例如，`ShouldScrollToTop`定義如下：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

將會建立上述`UIScrollViewCondition`委派的簽章，已如上所示，而且如果使用者未提供實作，傳回值會是 true。

除了`DefaultValue`屬性，您也可以使用`DefaultValueFromArgument`的指示來呼叫中傳回指定之參數的值產生器或`NoDefaultValue`指示產生器沒有預設值的參數。


### <a name="basetypeattribute"></a>BaseTypeAttribute

語法：

```csharp
public class BaseTypeAttribute : Attribute {
        public BaseTypeAttribute (Type t);

        // Properties
        public Type BaseType { get; set; }
        public string Name { get; set; }
        public Type [] Events { get; set; }
        public string [] Delegates { get; set; }
        public string KeepRefUntil { get; set; }
}
```

#### <a name="basetypename"></a>BaseType.Name

您使用`Name`屬性，即可控制這種類型會將繫結至 OBJECTIVE-C 世界中的名稱。 這通常用來指定 C# 類型的名稱與.NET Framework 設計方針、 相容，但其對應至未遵循這個慣例的 Objective C 中的名稱。

範例中，我們在下列範例中的對應 Objective C`NSURLConnection`類型`NSUrlConnection`，如.NET Framework 設計方針會使用 「 Url 」 而不是"URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

指定的名稱會指定產生做為值`[Register]`繫結中的屬性。 如果`Name`未指定，類型的簡短名稱做為值`Register`中產生的輸出屬性。

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events 和 BaseType.Delegates

這些屬性用於磁碟機的 C# 產生的事件樣式中產生的類別。 它們用來連結其 OBJECTIVE-C 委派類別的指定的類別。 您將會遇到許多情況下，類別使用委派類別來傳送通知和事件的位置。 例如`BarcodeScanner`必須隨附`BardodeScannerDelegate`類別。 `BarcodeScanner`類別通常會有您所要指派的執行個體的 「 委派 」 屬性`BarcodeScannerDelegate`，而此運作方式，您可能要公開給您的使用者以 C#-像樣式事件介面，並在這些情況下，您會使用`Events`和`Delegates`屬性`BaseType`屬性。

這些屬性永遠會一起設定和必須有相同數目的項目，並保持同步。`Delegates`陣列包含一個字串，您想要換行，每一個弱式型別的委派，事件陣列會包含您想要與其建立關聯的每個類型的一種類型。

```csharp
[BaseType (typeof (NSObject),
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIAccelerometerDelegate)})]
public interface UIAccelerometer {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIAccelerometerDelegate {
}
```


#### <a name="basetypekeeprefuntil"></a>BaseType.KeepRefUntil

如果建立此類別的新執行個體時，您可以套用這個屬性，該物件的執行個體將會保留之前所參考之方法`KeepRefUntil`已叫用。 這是有助於提升您的應用程式開發介面的可用性時不想讓使用者將使用您的程式碼周圍物件的參考。 這個屬性的值是在方法名稱`Delegate`類別，因此您必須使用此事件搭配和`Delegates`以及屬性。

下列範例顯示如何利用此`UIActionSheet`Xamarin.iOS 中：

```csharp
[BaseType (typeof (NSObject), KeepRefUntil="Dismissed")]
[BaseType (typeof (UIView),
           KeepRefUntil="Dismissed",
           Delegates=new string [] { "WeakDelegate" },
           Events=new Type [] {typeof(UIActionSheetDelegate)})]
public interface UIActionSheet {
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIActionSheetDelegate {
    [Export ("actionSheet:didDismissWithButtonIndex:"), EventArgs ("UIButton")]
    void Dismissed (UIActionSheet actionSheet, nint buttonIndex);
}
```


### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

當這個屬性會套用至介面定義將會使產生器無法產生預設建構函式。

當您需要使用其中一個類別中的其他建構函式初始化物件時，請使用這個屬性。


### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

當這個屬性套用至介面定義，它會為私用的旗標的預設建構函式。 這表示您可以仍具現化此類別的物件在內部從延伸模組檔案，但它不會只可讓您類別的使用者。


### <a name="categoryattribute"></a>CategoryAttribute

繫結 OBJECTIVE-C 類別，以及公開以 C# 擴充方法，以鏡像處理 OBJECTIVE-C 公開功能的方式，請在類型定義上使用這個屬性。

類別是用來擴充的方法和類別中可用的屬性集 OBJECTIVE-C 機制。   在實務上，它們可用來擴充功能的基底類別 (例如`NSObject`) 的特定架構中的連結時 (例如`UIKit`)，可用，但前提是已連結的新架構，以使它們的方法。   在某些其他情況下，它們會依功能用來組織類別中的功能。   它們是在夠用類似於 C# 擴充方法。

這是類別在目標 c： 會尋找

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上述範例中如果上找到文件庫會擴充的執行個體`UIView`方法`makeBackgroundRed`。

若要將那些繫結，您可以使用`[Category]`介面定義上的屬性。   當使用`Category`屬性的意義`[BaseType]`從用來指定擴充，而是要延伸的類型的基底類別的屬性變更。

下列示範如何`UIView`繫結和轉換成 C# 擴充方法擴充功能：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

將會建立上述`MyUIViewExtension`類別，其中包含`MakeBackgroundRed`擴充方法。   這表示您現在可以呼叫 「 MakeBackgroundRed"任何`UIView`子類別，讓您得到目標 c 相同的功能

在某些情況下，您會發現**靜態**類別內的成員如下列範例所示：

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

這會導致**正確**類別 C# 介面定義：

```csharp
[Category]
[BaseType (typeof (FooObject))]
interface FooObject_Extensions {

    // Incorrect Interface definition
    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

這是不正確因為使用`BoolMethod`延伸模組需要的執行個體`FooObject`但您要繫結 ObjC**靜態**延伸模組，這是因為以下事實的 C# 擴充方法的實作方法的副作用。

若要使用上述定義的唯一方法是以下列醜陋程式碼：

```csharp
(null as FooObject).BoolMethod (range);
```

若要避免此建議是內嵌`BoolMethod`內定義`FooObject`本身介面定義，這可讓您呼叫這項擴充功能，它可能是像`FooObject.BoolMethod (range)`。

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

如果發現，我們會發出警告 (BI1117)`[Static]`成員內`[Category]`定義。 如果您真的想要有`[Static]`成員內您`[Category]`定義您可以使用 play 警告`[Category (allowStaticMembers: true)]`或而是將您的成員或`[Category]`介面定義與`[Internal]`。


### <a name="staticattribute"></a>StaticAttribute

當這個屬性會套用至類別只會產生靜態類別，一個不是衍生自`NSObject`所以`[BaseType]`屬性會被忽略。 靜態類別用來裝載您要公開的 C 公用變數。

例如: 

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

會產生具有下列應用程式開發介面的 C# 類別：

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```


## <a name="protocol-definitionsmodel"></a>通訊協定定義模型

模型通常會使用通訊協定的實作。
它們不同於執行階段會將只登錄 OBJECTIVE-C 實際上已覆寫的方法。
否則，將不會註冊方法。

這通常表示當您子類別的類別，已標示為與`ModelAttribute`，您不應該呼叫基底方法。   呼叫該方法會擲回例外狀況，您應該要在您覆寫任何方法的子類別上實作的整個行為。


### <a name="abstractattribute"></a>AbstractAttribute

根據預設，是一種通訊協定的一部分的成員是不必要的。 這可讓使用者建立的子類別`Model`物件只會在 C# 類別衍生並覆寫他們很關心的方法。 Objective C 合約有時需要使用者提供這個方法的實作 (那些標幟@requiredObjective C 中的指示詞)。 在這些情況下，您應該加上旗標與那些方法`Abstract`屬性。

`Abstract`屬性可以套用至方法或屬性，並造成加上旗標產生的成員為 「 抽象 」 和類別是抽象類別產生器。

下列取自 Xamarin.iOS:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UITableViewDataSource {
    [Export ("tableView:numberOfRowsInSection:")]
    [Abstract]
    nint RowsInSection (UITableView tableView, nint section);
}
```

<a name="DefaultValueAttribute" />

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

指定要傳回之模型方法中，如果使用者未提供一種方法中的模型物件，這個特定方法的預設值

語法：

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

例如，在下列的虛數委派類別，如`Camera`類別，我們提供`ShouldUploadToServer`這會公開為屬性`Camera`類別。 如果使用者`Camera`類別沒有明確設定 lambda，可回應 true 或 false 的值，傳回的預設值在此情況下會為 false，我們已在指定的值`DefaultValue`屬性：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

如果使用者設定的處理常式中的虛數的類別，這個值會遭到忽略：

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

另請參閱： [NoDefaultValueAttribute](#NoDefaultValueAttribute)， [DefaultValueFromArgumentAttribute](#DefaultValueFromArgumentAttribute)。

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

語法：

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

當模型類別傳回值的方法上提供這個屬性會指示來傳回指定之參數的值，如果使用者未提供自己的方法或 lambda 的產生器。

範例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

在上述案例如果使用者`NSAnimation`類別選擇要使用的任何 C# 事件/屬性，而且未設定`NSAnimation.ComputeAnimationCurve`方法或 lambda 的傳回值會進行參數中傳遞的值。

另請參閱： [NoDefaultValueAttribute](#NoDefaultValueAttribute)， [DefaultValueAttribute](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

有時道理不公開事件時，或委派屬性從模型類別，到主機類別讓加入此屬性會指示以避免任何以其裝飾的方法產生的產生器。

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);

    [Export ("imagePickerController:didFinishPickingImage:"), IgnoredInDelegate)] // No event generated for this method
    void FinishedPickingImage (UIImagePickerController picker, UIImage image);
}
```

### <a name="delegatenameattribute"></a>DelegateNameAttribute

這個屬性用於模型方法的傳回值來設定要使用的委派簽章的名稱。

範例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

使用上述的定義，產生器會產生下列公用宣告：

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

這個屬性用來允許變更產生的裝載類別中的屬性名稱的產生器。 有時候會很有用時 FooDelegate 類別方法的名稱對於委派類別，但會有點奇怪中做為屬性的主控類別。

這也是真的很有用 （和所需） 當您有兩個或合理的多個多載方法會保留其名為 FooDelegate 類別中，但您想要在具有較佳的指定名稱的裝載類別中公開它們。

範例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

使用上述的定義，產生器會產生下列公用主機類別中的宣告：

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

### <a name="eventargsattribute"></a>EventArgsAttribute

接受一個以上參數的事件 （Objective C 中的慣例是委派類別中的第一個參數是寄件者物件的執行個體） 您必須提供您想要產生的 EventArgs 類別需要名稱。 做法是使用`EventArgs`屬性中的方法宣告上您`Model`類別。

例如: 

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述宣告會產生`UIImagePickerImagePickedEventArgs`類別衍生自 EventArgs 且組件兩個參數，`UIImage`和`NSDictionary`。 產生器產生這個錯誤：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然後，它會公開下列 UIImagePickerController 類別中：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```


### <a name="eventnameattribute"></a>EventNameAttribute

這個屬性用來允許變更的事件或在類別中產生的屬性名稱的產生器。 有時候會很有用時名稱`Model`類別方法適合模型類別，但看起來會奇數原始類別中的事件或屬性。

例如，`UIWebView`使用從下列位元`UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

上述公開`LoadingFinished`中方法`UIWebViewDelegate`，但`LoadFinished`為連結中的最多事件`UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```


### <a name="modelattribute"></a>ModelAttribute

當您將套用`Model`您應用程式開發介面，執行階段的合約中的類型定義的屬性會產生特殊的程式碼，將只叫用到的類別中的方法如果使用者已覆寫類別中的方法。 這個屬性通常會套用至包裝 OBJECTIVE-C 委派類別的所有 Api。

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

指定在模型上的方法不會提供預設的傳回值。

這會搭配 Objective C 執行階段回應"false"Objective C 執行階段要求，以判斷指定的選取器。 是否此類別中實作。

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

另請參閱： [DefaultValueAttribute](#DefaultValueAttribute)和[DefaultValueAttribute](#DefaultValueAttribute)。

## <a name="protocols"></a>通訊協定

在 C# 中確實存在 OBJECTIVE-C 通訊協定概念。 通訊協定類似於 C# 介面但它們的不同處在於會採用該類別必須實作所有的方法和屬性宣告使用通訊協定。 而是一些方法和屬性是選擇性的。

某些通訊協定通常用做為模型類別，這些應該繫結使用的模型屬性。

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
```

已加入從 MonoTouch 7.0 開始新的和改進的通訊協定，繫結功能。  包含任何定義`[Protocol]`屬性實際上會產生三個支援的類別來大幅改善您使用的通訊協定的方式：

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

**類別實作**提供完整的抽象類別，您可以用來覆寫個別的方法，並取得完整的型別安全。 但是，由於 C# 中不支援多重繼承，所以有很的案例，您可能會在此設定需要不同的基底類別，但仍想要實作介面。

這是 where 產生**介面定義**傳入。  它是具有所有必要的方法，從通訊協定的介面。  這可讓開發人員用來實作您的通訊協定只是實作介面。  執行階段會自動登錄型別以採用之通訊協定。

請注意，此介面只列出需要的方法，且沒有公開選擇性的方法。   這表示採用之通訊協定的類別會收到需要的方法，檢查完整的型別，但就必須求助於弱式類型 （以手動方式使用的匯出屬性，並符合簽章） 為選擇性通訊協定方法。

為了更方便使用的通訊協定會使用的 API，繫結工具也會產生延伸模組方法類別公開的所有選用的方法。   這表示，只要您使用應用程式開發介面，您將能夠處理通訊協定為具有所有方法。

如果您想要在您的 API 中使用的通訊協定定義，您必須在您的應用程式開發介面定義中撰寫基本架構空的介面。   如果您想要使用 MyProtocol API 中，您需要執行這項操作：

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

### <a name="adopting-protocol-generated-interfaces"></a>採用通訊協定產生的介面

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

### <a name="protocol-inlining"></a>內嵌通訊協定

當您繫結已宣告為採用一種通訊協定的現有 Objective C 類型時，您會想要內嵌通訊協定直接。 若要這樣做，只是宣告您的通訊協定 」 為不含任何介面`[BaseType]`屬性，並列出基底介面的介面清單中的通訊協定。

範例：

```csharp
interface SpeakProtocol {
    [Export ("say:")]
    void Say (string msg);
}

[BaseType (typeof (NSObject))]
interface Robot : SpeakProtocol {
    [Export ("awake")]
    bool Awake { get; set; }
}
```


## <a name="member-definitions"></a>成員的定義

本節中的屬性會套用至類型的個別成員： 屬性和方法宣告。


### <a name="alignattribute"></a>AlignAttribute

用來指定屬性的傳回類型的對齊值。 某些屬性接受指標必須在特定界限對齊的位址 (在發生這種情況，例如某些 Xamarin.iOS`GLKBaseEffect`屬性必須為 16 位元組對齊)。 您可以使用這個屬性來裝飾 getter，並使用對齊值。 這通常會搭配`OpenTK.Vector4`和`OpenTK.Matrix4`Objective C 應用程式開發介面與整合時，型別。

範例：

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```


### <a name="appearanceattribute"></a>AppearanceAttribute

`Appearance`屬性僅限於 iOS5 引進外觀管理員。

`Appearance`屬性可以套用至任何方法或屬性，參與`UIAppearance`架構。 當這個屬性會套用至方法或類別中的屬性時，它會直接繫結產生器來建立用來設定這個類別的所有執行個體或符合特定準則的執行個體的強型別外觀類別。

範例：

```csharp
public interface UIToolbar {
    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);
}
```

上述會 UIToolbar 中產生下列程式碼：

```csharp
public partial class UIToolbar {
    public partial class UIToolbarAppearance : UIView.UIViewAppearance {
        public virtual void SetBackgroundImage (UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);
        public virtual UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics)
    }
    public static new UIToolbarAppearance Appearance { get; }
    public static new UIToolbarAppearance AppearanceWhenContainedIn (params Type [] containers);
}
```

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute (Xamarin.iOS 5.4)

使用`AutoReleaseAttribute`方法和屬性，以包裝方法引動過程的方法上`NSAutoReleasePool`。

Objective C 中有某些方法會傳回新增到預設的值`NSAutoReleasePool`。 根據預設，這些將會進入您的執行緒`NSAutoReleasePool`，但只要 managed 的物件存留 Xamarin.iOS 也會保留物件參考，因為您可能不想將額外的參考保留在`NSAutoReleasePool`其中只取得清空直到您的執行緒傳回控制權傳輸至下一個執行緒，或返回主迴圈。

大量的屬性，例如套用這個屬性 (例如`UIImage.FromFile`) 加入預設的物件傳回`NSAutoReleasePool`。 若沒有這個屬性，只要您的執行緒未將控制權傳回主迴圈會保留映像。 Uf 執行緒某種背景程式下載程式會永遠保持運作，等候工作，影像就永遠不會釋放。

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`ForcedTypeAttribute`用來強制執行的 managed 型別建立，即使傳回 unmanaged 的物件不符合繫結定義中所描述的類型。

標頭中所描述的類型與原生方法的傳回的類型不相符時這非常有用，例如下列 OBJECTIVE-C 定義從`NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

清楚地說明，它會傳回`NSURLSessionDownloadTask`執行個體，但尚未它**傳回** `NSURLSessionTask`，這是超級類別，因此不轉換成`NSURLSessionDownloadTask`。 既然我們已經在型別安全內容中`InvalidCastException`即將發生的狀況。

若要符合的標頭的描述，並避免`InvalidCastException`、`ForcedTypeAttribute`用。

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

`ForcedTypeAttribute`也接受名為的布林值`Owns`也就是`false`預設`[ForcedType (owns: true)]`。 擁有參數用來依照[擁有權原則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html)如**核心 Foundation**物件。

`ForcedTypeAttribute`只適用於在`parameters`，`properties`和`return value`。

### <a name="bindasattribute"></a>BindAsAttribute

`BindAsAttribute`允許繫結`NSNumber`，`NSValue`和`NSString`（列舉） 到更精確的 C# 類型。 屬性可以用來建立更好、 更正確透過原生 API 的.NET API。

您可以將方法 （在傳回值）、 參數和屬性與裝飾`BindAs`。 唯一的限制是，您的成員**必須不**內`[Protocol]`或`[Model]`介面。

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

目前支援的封裝類型包括：

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

下列 C# 支援的資料類型要封裝從/到`NSValue`:

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint / PointF
* CGRect / RectangleF
* CGSize / SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

下列 C# 支援的資料類型要封裝從/到`NSNumber`:

* bool
* byte
* double
* float
* short
* int
* long
* sbyte
* ushort
* uint
* ulong
* nfloat
* nint
* nuint
* 列舉

#### <a name="nsstring"></a>NSString

`[BindAs]` 適用於與 conjuntion[列舉揭開 NSString 常數](#enum-attributes)讓您可以建立較佳的.NET API，例如：

```csharp
[BindAs (typeof (CAScroll))]
[Export ("supportedScrollMode")]
NSString SupportedScrollMode { get; set; }
```

會輸出：

```csharp
[Export ("supportedScrollMode")]
CAScroll SupportedScrollMode { get; set; }
```

我們將會處理`enum`  <->  `NSString`才提供的列舉類型的轉換`[BindAs]`是[揭開 NSString 常數](#enum-attributes)。

#### <a name="arrays"></a>陣列

`[BindAs]` 也支援陣列的任何支援的型別，您可以有下列 API 定義做為範例：

```csharp
[return: BindAs (typeof (CAScroll []))]
[Export ("getScrollModesAt:")]
NSString [] GetScrollModes ([BindAs (typeof (CGRect []))] NSValue [] rects);
```

會輸出：

```csharp
[Export ("getScrollModesAt:")]
CAScroll? [] GetScrollModes (CGRect [] rects) { ... }
```

`rects`參數將會封裝成`NSArray`包含`NSValue`每個`CGRect`就會傳回的陣列和`CAScroll?`的已建立使用傳回的值`NSArray`包含`NSStrings`。

### <a name="bindattribute"></a>BindAttribute

`Bind`屬性有兩個用途一個套用至方法或屬性宣告，並套用至個別 getter 或 setter 屬性中的另一個。

當用於方法或屬性，繫結屬性的效果會為產生的方法，會叫用指定的選取器。 結果產生的方法不使用裝飾，但`[Export]`屬性，這表示，它不能參與的方法覆寫。 這通常用於搭配`Target`實作 Objective C 的擴充方法的屬性。

例如: 

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

當用於 getter 或 setter，`Bind`屬性用來改變產生 getter 和 setter Objective C 的選取器名稱的屬性時，由程式碼產生器所推斷的預設值。 根據預設屬性與名稱"fooBar"，加上旗標時產生器會產生 getter"fooBar"匯出和"setFooBar:"的 setter。 在少數情況下，Objective C 不遵循這個慣例，通常它們 getter 將名稱變更為"isFooBar"。
您會使用這個屬性，以通知的產生器。

例如: 

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```


### <a name="asyncattribute"></a>AsyncAttribute

只用於 Xamarin.iOS 6.3 及更新版本。

這個屬性可以套用至這些方法會採用完成處理常式時為其最後一個引數。

您可以使用`[Async]`其最後一個引數為回呼的方法上的屬性。  當您將此套用至方法時，繫結產生器會產生後置詞，該方法的版本`Async`。  如果回呼會不採用任何參數，傳回值將是`Task`，如果回呼採用參數，結果會是工作&lt;T&gt;。

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

下列會產生這個 async 方法：

```csharp
Task LoadFileAsync (string file);
```

如果回呼採用多個參數，您應該設定`ResultType`或`ResultTypeName`來指定所要產生的型別會保留所有屬性的名稱。

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

下列將會產生這個 async 方法，其中`FileLoading`包含要存取 「 檔案 」 和 「 byteCount 」 屬性：

```csharp
Task<FileLoading> LoadFile (string file);
```

如果回呼的最後一個參數是`NSError`，然後產生`Async`方法會檢查，如果值不是 null，而且如果這種情況，請在產生的非同步方法會設定工作例外狀況。

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

上述會產生下列的非同步方法：

```csharp
Task<string> UploadAsync (string file);
```

錯誤時，產生的工作必須設定為例外狀況和`NSErrorException`包裝產生`NSError`。

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

使用這個屬性來指定傳回的值`Task`物件。   這個參數會使用現有的類型，因此它必須其中一個核心應用程式開發介面定義中定義。

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

使用這個屬性來指定傳回的值`Task`物件。   這個參數會採用所需的類型名稱的名稱，產生器會產生一系列的屬性，其中一個回呼會針對每個參數。

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

使用此屬性來自訂產生的非同步方法的名稱。   預設值是使用方法的名稱，並將文字"Async"附加，您可以使用這個選項變更此預設值。

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

這個屬性會套用至字串參數或字串的屬性和指示以不使用零複製字串的這個參數，封送處理程式碼產生器，以及 C# 字串改為建立新的 NSString 執行個體。
如果您指示使用零複製字串封送處理使用產生器，在字串上才需要此屬性`--zero-copy`命令列選項或設定組件層級屬性`ZeroCopyStringsAttribute`。

這是必要的屬性宣告中以 OBJECTIVE-C 其中是 「 保留 」 或 「 指派 」 的屬性，而不是 「 複製 」 屬性。 這些通常會發生在已進行錯誤 「 最佳化 「 開發人員的協力廠商程式庫中。 一般情況下，「 保留 」 或 「 指派 」`NSString`屬性不正確，因為`NSMutableString`或使用者衍生的類別`NSString`可能改變稍微中斷應用程式程式庫程式碼的情況下字串的內容。 通常這是因為太早最佳化。

下圖顯示兩個這類屬性在目標 c:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```


### <a name="disposeattribute"></a>DisposeAttribute

當您將套用`DisposeAttribute`至類別時，您會提供將加入的程式碼片段`Dispose()`類別的方法實作。

因為`Dispose`方法會由自動產生`bmac-native`和`btouch-native`工具，您必須使用`Dispose`屬性將某些程式碼在產生`Dispose`方法實作。

例如: 

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```


### <a name="exportattribute"></a>ExportAttribute

`Export`屬性用來在方法或屬性，以公開成 Objective C 執行階段旗標。 這個屬性會繫結工具與實際的 Xamarin.iOS 和 Xamarin.Mac 執行階段之間共用。 方法中，參數會傳遞逐字產生的程式碼，屬性的 getter 和 setter 匯出會產生與基底宣告為基礎 (請參閱節`BindAttribute`如需如何變更繫結工具的行為)。

語法：

```csharp
public enum ArgumentSemantic {
    None, Assign, Copy, Retain.
}

[AttributeUsage (AttributeTargets.Method | AttributeTargets.Constructor | AttributeTargets.Property)]
public class ExportAttribute : Attribute {
    public ExportAttribute();
    public ExportAttribute (string selector);
    public ExportAttribute (string selector, ArgumentSemantic semantic);
    public string Selector { get; set; }
    public ArgumentSemantic ArgumentSemantic { get; set; }
}
```

[選取器](http://developer.apple.com/library/ios/#documentation/cocoa/conceptual/objectivec/Chapters/ocSelectors.html)，代表基礎 OBJECTIVE-C 方法或屬性的名稱進行繫結。


#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic


### <a name="fieldattribute"></a>FieldAttribute

此屬性用來做為欄位，會視需要載入，並公開至 C# 程式碼公開 C 全域變數。 通常這必要取得 C 或 Objective C 中所定義，可能是用於某些應用程式開發介面，其中一個語彙基元或其值為不透明和必須做的常數值層是由使用者程式碼。

語法：

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName`是 C 符號連結。 依預設這會從推斷其名稱與命名空間的型別定義所在的程式庫將其載入。 如果這不是符號查閱其中的程式庫，您應該將`libraryName`參數。 如果您要連結的靜態程式庫，使用"__Internal"做為`libraryName`參數。

產生的屬性一定是靜態的。

標示的欄位屬性的屬性可以是下列類型：

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* 列舉

不支援 setter[列舉揭開 NSString 常數](#enum-attributes)，但它們可以手動繫結必要。

範例：

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

### <a name="internalattribute"></a>InternalAttribute

`Internal`屬性可以套用至方法或屬性，但是加上旗標以 「 內部 」 C# 關鍵字讓產生的組件中的程式碼只存取程式碼的產生的程式碼作用。 這通常用來隱藏太低層級的 Api 或提供您想要改善時，或產生器不支援的 api 的次佳的公用 API，需要進行一些手動編碼。

當設計的繫結，您通常會隱藏方法或屬性使用這個屬性，提供不同的名稱的方法或屬性，而然後在您的 C# 互補的支援檔案，您會加入的強類型包裝函式公開基礎功能。

例如: 

```csharp
[Internal]
[Export ("setValue:forKey:");
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

然後，在支援的檔案中，您可能會有一些類似的程式碼：

```csharp
public NSObject this [NSObject idx] {
    get {
        return _GetValueForKey (idx);
    }
    set {
        _SetValueForKey (value, idx);
    }
}
```

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

這個屬性旗標的支援欄位的屬性來標註具有.NET`[ThreadStatic]`屬性。 這非常有用，如果欄位是執行緒的靜態變數。

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin.iOS 6.0.6)

這個屬性會使方法支援原生 (ObjectiveC) 例外狀況。
而不是呼叫`objc_msgSend`引動過程會直接進行自訂的 trampoline 攔截 ObjectiveC 例外狀況，並將其封送處理成 managed 例外狀況。

目前只有少數`objc_msgSend`支援簽章 (是否不支援簽章，當應用程式使用之繫結的原生連結失敗且遺失 monotouch_ 找出*_objc_msgSend*符號)，但更多可在要求中加入。


### <a name="newattribute"></a>NewAttribute

這個屬性會套用至方法和屬性具有產生器產生的"new"關鍵字宣告之前。

它用來避免編譯器警告時導入的子類別的基底類別中已經有相同的方法或屬性的名稱。


### <a name="notificationattribute"></a>NotificationAttribute

您可以將此屬性套用至欄位產生器產生強型別協助程式通知類別。

這個屬性可以使用不含引數不包含任何承載的通知，或者您可以指定`System.Type`參考另一個介面，在應用程式開發介面定義中，通常名稱結尾"EventArgs"。 產生器會變成介面類別的子類別`EventArgs`，而且將包含所有列出的屬性。 `[Export]`屬性應該用於`EventArgs`類別，以列出用來查閱 OBJECTIVE-C 字典擷取值的索引鍵的名稱。

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
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

您的程式碼的使用者便可輕鬆地訂閱通知張貼至[NSDefaultCenter](https://developer.xamarin.com/api/property/Foundation.NSNotificationCenter.DefaultCenter/)使用如下的程式碼：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

或若要設定要觀察特定物件。 如果您要傳入`null`至`objectToObserve`這個方法的行為就像其對等。

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

傳回的值`ObserveDidStart`可用來輕鬆地停止接收通知，就像這樣：

```csharp
token.Dispose ();
```

您可以呼叫[NSNotification.DefaultCenter.RemoveObserver](https://developer.xamarin.com/api/member/Foundation.NSNotificationCenter.RemoveObserver/p/Foundation.NSObject//)和將語彙基元。 如果您的通知中包含參數，您應該指定 helper`EventArgs`介面，像這樣：

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

將會產生上述`MyScreenChangedEventArgs`類別`ScreenX`和`ScreenY`屬性，將會提取的資料從[NSNotification.UserInfo](https://developer.xamarin.com/api/property/Foundation.NSNotification.UserInfo/)字典的索引鍵**ScreenXKey**和**ScreenYKey**分別並套用適當的轉換。 `[ProbePresence]`探查如果索引鍵在設定使用屬性產生器`UserInfo`，而不是嘗試擷取值。 這用的情況，索引鍵的目前狀態的值 （通常為布林值）。

這可讓您撰寫程式碼如下：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

在某些情況下，沒有任何傳遞字典上的值相關聯的常數。  Apple 有時會使用公用符號常數和有時會使用字串常數。  根據預設`[Export]`屬性，在您提供`EventArgs`類別將用做為公用符號指定的名稱來查閱在執行階段。  如果不這種情況，並改用它應該查閱為字串常數，則傳遞`ArgumentSemantic.Assign`匯出屬性的值。

**Xamarin.iOS 8.4 的新功能**

某些情況下，通知將會開始存留期沒有任何引數，因此使用`[Notification]`不是可接受的引數。  但是，某些情況下，將會導入參數，以通知。  若要支援此案例中，可以多次套用屬性。

如果您正在開發繫結，而且您想要避免破壞現有的使用者程式碼，您會開啟現有通知從：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

版本，其中列出通知屬性兩次，就像這樣：

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

### <a name="nullallowedattribute"></a>NullAllowedAttribute

這套用至屬性時它會標示為允許 null 指派給它值的屬性。 這是唯一有效的參考型別。

當套用至方法的簽章中的參數表示指定的參數可以是 null，而且不會檢查要執行的傳遞 null 值。

如果參考型別沒有這個屬性，繫結工具將會產生檢查之前將它傳遞給 Objective C 指派的值，並將產生的檢查作業將會擲回`ArgumentNullException`如果指派的值是 null。

例如: 

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute"/>

### <a name="overrideattribute"></a>OverrideAttribute

使用這個屬性，指示繫結產生器的繫結這個特定的方法應該標幟"覆寫 」 關鍵字。


### <a name="presnippetattribute"></a>PreSnippetAttribute

您可以使用這個屬性將要插入的輸入的參數已驗證之後，但之前 Objective C 程式碼呼叫某些程式碼

範例：

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```


### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

您可以使用這個屬性將一些程式碼來插入之前的任何參數來驗證所產生的方法。

範例：

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```


### <a name="postgetattribute"></a>PostGetAttribute

指示要叫用指定的屬性，這個類別，以從中提取的值從繫結產生器。

這個屬性通常會用來重新整理快取指向的物件參考，以避免參考的物件圖形中。 通常它會顯示的作業，像是新增/移除程式碼中。 這個方法使用，讓我們加入或移除，以確保更新內部快取項目之後保持受管理的參考，實際上正在使用中的物件。 這是可行的因為指定的繫結中繫結工具會產生的支援欄位的所有參考的物件。

範例：

```csharp
[BaseType (typeof (NSObject))]
[Since (4,0)]
public interface NSOperation {
    [Export ("addDependency:")][PostGet ("Dependencies")]
    void AddDependency (NSOperation op);

    [Export ("removeDependency:")][PostGet ("Dependencies")]
    void RemoveDependency (NSOperation op);

    [Export ("dependencies")]
    NSOperation [] Dependencies { get; }
}
```

在此情況下，`Dependencies`屬性將會叫用後新增或移除相依性從`NSOperation`載入物件，以防止記憶體流失以及記憶體損毀的物件，確保我們代表實際的圖形。


### <a name="postsnippetattribute"></a>PostSnippetAttribute

您可以使用這個屬性將某些 C# 原始程式碼的程式碼已叫用基礎 OBJECTIVE-C 方法之後插入

範例：

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```


### <a name="proxyattribute"></a>ProxyAttribute

這個屬性會套用至傳回值，將它們標示為 proxy 物件。 不可以從使用者繫結區分某些 OBJECTIVE-C Api 傳回 proxy 物件。 這個屬性的影響是加上旗標的物件為`DirectBinding`物件。 對於 Xamarin.Mac 案例，您可以看到[討論這個 bug](https://bugzilla.novell.com/show_bug.cgi?id=670844)。


### <a name="retainlistattribute"></a>RetainListAttribute

指示要保留的受管理的參考參數或移除參數的內部參考的產生器。 這用來保留物件參考。

語法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果 「 doAdd"的值為 true，則參數加入至`__mt_{0}_var List<NSObject>;`。 其中`{0}`取代給定`listName`。 您必須在 api 互補部分類別中宣告此支援欄位。

如需範例，請參閱[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (Xamarin.iOS 6.0)

這可以套用至傳回類型以指出產生器應該呼叫`Release`上的物件，再加以傳回。 只有時才需要一種方法可讓您保留物件 （而不是最常見的案例 autoreleased 物件）

範例：

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

此外這個屬性會傳播至產生的程式碼，以便 Xamarin.iOS 執行階段可讓您知道它必須保留時從這類函式返回 Objective C 的物件。


### <a name="sealedattribute"></a>SealedAttribute

指示加上旗標所產生的方法，為已密封的產生器。 如果未指定此屬性，預設值是產生的虛擬方法 （虛擬方法、 抽象方法或根據其他屬性如何用覆寫）。


### <a name="staticattribute"></a>StaticAttribute

當`Static`屬性套用至方法或屬性這會產生靜態方法或屬性。 如果未指定此屬性，產生器會產生執行個體方法或屬性。


### <a name="transientattribute"></a>TransientAttribute

使用此屬性為旗標屬性的值是暫時性的也就是建立暫時的 Io，但長時間不存在的物件。 當這個屬性會套用至屬性時，產生器不會建立為此屬性，這表示 managed 的類別不會保留物件參考支援欄位。


### <a name="wrapattribute"></a>WrapAttribute

在 Xamarin.iOS/Xamarin.Mac 繫結，設計`Wrap`屬性用來包裝的強型別物件的弱式型別的物件。 此派上用場大部分利用 OBJECTIVE-C 「 委派 」 物件通常為型別宣告`id`或`NSObject`。 公開的委派或資料來源為型別是使用 Xamarin.iOS 和 Xamarin.Mac 的慣例`NSObject`和使用的慣例"Weak"+ 所公開的名稱來命名。 從 Objective C 的 「 識別碼委派 」 屬性會公開為`NSObject WeakDelegate { get; set; }`API 合約檔案中的屬性。

但通常的值指派給此委派是強型別，因此我們介面的強型別，並套用`Wrap`屬性，也就是說，使用者可以選擇使用弱式型別，如果他們需要某些 fine 控制項，或必須求助於低階 tricks，或者也可以使用強型別屬性的運作。

範例：

```csharp
[BaseType (typeof (NSObject))]
interface Demo {
     [Export ("delegate"), NullAllowed]
     NSObject WeakDelegate { get; set; }

     [Wrap ("WeakDelegate")]
     DemoDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
[Model][Protocol]
interface DemoDelegate {
    [Export ("doDemo")]
    void DoDemo ();
}
```

這是使用者會如何使用委派的弱式型別版本：

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

這是使用者會使用強類型版本，請注意，使用者利用 C# 類型系統，並使用覆寫關鍵字來宣告其意圖，他不需要以手動方式裝飾的方法和`Export`，因為我們適用於使用者的繫結：

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```


另一個用途`Wrap`屬性是要支援強類型版本的方法。   例如: 

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

所產生的成員`[Wrap]`不`virtual`根據預設，如果您需要`virtual`成員可以設定為`true`選擇性`isVirtual`參數。

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

## <a name="parameter-attributes"></a>參數屬性

本章節描述您可以套用到方法定義中的參數屬性，以及`NullAttribute`，套用至整個屬性。

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

這個屬性會套用至 C# 委派宣告中有問題的參數符合呼叫慣例的 OBJECTIVE-C 區塊，而且應該封送處理它以這種方式通知繫結器的參數型別。

這通常用於在目標 c： 定義像這樣的回呼

```csharp
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另請參閱： [CCallback](#CCallback)。

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

這個屬性會套用至 C# 委派宣告中有問題的參數符合 C ABI 函式指標呼叫慣例，而且應該封送處理它以這種方式通知繫結器的參數型別。

這通常用於在目標 c： 定義像這樣的回呼

    typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);

另請參閱： [BlockCallback](#BlockCallback)。

### <a name="params"></a>Params

您可以使用`[Params]`屬性能夠插入定義中的 「 參數 」 的產生器的方法定義的最後一個陣列參數。   這可讓繫結，以便輕鬆地進行選擇性參數。

例如，下列定義：

    [Export ("loadFiles:")]
    void LoadFiles ([Params]NSUrl [] files);

可讓撰寫下列程式碼：

    foo.LoadFiles (new NSUrl (url));
    foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));

這有優點，就不需要使用者建立的傳遞項目陣列。

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

您可以使用`[PlainString]`屬性字串參數，指示繫結產生器，若要為 C 字串，而不是傳遞做為參數傳遞的字串前面`NSString`。

大多數 OBJECTIVE-C Api 取用`NSString`參數，但少數幾個應用程式開發介面公開`char *`API，用於傳遞的字串，而不`NSString`變化。
使用`[PlainString]`在這些情況下。

例如，下列 Objective C 宣告：

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

應該繫結像這樣：

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```


### <a name="retainattribute"></a>RetainAttribute

指示要保留指定的參數參考的產生器。 產生器會為此欄位提供的備份存放區，或您可以指定名稱 ( `WrapName`) 來儲存的值。 這是用來存放做為參數傳遞至 Objective C，而且當您知道 Objective C 則只會讓此物件的複本的 managed 物件的參考。 比方說，像是 API`SetDisplay (SomeObject)`會使用這個屬性，因為它可能 SetDisplay，無法只顯示一個物件一次。 如果您要追蹤的多個物件 （例如，適用於類似堆疊的應用程式開發介面） 會使用`RetainList`屬性。

語法：

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```


### <a name="retainlistattribute"></a>RetainListAttribute

指示要保留的受管理的參考參數或移除參數的內部參考的產生器。 這用來保留物件參考。

語法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果 「 doAdd"的值為 true，則參數加入至`__mt_{0}_var List<NSObject>`。 其中`{0}`取代給定`listName`。 您必須在 api 互補部分類別中宣告此支援欄位。

如需範例，請參閱[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)


### <a name="transientattribute"></a>TransientAttribute

此屬性會套用至參數，並從 Objective C 轉換成 C# 時，才會使用。  在各種 OBJECTIVE-C NSObjects 這些轉換期間包裝參數至受管理物件的表示法。

執行階段將採用的原生物件的參考，並保留參考，直到物件的最後一個受管理的參考就會消失，而且 GC 有機會執行。

在少數情況下，是很重要的 C# 執行階段不會保留原始物件的參考。  這有時會基礎原生程式碼已附加至參數的生命週期的特殊行為。  例如： 參數的解構函式會執行某些清除動作，或處置某些寶貴的資源。

這個屬性會通知您想要覆寫方法的傳回回到 OBJECTIVE-C 時盡可能處置物件的執行階段。

規則很簡單： 如果執行階段必須建立新的 managed 的表示從原生的物件，則函式結尾的原生物件的保留天數計數皆會予以捨棄，並受管理物件的控制代碼屬性將進行清除。   這表示如果您保留 managed 物件的參考，該參考將會變成無用 （其上叫用方法將會擲回例外狀況）。

如果不是傳遞的物件，或如果已有未處理受管理的物件的表示，強制的 dispose 不會發生。 


## <a name="property-attributes"></a>屬性的屬性

### <a name="notimplementedattribute"></a>NotImplementedAttribute

這個屬性用來支援 OBJECTIVE-C 慣用語，其中具有 getter 的屬性在基底類別引進並處於可變動的子類別介紹 setter。

由於 C# 不支援此模型，必須有 getter 和 setter 的基底類別，並且子類別可以使用[OverrideAttribute](#OverrideAttribute)。

這個屬性只用於屬性 setter，而用來支援可變動的慣用語目標 c

範例：

```csharp
[BaseType (typeof (NSObject))]
interface MyString {
    [Export ("initWithValue:")]
    IntPtr Constructor (string value);

    [Export ("value")]
    string Value {
        get;

    [NotImplemented ("Not available on MyString, use MyMutableString to set")]
        set;
    }
}

[BaseType (typeof (MyString))]
interface MyMutableString {
    [Export ("value")]
    [Override]
    string Value { get; set; }
}
```

<a name="enum-attributes"/>

## <a name="enum-attributes"></a>Enum 屬性

對應`NSString`列舉值的常數是用來建立較佳的.NET API。 它：

* 可讓程式碼完成功能，以進行更有用，顯示**只**api; 正確的值
* 將型別安全，您無法使用另一個`NSString`常數不正確的內容; 和
* 可讓隱藏某些常數，讓程式碼完成功能，顯示較短的 API 清單，而不會遺失的功能。

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
```

從上述的繫結定義會建立產生器`enum`本身，也會建立`*Extensions`靜態包含列舉值之間的兩個方法轉換方法的型別和`NSString`常數。 這表示常數仍然可以提供給開發人員即使它們不是應用程式開發介面的一部分。

例如：

```csharp
// using the NSString constant in a different API / framework / 3rd party code
CallApiRequiringAnNSString (NSRunLoopMode.Default.GetConstant ());
```

```csharp
// converting the constants from a different API / framework / 3rd party code
var constant = CallApiReturningAnNSString ();
// back into an enum value
CallApiWithEnum (NSRunLoopModeExtensions.GetValue (constant));
```

### <a name="defaultenumvalueattribute"></a>DefaultEnumValueAttribute

您可以裝飾**一個**以這個屬性的列舉值。 這會成為所傳回的列舉值已不在已知的常數。

與上述範例中：

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

如果沒有列舉值裝飾時則`NotSupportedException`就會擲回。

### <a name="errordomainattribute"></a>ErrorDomainAttribute

錯誤碼是繫結為列舉值。 通常是相關的錯誤網域，所以不一定可以輕鬆地尋找適用於哪一個 （或其中一個即使存在）。

您可以使用這個屬性與列舉本身的網域時發生錯誤。

範例：

```csharp
    [Native]
    [ErrorDomain ("AVKitErrorDomain")]
    public enum AVKitError : nint {
        None = 0,
        Unknown = -1000,
        PictureInPictureStartFailed = -1001
    }
```

然後，您可以呼叫擴充方法`GetDomain`取得網域的任何錯誤的常數。

### <a name="fieldattribute"></a>FieldAttribute

這是相同`[Field]`屬性用於型別內的常數。 它也可用在列舉內部對應具有特定常數的值。

A`null`值可用來指定應該傳回的列舉值，如果`null``NSString`常數指定。

與上述範例中：

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

如果沒有`null`值存在於則`ArgumentNullException`就會擲回。

## <a name="global-attributes"></a>全域屬性

全域屬性可能會套用使用`[assembly:]`屬性修飾詞，像`LinkWithAttribute`，也可使用任何地方，例如`Lion`和`Since`屬性。


### <a name="linkwithattribute"></a>LinkWithAttribute

這是可讓開發人員指定的連結不需要手動設定 gcc_flags 文件庫的取用者重複使用的繫結的程式庫所需的旗標和額外 mtouch 引數傳遞至文件庫的組件層級屬性。

語法：

```csharp
// In properties
[Flags]
public enum LinkTarget {
    Simulator    = 1,
    ArmV6    = 2,
    ArmV7    = 4,
    Thumb    = 8,
}

[AttributeUsage(AttributeTargets.Assembly, AllowMultiple=true)]
public class LinkWithAttribute : Attribute {
    public LinkWithAttribute ();
    public LinkWithAttribute (string libraryName);
    public LinkWithAttribute (string libraryName, LinkTarget target);
    public LinkWithAttribute (string libraryName, LinkTarget target, string linkerFlags);
    public bool ForceLoad { get; set; }
    public string Frameworks { get; set; }
    public bool IsCxx { get; set;  }
    public string LibraryName { get; }
    public string LinkerFlags { get; set; }
    public LinkTarget LinkTarget { get; set; }
    public bool NeedsGccExceptionHandling { get; set; }
    public bool SmartLink { get; set; }
    public string WeakFrameworks { get; set; }
}
```

這個屬性會套用在組件層級，比方說，這是什麼[CorePlot 繫結](https://github.com/mono/monotouch-bindings/tree/master/CorePlot)使用：

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

當您使用`LinkWith`屬性，指定`libraryName`內嵌至產生的組件，讓使用者能夠隨附於單一 DLL 中包含未受管理的相依性以及正確使用時所需要的命令列旗標從 Xamarin.iOS 程式庫。

它也可提供`libraryName`的情況下`LinkWith`屬性可以用來只指定 其他連結器旗標：

 ``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
 ```


#### <a name="linkwithattribute-constructors"></a>LinkWithAttribute 建構函式

這些建構函式可讓您指定要與連結和嵌入您產生的組件，支援程式庫支援的目標以及任何選擇性的程式庫旗標所需的程式庫連結的媒體櫃。

請注意，推斷的 Xamarin.iOS LinkTarget 引數，而且不需要設定。

例如：

```csharp
// Specify additional linker:
[assembly: LinkWith (LinkerFlags = "-sqlite3")]

// Specify library name for the constructor:
[assembly: LinkWith ("libDemo.a");

// Specify library name, and link target for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator);

// Specify only the library name, link target and linker flags for the constructor:
[assembly: LinkWith ("libDemo.a", LinkTarget.Thumb | LinkTarget.Simulator, SmartLink = true, ForceLoad = true, IsCxx = true);
```


#### <a name="linkwithattributeforceload"></a>LinkWithAttribute.ForceLoad

`ForceLoad`屬性用來決定是否`-force_load`連結旗標用來連結原生程式庫。 現在，這應該一律是 true。


#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute.Frameworks

要繫結的程式庫是否有任何架構上的硬碟需求 (以外`Foundation`和`UIKit`)，您應該設定`Frameworks`屬性設為字串，其中包含一個必要的平台架構以空格分隔的清單。 例如，如果您要繫結的程式庫需要`CoreGraphics`和`CoreText`，您會設定`Frameworks`屬性`"CoreGraphics CoreText"`。


#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

設定此屬性設定為 true，如果產生的可執行檔必須使用 c + + 編譯器，而不預設值，這是 C 編譯器進行編譯。 如果您要繫結的程式庫以 c + + 撰寫，請使用此選項。


#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

包裝的 unmanaged 程式庫名稱。 這是 「.a"副檔名的檔案，而且它可以包含多個平台 （例如 ARM 和 x86 模擬器） 的物件程式碼。

舊版的 Xamarin.iOS 檢查`LinkTarget`屬性來判斷平台程式庫支援，但這是現在自動偵測和`LinkTarget`屬性會被忽略。


#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

`LinkerFlags`字串為提供的方式來指定原生程式庫連結至應用程式時，任何其他連結器旗標所需的繫結作者。

例如，如果原生程式庫需要 libxml2 和 zlib，您可以將`LinkerFlags`字串至`"-lxml2 -lz"`。


#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

舊版的 Xamarin.iOS 檢查`LinkTarget`屬性來判斷平台程式庫支援，但這是現在自動偵測和`LinkTarget`屬性會被忽略。

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

設定此屬性設定為 true，如果您要連結程式庫需要 GCC 例外狀況處理程式庫 (gcc_eh)


#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

`SmartLink`屬性應該設定為 true，讓 Xamarin.iOS 判斷是否`ForceLoad`是否為必要。


#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

`WeakFrameworks`屬性的運作方式的相同方式`Frameworks`屬性，但在連結階段`-weak_framework`規範時，會傳遞至 gcc 上，針對每個列出的架構。

`WeakFrameworks` 可讓程式庫和應用程式，以針對平台架構弱的連結，使其可以選擇性地使用這些如果可用，但不是會在其上如果媒體櫃用來新增額外的功能上就很有用的硬式相依性更新版本iOS 的版本。 如需使用弱式連結的詳細資訊，請參閱 Apple 文件上[弱式連結](http://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html)。

非常適合以弱式連結會`Frameworks`喜歡帳戶`CoreBluetooth`， `CoreImage`， `GLKit`，`NewsstandKit`和`Twitter`因為只會出現在 iOS 5。

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) 和 LionAttribute (macOS)

您使用`Since`為具有時間引入在某個時點的屬性旗標的 Api。 屬性應該只用來加上旗標類型和方法可能會導致執行階段問題，如果基礎類別、 方法或屬性無法使用。

語法：

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

它應該一般情況下不會套用至列舉型別、 條件約束或新的結構為那些不會造成執行階段錯誤，若在執行較舊版本的作業系統的裝置上。

當套用至類型的範例：

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

套用至新的成員時的範例：

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

`Lion`屬性會套用相同的方式，但用於 Lion 導入的型別。 若要使用的原因`Lion`與更特定的版本號碼，可在 iOS 中是主要 OS X 版本鮮少發生，而且很容易記住作業系統的版本比其 codename 時經常修改該 iOS


### <a name="adviceattribute"></a>AdviceAttribute

使用這個屬性，讓開發人員可能會比較方便，才能使用其他 Api 的相關的提示。   例如，如果您提供應用程式開發介面的強類型版本，您可以使用這個屬性弱型別屬性引導開發人員更好的應用程式開發介面。

這個屬性的資訊會顯示文件中，並可以為了給使用者的建議如何改善開發工具

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

只用於 Xamarin.iOS 5.4 及更新版本。

這個屬性會指示產生器，這個特定的文件庫的繫結 (如果套用`[assembly:]`) 或型別應該使用快速的零複製字串封送處理。 這個屬性就相當於將傳遞命令列選項`--zero-copy`給產生器。

產生器時請使用零複製的字串，可有效使用做為字串而不會產生新的建立取用 Objective C 相同的 C# 字串`NSString`物件，並避免將資料從 C# 字串複製到 Objective C 字串。 使用零複製字串的唯一缺點是，您必須確定任何字串屬性，其包裝剛好標示為 「 保留 」 或 「 複製 」 具有`DisableZeroCopy`屬性設定。 這是需要因為零複製字串的控制代碼會在堆疊上配置和函式傳回時無效。

範例：

```csharp
[ZeroCopyStrings]
[BaseType (typeof (NSObject))]
interface MyBinding {
    [Export ("name")]
    string Name { get; set; }

    [Export ("domain"), NullAllowed]
    string Domain { get; set; }

    [DisablZeroCopy]
    [Export ("someRetainedNSString")]
    string RetainedProperty { get; set; }
}

```

您也可以套用在組件層級的屬性，它會套用至所有類型的組件：

    [assembly:ZeroCopyStrings]

## <a name="strongly-typed-dictionaries"></a>強型別字典

與 Xamarin.iOS 8.0 我們引進了輕鬆地建立該換行強型別類別支援`NSDictionaries`。

雖然它一直都可使用[DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/)資料類型與手動的 API，它現在是更容易執行這項操作。  如需詳細資訊，請參閱[面對強式型別](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types)。


### <a name="strongdictionary"></a>StrongDictionary

當這個屬性會套用至介面時，產生器會產生具有相同名稱做為衍生自介面類別[DictionaryContainer](https://developer.xamarin.com/api/type/Foundation.DictionaryContainer/)並將轉換成強類型介面中定義的每一個屬性getter 和 setter 的字典。

這會自動產生的類別可以具現化，從現有`NSDictionary`或已新建立的。

這個屬性接受一個參數，包含用來存取字典的項目索引鍵的類別名稱。   依預設具有屬性的介面中每一個屬性會查閱中指定的型別名稱後置詞，「 索引鍵 」 的成員。

例如: 

```csharp
[StrongDictionary ("MyOptionKeys")]
interface MyOption {
    string Name { get; set; }
    nint    Age  { get; set; }
}

[Static]
interface MyOptionKeys {
    // In Objective-C this is "NSString *MYOptionNameKey;"
    [Field ("MYOptionNameKey")]
    NSString NameKey { get; }

    // In Objective-C this is "NSString *MYOptionAgeKey;"
    [Field ("MYOptionAgeKey")]
    NSString AgeKey { get; }
}

```

在上述情況中，`MyOption`類別將會產生的字串屬性`Name`，將會使用`MyOptionKeys.NameKey`到擷取字串字典索引鍵。   會使用`MyOptionKeys.AgeKey`做為要擷取的字典索引鍵`NSNumber`包含整數的值

如果您想要使用不同的索引鍵，您可以使用匯出屬性的屬性，例如：

```csharp
[StrongDictionary ("MyColoringKeys")]
interface MyColoringOptions {
    [Export ("TheName")]  // Override the default which would be NameKey
    string Name { get; set; }

    [Export ("TheAge")] // Override the default which would be AgeKey
    nint    Age  { get; set; }
}

[Static]
interface MyColoringKeys {
    // In Objective-C this is "NSString *MYColoringNameKey"
    [Field ("MYColoringNameKey")]
    NSString TheName { get; }

    // In Objective-C this is "NSString *MYColoringAgeKey"
    [Field ("MYColoringAgeKey")]
    NSString TheAge { get; }
}
```

#### <a name="strong-dictionary-types"></a>強式的字典類型

支援下列資料型別`StrongDictionary`定義：

|C# 介面類型|`NSDictionary` 儲存類型|
|---|---|
|`bool`|`Boolean` 儲存在 `NSNumber`|
|列舉值|整數儲存在 `NSNumber`|
|`int`|儲存在 32 位元整數 `NSNumber`|
|`uint`|儲存在 32 位元不帶正負號的整數 `NSNumber`|
|`nint`|`NSInteger` 儲存在 `NSNumber`|
|`nuint`|`NSUInteger` 儲存在 `NSNumber`|
|`long`|儲存在 64 位元整數 `NSNumber`|
|`float`|儲存為 32 位元整數 `NSNumber`|
|`double`|儲存為 64 位元整數 `NSNumber`|
|`NSObject` 和子類別|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`Array`的 `NSObject`|`NSArray`|
|C#`Array`的列舉型別|`NSArray` 包含`NSNumber`值|
