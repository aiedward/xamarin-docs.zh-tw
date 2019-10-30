---
title: 系結類型參考指南
description: 本參考指南說明在建立C#與目標 C 程式庫的系結時，所需瞭解的各種屬性和概念。
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: e89cbf98dbaf5a96fdfa51069f580b914ba5ff76
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016307"
---
# <a name="binding-types-reference-guide"></a>系結類型參考指南

本檔說明您可以用來標注 API 合約檔案以驅動系結和所產生程式碼的屬性清單

在C#大部分的情況下，都是以定義目標 C 程式碼的介面定義的方式，來C#撰寫 xamarin 和 xamarin API 合約。 此程式牽涉到混合介面宣告，加上 API 合約可能需要的一些基本類型定義。 如需系結類型的簡介，請參閱我們的附屬指南系結[目標-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)。

## <a name="type-definitions"></a>型別定義

語法：

```csharp
[BaseType (typeof (BTYPE))
interface MyType : [Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

合約定義中的每個介面，都具有[`[BaseType]`](#BaseTypeAttribute)屬性，可宣告產生之物件的基底類型。 在上述宣告中，會產生C#系結至名為`MyType`之目標-C 類型的 `MyType` 類別類型。

如果您使用介面繼承語法指定 typename 之後的任何類型（在上述範例中 `Protocol1` 和 `Protocol2`），這些介面的內容將會內嵌，就好像它們已屬於 `MyType`的合約一樣。
Xamarin iOS 介面類別型採用通訊協定的方式，是將通訊協定中宣告的所有方法和屬性內嵌到類型本身。

以下顯示如何在 Xamarin iOS 合約中定義 `UITextField` 的目標-C 宣告：

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

會以下列方式撰寫為C# API 合約：

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

您可以將其他屬性套用至介面，以及設定[`[BaseType]`](#BaseTypeAttribute)屬性，以控制程式代碼產生的許多其他層面。

### <a name="generating-events"></a>產生事件

其中一個 Xamarin. iOS 和 Xamarin. Mac API 設計的功能是將目標 C 委派類別對應為C#事件和回呼。 使用者可以選擇以每個實例為基礎，不論他們是否想要採用目標 C 程式設計模式，方法是指派給屬性（例如） `Delegate` 類別的實例，以執行目標 C 執行時間所呼叫的各種方法，或是選擇C#-樣式的事件和屬性。

我們來看一下如何使用目標-C 模型的其中一個範例：

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

在上述範例中，您可以看到我們已選擇覆寫兩個方法，一個是滾動事件發生的通知，而第二個是應傳回布林值的回呼，指示 `scrollView` 是否應滾動到頂端或 not.

此C#模型可讓您的程式庫使用者使用C#事件語法或屬性語法來接聽通知，以連結預期會傳回值的回呼。

這就是相同C#功能的程式碼使用 lambda 的樣子：

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

由於事件不會傳回值（它們具有 void 傳回型別），因此您可以連接多個複本。 `ShouldScrollToTop` 不是事件，而是具有具有此簽章之型別 `UIScrollViewCondition` 的屬性：

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

它會傳回 `bool` 值，在此案例中，lambda 語法可讓我們只從 `MakeDecision` 函數傳回值。

系結產生器支援產生事件和屬性，以將 `UIScrollView` 之類的類別連結 `UIScrollViewDelegate` （我們稱之為模型類別），這是藉由使用 `Events` 和 `Delegates` 參數來標注[`[BaseType]`](#BaseTypeAttribute)定義來完成的。如下所述。 除了使用這些參數來標注[`[BaseType]`](#BaseTypeAttribute)之外，還必須通知產生器更多元件。

對於採用多個參數的事件（在目標-C 中，慣例是委派類別中的第一個參數是傳送者物件的實例），您必須提供所產生 `EventArgs` 類別所需的名稱。 這是使用模型類別中方法宣告的[`[EventArgs]`](#EventArgsAttribute)屬性來完成。 例如:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述宣告會產生衍生自 `EventArgs` 的 `UIImagePickerImagePickedEventArgs` 類別，並將這兩個參數、`UIImage` 和 `NSDictionary`封裝在一起。 產生器會產生下列結果：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然後，它會在 `UIImagePickerController` 類別中公開下列內容：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

傳回值的模型方法會以不同的方式系結。 這兩者都需要產生C#之委派的名稱（方法的簽章），而且也會傳回預設值，以防使用者不提供自己的執行。 例如，`ShouldScrollToTop` 定義如下：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

上述程式會使用上述所示的簽章來建立 `UIScrollViewCondition` 委派，如果使用者未提供執行，則傳回值將會是 true。

除了[`[DefaultValue]`](#DefaultValueAttribute)屬性之外，您也可以使用[`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)屬性，指示產生器傳回呼叫中指定參數的值，或用來指示產生器的[`[NoDefaultValue]`](#NoDefaultValueAttribute)參數。沒有預設值。

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

您可以使用 [`Name`] 屬性來控制這個型別在目標-C 世界中將系結的名稱。 這通常用來為C#型別提供與 .NET Framework 設計方針相容的名稱，但會對應到不遵循該慣例的目標-C 中的名稱。

例如，在下列情況下，我們會將目標-C `NSURLConnection` 類型對應到 `NSUrlConnection`，因為 .NET Framework 設計方針使用 "Url" 而不是 "URL"：

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

指定的名稱會用來做為系結中產生的 `[Register]` 屬性的值。 如果未指定 `Name`，就會使用類型的簡短名稱做為所產生輸出中 `[Register]` 屬性的值。

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType. 事件和 BaseType。委派

這些屬性是用來驅動C#產生的類別中的產生樣式事件。 它們可用來連結指定的類別與其目標-C 委派類別。 在許多情況下，類別會使用委派類別來傳送通知和事件。 例如，`BarcodeScanner` 會有隨附的 `BardodeScannerDelegate` 類別。 `BarcodeScanner` 類別通常會有一個 `Delegate` 的屬性，您可以將 `BarcodeScannerDelegate` 的實例指派給，而在這種情況下，您可能會想要向C#使用者公開類似的樣式事件介面，而在這些情況下，您會使用`Events`和`Delegates`[`[BaseType]`](#BaseTypeAttribute)屬性的屬性。

這些屬性一律會一起設定，而且必須具有相同數目的元素，且保持同步。`Delegates` 陣列會針對您想要包裝的每一個弱型別委派包含一個字串，而 `Events` 陣列會針對您想要與之產生關聯的每個型別，各包含一個型別。

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

#### <a name="basetypekeeprefuntil"></a>BaseType. KeepRefUntil

如果您在建立這個類別的新實例時套用這個屬性，則會保留該物件的實例，直到叫用 `KeepRefUntil` 所參考的方法為止。 當您不想要讓使用者保留物件的參考以使用您的程式碼時，這非常適合用來改善 Api 的可用性。 這個屬性的值是 `Delegate` 類別中的方法名稱，因此，您也必須搭配 `Events` 和 `Delegates` 屬性來使用這項功能。

下列範例示範如何在 Xamarin 中 `UIActionSheet` 使用此功能：

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

<a name="DesignatedDefaultCtorAttribute" />

### <a name="designateddefaultctorattribute"></a>DesignatedDefaultCtorAttribute

當這個屬性套用至介面定義時，它會在預設（產生）的函式上產生 `[DesignatedInitializer]` 屬性，而此函式會對應至 `init` 選取器。

<a name="DisableDefaultCtorAttribute" />

### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

當此屬性套用至介面定義時，它會防止產生器產生預設的函式。

當您需要使用類別中的其中一個其他函式來初始化物件時，請使用這個屬性。

<a name="PrivateDefaultCtorAttribute" />

### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

當此屬性套用至介面定義時，它會將預設的函式標示為私用。 這表示您仍然可以從延伸模組檔案內部將此類別的物件具現化，但您的類別的使用者就無法存取它。

<a name="CategoryAttribute" />

### <a name="categoryattribute"></a>CategoryAttribute

在類型定義上使用此屬性可系結目標 C 類別，並將其公開C#為擴充方法，以反映目標-c 公開功能的方式。

類別目錄是一個目標 C 機制，用來擴充類別中可用的方法和屬性集合。   在實務上，當特定架構連結到（例如 `UIKit`）時，它們會用來擴充基類的功能（例如 `NSObject`），使其方法可供使用，但只有在新架構連結時才會使用。   在某些其他情況下，它們是用來依功能來組織類別中的功能。   它們與C#擴充方法的精神很類似。

這是類別在目標-C 中的樣子：

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上述範例位於程式庫上，會使用 `makeBackgroundRed`方法擴充 `UIView` 的實例。

若要系結，您可以在介面定義上使用[`[Category]`](#CategoryAttribute)屬性。   使用[`[Category]`](#CategoryAttribute)屬性時， [`[BaseType]`](#BaseTypeAttribute)屬性的意義會從用來指定要擴充的基類，改為要擴充的類型。

以下顯示如何系結 `UIView` 延伸模組，並將其C#轉換為擴充方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

上述會建立一個 `MyUIViewExtension` 包含 `MakeBackgroundRed` 擴充方法的類別。   這表示您現在可以在任何 `UIView` 子類別上呼叫 `MakeBackgroundRed`，提供您在目標-C 上取得的相同功能。

在某些情況下，您會在類別內尋找**靜態**成員，如下列範例所示：

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

這會導致**不正確**的類別C#介面定義：

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

這是不正確的，因為若要使用 `BoolMethod` 的延伸模組，您需要 `FooObject` 的實例，但您要系結 ObjC**靜態**延伸模組，這是因為C#擴充方法的實作用而造成的副作用。

使用上述定義的唯一方式是下列不好的程式碼：

```csharp
(null as FooObject).BoolMethod (range);
```

若要避免這種情況，建議您將 `BoolMethod` 定義內嵌在 `FooObject` 介面定義本身中，這可讓您呼叫此延伸模組，就像它預期 `FooObject.BoolMethod (range)`。

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

每當我們在[`[Category]`](#CategoryAttribute)定義內找到[`[Static]`](#StaticAttribute)成員時，就會發出警告（BI1117）。 如果您真的想要在[`[Category]`](#CategoryAttribute)定義內擁有[`[Static]`](#StaticAttribute)成員，您可以使用 `[Category (allowStaticMembers: true)]` 或藉由將您的成員或[`[Category]`](#CategoryAttribute)介面定義與[`[Internal]`](#InternalAttribute)裝飾，以讓警告成為靜音。

<a name="StaticAttribute_Class" />

### <a name="staticattribute"></a>StaticAttribute

當這個屬性套用至類別時，它只會產生靜態類別，而不是衍生自 `NSObject`，因此[`[BaseType]`](#BaseTypeAttribute)屬性會被忽略。 靜態類別是用來裝載您想要公開的 C 公用變數。

例如:

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

會產生具有C#下列 API 的類別：

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>通訊協定/模型定義

模型通常是由通訊協定執行所使用。
它們的差異在於，執行時間只會向目標-C 註冊實際已覆寫的方法。
否則，將不會註冊方法。

這通常表示當您將已使用 `ModelAttribute`旗標的類別子類別化時，您不應該呼叫基底方法。   呼叫該方法將會擲回例外狀況，您應該針對您所覆寫的任何方法，在子類別上執行整個行為。

<a name="AbstractAttribute" />

### <a name="abstractattribute"></a>AbstractAttribute

根據預設，屬於通訊協定一部分的成員不是強制性的。 這可讓使用者建立 `Model` 物件的子類別，方法是只從中C#的類別衍生，並只覆寫他們關心的方法。 有時，目標-C 合約會要求使用者提供此方法的實作為（以目標-C 中的 `@required` 指示詞標示）。 在這些情況下，您應該使用 `[Abstract]` 屬性來標示這些方法。

`[Abstract]` 屬性可以套用至方法或屬性，並使產生器將產生的成員標示為抽象，而類別則是抽象類別。

以下是從 Xamarin 取得：

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

指定模型方法傳回的預設值（如果使用者未在模型物件中提供此特定方法的方法）

語法：

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

例如，在 `Camera` 類別的下列虛數委派類別中，我們提供的 `ShouldUploadToServer` 會公開為 `Camera` 類別上的屬性。 如果 `Camera` 類別的使用者未將值明確設定為可以回應 true 或 false 的 lambda，則在此情況下，預設值會傳回 false，也就是我們在 `DefaultValue` 屬性中指定的值。:

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("camera:shouldPromptForAction:"), DefaultValue (false)]
    bool ShouldUploadToServer (Camera camera, CameraAction action);
}
```

如果使用者在虛數類別中設定處理常式，則會忽略這個值：

```csharp
var camera = new Camera ();
camera.ShouldUploadToServer = (camera, action) => return SomeDecision ();
```

另請參閱： [`[NoDefaultValue]`](#NoDefaultValueAttribute)、 [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)。

<a name="DefaultValueFromArgumentAttribute" />

### <a name="defaultvaluefromargumentattribute"></a>DefaultValueFromArgumentAttribute

語法：

```csharp
public class DefaultValueFromArgumentAttribute : Attribute {
    public DefaultValueFromArgumentAttribute (string argument);
    public string Argument { get; }
}
```

當在傳回模型類別值的方法上提供此屬性時，會指示產生器在使用者未提供自己的方法或 lambda 時，傳回指定之參數的值。

範例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

在上述情況下，如果 `NSAnimation` 類別的使用者選擇使用任何C#事件/屬性，但未將`NSAnimation.ComputeAnimationCurve`設定為方法或 lambda，則傳回值會是在進度參數中傳遞的值。

另請參閱： [`[NoDefaultValue]`](#NoDefaultValueAttribute)、 [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

有時候，不要將事件或委派屬性從模型類別公開至主機類別，因此加入此屬性將會指示產生器避免產生任何以它裝飾的方法。

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

這個屬性會在傳回值的模型方法中使用，以設定要使用之委派簽章的名稱。

範例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, float progress);
}
```

使用上述定義時，產生器會產生下列公用宣告：

```csharp
public delegate float NSAnimationProgress (MonoMac.AppKit.NSAnimation animation, float progress);
```

### <a name="delegateapinameattribute"></a>DelegateApiNameAttribute

這個屬性是用來允許產生器變更主機類別中所產生的屬性名稱。 有時候，當 FooDelegate 類別方法的名稱對委派類別很有意義，但在主機類別中看起來像是屬性時，它會很有用。

當您有兩個以上的多載方法時，這也非常有用（而且需要），但您想要以更好的名稱在主機類別中公開它們。

範例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateApiName ("ComputeAnimationCurve"), DelegateName ("Func<NSAnimation, float, float>"), DefaultValueFromArgument ("progress")]
    float GetValueForProgress (NSAnimation animation, float progress);
}
```

使用上述定義時，產生器會在 host 類別中產生下列公用宣告：

```csharp
public Func<NSAnimation, float, float> ComputeAnimationCurve { get; set; }
```

<a name="EventArgsAttribute" />

### <a name="eventargsattribute"></a>EventArgsAttribute

對於接受一個以上參數的事件（在目標-C 中，慣例是委派類別中的第一個參數是傳送者物件的實例），您必須提供您想要的名稱，讓產生的 EventArgs 類別成為。 這是使用 `Model` 類別中方法宣告的 `[EventArgs]` 屬性來完成。

例如:

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述宣告會產生衍生自 EventArgs 的 `UIImagePickerImagePickedEventArgs` 類別，並將這兩個參數、`UIImage` 和 `NSDictionary`封裝在一起。 產生器會產生下列結果：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然後，它會在 `UIImagePickerController` 類別中公開下列內容：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

### <a name="eventnameattribute"></a>EventNameAttribute

這個屬性是用來允許產生器變更在類別中產生之事件或屬性的名稱。 有時，當模型類別的名稱對模型類別很有意義，但在原始類別中看似奇怪的事件或屬性時，它會很有用。

例如，`UIWebView` 會從 `UIWebViewDelegate`使用下列位：

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

上述會公開 `LoadingFinished` 做為 `UIWebViewDelegate`中的方法，但 `LoadFinished` 做為要在 `UIWebView`中連結的事件：

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute" />

### <a name="modelattribute"></a>ModelAttribute

當您將 `[Model]` 屬性套用至合約 API 中的類型定義時，執行時間會產生特殊程式碼，只有在使用者已覆寫類別中的方法時，才會對類別中的方法進行調用。 這個屬性通常會套用至包裝目標-C 委派類別的所有 Api。

<a name="NoDefaultValueAttribute" />

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

指定模型上的方法不提供預設的傳回值。

這適用于目標 C 執行時間，方法是回應 `false` 至目標-C 執行時間要求，以判斷指定的選取器是否在這個類別中執行。

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

另請參閱： [`[DefaultValue]`](#DefaultValueAttribute)、 [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute" />

## <a name="protocols"></a>通訊協定

目標-C 通訊協定概念實際上並不存在於C#中。 通訊協定與C#介面類別似，但不同之處在于通訊協定中宣告的所有方法和屬性都必須由採用它的類別來執行。 相反地，有些方法和屬性是選擇性的。

有些通訊協定通常做為模型類別使用，而這些是應該使用[`[Model]`](#ModelAttribute)屬性來系結的。

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

從 Xamarin. iOS 7.0 開始已納入全新且改良的通訊協定系結功能。  包含 `[Protocol]` 屬性的任何定義，實際上都會產生三個支援的類別，可大幅改善您取用通訊協定的方式：

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

**類別執行**會提供完整的抽象類別，您可以覆寫個別的方法，並取得完整的型別安全。 但由於C#不支援多重繼承，因此在某些情況下，您可能需要不同的基類，但仍想要執行介面。

這是產生之**介面定義**的來源。  它是一種介面，具有來自通訊協定的所有必要方法。  這可讓想要執行通訊協定的開發人員只執行介面。  執行時間會自動將類型註冊為採用通訊協定。

請注意，介面只會列出必要的方法，並會公開選擇性的方法。   這表示採用通訊協定的類別會針對所需的方法取得完整的類型檢查，但必須利用選擇性通訊協定方法的弱式輸入（手動使用匯出屬性和比對簽章）。

為了方便取用使用通訊協定的 API，系結工具也會產生擴充方法類別，以公開所有選用的方法。   這表示只要您使用 API，您就能夠將通訊協定視為具有所有方法。

如果您想要在 API 中使用通訊協定定義，就必須在 API 定義中撰寫基本架構空白介面。   如果您想要在 API 中使用 MyProtocol，您必須執行下列動作：

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

這是必要的，因為在系結時，`IMyProtocol` 不存在，這就是為什麼您需要提供空的介面。

### <a name="adopting-protocol-generated-interfaces"></a>採用通訊協定產生的介面

每當您執行為通訊協定產生的其中一個介面時，如下所示：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

介面方法的執行會自動以適當的名稱匯出，因此它相當於：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

如果介面是隱含或明確地實作為，就不會有任何影響。

### <a name="protocol-inlining"></a>通訊協定內嵌

當您系結已宣告為採用通訊協定的現有目標-C 類型時，您會想要直接內嵌通訊協定。 若要這麼做，只需將您的通訊協定宣告為沒有任何[`[BaseType]`](#BaseTypeAttribute)屬性的介面，並在介面的基底介面清單中列出通訊協定。

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

本節中的屬性會套用至類型的個別成員：屬性和方法宣告。

### <a name="alignattribute"></a>AlignAttribute

用來指定屬性傳回類型的對齊值。 某些屬性會取得必須在特定界限對齊之位址的指標（在 Xamarin 中，這種情況會發生，例如，某些 `GLKBaseEffect` 屬性必須對齊16位元組）。 您可以使用這個屬性來裝飾 getter，並使用對齊值。 這通常與目標-C Api 整合時，與 `OpenTK.Vector4` 和 `OpenTK.Matrix4` 類型搭配使用。

範例：

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```

