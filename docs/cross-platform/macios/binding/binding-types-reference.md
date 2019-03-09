---
title: 繫結型別參考指南
description: 本參考指南描述各種屬性和概念，必要時建立，了解C#繫結 Objective C 程式庫。
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: d460bf867ce09e614be76d0a4a7ffef01420cf82
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669475"
---
# <a name="binding-types-reference-guide"></a>繫結型別參考指南

本文件說明您可以使用加上註解您的 API 合約檔案到磁碟機繫結，並產生的程式碼的屬性清單

Xamarin.iOS 和 Xamarin.Mac API 合約以C#大多以介面定義來定義 OBJECTIVE-C 程式碼顯示的方式C#。 此程序牽涉到混合的介面宣告加上 API 合約可能需要一些基本的型別定義。 繫結類型的簡介，請參閱我們的附屬指南[繫結 Objective C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)。

## <a name="type-definitions"></a>型別定義

語法：

```csharp
[BaseType (typeof (BTYPE))
interface MyType [: Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

在您的合約定義具有每個介面[ `[BaseType]` ](#BaseTypeAttribute)宣告產生物件的基底類型的屬性。 在上述宣告`MyType`類別C#會產生型別，繫結到 OBJECTIVE-C 類型呼叫`MyType`。

如果您指定的類型名稱之後的任何型別 (在上述範例中`Protocol1`並`Protocol2`) 使用介面繼承語法這些介面的內容都將予以內嵌好像它們一直為合約的一部分`MyType`。
Xamarin.iOS 介面型別會採用一種通訊協定的方式內嵌這所有的方法和型別本身到通訊協定中所宣告的屬性。

下列顯示如何的 Objective C 宣告`UITextField`就 Xamarin.iOS 合約中定義：

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

就像這樣做為可寫入C#API 合約：

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

您可以透過將其他屬性套用至介面，以及設定控制的程式碼產生的許多其他方面[ `[BaseType]` ](#BaseTypeAttribute)屬性。


### <a name="generating-events"></a>產生事件

Xamarin.iOS 和 Xamarin.Mac API 設計的一項功能是我們對應 OBJECTIVE-C 委派類別做為C#事件和回呼。 使用者可以選擇以個別執行個體為基礎他們是否想要採用的 Objective C 程式設計模式，將指派給屬性，例如`Delegate`實作 OBJECTIVE-C 執行階段會呼叫，各種方法的類別，或藉由執行個體選擇C#-樣式事件和屬性。

讓我們查看如何使用 OBJECTIVE-C 模型的其中一個範例：

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

在上述範例中，您可以看到，我們已選擇覆寫兩種方法，其中的捲動事件所的位置，以及第二個，通知是回呼，應該會傳回布林值，指示`scrollView`是否應該捲動到前與否。

C#模型可讓您的程式庫的使用者來接聽通知使用C#事件的語法或屬性語法來連結應該傳回值的回呼。

這是如何C#程式碼使用 lambda 看起來相同的功能：

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

因為事件不會傳回的值 （不會有 void 的傳回型別），您可以連接多個複本。 `ShouldScrollToTop`不是事件，而是與型別屬性`UIScrollViewCondition`具有此簽章：

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

它會傳回`bool`值，在此情況下 lambda 語法可讓我們只會傳回的值從`MakeDecision`函式。

繫結產生器支援產生的事件及連結類別，以如下的內容`UIScrollView`使用其`UIScrollViewDelegate`（也稱之為模型類別），這是藉由加上附註您[ `[BaseType]` ](#BaseTypeAttribute)定義`Events`和`Delegates`參數 （如下所述）。 除了標註[ `[BaseType]` ](#BaseTypeAttribute)使用這些參數是為了通知的產生器的一些詳細的元件。

採用多個參數的事件 （Objective c 的慣例是委派類別中的第一個參數是傳送者物件的執行個體） 您必須提供您想要的名稱產生`EventArgs`類別。 做法是使用[ `[EventArgs]` ](#EventArgsAttribute)模型類別的方法宣告的屬性。 例如: 

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述宣告會產生`UIImagePickerImagePickedEventArgs`類別衍生自`EventArgs`組件的這兩個參數，並`UIImage`而`NSDictionary`。 產生器會產生這個：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然後它會公開下列`UIImagePickerController`類別：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

模型方法傳回值會以不同的方式繫結。 這些類型需要這兩個名稱產生C#委派 （方法的簽章） 以及預設值傳回，如果使用者不會提供自己的實作。 比方說，`ShouldScrollToTop`定義如下：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

將建立上述`UIScrollViewCondition`委派簽章的已如上所示，和如果使用者未提供實作，傳回的值會是 true。

除了[ `[DefaultValue]` ](#DefaultValueAttribute)屬性，您也可以使用[ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute)屬性，以指示產生器以傳回指定的參數值呼叫或[ `[NoDefaultValue]` ](#NoDefaultValueAttribute)指示產生器，沒有預設值的參數。

<a name="BaseTypeAttribute" />

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

您使用`Name`屬性，即可控制這個型別會繫結 Objective C 的世界中的名稱。 這通常用來提供C#輸入的名稱符合.NET Framework 設計方針，但它會對應至未遵循這個慣例的 Objective c 的名稱。

範例中，在下列情況中，我們對應 Objective C`NSURLConnection`輸入至`NSUrlConnection`，因為.NET Framework 設計方針使用 「 Url 」，而不是"URL":

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

指定的名稱可做為值產生`[Register]`繫結中的屬性。 如果`Name`未指定，此類型的簡短名稱做為值`[Register]`中產生的輸出屬性。

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType.Events 和 BaseType.Delegates

這些屬性用來驅動產生C#-樣式在產生的類別中的事件。 它們用來連結具有其 OBJECTIVE-C 委派類別的指定的類別。 您將會遇到許多情況下，類別會使用委派類別來傳送通知和事件。 比方說`BarcodeScanner`會有附隨`BardodeScannerDelegate`類別。 `BarcodeScanner`類別通常會有`Delegate`屬性，您會指派給執行個體`BarcodeScannerDelegate`，而此運作方式，您可能要公開給使用者C#-例如樣式事件介面，並在這些情況下，您就會使用`Events`並`Delegates`的屬性[ `[BaseType]` ](#BaseTypeAttribute)屬性。

這些屬性永遠會一起設定，必須有相同數目的項目並保持同步。`Delegates`陣列包含一個字串，針對您想要換行，每弱型別委派和`Events`陣列會包含您想要與它相關聯的每個類型的一種類型。

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

如果建立這個類別的新執行個體時，您就會套用這個屬性，該物件的執行個體將會保留之前所參考的方法`KeepRefUntil`已叫用。 此方法時，為了改進您的 Api 的可用性不想讓您將使用您的程式碼周圍的物件參考的使用者。 這個屬性的值是在方法的名稱`Delegate`類別中，因此您必須使用這個選項搭配`Events`和`Delegates`屬性。

下列範例顯示如何使用此名稱依`UIActionSheet`在 Xamarin.iOS 中：

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

當這個屬性套用至介面定義，它會防止產生器產生的預設建構函式。

當您需要使用其中一個類別中的其他建構函式初始化物件時，請使用這個屬性。


### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

當這個屬性套用至介面定義，它會為私用的旗標的預設建構函式。 這表示，您可以仍具現化這個類別的物件在內部從擴充檔案，但它不只是存取您的類別。

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

型別定義上使用這個屬性，繫結 OBJECTIVE-C 類別，以及公開這些做為C#擴充方法，以鏡像處理 OBJECTIVE-C 的方式公開的功能。

類別是以 OBJECTIVE-C 機制用來擴充一組方法和類別中可用的屬性。   在實務上，它們用來擴充基底類別的功能 (例如`NSObject`) 以特定架構中的連結時 (例如`UIKit`)，讓它們的方法，可供使用，但只有在 連結新的架構。   在某些其他情況下，它們會依功能用來組織類別中的功能。   他們是類似的精神，若要在C#擴充方法。

這是一個類別會如下所示在目標 c:

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上述範例在會延伸的執行個體的程式庫中找到`UIView`方法使用`makeBackgroundRed`。

若要將這些繫結，您可以使用[ `[Category]` ](#CategoryAttribute)介面定義上的屬性。   使用時[ `[Category]` ](#CategoryAttribute)屬性的意義[ `[BaseType]` ](#BaseTypeAttribute)屬性被用來指定基底類別延伸，為要擴充的型別會變更。

下列示範如何`UIView`擴充功能會繫結和轉換成C#擴充方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

將建立上述`MyUIViewExtension`類別，其中包含`MakeBackgroundRed`擴充方法。   這表示，您現在可以呼叫`MakeBackgroundRed`任何`UIView`子類別，讓您在 OBJECTIVE-C 相同的功能

在某些情況下，您會發現**靜態**內類別的成員是由下列範例所示：

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

這會導致**不正確**分類C#介面定義：

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

這不是正確因為使用`BoolMethod`延伸模組需要的執行個體`FooObject`但您要繫結 ObjC**靜態**延伸模組，這是因為方式的事實的副作用C#實作的擴充方法.

使用上述定義的唯一方法是由下列不美觀的程式碼：

```csharp
(null as FooObject).BoolMethod (range);
```

若要避免此建議是以內嵌`BoolMethod`內定義`FooObject`本身的介面定義，這可讓您呼叫此擴充功能，它可能是像`FooObject.BoolMethod (range)`。

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

每當我們找到時，我們會發出警告 (BI1117) [ `[Static]` ](#StaticAttribute)內的成員[ `[Category]` ](#CategoryAttribute)定義。 如果您真的想已[ `[Static]` ](#StaticAttribute)內的成員您[ `[Category]` ](#CategoryAttribute)定義您可以使用 play 警告`[Category (allowStaticMembers: true)]`或裝飾您的成員或[`[Category]` ](#CategoryAttribute)介面定義[ `[Internal]` ](#InternalAttribute)。

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

當這個屬性會套用至類別時就會產生靜態類別，其中不是衍生自`NSObject`，因此[ `[BaseType]` ](#BaseTypeAttribute)屬性會被忽略。 靜態類別用來裝載您想要公開 （expose） 的 C 公用變數。

例如: 

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

將會產生C#類別的下列 API:

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>通訊協定/模型定義

模型通常會使用通訊協定的實作。
它們的差異在於執行階段會只向 OBJECTIVE-C 實際上已覆寫的方法。
否則，將不會註冊方法。

這通常表示，當您子類別的類別，使用已標示`ModelAttribute`，您不應該呼叫基底方法。   呼叫該方法會擲回例外狀況，您應在您針對您覆寫任何方法的子類別上實作整個的行為。

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

根據預設，屬於一種通訊協定的成員是不必要的。 這可讓使用者建立的子類別`Model`藉由只衍生自的類別中的物件C#，並覆寫只在意的方法。 OBJECTIVE-C 合約有時需要使用者提供這個方法的實作 (這些會標示`@required`指示詞在 OBJECTIVE-C 中)。 在這些情況下，您應該加上旗標與這些方法`[Abstract]`屬性。

`[Abstract]`屬性可以套用至方法或屬性，並造成產生器以旗標產生的成員，以及抽象類別的抽象類別。

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

指定要傳回之模型的方法中，如果使用者不會提供一種方法中的模型物件，這個特定方法的預設值

語法：

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

例如，在下列的虛數的委派類別，如`Camera`類別，我們會提供`ShouldUploadToServer`這會公開為屬性`Camera`類別。 如果使用者`Camera`類別沒有明確設定 lambda 可以回應 true 或 false 的值，傳回的預設值在此情況下會為 false，我們已在指定的值`DefaultValue`屬性：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

如果使用者設定的處理常式中的虛數的類別，則會忽略此值：

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

另請參閱： [ `[NoDefaultValue]` ](#NoDefaultValueAttribute)， [ `[DefaultValueFromArgument]` ](#DefaultValueFromArgumentAttribute)。

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

語法：

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

當方法傳回值上的模型類別上提供這個屬性會指示產生器以傳回指定之參數的值，如果使用者未提供自己的方法或 lambda。

範例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

上述案例中的使用者`NSAnimation`類別選擇要使用的任何C#事件/屬性，以及未設定`NSAnimation.ComputeAnimationCurve`方法或 lambda 的傳回值會進行參數中傳遞的值。

另請參閱： [ `[NoDefaultValue]` ](#NoDefaultValueAttribute)， [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

有時候合理不能公開事件，或委派屬性從 模型類別，到主機類別讓新增這個屬性會指示產生器以避免任何的產生使用它來裝飾的方法。

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

這個屬性會在模型的方法會傳回值，以設定要使用的委派簽章的名稱。

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

這個屬性用來允許產生器以變更的主機類別中產生的屬性名稱。 有時很有用時適合委派類別，FooDelegate 類別方法的名稱，但看起來會奇數主機類別，做為屬性中。

這也是很有用 （和所需） 當您有兩個或合理的多個多載方法保留它們的命名方式是在 FooDelegate 類別，但您想要在具有較佳的指定名稱的主機類別中公開它們。

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

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

採用多個參數的事件 （Objective c 的慣例是委派類別中的第一個參數是傳送者物件的執行個體） 您必須提供您想要產生的 EventArgs 類別的名稱。 做法是使用`[EventArgs]`屬性的方法宣告您`Model`類別。

例如: 

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述宣告會產生`UIImagePickerImagePickedEventArgs`類別衍生自 EventArgs，組件的這兩個參數，`UIImage`而`NSDictionary`。 產生器會產生這個：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然後它會公開下列`UIImagePickerController`類別：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```


