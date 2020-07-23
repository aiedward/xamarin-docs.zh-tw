---
title: 系結類型參考指南
description: '本參考指南說明在建立 c # 系結至目標 C 程式庫時，所需瞭解的各種屬性和概念。'
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 8549e993bf46ffd3b24ad8ec495791eb25b25023
ms.sourcegitcommit: bd49f28105218f04e978e58143bba8cdec9fd4a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86925980"
---
# <a name="binding-types-reference-guide"></a>系結類型參考指南

本檔說明您可以用來標注 API 合約檔案以驅動系結和所產生程式碼的屬性清單

以 c # 撰寫的 xamarin 和 Xamarin API 合約大多是用來定義 c # 的目標 C 程式碼方式的介面定義。 此程式牽涉到混合介面宣告，加上 API 合約可能需要的一些基本類型定義。 如需系結類型的簡介，請參閱我們的附屬指南系結[目標-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)。

## <a name="type-definitions"></a>型別定義

語法：

```csharp
[BaseType (typeof (BTYPE))
interface MyType : [Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

合約定義中具有屬性的每個介面都會宣告 [`[BaseType]`](#BaseTypeAttribute) 所產生之物件的基底類型。 在上述宣告中， `MyType` 會產生類別 c # 型別，以系結至名為的目標-C 型別 `MyType` 。

如果您使用介面繼承語法指定 typename （在上述範例中為）後面的任何類型，則 `Protocol1` `Protocol2` 這些介面的內容將會內嵌，就好像它們已經是的合約的一部分一樣 `MyType` 。
Xamarin iOS 介面類別型採用通訊協定的方式，是將通訊協定中宣告的所有方法和屬性內嵌到類型本身。

以下顯示如何 `UITextField` 在 Xamarin iOS 合約中定義的目標-C 宣告：

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

會以下列格式寫成 c # API 合約：

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

您可以藉由將其他屬性套用至介面，以及設定屬性，來控制程式代碼產生的許多其他層面 [`[BaseType]`](#BaseTypeAttribute) 。

### <a name="generating-events"></a>產生事件

其中一個 Xamarin. iOS 和 Xamarin. Mac API 設計的功能是將目標 C 委派類別對應為 c # 事件和回呼。 使用者可以選擇以每個實例為基礎，不論他們是否想要採用目標 C 程式設計模式，方法是指派給屬性（例如，可執行 `Delegate` 目標 c 執行時間所呼叫之各種方法的類別實例），或選擇 c # 樣式的事件和屬性。

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

在上述範例中，您可以看到我們已選擇覆寫兩個方法，一個是滾動事件發生的通知，而第二個是應傳回布林值的回呼，指示 `scrollView` 是否應將其移至頂端。

C # 模型可讓您程式庫的使用者使用 c # 事件語法或屬性語法來接聽通知，以連結預期會傳回值的回呼。

這是使用 lambda 時，相同功能的 c # 程式碼看起來會像這樣：

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

由於事件不會傳回值（它們具有 void 傳回型別），因此您可以連接多個複本。 不是事件，而是具有具有此簽章之 `ShouldScrollToTop` 類型的屬性 `UIScrollViewCondition` ：

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

它會傳回 `bool` 值，在此案例中，lambda 語法可讓我們直接從函式傳回值 `MakeDecision` 。

系結產生器支援產生事件和屬性，其會將類別 `UIScrollView` 與其連結 `UIScrollViewDelegate` （也就是呼叫模型類別），這是藉由 [`[BaseType]`](#BaseTypeAttribute) 使用和參數標注您的定義 `Events` （如下 `Delegates` 所述）來完成。 除了使用這些參數來標注，還 [`[BaseType]`](#BaseTypeAttribute) 必須通知產生器更多元件。

對於採用多個參數的事件（在目標-C 中，慣例是委派類別中的第一個參數是傳送者物件的實例），您必須提供所產生之類別所需的名稱 `EventArgs` 。 這是在 [`[EventArgs]`](#EventArgsAttribute) 模型類別中方法宣告的屬性上完成。 例如：

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述宣告會產生 `UIImagePickerImagePickedEventArgs` 衍生自的類別， `EventArgs` 並將這兩個參數、和封裝在一起 `UIImage` `NSDictionary` 。 產生器會產生下列結果：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然後，它會在類別中公開下列 `UIImagePickerController` 內容：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

傳回值的模型方法會以不同的方式系結。 這兩者都需要產生之 c # 委派的名稱（方法的簽章），而且也會傳回預設值，以在使用者未提供本身的情況下傳回。 例如， `ShouldScrollToTop` 定義如下：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

上述 `UIScrollViewCondition` 程式會使用上面顯示的簽章建立委派，如果使用者未提供執行，則傳回值將會是 true。

除了 [`[DefaultValue]`](#DefaultValueAttribute) 屬性之外，您也可以使用屬性來指示產生器傳回 [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute) 呼叫中指定之參數的值，或用來指示產生器沒有 [`[NoDefaultValue]`](#NoDefaultValueAttribute) 預設值的參數。

<a name="BaseTypeAttribute"></a>

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

您可以使用 `Name` 屬性來控制此類型在目標-C 世界中將系結的名稱。 這通常是用來提供 c # 型別與 .NET Framework 設計方針相容的名稱，但會對應到不遵循該慣例的目標-C 中的名稱。

例如，在下列情況下，我們會將目標-C `NSURLConnection` 類型對應至 `NSUrlConnection` ，因為 .NET Framework 設計方針使用 "url"，而不是 "url"：

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

指定的名稱會當做系結中產生之屬性的值使用 `[Register]` 。 如果 `Name` 未指定，則會在 `[Register]` 產生的輸出中使用類型的簡短名稱當做屬性的值。

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType. 事件和 BaseType。委派

這些屬性可用來驅動產生之類別中的 c # 樣式事件產生。 它們可用來連結指定的類別與其目標-C 委派類別。 在許多情況下，類別會使用委派類別來傳送通知和事件。 例如， `BarcodeScanner` 會有附屬 `BardodeScannerDelegate` 類別。 `BarcodeScanner`類別通常會有一個 `Delegate` 屬性，您可以將的實例指派 `BarcodeScannerDelegate` 給，雖然這是可行的，但您可能會想要向使用者公開類似 c # 的樣式事件介面，而在這些情況下，您會使用屬性的 `Events` 和 `Delegates` 屬性 [`[BaseType]`](#BaseTypeAttribute) 。

這些屬性一律會一起設定，而且必須具有相同數目的元素，且保持同步。`Delegates`陣列會針對您想要包裝的每個弱型別委派包含一個字串，而且 `Events` 陣列會針對您想要與之產生關聯的每個型別，各包含一個型別。

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

如果您在建立這個類別的新實例時套用這個屬性，則會保留該物件的實例，直到叫用所參考的方法為止 `KeepRefUntil` 。 當您不想要讓使用者保留物件的參考以使用您的程式碼時，這非常適合用來改善 Api 的可用性。 這個屬性的值是類別中的方法名稱 `Delegate` ，因此，您也必須將它與 `Events` 和屬性搭配使用 `Delegates` 。

下列範例示範如何 `UIActionSheet` 在 Xamarin 中使用此功能：

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

<a name="DesignatedDefaultCtorAttribute"></a>

### <a name="designateddefaultctorattribute"></a>DesignatedDefaultCtorAttribute

當這個屬性套用至介面定義時，它會 `[DesignatedInitializer]` 在預設（產生的）的函式上產生屬性，而此函式會對應至 `init` 選取器。

<a name="DisableDefaultCtorAttribute"></a>

### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

當此屬性套用至介面定義時，它會防止產生器產生預設的函式。

當您需要使用類別中的其中一個其他函式來初始化物件時，請使用這個屬性。

<a name="PrivateDefaultCtorAttribute"></a>

### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

當此屬性套用至介面定義時，它會將預設的函式標示為私用。 這表示您仍然可以從延伸模組檔案內部將此類別的物件具現化，但您的類別的使用者就無法存取它。

<a name="CategoryAttribute"></a>

### <a name="categoryattribute"></a>CategoryAttribute

在類型定義上使用此屬性來系結目標 C 類別，並將其公開為 c # 擴充方法，以鏡像目標-C 公開功能的方式。

類別目錄是一個目標 C 機制，用來擴充類別中可用的方法和屬性集合。   實際上，它們是用來擴充基類的功能（例如 `NSObject` ），當特定架構連結在中時（例如） `UIKit` ，讓其方法可用，但只有在新架構連結時才會使用。   在某些其他情況下，它們是用來依功能來組織類別中的功能。   它們與 c # 擴充方法的精神很相似。

這是類別在目標-C 中的樣子：

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上述範例位於使用方法擴充實例的程式庫上 `UIView` `makeBackgroundRed` 。

若要系結，您可以在 [`[Category]`](#CategoryAttribute) 介面定義上使用屬性。   使用屬性時 [`[Category]`](#CategoryAttribute) ，屬性的意義會 [`[BaseType]`](#BaseTypeAttribute) 從用來指定要擴充的基類，改為要擴充的類型。

以下顯示擴充功能的系結 `UIView` 和轉換成 c # 擴充方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

上述會建立 `MyUIViewExtension` 包含 `MakeBackgroundRed` 擴充方法的類別。   這表示您現在可以 `MakeBackgroundRed` 在任何子類別上呼叫 `UIView` ，提供您在目標-C 上取得的相同功能。

在某些情況下，您會在類別內尋找**靜態**成員，如下列範例所示：

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

這會導致**不正確**的類別 c # 介面定義：

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

這是不正確的，因為若要使用此延伸模組， `BoolMethod` 您需要的實例 `FooObject` ，但您要系結 ObjC**靜態**延伸模組，這是因為 c # 擴充方法的執行方式的事實所產生的副作用。

使用上述定義的唯一方式是下列不好的程式碼：

```csharp
(null as FooObject).BoolMethod (range);
```

若要避免這種情況，建議您將定義內嵌在 `BoolMethod` `FooObject` 介面定義本身中，這可讓您如預期般呼叫此延伸模組 `FooObject.BoolMethod (range)` 。

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

每當我們發現定義內的成員時，就會發出警告（BI1117） [`[Static]`](#StaticAttribute) [`[Category]`](#CategoryAttribute) 。 如果您真的想要在 [`[Static]`](#StaticAttribute) 定義內擁有成員 [`[Category]`](#CategoryAttribute) ，您可以使用 `[Category (allowStaticMembers: true)]` 或藉由將您的成員或介面定義裝飾在一起，讓警告成為靜音 [`[Category]`](#CategoryAttribute) [`[Internal]`](#InternalAttribute) 。

<a name="StaticAttribute_Class"></a>

### <a name="staticattribute"></a>StaticAttribute

當這個屬性套用至類別時，它只會產生靜態類別，而不是衍生自 `NSObject` ，因此會 [`[BaseType]`](#BaseTypeAttribute) 忽略屬性。 靜態類別是用來裝載您想要公開的 C 公用變數。

例如：

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

會產生具有下列 API 的 c # 類別：

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>通訊協定/模型定義

模型通常是由通訊協定執行所使用。
它們的差異在於，執行時間只會向目標-C 註冊實際已覆寫的方法。
否則，將不會註冊方法。

這通常表示當您將已使用標記的類別子類別化時 `ModelAttribute` ，不應該呼叫基底方法。   呼叫該方法將會擲回下列例外狀況： Foundation. You_Should_Not_Call_base_In_This_Method。 您應該針對您覆寫的任何方法，在子類別上執行整個行為。

<a name="AbstractAttribute"></a>

### <a name="abstractattribute"></a>AbstractAttribute

根據預設，屬於通訊協定一部分的成員不是強制性的。 這可讓使用者建立物件的子類別， `Model` 方法是只使用 c # 中的類別衍生，並只覆寫他們關心的方法。 有時，目標-C 合約會要求使用者提供此方法的實作為（ `@required` 在目標-c 中以指示詞標示）。 在這些情況下，您應該使用屬性來標記這些方法 `[Abstract]` 。

`[Abstract]`屬性可以套用至方法或屬性，並使產生器將產生的成員標示為抽象，而類別則是抽象類別。

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

<a name="DefaultValueAttribute"></a>

### <a name="defaultvalueattribute"></a>DefaultValueAttribute

指定模型方法傳回的預設值（如果使用者未在模型物件中提供此特定方法的方法）

語法：

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

例如，在類別的下列虛數委派類別中 `Camera` ，我們提供， `ShouldUploadToServer` 它會公開為類別上的屬性 `Camera` 。 如果類別的使用者 `Camera` 未將值明確設定為可以回應 true 或 false 的 lambda，則在此情況下，預設值會傳回 false，也就是我們在屬性中所指定的值 `DefaultValue` ：

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

另請參閱： [`[NoDefaultValue]`](#NoDefaultValueAttribute) 、 [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute) 。

<a name="DefaultValueFromArgumentAttribute"></a>

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

在上述情況下，如果類別的使用者 `NSAnimation` 選擇使用任何 c # 事件/屬性，但未將設定 `NSAnimation.ComputeAnimationCurve` 為方法或 lambda，則傳回值會是在進度參數中傳遞的值。

另請參閱： [`[NoDefaultValue]`](#NoDefaultValueAttribute) 、[`[DefaultValue]`](#DefaultValueAttribute)

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

<a name="EventArgsAttribute"></a>

### <a name="eventargsattribute"></a>EventArgsAttribute

對於接受一個以上參數的事件（在目標-C 中，慣例是委派類別中的第一個參數是傳送者物件的實例），您必須提供您想要的名稱，讓產生的 EventArgs 類別成為。 這是使用 `[EventArgs]` 類別中方法宣告的屬性來完成 `Model` 。

例如：

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述宣告會產生 `UIImagePickerImagePickedEventArgs` 衍生自 EventArgs 的類別，並同時封裝和這兩個參數 `UIImage` `NSDictionary` 。 產生器會產生下列結果：

```csharp
public partial class UIImagePickerImagePickedEventArgs : EventArgs {
    public UIImagePickerImagePickedEventArgs (UIImage image, NSDictionary editingInfo);
    public UIImage Image { get; set; }
    public NSDictionary EditingInfo { get; set; }
}
```

然後，它會在類別中公開下列 `UIImagePickerController` 內容：

```csharp
public event EventHandler<UIImagePickerImagePickedEventArgs> FinishedPickingImage { add; remove; }
```

### <a name="eventnameattribute"></a>EventNameAttribute

這個屬性是用來允許產生器變更在類別中產生之事件或屬性的名稱。 有時，當模型類別的名稱對模型類別很有意義，但在原始類別中看似奇怪的事件或屬性時，它會很有用。

例如，會 `UIWebView` 從使用下列位 `UIWebViewDelegate` ：

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

上述會將公開 `LoadingFinished` 為中的方法 `UIWebViewDelegate` ，但 `LoadFinished` 做為要連結至中的事件 `UIWebView` ：

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute"></a>

### <a name="modelattribute"></a>ModelAttribute

當您將 `[Model]` 屬性套用至合約 API 中的類型定義時，執行時間會產生特殊程式碼，只有在使用者已覆寫類別中的方法時，才會對類別中的方法叫用。 這個屬性通常會套用至包裝目標-C 委派類別的所有 Api。

<a name="NoDefaultValueAttribute"></a>

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

指定模型上的方法不提供預設的傳回值。

這適用于目標 C 執行時間，方法是回應 `false` 目標-c 執行時間要求，以判斷指定的選取器是否在這個類別中執行。

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

另請參閱： [`[DefaultValue]`](#DefaultValueAttribute) 、[`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute"></a>