### <a name="appearanceattribute"></a>AppearanceAttribute

`[Appearance]` 屬性僅限於 iOS 5，其中引進了外觀管理員。

`[Appearance]` 屬性可以套用至參與 `UIAppearance` 架構的任何方法或屬性。 當這個屬性套用至類別中的方法或屬性時，它會指示系結產生器建立強型別外觀類別，用來為這個類別的所有實例或符合特定準則的實例進行樣式。

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

上述會在 UIToolbar 中產生下列程式碼：

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

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute （Xamarin. iOS 5.4）

使用方法和屬性上的 `[AutoReleaseAttribute]`，將方法調用包裝在 `NSAutoReleasePool`中的方法。

在 [目標-C] 中，有一些方法會傳回已新增至預設 `NSAutoReleasePool`的值。 根據預設，這些會移至您的執行緒 `NSAutoReleasePool`，但由於當 managed 物件存在時，您可能不想要在 `NSAutoReleasePool` 中保留物件的參考，只有在您的執行緒傳回之前，才會耗盡該參考控制到下一個執行緒，或回到主要迴圈。

此屬性適用于會傳回已加入預設 `NSAutoReleasePool`之物件的繁重屬性（例如 `UIImage.FromFile`）。 如果沒有這個屬性，只要您的執行緒未將控制權傳回給主迴圈，就會保留影像。 如果您的往來文章為某種背景下載程式，且一律為運作中並等候工作，則不會釋放映射。

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]` 可用來強制建立 managed 類型，即使傳回的非受控物件與系結定義中描述的類型不符也一樣。

當標頭中所述的類型不符合原生方法的傳回型別時，這會很有用，例如從 `NSURLSession`取得下列目標 C 定義：

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

它會清楚指出它會傳回 `NSURLSessionDownloadTask` 實例，但**卻會傳回**`NSURLSessionTask`，這是一個超級類別，因此無法轉換成 `NSURLSessionDownloadTask`。 因為我們在型別安全的內容中，所以會發生 `InvalidCastException`。

為了符合標頭描述並避免 `InvalidCastException`，會使用 `[ForcedTypeAttribute]`。

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

`[ForcedTypeAttribute]` 也會接受名為 `Owns` 的布林值，預設會 `false` `[ForcedType (owns: true)]`。 擁有參數是用來遵循**Core Foundation**物件的[擁有權原則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html)。

`[ForcedTypeAttribute]` 只有在參數、屬性和傳回值上才有效。

<a name="BindAsAttribute" />

### <a name="bindasattribute"></a>BindAsAttribute

`[BindAsAttribute]` 允許將 `NSNumber`、`NSValue` 和 `NSString`（列舉）系結至更C#精確的類型。 屬性可以透過原生 API，用來建立更好、更精確的 .NET API。

您可以使用 `BindAs`來裝飾方法（在傳回值上）、參數和屬性。 唯一的限制是您的成員**不得**在 `[Protocol]` 或[`[Model]`](#ModelAttribute)介面內。

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

在內部，我們將 `bool?` <-> `NSNumber` 和 `CGRect` <-> `NSValue` 轉換。

目前支援的封裝類型如下：

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

下列C#資料類型支援封裝從/到`NSValue`：

* CGAffineTransform
* NSRange
* CGVector
* SCNMatrix4
* CLLocationCoordinate2D
* SCNVector3
* SCNVector4
* CGPoint/PointF
* Rectanglef/RectangleF
* CGSize/SizeF
* UIEdgeInsets
* UIOffset
* MKCoordinateSpan
* CMTimeRange
* CMTime
* CMTimeMapping
* CATransform3D

#### <a name="nsnumber"></a>NSNumber

下列C#資料類型支援封裝從/到`NSNumber`：

* bool
* byte
* 雙線
* 浮動
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

[`[BindAs]`](#BindAsAttribute)在 conjuntion 中使用[NSString 常數所支援](#enum-attributes)的列舉，讓您可以建立更好的 .net API，例如：

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

只有當所提供的列舉[`[BindAs]`](#BindAsAttribute)類型是[由 NSString 常數支援](#enum-attributes)時，我們才會處理 `enum` <-> `NSString` 轉換。

#### <a name="arrays"></a>陣列

[`[BindAs]`](#BindAsAttribute)也支援任何支援類型的陣列，您可以使用下列 API 定義做為範例：

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

`rects` 參數會封裝成 `NSArray`，其中包含每個 `CGRect` 的 `NSValue`，而在傳回時，您會取得一個 `CAScroll?` 陣列，其中已使用包含 `NSArray` 的傳回 `NSStrings`值建立。

<a name="BindAttribute" />

### <a name="bindattribute"></a>BindAttribute

`[Bind]` 屬性在套用至方法或屬性宣告時有兩個使用一個，另一個則適用于屬性中的個別 getter 或 setter。

當用於方法或屬性時，`[Bind]` 屬性的效果會產生叫用指定之選取器的方法。 但產生的產生方法不會使用[`[Export]`](#ExportAttribute)屬性來裝飾，這表示它不能參與方法覆寫。 這通常與用來執行目標-C 擴充方法的 `[Target]` 屬性搭配使用。

例如:

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

在 getter 或 setter 中使用時，當產生屬性的 getter 和 setter 目標-C 選取器名稱時，會使用 `[Bind]` 屬性來改變程式碼產生器所推斷的預設值。 根據預設，當您將名稱為的屬性標示為 `fooBar`時，產生器會針對 getter `setFooBar:` 和 setter 的 `fooBar` 匯出。 在少數情況下，目標-C 並不會遵循此慣例，通常會將 getter 名稱變更為 `isFooBar`。
您會使用這個屬性來通知產生器。

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

僅適用于 Xamarin. iOS 6.3 和更新版本。

這個屬性可以套用至接受完成處理常式做為最後一個引數的方法。

您可以在最後一個引數為回呼的方法上使用 `[Async]` 屬性。  當您將此套用至方法時，系結產生器將會產生該方法的版本，並 `Async`尾碼。  如果回呼不接受任何參數，則傳回值會是 `Task`，如果回呼接受參數，則結果會是 `Task<T>`。

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

下列會產生這個非同步方法：

```csharp
Task LoadFileAsync (string file);
```

如果回呼接受多個參數，您應該設定 `ResultType` 或 `ResultTypeName`，以指定將保留所有屬性之產生類型的所需名稱。

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

下列會產生這個非同步方法，其中 `FileLoading` 包含可存取 `files` 和 `byteCount`的屬性：

```csharp
Task<FileLoading> LoadFile (string file);
```

如果回呼的最後一個參數是 `NSError`，則產生的 `Async` 方法會檢查值是否不是 null，如果是這種情況，則產生的非同步方法將會設定工作例外狀況。

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

上述會產生下列非同步方法：

```csharp
Task<string> UploadAsync (string file);
```

發生錯誤時，所產生的工作會將例外狀況設定為包裝所產生之 `NSError`的 `NSErrorException`。

#### <a name="asyncattributeresulttype"></a>AsyncAttribute. ResultType

使用此屬性來指定傳回的 `Task` 物件的值。   這個參數會採用現有的型別，因此必須在其中一個核心 api 定義中定義它。

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

使用此屬性來指定傳回的 `Task` 物件的值。   這個參數會採用所需類型名稱的名稱，產生器會產生一系列的屬性，也就是回呼所採用的每個參數各一個。

#### <a name="asyncattributemethodname"></a>AsyncAttribute。方法名稱

使用此屬性可自訂所產生之非同步方法的名稱。   預設值是使用方法的名稱並附加 "Async" 文字，您可以使用這個來變更這個預設值。

<a name="DesignatedInitializerAttribute" />

### <a name="designatedinitializerattribute"></a>DesignatedInitializerAttribute

當此屬性套用至函式時，它會在最終平臺元件中產生相同的 `[DesignatedInitializer]`。 這是為了協助 IDE 指出子類別中應該使用哪個函式。

這應該對應到 `__attribute__((objc_designated_initializer))`的目標-C/clang 使用。

<a name="DisableZeroCopyAttribute" />

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

這個屬性會套用至字串參數或字串屬性，並指示程式碼產生器不要針對此參數使用零複製字串封送處理，而改為從C#字串建立新的 NSString 實例。
只有當您使用 `--zero-copy` 命令列選項或 `ZeroCopyStringsAttribute`設定元件層級屬性，指示產生器使用零複製字串封送處理時，才需要此屬性。

當以目標-C 宣告屬性為 `retain` 或 `assign` 屬性，而不是 `copy` 屬性時，這是必要的。 這些通常是在協力廠商程式庫中發生，這些是由開發人員錯誤地「優化」的。 一般來說，`retain` 或 `assign` `NSString` 屬性不正確，因為 `NSString` 的 `NSMutableString` 或使用者衍生的類別可能會改變字串的內容，而不會知道程式庫程式碼，因此會稍微中斷應用程式。 這種情況通常是因為過早的優化所造成。

以下顯示目標-C 中的兩個這類屬性：

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```