### <a name="eventnameattribute"></a>EventNameAttribute

這個屬性用來允許產生器以變更的事件或類別中產生的屬性名稱。 有時候適合模型類別方法的名稱作為模型類別中，有意義，但會有點奇怪，原始的類別中做為事件或屬性時。

例如，`UIWebView`會使用從下列的位元`UIWebViewDelegate`:

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

上述的公開`LoadingFinished`做為方法中`UIWebViewDelegate`，但`LoadFinished`做為最多可連結的事件`UIWebView`:

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

當您將套用`[Model]`到您的 API，執行階段的合約類型定義的屬性將會產生特殊的程式碼，只會呈現引動過程中類別的方法如果使用者已覆寫類別中的方法。 這個屬性通常會套用至包裝 OBJECTIVE-C 委派類別的所有 Api。

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

指定在模型上的方法不會提供預設的傳回值。

這適用於 OBJECTIVE-C 執行階段回應`false`Objective C 執行階段要求，以判斷指定的選取器，是否要實作此類別中。

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

另請參閱： [ `[DefaultValue]` ](#DefaultValueAttribute)， [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>通訊協定

OBJECTIVE-C 通訊協定概念不真的存在於C#。 通訊協定會類似於C#介面，但它們的差異在於，所有的方法，並採用它的類別必須實作一種通訊協定中所宣告的屬性。 相反地，某些方法和屬性是選擇性。

某些通訊協定通常會使用做為模型類別，這些應該使用繫結[ `[Model]` ](#ModelAttribute)屬性。

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

從 Xamarin.iOS 7.0 開始全新和改進的通訊協定，繫結功能已納入。  包含任何定義`[Protocol]`屬性實際上會產生三個支援的類別，來大幅改善，您會使用通訊協定的方式：

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

**類別實作**提供完整的抽象類別，您可以用來覆寫個別方法，並取得完整的型別安全。 但因為C#不支援多重繼承，有些的情況，您可能會在此設定需要不同的基底類別，但仍想要實作的介面。

這正是產生**介面定義**傳入。  它是具有所有必要的方法，從 通訊協定的介面。  這可讓開發人員用來實作您的通訊協定，只是實作介面。  執行階段會自動將註冊類型為採用之通訊協定。

請注意，此介面只會列出所需的方法，且並未公開 （expose） 的選擇性方法。   這表示，採用之通訊協定的類別會檢查所需的方法的完整型別，但必須求助於弱式類型 （手動使用匯出的屬性，並符合簽章） 的選擇性通訊協定方法。

為了更方便取用的 API，會使用通訊協定，繫結工具也會產生公開 （expose） 所有選擇性方法的擴充方法類別。   這表示，只要您使用 API，您將能夠處理通訊協定為具有所有方法。

如果您想要使用的通訊協定定義在您的 API，您必須撰寫您的 API 定義基本架構空的介面。   如果您想要使用 MyProtocol API 中，您會需要執行這項操作：

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

### <a name="adopting-protocol-generated-interfaces"></a>採用的通訊協定產生介面

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

### <a name="protocol-inlining"></a>內嵌通訊協定

當您繫結現有的 OBJECTIVE-C 型別已宣告為採用一種通訊協定時，您會想要內嵌通訊協定直接。 若要這樣做，只是宣告您的通訊協定 」 為不含任何介面[ `[BaseType]` ](#BaseTypeAttribute)屬性，並列出基底介面的介面清單中的通訊協定。

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


## <a name="member-definitions"></a>成員定義

在本節中的屬性套用至類型的個別成員： 屬性和方法宣告。


### <a name="alignattribute"></a>AlignAttribute

用來指定屬性的傳回類型的對齊值。 某些屬性會採用指標必須在特定界限對齊的位址 (在發生這種情況，例如某些 Xamarin.iOS`GLKBaseEffect`屬性必須為 16 位元組對齊)。 您可以使用這個屬性來裝飾 getter，並使用對齊值。 這通常會搭配`OpenTK.Vector4`和`OpenTK.Matrix4`類型時的 Objective C Api 整合。

範例：

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```


### <a name="appearanceattribute"></a>AppearanceAttribute

`[Appearance]`屬性僅限於 iOS 5、 引進外觀管理員。

`[Appearance]`屬性可以套用至任何方法或屬性，參與`UIAppearance`framework。 當這個屬性會套用至方法或類別中的屬性時，它會直接繫結產生器，以建立用來設定樣式的這個類別的所有執行個體或符合特定準則的執行個體的強型別外觀類別。

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

使用`[AutoReleaseAttribute]`方法和屬性，以包裝方法引動過程中的方法上`NSAutoReleasePool`。

在 OBJECTIVE-C 中，有一些方法會傳回值，新增到預設`NSAutoReleasePool`。 根據預設，這些會移至您的執行緒`NSAutoReleasePool`，但只要受管理的物件存留的 Xamarin.iOS 也會保留您物件的參考，因為您可能不想將額外的參考保留在`NSAutoReleasePool`這只會取得清空直到您的執行緒傳回控制項至下一個執行緒，或返回主迴圈。

大量的屬性，例如套用這個屬性 (例如`UIImage.FromFile`)，會傳回已新增為預設值的物件`NSAutoReleasePool`。 如果沒有這個屬性中，映像會保留，只要您的執行緒未傳回至主迴圈的控制項。 Uf 您的執行緒是某種類型的背景程式下載程式會永遠保持運作，並等候工作時，影像就永遠不會釋放。

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]`用來強制執行的 managed 型別建立，即使傳回 unmanaged 的物件不符合繫結定義中所描述的類型。

標頭中所描述的類型與原生方法的傳回的類型不相符時，這是很有用，例如，採取下列 OBJECTIVE-C 定義從`NSURLSession`:

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

它清楚地指出它會傳回`NSURLSessionDownloadTask`執行個體，但尚未它**會傳回** `NSURLSessionTask`，這是超級類別，因此不轉換成`NSURLSessionDownloadTask`。 既然我們已經在型別安全內容中`InvalidCastException`會發生。

若要符合的標頭的描述，並避免`InvalidCastException`，則`[ForcedTypeAttribute]`用。

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

`[ForcedTypeAttribute]`也接受名為的布林值`Owns`也就是說`false`預設`[ForcedType (owns: true)]`。 擁有參數用來依照[擁有權原則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html)for**核心基礎**物件。

`[ForcedTypeAttribute]`只適用於在參數、 屬性和傳回值。

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

`[BindAsAttribute]`允許繫結`NSNumber`，`NSValue`並`NSString`（列舉） 更多精確C#類型。 屬性可用來建立更好、 更精確，在原生 api 的.NET API。

您可以將方法 （在傳回的值）、 參數和屬性與裝飾`BindAs`。 唯一的限制是，您的成員**不得**內被`[Protocol]`或[ `[Model]` ](#ModelAttribute)介面。

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

目前支援的封裝類型包括：

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

下列C#資料類型支援從/至封裝`NSValue`:

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

下列C#資料類型支援從/至封裝`NSNumber`:

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

[`[BindAs]`](#BindAsAttribute) 適用於使用 conjuntion [NSString 常數的列舉支援](#enum-attributes)因此您可以建立更佳的.NET API 中，例如：

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

我們將會處理`enum`  <->  `NSString`才提供的列舉類型的轉換[ `[BindAs]` ](#BindAsAttribute)是[後盾 NSString 常數](#enum-attributes)。

#### <a name="arrays"></a>陣列

[`[BindAs]`](#BindAsAttribute) 也支援陣列的任何支援的類型，您可以有下列的 API 定義，做為範例：

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

`rects`參數將會封裝到`NSArray`，其中包含`NSValue`每個`CGRect`然後就會傳回的陣列`CAScroll?`其中已建立使用傳回的值`NSArray`包含`NSStrings`。

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

`[Bind]`屬性有兩個用途時套用至方法或屬性宣告，並套用至個別的 getter 或 setter 的屬性時的另一個。

當用於方法或屬性，則程式`[Bind]`屬性是要產生方法叫用指定的選取器。 但結果產生的方法不使用裝飾[ `[Export]` ](#ExportAttribute)屬性，這表示，它不能參與方法覆寫。 這通常用於搭配`[Target]`實作 Objective C 的擴充方法的屬性。

例如: 

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

當用於 getter 或 setter，`[Bind]`屬性用來改變推斷程式碼產生器所產生的 getter 和 setter OBJECTIVE-C 選取器名稱的屬性時的預設值。 根據預設，當旗標名稱的屬性`fooBar`，產生器會產生`fooBar`匯出的 getter 和`setFooBar:`setter。 在少數情況下，OBJECTIVE-C 未遵循此慣例，通常它們 getter 將名稱變更為`isFooBar`。
您會使用這個屬性，以通知這個產生器。

例如: 

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute" />

### <a name="asyncattribute"></a>AsyncAttribute

只適用於 Xamarin.iOS 6.3 和較新。

此屬性可以套用至採用完成處理常式，其最後一個引數的方法。

您可以使用`[Async]`其最後一個引數為回呼的方法上的屬性。  當您將此套用至方法時，繫結產生器會產生該方法具有尾碼的版本`Async`。  如果回呼不接受任何參數，將會傳回值`Task`，如果回呼會採用參數，結果會是`Task<T>`。

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

下列會產生這個非同步方法：

```csharp
Task LoadFileAsync (string file);
```

如果回呼不接受多個參數，您應該設定`ResultType`或`ResultTypeName`來指定所要產生的型別會保留所有屬性的名稱。

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

下列會產生這個非同步方法，其中`FileLoading`包含用來存取屬性`files`和`byteCount`:

```csharp
Task<FileLoading> LoadFile (string file);
```

如果回呼的最後一個參數是`NSError`，然後產生`Async`方法會檢查，如果值不是 null，並產生的非同步方法，如果這種情況，將設定的工作例外狀況。

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

以上會產生下列非同步方法：

```csharp
Task<string> UploadAsync (string file);
```

而發生錯誤時，產生的工作必須設定為例外狀況`NSErrorException`包裝產生`NSError`。

#### <a name="asyncattributeresulttype"></a>AsyncAttribute.ResultType

使用這個屬性來指定傳回值`Task`物件。   這個參數會使用現有的類型，因此它必須定義在您的核心 api 定義的其中一個。

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

使用這個屬性來指定傳回值`Task`物件。   這個參數會採用您所需的型別名稱的名稱，產生器會產生一系列的屬性，其中每個參數接受回呼。

#### <a name="asyncattributemethodname"></a>AsyncAttribute.MethodName

您可以使用這個屬性，來自訂產生的非同步方法的名稱。   預設值是使用方法的名稱，並將文字"Async"附加，您可以使用這個選項變更此預設值。

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

這個屬性會套用至字串參數或字串屬性，並指示程式碼產生器，以使用零複製字串封送處理這個參數，並改為建立新的 NSString 執行個體，從C#字串。
如果您指示產生器以使用零複製字串封送處理使用這個屬性才需要字串`--zero-copy`命令列選項或設定組件層級屬性`ZeroCopyStringsAttribute`。

這是必要的情況下在 OBJECTIVE-C 是宣告之屬性的位置`retain`或是`assign`屬性，而不是`copy`屬性。 這些通常會發生在已進行錯誤 「 最佳化 」 的開發人員的協力廠商程式庫中。 一般情況下，`retain`或是`assign``NSString`屬性不正確，因為`NSMutableString`或使用者衍生的類別`NSString`可能修改的字串稍有重大的程式庫程式碼的情況下的內容應用程式。 通常這是因為太早最佳化。

下面顯示兩個這類屬性目標 c:

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```


### <a name="disposeattribute"></a>DisposeAttribute

當您套用`[DisposeAttribute]`到類別，您可以提供程式碼片段會新增至`Dispose()`類別的方法實作。

由於`Dispose`方法會自動產生`bmac-native`並`btouch-native`工具，您需要使用`[Dispose]`插入所產生的一些程式碼的屬性`Dispose`方法實作。

例如: 

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

`[Export]`屬性用來將方法或屬性來公開以 OBJECTIVE-C 執行階段。 這個屬性會繫結工具與實際的 Xamarin.iOS 和 Xamarin.Mac 執行階段之間共用。 方法中，參數會傳遞逐字產生的程式碼，屬性的 getter 和 setter 匯出會產生與基底宣告為基礎 (請參閱節[`[BindAttribute]`](#BindAttribute)如需有關如何變更資訊繫結工具的行為）。

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

[選取器](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html)代表基礎 OBJECTIVE-C 方法或屬性進行繫結的名稱。

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

此屬性用來做為欄位，視需要載入，並公開至公開 C 全域變數C#程式碼。 通常這必要取得 C 或 Objective C 中定義的和，可能是在一些 Api 中，使用其中一個語彙基元或其值是不透明的而且必須使用做為常數的值-是由使用者程式碼。

語法：

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName`是 C 符號連結。 依預設這會從命名空間來推斷其名稱的型別定義所在的程式庫將其載入。 如果這不是其中會查閱符號的程式庫，您應該傳遞`libraryName`參數。 如果您要連結的靜態程式庫，使用`__Internal`做為`libraryName`參數。

產生的屬性一律是靜態的。

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

不支援 setter[列舉後盾 NSString 常數](#enum-attributes)，但它們可以手動繫結如有需要。

範例：

```csharp
[Static]
interface CameraEffects {
     [Field ("kCameraEffectsZoomFactorKey", "CameraLibrary")]
     NSString ZoomFactorKey { get; }
}
```

<a name="InternalAttribute" />

### <a name="internalattribute"></a>InternalAttribute

`[Internal]`屬性可以套用至方法或屬性和其加上旗標與產生的程式碼的影響`internal`C#使程式碼的程式碼只能存取產生的組件中的關鍵字。 這通常用來隱藏太低層級的 Api 或提供您想要改善時，或不產生器所支援的 api 的次佳的公用 API，而且需要某些手動編碼。

當您設計的繫結時，您通常會隱藏方法或使用此屬性的屬性並提供不同的名稱，此方法或屬性，然後在您C#互補的支援檔案，您可以加入的強型別包裝函式公開 （expose）基礎的功能。

例如: 

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

然後，您也可以在支援的檔案中，您可能會有一些程式碼，就像這樣：

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

<a name="IsThreadStaticAttribute" />

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

這個屬性加上旗標來加以註解使用.NET 屬性的支援欄位`[ThreadStatic]`屬性。 這非常有用，如果欄位是執行緒靜態變數。

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin.iOS 6.0.6)

這個屬性會讓方法支援原生 (OBJECTIVE-C) 例外狀況。
而不是呼叫`objc_msgSend`引動過程會直接通過自訂 trampoline 會攔截 ObjectiveC 例外狀況，並將它們封送處理成 managed 例外狀況。

目前只有少數`objc_msgSend`支援簽章 (是否使用此繫結的應用程式的原生連結而遺失 monotouch_ 失敗時，不支援簽章找出 *_objc_msgSend*符號)，但更多可在要求中新增。


### <a name="newattribute"></a>NewAttribute

這個屬性會套用至方法和屬性，以有產生器產生`new`關鍵字宣告前面。

它用來避免編譯器警告時相同的方法或屬性名稱中導入已存在於基底類別的子類別。

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

您可以套用這個屬性，將產生器產生強型別協助程式 Notifications 類別的欄位。

這個屬性可以用於不含引數執行沒有承載的通知，或者您可以指定`System.Type`參考另一個介面，在 API 定義中，通常與名稱結尾"EventArgs"。 產生器會將介面變成類別子類別化`EventArgs`，且會包含所有列出的屬性。 [ `[Export]` ](#ExportAttribute)屬性應該用於`EventArgs`類別，以列出用來查閱 OBJECTIVE-C 字典擷取值的索引鍵的名稱。

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

您的程式碼的使用者可以接著輕鬆訂閱通知張貼至[NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter)使用如下的程式碼：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

或若要設定要觀察特定物件。 如果您傳遞`null`至`objectToObserve`這個方法的行為就像其對等。

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

傳回的值從`ObserveDidStart`可用來輕鬆地停止接收通知，像這樣：

```csharp
token.Dispose ();
```

您也可以呼叫[NSNotification.DefaultCenter.RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject))並傳遞 token。 如果您的通知中包含參數，您應該指定 helper`EventArgs`介面，就像這樣：

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

會產生上述`MyScreenChangedEventArgs`類別`ScreenX`並`ScreenY`屬性，可將擷取的資料[NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo)字典的索引鍵`ScreenXKey`和`ScreenYKey`分別並套用適當的轉換。 `[ProbePresence]`屬性產生器用來探查，如果已設定金鑰`UserInfo`，而不是嘗試擷取值。 這用於的情況下，索引鍵的目前狀態 （通常適用於布林值） 的值。

這可讓您撰寫如下的程式碼：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

在某些情況下，沒有任何傳遞字典的值相關聯的常數。  Apple 有時候會使用公用符號常數和有時候會使用字串常數。  依預設[ `[Export]` ](#ExportAttribute)屬性，在您提供`EventArgs`類別將指定的名稱做為公用符號來查閱在執行階段。  如果這不是，並改用它應該查閱為字串常數，則傳遞`ArgumentSemantic.Assign`匯出屬性的值。

**Xamarin.iOS 8.4 的新功能**

有時候，通知就會開始生命週期，而不需要任何引數，因此使用[ `[Notification]` ](#NotificationAttribute)不是可接受的引數。  但有時候，將會介紹通知的參數。  若要支援此案例中，屬性可以多次套用。

如果您正在開發的繫結，而且您想要避免破壞現有的使用者程式碼，您會開啟從現有的通知：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

成的版本，列出通知屬性兩次，就像這樣：

```csharp
interface MyClass {
    [Notification]
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

<a name="NullAllowedAttribute" />

### <a name="nullallowedattribute"></a>NullAllowedAttribute

這套用至屬性時它就會標示為允許值的屬性`null`指派給它。 這只適用於參考型別。

這套用至方法簽章，指出指定的參數可以是 null，而且應該傳遞中執行任何檢查參數`null`值。

如果參考型別沒有這個屬性，繫結工具會產生傳遞給 OBJECTIVE-C 之前指派的值檢查，並將產生的檢查作業將會擲回`ArgumentNullException`指派的值是否`null`。

例如: 

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute" />

### <a name="overrideattribute"></a>OverrideAttribute

使用這個屬性來指示繫結產生器，應該使用標示這個特定方法的繫結`override`關鍵字。

### <a name="presnippetattribute"></a>PreSnippetAttribute

您可以使用這個屬性將一些程式碼來驗證所有輸入的參數之後，但在 OBJECTIVE-C 程式碼呼叫之前插入

範例：

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

您可以使用這個屬性將一些程式碼來插入之前的任何參數來驗證產生的方法。

範例：

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

會指示繫結產生器，以叫用指定的屬性，從這個類別，以從中擷取值。

這個屬性通常會用來重新整理指向保留參考的物件圖形的參考物件的快取中。 通常它會顯示在具有作業，例如新增/移除程式碼。 這個方法使用，以便加入或移除，以確保更新內部快取中的項目之後我們會讓受管理的參考，實際上正在使用中的物件。 這可能是因為繫結工具會產生參考的所有物件的支援欄位中指定的繫結。

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

在此情況下，`Dependencies`屬性將會叫用後新增或移除相依性從`NSOperation`載入的物件，確保我們的代表實際的圖形物件，防止記憶體流失以及記憶體損毀。

### <a name="postsnippetattribute"></a>PostSnippetAttribute

您可以使用這個屬性將一些C#來源的程式碼已叫用基礎 OBJECTIVE-C 方法之後要插入的程式碼

範例：

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

此屬性會套用至傳回值以標示它們為 proxy 物件。 某些不區別使用者繫結的 Objective C Api 傳回的 proxy 物件。 這個屬性的作用是為物件加上旗標`DirectBinding`物件。 Xamarin.mac 的案例，您可以看到[討論這個 bug](https://bugzilla.novell.com/show_bug.cgi?id=670844)。

### <a name="retainlistattribute"></a>RetainListAttribute

指示產生器以保留給參數的受管理的參考或移除參數的內部參考。 這用來保留物件參考。

語法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果值`doAdd`為 true，則參數會加入至`__mt_{0}_var List<NSObject>;`。 何處`{0}`會取代指定`listName`。 您必須在 api 互補部分類別中宣告此支援欄位。

如需範例，請參閱[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute (Xamarin.iOS 6.0)

這可以套用至傳回類型以指出產生器應該呼叫`Release`上前將它傳回的物件。 如此時才需要一種方法可讓您保留物件 （而不是 autoreleased 物件，這是最常見的案例）

範例：

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

此外這個屬性會傳播到產生的程式碼中，以便 Xamarin.iOS 執行階段可讓您知道它必須保留這類函式返回 OBJECTIVE-C 物件。


### <a name="sealedattribute"></a>SealedAttribute

指示產生器以旗標產生的方法，為已密封。 如果未指定此屬性，預設值是產生的虛擬方法 （一種虛擬方法、 抽象方法或根據其他屬性使用的方式覆寫）。

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

當`[Static]`屬性套用至方法或屬性，這會產生靜態方法或屬性。 如果未指定此屬性，產生器會產生執行個體方法或屬性。


### <a name="transientattribute"></a>TransientAttribute

使用這個旗標屬性的值是暫時性的也就是由 iOS 暫時建立，但不是長時間執行的物件的屬性。 當這個屬性套用至屬性時，產生器就不會建立支援欄位，這個屬性，這表示 managed 的類別不會保留物件的參考。

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

在設計中 Xamarin.iOS/Xamarin.Mac 繫結，`[Wrap]`屬性用來包裝的強型別物件的弱式型別物件。 此派上用場主要與 OBJECTIVE-C 委派物件通常會宣告為型別`id`或`NSObject`。 使用 Xamarin.iOS 和 Xamarin.Mac 的慣例是公開 （expose） 這些委派或資料來源為型別`NSObject`和使用的慣例"Weak"+ 所公開的名稱來命名。 `id delegate`從 OBJECTIVE-C 的屬性會公開為`NSObject WeakDelegate { get; set; }`API 合約檔案中的屬性。

但通常會指派給此委派的值是強型別，因此我們介面的強型別，並套用`[Wrap]`屬性，也就是說，使用者可以選擇使用弱式型別，如果它們需要一些 fine 控制項，或如果他們需要求助於低階 tricks，或者也可以使用強型別 屬性來執行大部分的工作。

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

這是使用者會使用委派的弱式型別版本的方式：

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

這是使用者如何使用強型別版本，請注意，使用者會利用C#的型別系統，並使用覆寫關鍵字來宣告其意圖和他不必以手動方式來裝飾方法`[Export]`，因為我們未在使用者的繫結運作的：

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

另一個使用`[Wrap]`屬性是為了支援強型別版本的方法。  例如: 

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

所產生的成員`[Wrap]`不是`virtual`根據預設，如果您需要`virtual`您可以將它設定為成員`true`選擇性`isVirtual`參數。

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

本章節描述您可以套用至方法定義中參數的屬性，以及`[NullAttribute]`，套用至整個屬性。

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

這個屬性會套用至參數中的型別C#委派的宣告，以通知有問題的參數符合 OBJECTIVE-C 繫結器封鎖的呼叫慣例和應封送處理它以這種方式。

這通常用來在目標 c： 定義如下的回呼

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另請參閱：[CCallback](#CCallback)。

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

這個屬性會套用至參數中的型別C#委派來通知繫結器，有問題的參數符合呼叫慣例的 C ABI 函式指標，而且應封送處理它以這種方式宣告。

這通常用來在目標 c： 定義如下的回呼

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另請參閱：[BlockCallback](#BlockCallback)。

### <a name="params"></a>params

您可以使用`[Params]`上最後一個陣列參數有插入定義中的 「 參數 」 的產生器方法定義的屬性。   這可讓繫結，以便輕鬆地進行選擇性參數。

例如，下列定義：

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

可讓撰寫下列程式碼：

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

這有額外的好處，它不需要使用者建立單純的傳遞項目陣列。

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

您可以使用`[PlainString]`屬性，指示繫結產生器，以將字串做為 C 字串，而不是傳遞做為參數傳遞的字串參數前面`NSString`。

大部分的 Objective C Api 取用`NSString`參數，但是少數幾個 Api 會公開`char *`API 傳遞的字串，而不`NSString`變化。
使用`[PlainString]`在這些情況下。

例如，下列的 Objective C 宣告：

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

應該要繫結，就像這樣：

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

指示產生器以保留指定的參數的參考。 產生器會為此欄位提供的備份存放區，或者您可以指定一個名稱 ( `WrapName`) 來儲存的值。 這是用來存放做為參數傳遞至 OBJECTIVE-C 和當您知道 Objective C 將只會保留物件的這個複本的 managed 物件的參考。 比方說，API，例如`SetDisplay (SomeObject)`會使用這個屬性，因為它可能 SetDisplay，可以只顯示一個物件一次。 如果您要追蹤的多個物件 （例如，適用於類似堆疊的 API) 會使用`[RetainList]`屬性。

語法：

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```


### <a name="retainlistattribute"></a>RetainListAttribute

指示產生器以保留給參數的受管理的參考或移除參數的內部參考。 這用來保留物件參考。

語法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果值`doAdd`為 true，則參數會加入至`__mt_{0}_var List<NSObject>`。 何處`{0}`會取代指定`listName`。 您必須在 api 互補部分類別中宣告此支援欄位。

如需範例，請參閱[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)


### <a name="transientattribute"></a>TransientAttribute

此屬性套用至參數，並從 OBJECTIVE-C 來轉換時，才會使用C#。  在這些不同的 Objective C 的轉換期間`NSObject`參數會包裝成受管理物件的表示。

執行階段會採用的原生物件的參考，並保留參考，直到物件的最後一個 managed 的參考已不存在，而且 GC 有機會執行。

在少數情況下，很重要的C#執行階段將保留的原生物件的參考。  此外，這有時會發生於基礎原生程式碼已附加至參數的生命週期的特殊行為。  例如： 參數的解構函式會執行某些清除動作，或處置一些非常寶貴的資源。

這個屬性會通知您想要回到 Objective C 的覆寫方法時，盡可能處置物件的執行階段。

規則很簡單： 如果執行階段必須從原生的物件，建立新的 managed 的表示，然後在函式結束時，會卸除的原生物件的保留計數，並將清除的 managed 物件的控制代碼屬性。   這表示如果您保留的 managed 物件的參考，該參考將會變成無用 （叫用方法，它將會擲回例外狀況）。

如果未建立傳遞的物件，或如果已有未處理受管理的物件的表示，強制的 dispose 不會發生。 


## <a name="property-attributes"></a>Property 屬性

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

這個屬性用來支援 OBJECTIVE-C 慣用語，其中具有 getter 的屬性在基底類別引進，並可變動的子類別介紹 setter。

因為C#不的支援此模型中，基底類別必須有 setter 和 getter，及子類別可以使用[OverrideAttribute](#OverrideAttribute)。

這個屬性只會在屬性 setter，並用來支援在 OBJECTIVE-C 中的可變動的慣用語

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

<a name="enum-attributes" />

## <a name="enum-attributes"></a>Enum 屬性

對應`NSString`列舉值的常數是一種簡單的方法來建立更佳的.NET API。 它：

* 可讓程式碼完成功能成為更有用，顯示**只**API; 的正確值
* 將型別安全，您無法使用另一個`NSString`常數中不正確的內容; 及
* 可讓隱藏一些常數，讓程式碼自動完成功能顯示較短的 API 清單，而不會失去功能。

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

從上述的繫結定義會建立產生器`enum`本身，也會建立`*Extensions`包含列舉值之間的兩個方法轉換方法的靜態型別和`NSString`常數。 這表示常數會保持可供開發人員即使它們不是 API 的一部分。

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

您可以裝飾**一個**具有此屬性的列舉值。 這會成為所傳回的列舉值不在已知的常數。

從上面的範例：

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

如果沒有列舉值裝飾時則`NotSupportedException`就會擲回。

### <a name="errordomainattribute"></a>ErrorDomainAttribute

錯誤碼會繫結做為列舉值。 通常是為其網域時發生錯誤，而且它不一定容易尋找適用於哪一個 （或如果有即使的話）。

您可以使用這個屬性，列舉本身相關聯的錯誤網域。

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

然後，您就可以呼叫擴充方法`GetDomain`取得網域的任何錯誤常數。

### <a name="fieldattribute"></a>FieldAttribute

這是相同`[Field]`屬性用於型別中的常數。 它也可用在列舉對應特定常數的值。

A`null`值可用來指定應該傳回哪一個列舉值，如果`null``NSString`指定常數。

從上面的範例：

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

如果沒有`null`值則`ArgumentNullException`就會擲回。

## <a name="global-attributes"></a>全域屬性

全域屬性可以套用使用`[assembly:]`屬性修飾詞，例如[ `[LinkWithAttribute]` ](#LinkWithAttribute)也可以使用任何位置，例如[ `[Lion]` ](#SinceAndLionAttributes)和[ `[Since]`](#SinceAndLionAttributes)屬性。

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

這是可讓開發人員指定的連結來重複使用的繫結的程式庫，而不需要手動設定 gcc_flags 程式庫的取用者所需的旗標和額外的 mtouch 引數傳遞至程式庫的組件層級屬性。

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

當您使用`[LinkWith]`屬性，指定`libraryName`內嵌到產生的組件，讓使用者能夠提供單一的 DLL，其中包含未受管理的相依性以及正確使用時所需要的命令列旗標從 Xamarin.iOS 程式庫。

它也可提供`libraryName`，在此情況下`LinkWith`屬性可以用來只指定 其他連結器旗標：

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>LinkWithAttribute 建構函式

這些建構函式可讓您指定要與連結和內嵌至您產生的組件，支援程式庫支援的目標和任何選用的程式庫旗標所需的程式庫連結的程式庫。

請注意，`LinkTarget`引數由 Xamarin.iOS 推斷，因此不需要設定。

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

要繫結的程式庫是否強制規定任何架構上 (以外`Foundation`並`UIKit`)，您應該設定`Frameworks`屬性設為字串，包含以空格分隔清單的必要的平台架構。 比方說，如果您要繫結程式庫需要`CoreGraphics`並`CoreText`，您會設定`Frameworks`屬性設`"CoreGraphics CoreText"`。

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

設定此屬性設定為 true，如果產生的可執行檔必須使用 c + + 編譯器，而不預設值，也就是 C 編譯器進行編譯。 如果您要繫結的程式庫以 c + + 撰寫，請使用此選項。

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute.LibraryName

要包裝的 unmanaged 程式庫名稱。 這是 「.a"副檔名的檔案，它可以包含多個平台 （例如 ARM 和 x86 模擬器） 的物件程式碼。

檢查舊版 Xamarin.iOS`LinkTarget`屬性來判斷平台程式庫支援，但這是立即自動偵測和`LinkTarget`屬性會被忽略。

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

`LinkerFlags`字串可讓繫結作者指定連結到應用程式的原生程式庫時，所需任何額外的連結器旗標。

例如，如果原生程式庫需要 libxml2 和 zlib，您可以將`LinkerFlags`字串`"-lxml2 -lz"`。

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute.LinkTarget

檢查舊版 Xamarin.iOS`LinkTarget`屬性來判斷平台程式庫支援，但這是立即自動偵測和`LinkTarget`屬性會被忽略。

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

設定此屬性設定為 true，如果您要連結程式庫需要 GCC 例外狀況處理程式庫 (gcc_eh)

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute.SmartLink

`SmartLink`屬性應設為 true，讓 Xamarin.iOS 判斷是否`ForceLoad`為必要。

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

`WeakFrameworks`屬性的運作方式一樣`Frameworks`屬性，但是，在連結階段`-weak_framework`規範時，會傳遞至 gcc 上，每個列出的架構。

`WeakFrameworks` 可讓程式庫和應用程式，以針對平台架構的弱式連結，使其可以選擇性地使用它們如果可用，但不是會硬式相依性它們這非常有用，如果您的程式庫是上新增額外的功能更新iOS 版本。 如需弱式連結的詳細資訊，請參閱 Apple 的文件上[弱式連結](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html)。

很適合使用弱式連結會`Frameworks`像是帳戶`CoreBluetooth`， `CoreImage`， `GLKit`，`NewsstandKit`和`Twitter`因為它們只可在 iOS 5 中。

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) 和 LionAttribute (macOS)

您使用`[Since]`屬性旗標的 Api，為具有引進某一點的時間。 屬性應該只用來加上旗標可能會導致執行階段問題，如果基礎類別、 方法或屬性無法使用的類型和方法。

語法：

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

它應該在一般情況下不會套用至列舉型別、 條件約束或新的結構因為那些不會造成執行階段錯誤，若較舊版本的作業系統的裝置上執行。

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

`[Lion]`屬性會套用相同的方式，但與 Lion 導入的類型。 若要使用的原因`[Lion]`與更特定的版本號碼，可在 iOS 中是主要的 OS X 版本鮮少發生而容易記憶的作業系統，方法為透過其版本號碼比其代號名稱經常，修改該 iOS

### <a name="adviceattribute"></a>AdviceAttribute

您可以使用這個屬性可以讓開發人員可能會比較方便，才能使用其他 Api 的相關提示。   比方說，如果您提供強型別版本的 API，您可以使用這個屬性之弱型別屬性上引導開發人員更好的 API。

這個屬性的資訊顯示在文件和工具，可以用來提供有關如何改善的使用者建議

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

只適用於 Xamarin.iOS 5.4 和更新版本。

這個屬性會指示產生器，這個特定的程式庫的繫結 (如果套用與`[assembly:]`) 或型別應該使用快速的零複製字串封送處理。 這個屬性就相當於將傳遞命令列選項`--zero-copy`給產生器。

當使用零複製的字串時，產生器可以有效地使用相同C#字串做為字串，而不會造成建立新的 Objective C 會消耗`NSString`物件，並避免將複製的資料C#字串至Objective C 字串。 使用零複製字串的唯一缺點是，您必須確定任何字串屬性，您將包裝發生在標示為`retain`或是`copy`具有`[DisableZeroCopy]`屬性設定。 這是需要因為零複製字串的控制代碼會在堆疊上配置，而無效時傳回的函式。

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

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>強型別字典

與 Xamarin.iOS 8.0 中，我們開始支援該換行時輕鬆地建立強型別類別`NSDictionaries`。

雖然它一直都可以使用[DictionaryContainer](xref:Foundation.DictionaryContainer)資料類型以及手動的 API，現在正是執行這項操作容易許多。  如需詳細資訊，請參閱 <<c0> [ 呈現強型別](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types)。

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

當這個屬性套用至介面時，產生器將會產生具有相同名稱做為衍生自介面類別[DictionaryContainer](xref:Foundation.DictionaryContainer)並轉換成強型別介面中定義每個屬性getter 和 setter 的字典。

這會自動產生的類別可以具現化，從現有`NSDictionary`，已建立或新增。

這個屬性會採用一個參數，包含用來存取在字典上的項目之索引鍵的類別名稱。   預設屬性的介面中每一個屬性會查詢中指定的型別名稱後置詞 「 金鑰 」 的成員。

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

在上述案例中，`MyOption`類別將會產生的字串屬性`Name`，將會使用`MyOptionKeys.NameKey`作為索引鍵來擷取字串字典。   將根據`MyOptionKeys.AgeKey`做為要擷取字典索引鍵`NSNumber`其中包含 int。

如果您想要使用不同的金鑰，您可以使用匯出屬性的屬性，例如：

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

中支援下列資料類型`StrongDictionary`定義：

|C#介面類型|`NSDictionary` 儲存體類型|
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