## <a name="protocols"></a>通訊協定

C # 中的目標 C 通訊協定概念並不存在。 通訊協定與 c # 介面類別似，但不同之處在于通訊協定中宣告的所有方法和屬性都必須由採用它的類別來執行。 相反地，有些方法和屬性是選擇性的。

有些通訊協定通常做為模型類別使用，而這些是應該使用屬性來系結的 [`[Model]`](#ModelAttribute) 。

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

從 Xamarin. iOS 7.0 開始已納入全新且改良的通訊協定系結功能。  包含屬性的任何定義 `[Protocol]` 實際上都會產生三個支援的類別，以大幅改善您取用通訊協定的方式：

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

**類別執行**會提供完整的抽象類別，您可以覆寫個別的方法，並取得完整的型別安全。 但由於 c # 不支援多重繼承，因此在某些情況下，您可能需要不同的基類，但仍想要執行介面。

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

因為在系結時不存在，所以需要上述的 `IMyProtocol` ，這就是為什麼您需要提供空的介面。

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

當您系結已宣告為採用通訊協定的現有目標-C 類型時，您會想要直接內嵌通訊協定。 若要這麼做，只需將您的通訊協定宣告為不含任何屬性的介面 [`[BaseType]`](#BaseTypeAttribute) ，並在介面的基底介面清單中列出通訊協定。

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

用來指定屬性傳回類型的對齊值。 某些屬性會取得必須在特定界限對齊之位址的指標（在 Xamarin 中，這會發生，例如，某些 `GLKBaseEffect` 屬性必須對齊16位元組）。 您可以使用這個屬性來裝飾 getter，並使用對齊值。 `OpenTK.Vector4` `OpenTK.Matrix4` 當與目標-C api 整合時，這通常與和類型搭配使用。

範例：

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```

### <a name="appearanceattribute"></a>AppearanceAttribute

`[Appearance]`屬性限制為 iOS 5，其中引進了外觀管理員。

`[Appearance]`屬性可以套用至參與架構的任何方法或屬性 `UIAppearance` 。 當這個屬性套用至類別中的方法或屬性時，它會指示系結產生器建立強型別外觀類別，用來為這個類別的所有實例或符合特定準則的實例進行樣式。

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

在方法 `[AutoReleaseAttribute]` 和屬性上使用，以將方法調用包裝至中的方法 `NSAutoReleasePool` 。

在目標-C 中，有一些方法會傳回已新增至預設值的 `NSAutoReleasePool` 。 根據預設，這些會移至您的執行緒 `NSAutoReleasePool` ，但由於當 managed 物件存在時，您可能不想要在中保留物件的參考， `NSAutoReleasePool` 只有線上程將控制權傳回給下一個執行緒，或回到主要迴圈之前，才會耗盡該參考。

此屬性適用于高屬性的範例（例如），其會傳回已 `UIImage.FromFile` 加入至預設值的物件 `NSAutoReleasePool` 。 如果沒有這個屬性，只要您的執行緒未將控制權傳回給主迴圈，就會保留影像。 如果您的往來文章為某種背景下載程式，且一律為運作中並等候工作，則不會釋放映射。

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]`會用來強制建立 managed 類型，即使傳回的非受控物件不符合系結定義中所描述的類型也是如此。

當標頭中所述的類型不符合原生方法的傳回型別時，這會很有用，例如，接受下列的目標 C 定義 `NSURLSession` ：

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

它會清楚指出它會傳回 `NSURLSessionDownloadTask` 實例，但它會傳回**returns** ，也 `NSURLSessionTask` 就是超級類別，因此無法轉換成 `NSURLSessionDownloadTask` 。 因為我們在型別安全的內容中，所以 `InvalidCastException` 會發生。

為了符合標頭描述並避免 `InvalidCastException` ， `[ForcedTypeAttribute]` 會使用。

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

`[ForcedTypeAttribute]`也會接受名為 `Owns` 且預設為的布林值 `false` `[ForcedType (owns: true)]` 。 擁有參數是用來遵循**Core Foundation**物件的[擁有權原則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html)。

`[ForcedTypeAttribute]`只有在參數、屬性和傳回值上才有效。

<a name="BindAsAttribute"></a>

### <a name="bindasattribute"></a>BindAsAttribute

`[BindAsAttribute]`允許系結 `NSNumber` ， `NSValue` 並將（列舉 `NSString` ）轉換成更精確的 c # 類型。 屬性可以透過原生 API，用來建立更好、更精確的 .NET API。

您可以使用來裝飾方法（在傳回值上）、參數和屬性 `BindAs` 。 唯一的限制是您的成員**不得**在 `[Protocol]` 或 [`[Model]`](#ModelAttribute) 介面內。

例如：

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

在內部，我們將進行 `bool?`  <->  `NSNumber` 和 `CGRect`  <->  `NSValue` 轉換。

目前支援的封裝類型如下：

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

下列 c # 資料類型支援封裝從/到 `NSValue` ：

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

下列 c # 資料類型支援封裝從/到 `NSNumber` ：

* bool
* byte
* double
* FLOAT
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

`enum`  <->  `NSString` 只有當提供的列舉類型 [`[BindAs]`](#BindAsAttribute) 是[由 NSString 常數支援](#enum-attributes)時，我們才會處理轉換。

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

`rects`參數將封裝成 `NSArray` ，其中包含 `NSValue` 每個的， `CGRect` 而在傳回時，您將會取得陣列， `CAScroll?` 其已使用傳回之包含的值建立 `NSArray` `NSStrings` 。

<a name="BindAttribute"></a>

### <a name="bindattribute"></a>BindAttribute

屬性在套用 `[Bind]` 至方法或屬性宣告時，會有兩個使用一個，另一個則適用于屬性中的個別 getter 或 setter。

當用於方法或屬性時，屬性的效果 `[Bind]` 會產生叫用指定之選取器的方法。 但產生的產生方法不會以屬性裝飾 [`[Export]`](#ExportAttribute) ，這表示它不能參與方法覆寫。 這通常與用 `[Target]` 來執行目標-C 擴充方法的屬性搭配使用。

例如：

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

在 getter 或 setter 中使用時， `[Bind]` 當產生屬性的 getter 和 Setter 目標-C 選取器名稱時，會使用屬性來改變程式碼產生器所推斷的預設值。 根據預設，當您將具有名稱的屬性加上旗標時 `fooBar` ，產生器會針對 `fooBar` getter 和 setter 產生匯出 `setFooBar:` 。 在少數情況下，目標-C 並不遵循此慣例，通常會將 getter 名稱變更為 `isFooBar` 。
您會使用這個屬性來通知產生器。

例如：

```csharp
// Default behavior
[Export ("active")]
bool Active { get; set; }

// Custom naming with the Bind attribute
[Export ("visible")]
bool Visible { [Bind ("isVisible")] get; set; }
```

<a name="AsyncAttribute"></a>

### <a name="asyncattribute"></a>AsyncAttribute

僅適用于 Xamarin. iOS 6.3 和更新版本。

這個屬性可以套用至接受完成處理常式做為最後一個引數的方法。

您可以 `[Async]` 在其最後一個引數為回呼的方法上使用屬性。  當您將此套用至方法時，系結產生器會使用後置詞來產生該方法的版本 `Async` 。  如果回呼不接受任何參數，則傳回值會是 `Task` ，如果回呼接受參數，則結果會是 `Task<T>` 。

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

下列會產生這個非同步方法：

```csharp
Task LoadFileAsync (string file);
```

如果回呼接受多個參數，您應該設定 `ResultType` 或， `ResultTypeName` 以指定將保留所有屬性之產生類型的所需名稱。

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

下列會產生這個非同步方法，其中 `FileLoading` 包含可存取和的屬性 `files` `byteCount` ：

```csharp
Task<FileLoading> LoadFile (string file);
```

如果回呼的最後一個參數是，則 `NSError` 產生的 `Async` 方法會檢查值是否不是 null，如果是這種情況，則產生的非同步方法會設定工作例外狀況。

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

上述會產生下列非同步方法：

```csharp
Task<string> UploadAsync (string file);
```

發生錯誤時，所產生的工作會將例外狀況設定為 `NSErrorException` ，以包裝產生的 `NSError` 。

#### <a name="asyncattributeresulttype"></a>AsyncAttribute. ResultType

使用此屬性來指定傳回物件的值 `Task` 。   這個參數會採用現有的型別，因此必須在其中一個核心 api 定義中定義它。

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

使用此屬性來指定傳回物件的值 `Task` 。   這個參數會採用所需類型名稱的名稱，產生器會產生一系列的屬性，也就是回呼所採用的每個參數各一個。

#### <a name="asyncattributemethodname"></a>AsyncAttribute。方法名稱

使用此屬性可自訂所產生之非同步方法的名稱。   預設值是使用方法的名稱並附加 "Async" 文字，您可以使用這個來變更這個預設值。

<a name="DesignatedInitializerAttribute"></a>

### <a name="designatedinitializerattribute"></a>DesignatedInitializerAttribute

當此屬性套用至函式時，它會 `[DesignatedInitializer]` 在最終平臺元件中產生相同的。 這是為了協助 IDE 指出子類別中應該使用哪個函式。

這應該對應到的目標-C/clang 使用 `__attribute__((objc_designated_initializer))` 。

<a name="DisableZeroCopyAttribute"></a>

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

這個屬性會套用至字串參數或字串屬性，並指示程式碼產生器不要針對此參數使用零複製字串封送處理，而改為從 c # 字串建立新的 NSString 實例。
只有當您使用 `--zero-copy` 命令列選項或設定元件層級屬性來指示產生器使用零複製字串封送處理時，才需要此屬性 `ZeroCopyStringsAttribute` 。

在以目標-C 宣告屬性為 `retain` 或 `assign` 屬性而非屬性的情況下，這是必要的 `copy` 。 這些通常是在協力廠商程式庫中發生，這些是由開發人員錯誤地「優化」的。 一般而言， `retain` 或 `assign` `NSString` 屬性不正確，因為 `NSMutableString` 或的使用者衍生類別 `NSString` 可能會改變字串的內容，而不知道程式庫程式碼，因此會稍微中斷應用程式。 這種情況通常是因為過早的優化所造成。

以下顯示目標-C 中的兩個這類屬性：

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```

<a name="DisposeAttribute"></a>

### <a name="disposeattribute"></a>DisposeAttribute

當您將套用 `[DisposeAttribute]` 至類別時，您會提供程式碼片段，以加入至類別的 `Dispose()` 方法執行。

由於 `Dispose` 方法是由和工具自動產生 `bmac-native` ，因此您必須 `btouch-native` 使用 `[Dispose]` 屬性，在產生的方法執行中插入一些程式碼 `Dispose` 。

例如：

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute"></a>

### <a name="exportattribute"></a>ExportAttribute

`[Export]`屬性是用來標示要公開至目標-C 執行時間的方法或屬性。 此屬性會在系結工具與實際的 Xamarin 和 Xamarin 執行時間之間共用。 針對方法，參數會逐字傳遞至產生的程式碼，針對屬性，會根據基底宣告產生 getter 和 setter 匯出（如需如何改變系結工具行為的詳細資訊，請參閱上的一節 [`[BindAttribute]`](#BindAttribute) ）。

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

<a name="FieldAttribute"></a>

### <a name="fieldattribute"></a>FieldAttribute

這個屬性是用來將 C 全域變數公開為視需要載入並對 c # 程式碼公開的欄位。 通常需要這種方法來取得定義于 C 或目標-C 中的常數值，而且可能是某些 Api 中使用的 token，或其值不透明，且必須由使用者程式碼使用。

語法：

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName`是要連結的 C 符號。 根據預設，這會從已定義類型之命名空間中推斷名稱的程式庫載入。 如果這不是用來查閱符號的程式庫，您應該傳遞 `libraryName` 參數。 如果您要連結靜態程式庫，請使用 `__Internal` 做為 `libraryName` 參數。

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

<a name="InternalAttribute"></a>

### <a name="internalattribute"></a>InternalAttribute

`[Internal]`屬性可以套用至方法或屬性，而且其作用是使用 c # 關鍵字來標示產生的程式碼， `internal` 使程式碼只能供產生之元件中的程式碼存取。 這通常是用來隱藏太低層級的 Api，或提供您想要針對產生器不支援的 Api 或應用程式開發介面，而且需要一些程式碼撰寫的弱式公用 API。

當您設計系結時，通常會使用這個屬性來隱藏方法或屬性，並為方法或屬性提供不同的名稱，然後在您的 c # 互補支援檔案上，加入強型別包裝函式來公開基礎功能。

例如：

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

<a name="IsThreadStaticAttribute"></a>

### <a name="isthreadstaticattribute"></a>IsThreadStaticAttribute

這個屬性會將屬性的支援欄位標記為要以 .NET 屬性標注 `[ThreadStatic]` 。 如果欄位是執行緒靜態變數，這會很有用。

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions （Xamarin. iOS 6.0.6）

這個屬性會讓方法支援原生（目標-C）例外狀況。
叫用不 `objc_msgSend` 會直接呼叫，而是會透過自訂 trampoline 來攔截 ObjectiveC 例外狀況，並將其封送處理至 managed 例外狀況。

目前只支援幾個簽章 `objc_msgSend` （當使用系結之應用程式的原生連結失敗，並出現遺失的 monotouch_*_objc_msgSend*符號）時，您會發現簽章不受支援，但您也可以在要求中新增更多簽章。

### <a name="newattribute"></a>NewAttribute

這個屬性會套用至方法和屬性，讓產生器在宣告 `new` 前面產生關鍵字。

當相同的方法或屬性名稱是在基類中已存在的子類別中引進時，用來避免編譯器警告。

<a name="NotificationAttribute"></a>

### <a name="notificationattribute"></a>NotificationAttribute

您可以將此屬性套用至欄位，讓產生器產生強型別 helper 通知類別。

此屬性可用於不含任何承載之通知的引數，或者您可以指定 `System.Type` 參考 API 定義中另一個介面的，其名稱通常會以 "EventArgs" 結尾。 產生器會將介面轉換為子類別， `EventArgs` 且會包含此處列出的所有屬性。 [`[Export]`](#ExportAttribute)屬性應該用於 `EventArgs` 類別中，以列出用來查閱目標-C 字典以提取值的索引鍵名稱。

例如：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

上述程式碼會使用下列方法來產生一個嵌套類別 `MyClass.Notifications` ：

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

或設定要觀察的特定物件。 如果您傳遞 `null` 給 `objectToObserve` 這個方法的行為就像它的其他對等。

```csharp
var token = MyClass.Notifications.ObserverDidStart (objectToObserve, (notification) => {
    Console.WriteLine ("Observed the 'DidStart' event on objectToObserve!");
});
```

從傳回的值 `ObserveDidStart` 可以用來輕鬆地停止接收通知，如下所示：

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

上述將會產生 `MyScreenChangedEventArgs` 具有 `ScreenX` 和屬性的類別 `ScreenY` ，而這些屬性會使用索引鍵和分別從[NSNotification](xref:Foundation.NSNotification.UserInfo)中提取資料，並套用 `ScreenXKey` 適當的 `ScreenYKey` 轉換。 `[ProbePresence]`如果在中設定索引鍵，則會使用屬性來進行探查 `UserInfo` ，而不是嘗試將值解壓縮。 這適用于有索引鍵為值的情況（通常用於布林值）。

這可讓您撰寫如下的程式碼：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

在某些情況下，沒有任何常數與字典上傳遞的值相關聯。  Apple 有時會使用公用符號常數，有時會使用字串常數。  根據預設， [`[Export]`](#ExportAttribute) 您所提供類別中的屬性 `EventArgs` 會使用指定的名稱做為要在執行時間查閱的公用符號。  如果不是這種情況，您應該改為將其視為字串常數，然後將 `ArgumentSemantic.Assign` 值傳遞給 Export 屬性。

**Xamarin 的新功能。 iOS 8。4**

有時候，通知將會開始使用，而不會有任何引數，因此 [`[Notification]`](#NotificationAttribute) 可接受不含引數的用法。  但有時候會引進通知的參數。  若要支援此案例，屬性可以套用一次以上。

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

<a name="NullAllowedAttribute"></a>

### <a name="nullallowedattribute"></a>NullAllowedAttribute

當此套用至屬性時，它會將屬性標示為允許將值 `null` 指派給它。 這只對參考型別有效。

當這個套用到方法簽章中的參數時，它會指出指定的參數可以是 null，而且不應該執行任何檢查來傳遞 `null` 值。

如果參考型別沒有這個屬性，系結工具會先針對所指派的值產生檢查，再將它傳遞給目標-C，並產生檢查， `ArgumentNullException` 如果指派的值為，則會擲回 `null` 。

例如：

```csharp
// In properties

[NullAllowed]
UIImage IconFile { get; set; }

// In methods
void SetImage ([NullAllowed] UIImage image, State forState);
```

<a name="OverrideAttribute"></a>

### <a name="overrideattribute"></a>OverrideAttribute

使用這個屬性來指示系結產生器，此特定方法的系結應該以關鍵字標記 `override` 。

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

在此情況下， `Dependencies` 會在加入或移除物件的相依性之後叫用屬性 `NSOperation` ，以確保我們有代表實際載入物件的圖形，以防止記憶體流失和記憶體損毀。

### <a name="postsnippetattribute"></a>PostSnippetAttribute

您可以使用這個屬性，插入一些 c # 原始程式碼，以在程式碼叫用基礎目標-C 方法之後插入

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

如果的值 `doAdd` 為 true，則參數會加入至 `__mt_{0}_var List<NSObject>;` 。 其中 `{0}` 會以指定的取代 `listName` 。 您必須在 API 的互補部分類別中宣告此支援欄位。

如需範例，請參閱[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute （Xamarin. iOS 6.0）

這可以套用至傳回型別，以表示產生器應該 `Release` 在物件上呼叫，然後才傳回。 只有當方法提供您保留的物件（而不是最常見的案例）時，才需要這麼做

範例：

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

此外，此屬性會傳播至產生的程式碼，因此，在從這類函式傳回目標時，您必須保留物件。

### <a name="sealedattribute"></a>SealedAttribute

指示產生器將產生的方法標示為 sealed。 如果未指定此屬性，則預設值為根據其他屬性的使用方式，產生虛擬方法（虛擬方法、抽象方法或覆寫）。

<a name="StaticAttribute"></a>

### <a name="staticattribute"></a>StaticAttribute

當 `[Static]` 屬性套用至方法或屬性時，這會產生靜態方法或屬性。 如果未指定這個屬性，則產生器會產生實例方法或屬性。

### <a name="transientattribute"></a>TransientAttribute

使用此屬性來標示其值為暫時性的屬性，也就是由 iOS 暫時建立但不是長期的物件。 當此屬性套用至屬性時，產生器不會建立此屬性的支援欄位，這表示 managed 類別不會保留物件的參考。

<a name="WrapAttribute"></a>

### <a name="wrapattribute"></a>WrapAttribute

在設計 Xamarin/Xamarin 的系結 `[Wrap]` 時，屬性是用來包裝弱型別物件與強型別物件。 這主要是針對目標-C 委派物件，通常會宣告為類型 `id` 或 `NSObject` 。 Xamarin 和 Xamarin 所使用的慣例是將這些委派或資料來源公開為類型 `NSObject` ，並使用「弱式」慣例和所公開的名稱來命名。 `id delegate`來自目標-C 的屬性會公開為 `NSObject WeakDelegate { get; set; }` API 合約檔案中的屬性。

但是，指派給這個委派的值通常是強型別，因此，我們會呈現強型別並套用 `[Wrap]` 屬性，這表示使用者可以選擇使用弱型別（如果它們需要一些細微的控制，或是需要對低層級的訣竅使用強型別屬性），也可以在大部分的工作中使用強型別內容。

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

這就是使用者使用強型別版本的方式，請注意，使用者會利用 c # 的型別系統，並使用 override 關鍵字來宣告他的意圖，而且他不需要手動裝飾方法 `[Export]` ，因為我們已在使用者的系結中執行此作業：

```csharp
// This is the strong case,
class MyDelegate : DemoDelegate {
   override void Demo DoDemo () {}
}

var strongDemo = new Demo ();
demo.Delegate = new MyDelegate ();
```

屬性的另一個用法 `[Wrap]` 是支援強型別版本的方法。  例如：

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

當屬性套用至 `[Wrap]` 以屬性裝飾之類型內的方法時 `[Category]` ，您必須包含 `This` 作為第一個引數，因為正在產生擴充方法。 例如：

```csharp
[Wrap ("Write (This, image, options?.Dictionary, out error)")]
bool Write (CIImage image, CIImageRepresentationOptions options, out NSError error);
```

根據預設，所產生的成員 `[Wrap]` 不是 `virtual` ，如果您需要 `virtual` 成員，可以將設定為 `true` 選擇性的 `isVirtual` 參數。

```csharp
[BaseType (typeof (NSObject))]
interface FooExplorer {
    [Export ("fooWithContentsOfURL:")]
    void FromUrl (NSUrl url);

    [Wrap ("FromUrl (NSUrl.FromString (url))", isVirtual: true)]
    void FromUrl (string url);
}
```

`[Wrap]`也可以直接在屬性 getter 和 setter 中使用。
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

本節說明您可以套用至方法定義中之參數的屬性，以及 `[NullAttribute]` 適用于整個屬性的。

<a name="BlockCallback"></a>

### <a name="blockcallback"></a>BlockCallback

這個屬性會套用至 c # 委派宣告中的參數類型，以通知系結器中有問題的參數符合目標-C 區塊呼叫慣例，而且應該以這種方式封送處理。

這通常用於在目標-C 中定義如下的回呼：

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另請參閱： [CCallback](#CCallback)。

<a name="CCallback"></a>

### <a name="ccallback"></a>CCallback

這個屬性會套用至 c # 委派宣告中的參數類型，以通知系結器中有問題的參數符合 C ABI 函式指標呼叫慣例，因此應該以這種方式封送處理。

這通常用於在目標-C 中定義如下的回呼：

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另請參閱： [BlockCallback](#BlockCallback)。

### <a name="params"></a>Params

您可以在 `[Params]` 方法定義的最後一個 array 參數上使用屬性，讓產生器在定義中插入 "params"。   這可讓系結輕鬆地允許選擇性參數。

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

<a name="plainstring"></a>

### <a name="plainstring"></a>PlainString

您可以使用 `[PlainString]` 字串參數前面的屬性，指示系結產生器將字串當做 C 字串傳遞，而不是以的形式傳遞參數 `NSString` 。

大部分的目標-C Api 會使用 `NSString` 參數，但少數 api 會公開 `char *` 傳遞字串的 api，而不是 `NSString` 變化。
`[PlainString]`在這些情況下，請使用。

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

指示產生器保留指定參數的參考。 產生器將提供此欄位的備份存放區，或者您可以指定名稱（ `WrapName` ）來儲存的值。 這對於保存當做參數傳遞至目標-C 的 managed 物件參考很有用，而且當您知道目標-C 只會保留此物件的複本時。 例如，類似的 API `SetDisplay (SomeObject)` 會使用這個屬性，因為 SetDisplay 可能一次只能顯示一個物件。 如果您需要追蹤一個以上的物件（例如，針對類似堆疊的 API），您會使用 `[RetainList]` 屬性。

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

如果的值 `doAdd` 為 true，則參數會加入至 `__mt_{0}_var List<NSObject>` 。 其中 `{0}` 會以指定的取代 `listName` 。 您必須在 API 的互補部分類別中宣告此支援欄位。

如需範例，請參閱[foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs)和[NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="transientattribute"></a>TransientAttribute

這個屬性會套用至參數，而且只會在從目標-C 轉換成 c # 時使用。  在這些轉換期間，各種目標-C `NSObject` 參數會包裝成物件的 managed 標記法。

執行時間會參考原生物件，並保留參考，直到物件的最後一個 managed 參考消失，而且 GC 有機會執行。

在少數情況下，c # 執行時間不會保留原生物件的參考，這是很重要的。  當基礎機器碼已將特殊行為附加至參數的生命週期時，有時會發生這種情況。  例如：參數的析構函式將會執行某個清除動作，或處置一些寶貴的資源。

這個屬性會通知執行時間，您想要在從覆寫的方法回到目標-C 時，盡可能處置物件。

此規則很簡單：如果執行時間必須從原生物件建立新的 managed 標記法，則在函式的結尾，將會卸載原生物件的保留計數，而且會清除受管理物件的 Handle 屬性。   這表示，如果您保留受管理物件的參考，該參考將會變得毫無用處（在其上叫用方法會擲回例外狀況）。

如果未建立傳遞的物件，或如果已經有未完成的 managed 物件表示，則不會進行強制處置。 

## <a name="property-attributes"></a>Property 屬性

<a name="NotImplementedAttribute"></a>

### <a name="notimplementedattribute"></a>NotImplementedAttribute

這個屬性是用來支援目標-C 的用法，其中具有 getter 的屬性會引進基類，而可變的子類別會引進 setter。

因為 c # 不支援此模型，所以基類必須同時具有 setter 和 getter，而子類別可以使用[OverrideAttribute](#OverrideAttribute)。

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

<a name="enum-attributes"></a>

## <a name="enum-attributes"></a>列舉屬性

將 `NSString` 常數對應至列舉值是建立更好的 .NET API 的簡單方法。 這樣

* 藉由**只**顯示 API 的正確值，讓程式碼完成更有用。
* 加入型別安全，您不能 `NSString` 在不正確的內容中使用另一個常數，而且
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

在上述系結定義中，產生器會 `enum` 自行建立，而且也會建立 `*Extensions` 靜態型別，其中包含列舉值和常數之間的兩個方法轉換方法 `NSString` 。 這表示即使它們不是 API 的一部分，開發人員仍然可以使用常數。

範例：

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

如果未裝飾列舉值，則會擲回 `NotSupportedException` 。

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

這是 `[Field]` 用於類型內之常數的相同屬性。 它也可以在列舉內用來對應具有特定常數的值。

`null`如果 `null` `NSString` 指定了常數，則可以使用值來指定應該傳回的列舉值。

從上述範例中：

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

如果不 `null` 存在任何值，則 `ArgumentNullException` 會擲回。

## <a name="global-attributes"></a>全域屬性

全域屬性可以使用屬性修飾詞來套用 `[assembly:]` ，例如， [`[LinkWithAttribute]`](#LinkWithAttribute) 或可用於任何地方，例如 [`[Lion]`](#SinceAndLionAttributes) 和 [`[Since]`](#SinceAndLionAttributes) 屬性。

<a name="LinkWithAttribute"></a>

### <a name="linkwithattribute"></a>LinkWithAttribute

這是元件層級屬性，可讓開發人員指定在重複使用系結程式庫時所需的連結旗標，而不需強制媒體櫃的取用者手動設定 gcc_flags 和傳遞至程式庫的額外 mtouch 引數。

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

當您使用 `[LinkWith]` 屬性時，會將指定的 `libraryName` 內嵌至產生的元件，讓使用者能夠傳送單一 DLL，其中同時包含未受管理的相依性，以及正確取用來自 Xamarin 的程式庫所需的命令列旗標。

也可以不提供 `libraryName` ，在此情況下， `LinkWith` 屬性只能用來指定其他連結器旗標：

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>LinkWithAttribute 的構造函式

這些函式可讓您指定要連結的程式庫，並將其內嵌至產生的元件、程式庫支援的支援目標，以及程式庫所需的任何選用程式庫旗標。

請注意， `LinkTarget` 引數是由 Xamarin 推斷而不需要設定。

範例：

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

`ForceLoad`屬性是用來決定是否 `-force_load` 使用連結旗標來連結原生媒體櫃。 目前，這應該一律為 true。

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute Framework

如果要系結的程式庫在任何架構（而非和）上都有困難的需求 `Foundation` `UIKit` ，您應該將 `Frameworks` 屬性設定為包含所需平臺架構清單（以空格分隔）的字串。 例如，如果您要系結的程式庫需要 `CoreGraphics` 和 `CoreText` ，您可以將 `Frameworks` 屬性設定為 `"CoreGraphics CoreText"` 。

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

如果所產生的可執行檔必須使用 c + + 編譯器來編譯，而不是預設的（C 編譯器），請將此屬性設定為 true。 如果您要系結的程式庫是以 c + + 撰寫的，請使用此項。

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute. LibraryName

要組合之非受控程式庫的名稱。 這是副檔名為 ". a" 的檔案，它可以包含多個平臺的目的程式代碼（例如，適用于模擬器的 ARM 和 x86）。

舊版的 Xamarin `LinkTarget` 會檢查屬性，以判斷您的程式庫支援的平臺，但現在會自動偵測到，並 `LinkTarget` 忽略屬性。

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

此 `LinkerFlags` 字串提供一個方法，讓作者能夠在將原生程式庫連結至應用程式時，指定所需的任何其他連結器旗標。

例如，如果原生程式庫需要 libxml2 和 zlib，您可以將 `LinkerFlags` 字串設定為 `"-lxml2 -lz"` 。

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute. LinkTarget

舊版的 Xamarin `LinkTarget` 會檢查屬性，以判斷您的程式庫支援的平臺，但現在會自動偵測到，並 `LinkTarget` 忽略屬性。

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

如果您要連結的程式庫需要 GCC 例外狀況處理程式庫（gcc_eh），請將此屬性設定為 true

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute. SmartLink

`SmartLink`屬性應設定為 true，讓 Xamarin 能夠判斷是否 `ForceLoad` 為必要。

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

`WeakFrameworks`屬性的運作方式與 `Frameworks` 屬性相同，不同之處在于在連結時間， `-weak_framework` 規範會針對每個列出的架構傳遞至 gcc。

`WeakFrameworks`讓程式庫和應用程式能夠在平臺架構上進行弱式連結，讓它們可以選擇性地使用它們（如果有的話），但如果您的程式庫想要在較新版本的 iOS 上新增額外的功能，這會很有用。 如需弱式連結的詳細資訊，請參閱 Apple 的[弱式連結](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html)檔。

弱式連結的良好候選項目就 `Frameworks` 像是帳戶、 `CoreBluetooth` 、 `CoreImage` 、 `GLKit` 和， `NewsstandKit` `Twitter` 因為它們只能在 iOS 5 中使用。

<a name="SinceAndLionAttributes"></a>

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute （iOS）和 LionAttribute （macOS）

您可以使用 `[Since]` 屬性，將 api 標記為在特定時間點引進。 只有在無法使用基礎類別、方法或屬性時，才應該使用屬性來標示可能造成執行時間問題的類型和方法。

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

`[Lion]`屬性會以相同的方式套用，但適用于使用 Lion 所引進的類型。 使用 `[Lion]` 與 ios 中使用的更特定版本號碼的原因是，ios 的修改頻率很高，而主要 OS X 版本很少發生，而且比起版本號碼的代號更容易記住作業系統

### <a name="adviceattribute"></a>AdviceAttribute

使用此屬性可為開發人員提供其他可能更方便使用之 Api 的提示。   例如，如果您提供 API 的強型別版本，您可以在弱型別屬性上使用這個屬性，將開發人員導向至較佳的 API。

此屬性中的資訊會顯示在檔中，而且可加以開發，以提供使用者如何改進的建議。

### <a name="requiressuperattribute"></a>RequiresSuperAttribute

這是屬性的特殊子類別 `[Advice]` ，可以用來提示開發人員覆寫方法，而**需要**呼叫基底（覆寫）方法。

這會對應到 `clang`[`__attribute__((objc_requires_super))`](https://clang.llvm.org/docs/AttributeReference.html#objc-requires-super)

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

僅適用于 Xamarin. iOS 5.4 和更新版本。

這個屬性會指示產生器，此特定程式庫的系結（如果與一起套用 `[assembly:]` ）或類型應該使用快速零複製字串封送處理。 這個屬性相當於將命令列選項傳遞給產生器 `--zero-copy` 。

針對字串使用零複製時，產生器會有效地使用與目標為 C 的字串相同的 c # 字串，而不會產生新的 `NSString` 物件，並避免將資料從 c # 字串複製到目標 C 字串。 使用零份複製字串的唯一缺點是，您必須確定您所包裝的任何字串屬性，都會標示為 `retain` 或 `copy` 已 `[DisableZeroCopy]` 設定屬性。 這是必要的，因為零複製字串的控制碼是配置在堆疊上，而且在函式傳回時無效。

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

我們引進了 Xamarin. iOS 8.0，讓您輕鬆建立可包裝的強型別類別 `NSDictionaries` 。

雖然永遠都可以搭配使用[DictionaryContainer](xref:Foundation.DictionaryContainer)資料類型與手動 API，但現在更容易這麼做。  如需詳細資訊，請參閱[呈現強](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types)型別。

<a name="StrongDictionary"></a>

### <a name="strongdictionary"></a>StrongDictionary

當此屬性套用至介面時，產生器會產生與衍生自[DictionaryContainer](xref:Foundation.DictionaryContainer)的介面同名的類別，並將介面中定義的每個屬性轉換成字典的強型別 getter 和 setter。

這會自動產生可從現有的 `NSDictionary` 或已建立新的類別具現化的類別。

這個屬性接受一個參數，也就是類別的名稱，其中包含用來存取字典上元素的索引鍵。   根據預設，介面中具有屬性的每個屬性都會針對尾碼為 "Key" 的名稱，查閱指定類型中的成員。

例如：

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

在上述情況下， `MyOption` 類別會產生的字串屬性 `Name` ，它會使用 `MyOptionKeys.NameKey` 做為字典中的索引鍵來抓取字串。   和會使用 `MyOptionKeys.AgeKey` 做為字典中的索引鍵，來抓取 `NSNumber` 包含 int 的。

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

定義中支援下列資料類型 `StrongDictionary` ：

|C # 介面類別型|`NSDictionary`儲存類型|
|---|---|
|`bool`|`Boolean`儲存在`NSNumber`|
|列舉值|儲存在中的整數`NSNumber`|
|`int`|儲存在中的32位整數`NSNumber`|
|`uint`|32位不帶正負號的整數，儲存在`NSNumber`|
|`nint`|`NSInteger`儲存在`NSNumber`|
|`nuint`|`NSUInteger`儲存在`NSNumber`|
|`long`|儲存在中的64位整數`NSNumber`|
|`float`|32位整數，儲存為`NSNumber`|
|`double`|64位整數，儲存為`NSNumber`|
|`NSObject`和子類別|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C `Array` #`NSObject`|`NSArray`|
|C # `Array` 的列舉|`NSArray`包含 `NSNumber` 值|