<a name="DisposeAttribute" />

### <a name="disposeattribute"></a>DisposeAttribute

當您將 `[DisposeAttribute]` 套用至類別時，您會提供程式碼片段，以加入至類別的 `Dispose()` 方法執行。

由於 `Dispose` 方法是由 `bmac-native` 和 `btouch-native` 工具自動產生，因此您必須使用 `[Dispose]` 屬性，在產生的 `Dispose` 方法執行中插入一些程式碼。

例如:

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute" />

### <a name="exportattribute"></a>ExportAttribute

`[Export]` 屬性是用來將方法或屬性標示為要公開至目標-C 執行時間。 此屬性會在系結工具與實際的 Xamarin 和 Xamarin 執行時間之間共用。 針對方法，參數會逐字傳遞至產生的程式碼，針對屬性，會根據基底宣告產生 getter 和 setter 匯出（如需如何改變系結工具行為的詳細資訊，請參閱[`[BindAttribute]`](#BindAttribute)上的一節）。

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

[選取器](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html)代表所系結的基礎目標-C 方法或屬性的名稱。

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute" />

### <a name="fieldattribute"></a>FieldAttribute

這個屬性是用來將 C 全域變數公開為視需要載入並公開至C#程式碼的欄位。 通常需要這種方法來取得定義于 C 或目標-C 中的常數值，而且可能是某些 Api 中使用的 token，或其值不透明，且必須由使用者程式碼使用。

語法：

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName` 是要連結的 C 符號。 根據預設，這會從已定義類型之命名空間中推斷名稱的程式庫載入。 如果這不是用來查閱符號的程式庫，您應該傳遞 `libraryName` 參數。 如果您要連結靜態程式庫，請使用 `__Internal` 做為 `libraryName` 參數。

產生的屬性一律為靜態。

以 Field 屬性標示的屬性可以是下列類型：

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* 列舉

[NSString 常數](#enum-attributes)所支援的列舉不支援 setter，但如有需要，可以手動系結。

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

`[Internal]` 屬性可以套用至方法或屬性，而且它的效果是使用 `internal` C#關鍵字來標示產生的程式碼，使程式碼只能供產生的元件中的程式碼存取。 這通常是用來隱藏太低層級的 Api，或提供您想要針對產生器不支援的 Api 或應用程式開發介面，而且需要一些程式碼撰寫的弱式公用 API。

當您設計系結時，通常會使用這個屬性來隱藏方法或屬性，並為方法或屬性提供不同的名稱，然後在C#互補的支援檔案上，加入強型別包裝函式，以公開基礎功能。

例如:

```csharp
[Internal]
[Export ("setValue:forKey:")]
void _SetValueForKey (NSObject value, NSObject key);

[Internal]
[Export ("getValueForKey:")]
NSObject _GetValueForKey (NSObject key);
```

然後，在您的支援檔案中，您可能會有如下的程式碼：

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

這個屬性會將屬性的支援欄位標記為要以 .NET `[ThreadStatic]` 屬性標注。 如果欄位是執行緒靜態變數，這會很有用。

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions （Xamarin. iOS 6.0.6）

這個屬性會讓方法支援原生（目標-C）例外狀況。
叫用並不會直接呼叫 `objc_msgSend`，而是會透過自訂 trampoline 來攔截 ObjectiveC 例外狀況，並將其封送處理至 managed 例外狀況。

目前僅支援少數 `objc_msgSend` 簽章（當使用系結之應用程式的原生連結失敗並遺失 monotouch_ *_objc_msgSend*符號時，您會發現簽章不受支援），但可在要求中新增更多。

### <a name="newattribute"></a>NewAttribute

這個屬性會套用至方法和屬性，讓產生器在宣告前面產生 `new` 關鍵字。

當相同的方法或屬性名稱是在基類中已存在的子類別中引進時，用來避免編譯器警告。

<a name="NotificationAttribute" />

### <a name="notificationattribute"></a>NotificationAttribute

您可以將此屬性套用至欄位，讓產生器產生強型別 helper 通知類別。

此屬性可用於不含任何承載之通知的引數，或者您可以指定參考 API 定義中另一個介面的 `System.Type`，其名稱通常會以 "EventArgs" 結尾。 產生器會將介面轉換成子類別 `EventArgs` 的類別，而且會包含其中列出的所有屬性。 [`[Export]`](#ExportAttribute)屬性應該用於 `EventArgs` 類別中，以列出用來查閱目標-C 字典以提取值的索引鍵名稱。

例如:

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

上述程式碼會使用下列方法來產生一個嵌套類別 `MyClass.Notifications`：

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

然後，您程式碼的使用者就可以使用如下的程式碼，輕鬆地訂閱張貼至[NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter)的通知：

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

或設定要觀察的特定物件。 如果您將 `null` 傳遞給 `objectToObserve` 此方法的行為就如同它的其他對等。

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

來自 `ObserveDidStart` 的傳回值可以用來輕鬆地停止接收通知，如下所示：

```csharp
token.Dispose ();
```

或者，您可以呼叫[NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject))並傳遞權杖。 如果您的通知包含參數，您應該指定 helper `EventArgs` 介面，如下所示：

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

上述程式會產生 `MyScreenChangedEventArgs` 類別，其中包含 `ScreenX` 和 `ScreenY` 屬性，會分別使用索引鍵 `ScreenXKey` 和 `ScreenYKey` 提取[NSNotification](xref:Foundation.NSNotification.UserInfo)的資料，並套用適當的轉換。 如果在 `UserInfo`中設定索引鍵，則會使用 `[ProbePresence]` 屬性來進行探查，而不是嘗試將值解壓縮。 這適用于有索引鍵為值的情況（通常用於布林值）。

這可讓您撰寫如下的程式碼：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

在某些情況下，沒有任何常數與字典上傳遞的值相關聯。  Apple 有時會使用公用符號常數，有時會使用字串常數。  根據預設，在您提供的 `EventArgs` 類別中， [`[Export]`](#ExportAttribute)屬性會使用指定的名稱做為要在執行時間查閱的公用符號。  如果不是這種情況，則應該以字串常數的形式進行查閱，然後將 `ArgumentSemantic.Assign` 值傳遞至 Export 屬性。

**Xamarin 的新功能。 iOS 8。4**

有時候，通知將會開始使用，而不會有任何引數，因此可接受不含引數的[`[Notification]`](#NotificationAttribute) 。  但有時候會引進通知的參數。  若要支援此案例，屬性可以套用一次以上。

如果您正在開發系結，而您想要避免中斷現有的使用者程式碼，您可以從下列來源開啟現有的通知：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

為列出通知屬性兩次的版本，如下所示：

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

當此套用至屬性時，它會將屬性標示為允許指派 `null` 值。 這只對參考型別有效。

當這個套用到方法簽章中的參數時，它會指出指定的參數可以是 null，而且不應該執行任何檢查來傳遞 `null` 值。

如果參考型別沒有這個屬性，系結工具會先針對所指派的值產生檢查，再將它傳遞至目標-C，並產生檢查，如果指派的值是 `null`，將會擲回 `ArgumentNullException`。

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

使用這個屬性來指示系結產生器，此特定方法的系結應該以 `override` 關鍵字標記。

### <a name="presnippetattribute"></a>PreSnippetAttribute

您可以使用這個屬性，插入一些要在輸入參數經過驗證之後，但在程式碼呼叫目標-C 之前插入的程式碼。

範例：

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

您可以使用這個屬性插入一些要插入的程式碼，然後在產生的方法中驗證任何參數。

範例：

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

指示系結產生器從這個類別叫用指定的屬性，以從中提取值。

這個屬性通常是用來重新整理指向參考物件的快取，而這些物件會將物件圖形保留在參考中。 通常會顯示在具有 [新增/移除] 等作業的程式碼中。 會使用這個方法，如此一來，在加入或移除內部快取之後，才會更新內部快取，以確保我們會將受控參考保存在實際使用中的物件。 這是可能的，因為系結工具會為指定系結中的所有參考物件產生支援欄位。

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

在此情況下，會在新增或移除 `NSOperation` 物件的相依性之後叫用 `Dependencies` 屬性，以確保我們有代表實際載入物件的圖形，以防止記憶體流失和記憶體損毀。

### <a name="postsnippetattribute"></a>PostSnippetAttribute

您可以使用這個屬性，插入要C#在程式碼叫用基礎目標-C 方法之後插入的一些原始程式碼

範例：

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

這個屬性會套用至傳回值，以將其標示為 proxy 物件。 某些目標-C Api 會傳回無法與使用者系結區別的 proxy 物件。 這個屬性的效果是將物件標示為 `DirectBinding` 物件。 針對 Xamarin 中的案例，您可以看到[關於此錯誤的討論](https://bugzilla.novell.com/show_bug.cgi?id=670844)。

### <a name="retainlistattribute"></a>RetainListAttribute

指示產生器保留參數的 managed 參考，或移除參數的內部參考。 這是用來保留參考的物件。

語法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果 `doAdd` 的值為 true，則會將參數新增至 `__mt_{0}_var List<NSObject>;`。 其中 `{0}` 會取代為給定的 `listName`。 您必須在 API 的互補部分類別中宣告此支援欄位。

如需範例，請參閱[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute （Xamarin. iOS 6.0）

這可以套用至傳回型別，以表示產生器應該先在物件上呼叫 `Release`，然後再將它傳回。 只有當方法提供您保留的物件（而不是最常見的案例）時，才需要這麼做

範例：

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

此外，此屬性會傳播至產生的程式碼，因此，在從這類函式傳回目標時，您必須保留物件。

### <a name="sealedattribute"></a>SealedAttribute

指示產生器將產生的方法標示為 sealed。 如果未指定此屬性，則預設值為根據其他屬性的使用方式，產生虛擬方法（虛擬方法、抽象方法或覆寫）。

<a name="StaticAttribute" />

### <a name="staticattribute"></a>StaticAttribute

當 `[Static]` 屬性套用至方法或屬性時，這會產生靜態方法或屬性。 如果未指定這個屬性，則產生器會產生實例方法或屬性。

### <a name="transientattribute"></a>TransientAttribute

使用此屬性來標示其值為暫時性的屬性，也就是由 iOS 暫時建立但不是長期的物件。 當此屬性套用至屬性時，產生器不會建立此屬性的支援欄位，這表示 managed 類別不會保留物件的參考。

<a name="WrapAttribute" />

### <a name="wrapattribute"></a>WrapAttribute

在設計 Xamarin/Xamarin. Mac 系結時，`[Wrap]` 屬性是用來包裝弱型別物件與強型別物件。 這主要是針對目標-C 委派物件，通常會宣告為 `id` 或 `NSObject`類型。 Xamarin 和 Xamarin 所使用的慣例是將這些委派或資料來源公開為類型 `NSObject`，並使用「弱式」慣例和所公開的名稱來命名。 從目標-C `id delegate` 屬性會公開為 API 合約檔案中的 `NSObject WeakDelegate { get; set; }` 屬性。

但是，指派給這個委派的值通常是強型別，因此，我們會呈現強型別並套用 `[Wrap]` 屬性，這表示使用者可以選擇使用弱式型別，如果它們需要一些細微的控制，或需要對低層級的訣竅，或者可以針對大部分的工作使用強型別屬性。

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

這就是使用者使用弱式型別版本的委派的方式：

```csharp
// The weak case, user has to roll his own
class SomeObject : NSObject {
    [Export ("doDemo")]
    void CallbackForDoDemo () {}

}

var demo = new Demo ();
demo.WeakDelegate = new SomeObject ();
```

這就是使用者使用強型別版本的方式，請注意，使用者會利用C#的型別系統，並使用 override 關鍵字來宣告他的意圖，而且他不需要手動裝飾方法，`[Export]`，因為我們已在使用者的系結中執行此作業：

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

`[Wrap]` 屬性的另一個用法是支援強型別版本的方法。  例如:

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

當 `[Wrap]` 屬性套用至以 `[Category]` 屬性裝飾之類型內的方法時，您必須將 `This` 包含為第一個引數，因為正在產生擴充方法。 例如:

```csharp
[Wrap ("Write (This, image, options?.Dictionary, out error)")]
bool Write (CIImage image, CIImageRepresentationOptions options, out NSError error);
```

預設不會 `virtual` `[Wrap]` 所產生的成員。如果您需要 `virtual` 成員，您可以將設定為 `true` 選擇性的 `isVirtual` 參數。

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

`[Wrap]` 也可以直接在屬性 getter 和 setter 中使用。
這讓能夠完全控制它們，並視需要調整程式碼。
例如，請考慮下列使用智慧型列舉的 API 定義：

```csharp
// Smart enum.
enum PersonRelationship {
        [Field (null)]
        None,

        [Field ("FMFather", "__Internal")]
        Father,

        [Field ("FMMother", "__Internal")]
        Mother
}
```

介面定義：

```csharp
// Property definition.

[Export ("presenceType")]
NSString _PresenceType { get; set; }

PersonRelationship PresenceType {
    [Wrap ("PersonRelationshipExtensions.GetValue (_PresenceType)")]
    get;
    [Wrap ("_PresenceType = value.GetConstant ()")]
    set;
}
```

## <a name="parameter-attributes"></a>參數屬性

本節說明您可以套用至方法定義中之參數的屬性，以及適用于整個屬性的 `[NullAttribute]`。

<a name="BlockCallback" />

### <a name="blockcallback"></a>BlockCallback

這個屬性會套用至委派宣告中C#的參數類型，以通知系結器中有問題的參數符合目標-C 區塊呼叫慣例，而且應該以這種方式封送處理。

這通常用於在目標-C 中定義如下的回呼：

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另請參閱： [CCallback](#CCallback)。

<a name="CCallback" />

### <a name="ccallback"></a>CCallback

這個屬性會套用至委派宣告中C#的參數類型，以通知系結器中有問題的參數符合 C ABI 函式指標呼叫慣例，因此應該以這種方式封送處理。

這通常用於在目標-C 中定義如下的回呼：

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另請參閱： [BlockCallback](#BlockCallback)。

### <a name="params"></a>化

您可以使用方法定義之最後一個 array 參數上的 `[Params]` 屬性，讓產生器在定義中插入 "params"。   這可讓系結輕鬆地允許選擇性參數。

例如，下列定義：

```csharp
[Export ("loadFiles:")]
void LoadFiles ([Params]NSUrl [] files);
```

允許撰寫下列程式碼：

```csharp
foo.LoadFiles (new NSUrl (url));
foo.LoadFiles (new NSUrl (url1), new NSUrl (url2), new NSUrl (url3));
```

這麼做的優點是，它不需要使用者建立純粹用於傳遞元素的陣列。

<a name="plainstring" />

### <a name="plainstring"></a>PlainString

您可以使用字串參數前面的 `[PlainString]` 屬性，指示系結產生器將字串當做 C 字串傳遞，而不是將參數當做 `NSString`傳遞。

大部分的目標-C Api 都會使用 `NSString` 參數，但少數 Api 會公開用來傳遞字串的 `char *` API，而不是 `NSString` 變化。
在這些情況下，請使用 `[PlainString]`。

例如，下列的目標-C 宣告：

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

應該系結如下：

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

指示產生器保留指定參數的參考。 產生器將提供此欄位的備份存放區，您也可以指定名稱（`WrapName`）來儲存值。 這對於保存當做參數傳遞至目標-C 的 managed 物件參考很有用，而且當您知道目標-C 只會保留此物件的複本時。 例如，`SetDisplay (SomeObject)` 之類的 API 會使用這個屬性，因為 SetDisplay 可能一次只能顯示一個物件。 如果您需要追蹤一個以上的物件（例如，針對類似堆疊的 API），您會使用 `[RetainList]` 屬性。

語法：

```csharp
public class RetainAttribute {
    public RetainAttribute ();
    public RetainAttribute (string wrapName);
    public string WrapName { get; }
}
```

### <a name="retainlistattribute"></a>RetainListAttribute

指示產生器保留參數的 managed 參考，或移除參數的內部參考。 這是用來保留參考的物件。

語法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果 `doAdd` 的值為 true，則會將參數新增至 `__mt_{0}_var List<NSObject>`。 其中 `{0}` 會取代為給定的 `listName`。 您必須在 API 的互補部分類別中宣告此支援欄位。

如需範例，請參閱[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="transientattribute"></a>TransientAttribute

這個屬性會套用至參數，而且只有在從目標-C 轉換到C#時才會使用。  在這些轉換期間，各種目標-C `NSObject` 參數會包裝成物件的 managed 標記法。

執行時間會參考原生物件，並保留參考，直到物件的最後一個 managed 參考消失，而且 GC 有機會執行。

在少數情況下， C#執行時間不會保留原生物件的參考是很重要的。  當基礎機器碼已將特殊行為附加至參數的生命週期時，有時會發生這種情況。  例如：參數的析構函式將會執行某個清除動作，或處置一些寶貴的資源。

這個屬性會通知執行時間，您想要在從覆寫的方法回到目標-C 時，盡可能處置物件。

此規則很簡單：如果執行時間必須從原生物件建立新的 managed 標記法，則在函式的結尾，將會卸載原生物件的保留計數，而且會清除受管理物件的 Handle 屬性。   這表示，如果您保留受管理物件的參考，該參考將會變得毫無用處（在其上叫用方法會擲回例外狀況）。

如果未建立傳遞的物件，或如果已經有未完成的 managed 物件表示，則不會進行強制處置。 

## <a name="property-attributes"></a>屬性屬性

<a name="NotImplementedAttribute" />

### <a name="notimplementedattribute"></a>NotImplementedAttribute

這個屬性是用來支援目標-C 的用法，其中具有 getter 的屬性會引進基類，而可變的子類別會引進 setter。

因為C#不支援此模型，所以基類必須同時具有 setter 和 getter，而且子類別可以使用[OverrideAttribute](#OverrideAttribute)。

這個屬性僅用於屬性 setter 中，可用來支援在目標 C 中的可變用法。

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

## <a name="enum-attributes"></a>列舉屬性

將 `NSString` 常數對應至列舉值，是建立更好的 .NET API 的簡單方法。 這樣

* 藉由**只**顯示 API 的正確值，讓程式碼完成更有用。
* 加入型別安全，您不能在不正確的內容中使用另一個 `NSString` 常數;和
* 允許隱藏某些常數，讓程式碼完成顯示較短的 API 清單，而不會遺失功能。

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

在上述系結定義中，產生器會建立 `enum` 本身，而且也會建立 `*Extensions` 靜態類型，其中包含列舉值與 `NSString` 常數之間的兩個方法轉換方法。 這表示即使它們不是 API 的一部分，開發人員仍然可以使用常數。

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

您可以使用這個屬性裝飾**一個**列舉值。 如果列舉值未知，這會變成傳回的常數。

從上述範例中：

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

如果未裝飾列舉值，則會擲回 `NotSupportedException`。

### <a name="errordomainattribute"></a>ErrorDomainAttribute

錯誤碼會系結為列舉值。 通常會有一個錯誤網域，而且不一定容易找到哪一個（如果有的話）。

您可以使用這個屬性，將錯誤網域與列舉本身產生關聯。

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

接著，您可以呼叫擴充方法 `GetDomain` 來取得任何錯誤的網域常數。

### <a name="fieldattribute"></a>FieldAttribute

這與用於類型內之常數的 `[Field]` 屬性相同。 它也可以在列舉內用來對應具有特定常數的值。

如果指定 `null` `NSString` 常數，`null` 值可以用來指定應該傳回的列舉值。

從上述範例中：

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

如果不存在 `null` 值，則會擲回 `ArgumentNullException`。

## <a name="global-attributes"></a>全域屬性

全域屬性可以使用 `[assembly:]` 屬性修飾詞（例如[`[LinkWithAttribute]`](#LinkWithAttribute) ）套用，也可以在任何地方使用，例如[`[Lion]`](#SinceAndLionAttributes)和[`[Since]`](#SinceAndLionAttributes)屬性。

<a name="LinkWithAttribute" />

### <a name="linkwithattribute"></a>LinkWithAttribute

這是元件層級屬性，可讓開發人員指定在重複使用系結程式庫時所需的連結旗標，而不需強制媒體櫃的取用者手動設定傳遞至程式庫的 gcc_flags 和額外 mtouch 引數。

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

這個屬性會在元件層級套用，例如，這就是[CorePlot](https://github.com/mono/monotouch-bindings/tree/master/CorePlot)系結所使用的內容：

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

當您使用 `[LinkWith]` 屬性時，會將指定的 `libraryName` 內嵌到產生的元件中，讓使用者能夠傳送單一 DLL，其中同時包含未受管理的相依性，以及適當地使用程式庫所需的命令列旗標Xamarin. iOS。

也可以不提供 `libraryName`，在此情況下，`LinkWith` 屬性只能用來指定其他連結器旗標：

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>LinkWithAttribute 的構造函式

這些函式可讓您指定要連結的程式庫，並將其內嵌至產生的元件、程式庫支援的支援目標，以及程式庫所需的任何選用程式庫旗標。

請注意，`LinkTarget` 引數是由 Xamarin 推斷而不需要設定。

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

`ForceLoad` 屬性是用來決定是否使用 `-force_load` 連結旗標來連結原生媒體櫃。 目前，這應該一律為 true。

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute Framework

如果要系結的程式庫在任何架構上都有困難的需求（而不是 `Foundation` 和 `UIKit`），您應該將 `Frameworks` 屬性設定為包含所需平臺架構清單（以空格分隔）的字串。 例如，如果您要系結需要 `CoreGraphics` 和 `CoreText`的程式庫，您可以將 `Frameworks` 屬性設定為 [`"CoreGraphics CoreText"`]。

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

如果產生的可執行檔需要使用C++編譯器來編譯，而不是預設的（C 編譯器），請將此屬性設定為 true。 如果您要系結的程式庫已寫入，請C++使用此項。

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute. LibraryName

要組合之非受控程式庫的名稱。 這是副檔名為 ". a" 的檔案，它可以包含多個平臺的目的程式代碼（例如，適用于模擬器的 ARM 和 x86）。

舊版的 Xamarin 會核取 [`LinkTarget`] 屬性來判斷您的程式庫支援的平臺，但現在會自動偵測到，而 [`LinkTarget`] 屬性則會被忽略。

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

`LinkerFlags` 字串提供一個方法，讓作者能夠在將原生程式庫連結至應用程式時，指定所需的任何其他連結器旗標。

例如，如果原生程式庫需要 libxml2 和 zlib，您可以將 `LinkerFlags` 字串設定為 `"-lxml2 -lz"`。

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute. LinkTarget

舊版的 Xamarin 會核取 [`LinkTarget`] 屬性來判斷您的程式庫支援的平臺，但現在會自動偵測到，而 [`LinkTarget`] 屬性則會被忽略。

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

如果您要連結的程式庫需要 GCC 例外狀況處理程式庫（gcc_eh），請將此屬性設定為 true

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute. SmartLink

`SmartLink` 屬性應該設定為 true，讓 Xamarin 能夠判斷是否需要 `ForceLoad`。

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

`WeakFrameworks` 屬性的運作方式與 `Frameworks` 屬性相同，不同之處在于在連結時間，`-weak_framework` 規範會針對每個列出的架構傳遞至 gcc。

`WeakFrameworks` 可以讓程式庫和應用程式對平臺架構進行弱式連結，讓它們可以選擇性地使用它們（如果有的話），但不會對其進行困難的相依性，如果您的程式庫想要在其上新增額外的功能，這會很有用。較新版本的 iOS。 如需弱式連結的詳細資訊，請參閱 Apple 的[弱式連結](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html)檔。

弱式連結的良好候選項目會 `Frameworks`，例如帳戶、`CoreBluetooth`、`CoreImage`、`GLKit`、`NewsstandKit` 和 `Twitter`，因為它們僅適用于 iOS 5。

<a name="SinceAndLionAttributes" />

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute （iOS）和 LionAttribute （macOS）

您可以使用 `[Since]` 屬性，將 Api 標記為在特定時間點引進。 只有在無法使用基礎類別、方法或屬性時，才應該使用屬性來標示可能造成執行時間問題的類型和方法。

語法：

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

它通常不會套用至列舉、條件約束或新結構，因為如果在具有舊版作業系統的裝置上執行，則不會造成執行階段錯誤。

適用于類型的範例：

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

適用于新成員的範例：

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

`[Lion]` 屬性是以相同的方式套用，但適用于使用 Lion 所引進的類型。 使用 `[Lion]` 與 iOS 中使用的更特定版本號碼的原因是，iOS 的修改頻率很高，而主要 OS X 版本很少發生，而且比起版本號碼的代號更容易記住作業系統

### <a name="adviceattribute"></a>AdviceAttribute

使用此屬性可為開發人員提供其他可能更方便使用之 Api 的提示。   例如，如果您提供 API 的強型別版本，您可以在弱型別屬性上使用這個屬性，將開發人員導向至較佳的 API。

此屬性中的資訊會顯示在檔中，而且可加以開發，以提供使用者如何改進的建議。

### <a name="requiressuperattribute"></a>RequiresSuperAttribute

這是 `[Advice]` 屬性的特殊化子類別，可以用來提示開發人員覆寫方法，而這**需要**呼叫基底（覆寫）方法。

這會對應至 `clang` [`__attribute__((objc_requires_super))`](https://clang.llvm.org/docs/AttributeReference.html#objc-requires-super)

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

僅適用于 Xamarin. iOS 5.4 和更新版本。

這個屬性會指示產生器，此特定程式庫（如果套用 `[assembly:]`）或類型的系結應使用快速零複製字串封送處理。 這個屬性相當於將命令列選項 `--zero-copy` 傳遞給產生器。

針對字串使用零複製時，產生器會有效地使用與C#目標 c 字串相同的字串，而不會產生新的`NSString`物件，並避免將資料從C#字串複製到目標-c字串. 使用零份複製字串的唯一缺點是，您必須確定您所包裝的任何字串屬性都標示為 `retain` 或 `copy` 已設定 `[DisableZeroCopy]` 屬性。 這是必要的，因為零複製字串的控制碼是配置在堆疊上，而且在函式傳回時無效。

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

您也可以在元件層級套用屬性，它會套用至元件的所有類型：

```csharp
[assembly:ZeroCopyStrings]
```

## <a name="strongly-typed-dictionaries"></a>強型別字典

我們引進了 Xamarin. iOS 8.0，讓您輕鬆建立可包裝 `NSDictionaries`的強型別類別。

雖然永遠都可以搭配使用[DictionaryContainer](xref:Foundation.DictionaryContainer)資料類型與手動 API，但現在更容易這麼做。  如需詳細資訊，請參閱[呈現強](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types)型別。

<a name="StrongDictionary" />

### <a name="strongdictionary"></a>StrongDictionary

當此屬性套用至介面時，產生器會產生與衍生自[DictionaryContainer](xref:Foundation.DictionaryContainer)的介面同名的類別，並將介面中定義的每個屬性轉換成強型別 getter 和 setter （針對詞典.

這會自動產生可從現有 `NSDictionary` 具現化或已建立新的類別。

這個屬性接受一個參數，也就是類別的名稱，其中包含用來存取字典上元素的索引鍵。   根據預設，介面中具有屬性的每個屬性都會針對尾碼為 "Key" 的名稱，查閱指定類型中的成員。

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

在上述情況下，`MyOption` 類別將會產生 `Name` 的字串屬性，以使用 `MyOptionKeys.NameKey` 做為字典中的索引鍵來抓取字串。   和會使用 `MyOptionKeys.AgeKey` 做為字典中的索引鍵，以取出包含 int 的 `NSNumber`。

如果您想要使用不同的索引鍵，您可以在屬性上使用 export 屬性，例如：

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

#### <a name="strong-dictionary-types"></a>強式字典類型

`StrongDictionary` 定義支援下列資料類型：

|C#介面類別型|`NSDictionary` 儲存體類型|
|---|---|
|`bool`|`Boolean` 儲存在 `NSNumber` 中|
|列舉值|儲存在 `NSNumber` 中的整數|
|`int`|儲存在 `NSNumber` 中的32位整數|
|`uint`|儲存在 `NSNumber` 中的32位不帶正負號的整數|
|`nint`|`NSInteger` 儲存在 `NSNumber` 中|
|`nuint`|`NSUInteger` 儲存在 `NSNumber` 中|
|`long`|儲存在 `NSNumber` 中的64位整數|
|`float`|儲存為 `NSNumber` 的32位整數|
|`double`|儲存為 `NSNumber` 的64位整數|
|`NSObject` 和子類別|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C#`NSObject`的`Array`|`NSArray`|
|C#列舉的`Array`|包含 `NSNumber` 值的 `NSArray`|
