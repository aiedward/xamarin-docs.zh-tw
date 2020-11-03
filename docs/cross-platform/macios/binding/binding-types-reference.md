---
title: 系結類型參考指南
description: '本參考指南說明建立 c # 系結至目標 C 程式庫時，所需瞭解的各種屬性和概念。'
ms.prod: xamarin
ms.assetid: C6618E9D-07FA-4C84-D014-10DAC989E48D
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 764c6303956199982da779d87b0a29977575e767
ms.sourcegitcommit: 4f0223cf13e14d35c52fa72a026b1c7696bf8929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93278347"
---
# <a name="binding-types-reference-guide"></a>系結類型參考指南

本檔說明您可以用來標注 API 合約檔案的屬性清單，以驅動系結和產生的程式碼

Xamarin 和 Xamarin API 合約是以 c # 撰寫的，大部分是定義以 c # 呈現目標 C 程式碼方式的介面定義。 此程式牽涉到混合不同的介面宣告，再加上 API 合約可能需要的一些基本型別定義。 如需系結類型的簡介，請參閱我們的附屬指南：系結 [目標-C 程式庫](~/cross-platform/macios/binding/objective-c-libraries.md)。

## <a name="type-definitions"></a>型別定義

語法：

```csharp
[BaseType (typeof (BTYPE))
interface MyType : [Protocol1, Protocol2] {
     IntPtr Constructor (string foo);
}
```

合約定義中具有屬性的每個介面都會宣告 [`[BaseType]`](#BaseTypeAttribute) 產生之物件的基底類型。 在上述宣告中， `MyType` 會產生類別 c # 型別，以系結至稱為的目標 C 型別 `MyType` 。

如果您在上述範例中的 typename (之後指定任何型別 `Protocol1` ，而 `Protocol2`) 使用介面繼承語法，則這些介面的內容將會內嵌，就像是的合約的一部分一樣 `MyType` 。
當 Xamarin 的型別採用通訊協定的方式，就是將通訊協定中宣告的所有方法和屬性內嵌到型別本身。

下圖顯示如何 `UITextField` 在 Xamarin. iOS 合約中定義的目標 C 宣告：

```objc
@interface UITextField : UIControl <UITextInput> {

}
```

會以 c # API 合約的形式寫入：

```csharp
[BaseType (typeof (UIControl))]
interface UITextField : UITextInput {
}
```

您可以藉由將其他屬性套用至介面，以及設定屬性，來控制程式代碼產生的許多其他方面 [`[BaseType]`](#BaseTypeAttribute) 。

### <a name="generating-events"></a>產生事件

Xamarin 和 Xamarin API 設計的其中一項功能是，我們會將目標 C 委派類別對應為 c # 事件和回呼。 使用者可以選擇每個實例，不論他們是否想要採用目標 C 程式設計模式，方法是指派給屬性，例如實作為 `Delegate` 目標 c 執行時間所呼叫之各種方法的類別實例，或選擇 c # 樣式的事件和屬性。

讓我們看看如何使用目標 C 模型的其中一個範例：

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

在上述範例中，您可以看到我們選擇覆寫兩個方法，一個是滾動事件已發生的通知，而第二個則是應傳回布林值的回呼，指示 `scrollView` 它是否應該滾動至最上方。

C # 模型可讓您程式庫的使用者使用 c # 事件語法或屬性語法來攔截應傳回值的回呼，以接聽通知。

這是使用 lambda 的相同功能的 c # 程式碼看起來像這樣：

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

因為事件不會傳回值 (它們具有 void 傳回型別) 您可以連接多個複本。 不 `ShouldScrollToTop` 是事件，而是具有此簽章之類型的屬性 `UIScrollViewCondition` ：

```csharp
public delegate bool UIScrollViewCondition (UIScrollView scrollView);
```

它會傳回 `bool` 值，在此情況下，lambda 語法可讓我們直接從函式傳回值 `MakeDecision` 。

系結產生器支援產生事件和屬性（property），這些事件和屬性會連結類別 `UIScrollView` `UIScrollViewDelegate` ，例如 (的模型) 類別，這是藉由使用和 (參數來標注您的定義，如下 [`[BaseType]`](#BaseTypeAttribute) `Events` `Delegates`) 所述。 除了使用這些參數來批註之外，還 [`[BaseType]`](#BaseTypeAttribute) 必須通知產生器更多元件。

對於採用多個參數的事件 (在目標 C 中，慣例是委派類別中的第一個參數是傳送者物件的實例) 您必須提供所產生之類別所需的名稱 `EventArgs` 。 這是透過 [`[EventArgs]`](#EventArgsAttribute) 模型類別中的方法宣告上的屬性來完成。 例如：

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述宣告會產生 `UIImagePickerImagePickedEventArgs` 衍生自的類別 `EventArgs` ，並同時封裝參數、 `UIImage` 和 `NSDictionary` 。 產生器會產生下列結果：

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

傳回值的模型方法會以不同方式系結。 這兩種方法都需要產生的 c # 委派名稱 () 方法的簽章，也會傳回預設值，以免使用者自行提供執行。 例如， `ShouldScrollToTop` 定義如下：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface UIScrollViewDelegate {
    [Export ("scrollViewShouldScrollToTop:"), DelegateName ("UIScrollViewCondition"), DefaultValue ("true")]
    bool ShouldScrollToTop (UIScrollView scrollView);
}
```

上述 `UIScrollViewCondition` 程式會使用上方所示的簽章來建立委派，而且如果使用者沒有提供執行，則傳回值會是 true。

除了 [`[DefaultValue]`](#DefaultValueAttribute) 屬性之外，您也可以使用屬性（attribute），以指示產生器 [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute) 在呼叫中傳回指定之參數的值，或使用指示產生器沒有 [`[NoDefaultValue]`](#NoDefaultValueAttribute) 預設值的參數。

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

您可以使用 `Name` 屬性來控制此型別將在目標 C 世界中系結的名稱。 這通常是用來指定符合 .NET Framework 設計方針的名稱，但它會對應至目標 C 中不遵循該慣例的名稱。

例如，在下列案例中，我們會將目標 C `NSURLConnection` 類型對應至 `NSUrlConnection` ，因為 .NET Framework 的設計指導方針使用 "url" 而非 "url"：

```csharp
[BaseType (typeof (NSObject), Name="NSURLConnection")]
interface NSUrlConnection {
}
```

指定的名稱會當做系結中所產生屬性的值使用 `[Register]` 。 如果 `Name` 未指定，則會在產生的輸出中使用類型的簡短名稱做為屬性的值 `[Register]` 。

#### <a name="basetypeevents-and-basetypedelegates"></a>BaseType。事件和 BaseType。委派

這些屬性是用來在產生的類別中驅動 c # 樣式的事件產生。 它們是用來將指定的類別與其目標 C 委派類別連結。 在許多情況下，類別會使用委派類別來傳送通知和事件。 例如， `BarcodeScanner` 會有附屬 `BardodeScannerDelegate` 類別。 `BarcodeScanner`類別通常會有您要 `Delegate` 將實例指派給的屬性 `BarcodeScannerDelegate` ，但是您可能會想要對使用者公開類似 c # 的樣式事件介面，在這些情況下，您會使用 `Events` 屬性的和 `Delegates` 屬性 [`[BaseType]`](#BaseTypeAttribute) 。

這些屬性一律會一起設定，而且必須有相同數目的元素，且必須保持同步。 `Delegates` 陣列中每個您想要包裝的弱型別委派都有一個字串，而 `Events` 陣列針對您想要與其建立關聯的每個型別都包含一個型別。

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

如果您在建立這個類別的新實例時套用這個屬性，則會保留該物件的實例，直到叫用所參考的方法為止 `KeepRefUntil` 。 當您不想讓使用者使用您的程式碼來保留物件的參考時，這有助於改善 Api 的可用性。 這個屬性的值是類別中的方法名稱 `Delegate` ，因此您也必須將它與 `Events` 和屬性搭配使用 `Delegates` 。

下列範例示範如何在 Xamarin 中使用這種方式 `UIActionSheet` ：

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

當這個屬性套用至介面定義時，它會 `[DesignatedInitializer]` 在預設的 (產生的) 函式（對應至選取器）上產生屬性 `init` 。

<a name="DisableDefaultCtorAttribute"></a>

### <a name="disabledefaultctorattribute"></a>DisableDefaultCtorAttribute

當這個屬性套用至介面定義時，將會導致產生器無法產生預設的函式。

當您需要使用類別中的其中一個其他函式來初始化物件時，請使用此屬性。

<a name="PrivateDefaultCtorAttribute"></a>

### <a name="privatedefaultctorattribute"></a>PrivateDefaultCtorAttribute

當這個屬性套用至介面定義時，它會將預設的函式標示為私用。 這表示，您仍然可以從擴充檔內部具現化此類別的物件，但您的類別使用者將無法存取它。

<a name="CategoryAttribute"></a>

### <a name="categoryattribute"></a>CategoryAttribute

在類型定義上使用此屬性來系結目標 C 類別，並將這些類別公開為 c # 擴充方法，以反映目標 C 公開功能的方式。

類別是一種目標 C 機制，用來擴充類別中可用的方法和屬性集。   在實務上，它們可用來擴充基類的功能 (例如 `NSObject`) 當特定架構在 (例如) 時連結 `UIKit` ，讓其方法可供使用，但只有在新的架構已連結時才會使用。   在某些其他情況下，它們是用來依功能來組織類別中的功能。   它們與 c # 擴充方法的精神類似。

這是類別在目標 C 中的樣子：

```objc
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

上述範例會在使用方法來擴充實例的程式庫中找到 `UIView` `makeBackgroundRed` 。

若要系結，您可以 [`[Category]`](#CategoryAttribute) 在介面定義上使用屬性。   使用屬性時 [`[Category]`](#CategoryAttribute) ，屬性的意義會 [`[BaseType]`](#BaseTypeAttribute) 從用來指定要擴充的基類變更為要擴充的型別。

以下顯示延伸模組如何系結 `UIView` 並轉換成 c # 擴充方法：

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

上述程式會建立 `MyUIViewExtension` 一個包含擴充方法的類別 `MakeBackgroundRed` 。   這表示您現在可以對任何子類別進行呼叫 `MakeBackgroundRed` `UIView` ，為您提供的功能與目標 C 相同。

在某些情況下，您會在類別目錄中找到 **靜態** 成員，如下列範例所示：

```objc
@interface FooObject (MyFooObjectExtension)
+ (BOOL)boolMethod:(NSRange *)range;
@end
```

這會導致 **不正確** 的類別 c # 介面定義：

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

這是不正確的，因為使用此延伸模組時， `BoolMethod` 您需要的是實例 `FooObject` ，但您會系結 ObjC **靜態** 延伸模組，這是因為 c # 擴充方法的實作為原理的副作用。

使用上述定義的唯一方式是使用下列不好用的程式碼：

```csharp
(null as FooObject).BoolMethod (range);
```

若要避免這種情況，建議您將定義內嵌在 `BoolMethod` `FooObject` 介面定義本身內，如此就能讓您像預期一樣呼叫此擴充功能 `FooObject.BoolMethod (range)` 。

```csharp
[BaseType (typeof (NSObject))]
interface FooObject {

    [Static]
    [Export ("boolMethod:")]
    bool BoolMethod (NSRange range);
}
```

當我們在定義內找到成員時，我們會 (BI1117) 發出警告 [`[Static]`](#StaticAttribute) [`[Category]`](#CategoryAttribute) 。 如果您真的想要在 [`[Static]`](#StaticAttribute) 定義內擁有成員 [`[Category]`](#CategoryAttribute) ，您可以使用 `[Category (allowStaticMembers: true)]` 或藉由裝飾您的成員或 [`[Category]`](#CategoryAttribute) 介面定義，讓警告靜音 [`[Internal]`](#InternalAttribute) 。

<a name="StaticAttribute_Class"></a>

### <a name="staticattribute"></a>StaticAttribute

當這個屬性套用至類別時，它只會產生靜態類別，而不是衍生自的類別， `NSObject` 所以會 [`[BaseType]`](#BaseTypeAttribute) 忽略該屬性。 靜態類別是用來裝載您要公開的 C 公用變數。

例如：

```csharp
[Static]
interface CBAdvertisement {
    [Field ("CBAdvertisementDataServiceUUIDsKey")]
    NSString DataServiceUUIDsKey { get; }
```

將會使用下列 API 產生 c # 類別：

```csharp
public partial class CBAdvertisement  {
    public static NSString DataServiceUUIDsKey { get; }
}
```

## <a name="protocolmodel-definitions"></a>通訊協定/模型定義

模型通常是由通訊協定執行所使用。
它們不同的是，執行時間只會向目標注冊實際被覆寫的方法。
否則，將不會註冊此方法。

一般來說，這表示當您將已加上旗標的類別子類別化時 `ModelAttribute` ，您不應該呼叫基底方法。   呼叫該方法將會擲回下列例外狀況： Foundation.You_Should_Not_Call_base_In_This_Method。 您應該針對任何您覆寫的方法，在子類別上執行整個行為。

<a name="AbstractAttribute"></a>

### <a name="abstractattribute"></a>AbstractAttribute

依預設，屬於通訊協定一部分的成員不是強制的。 這可讓使用者建立物件的子類別， `Model` 方法是只從 c # 中的類別衍生，並只覆寫他們關心的方法。 有時，目標 C 合約會要求使用者提供此方法的實 (這些會以 `@required` 目標-c) 中的指示詞標示。 在這些情況下，您應該使用屬性來標記這些方法 `[Abstract]` 。

`[Abstract]`屬性可以套用至方法或屬性，並使產生器將產生的成員標記為抽象，而且類別必須是抽象類別。

以下是從 Xamarin 取得的：

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

如果使用者未在模型物件中提供這個特定方法的方法，則指定模型方法要傳回的預設值。

語法：

```csharp
public class DefaultValueAttribute : Attribute {
        public DefaultValueAttribute (object o);
        public object Default { get; set; }
}
```

例如，在下列類別的虛數委派類別中 `Camera` ，我們會提供， `ShouldUploadToServer` 它會公開為類別上的屬性 `Camera` 。 如果類別的使用者 `Camera` 未明確將值設定為可回應 true 或 false 的 lambda，在此案例中，預設值會傳回 false，也就是我們在屬性中指定的值 `DefaultValue` ：

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

當針對模型類別傳回值的方法提供這個屬性時，如果使用者未提供自己的方法或 lambda，則會指示產生器傳回指定之參數的值。

範例：

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
public interface NSAnimationDelegate {
    [Export ("animation:valueForProgress:"), DelegateName ("NSAnimationProgress"), DefaultValueFromArgumentAttribute ("progress")]
    float ComputeAnimationCurve (NSAnimation animation, nfloat progress);
}
```

在上述案例中，如果類別的使用者 `NSAnimation` 選擇使用任何 c # 事件/屬性，而且未設定 `NSAnimation.ComputeAnimationCurve` 為方法或 lambda，則傳回值會是在進度參數中傳遞的值。

另請參閱： [`[NoDefaultValue]`](#NoDefaultValueAttribute) 、 [`[DefaultValue]`](#DefaultValueAttribute)

### <a name="ignoredindelegateattribute"></a>IgnoredInDelegateAttribute

有時候，將事件或委派屬性從模型類別公開至主機類別很合理，因此加入此屬性會指示產生器避免產生任何以它裝飾的方法。

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

這個屬性是用來允許產生器變更在主機類別中產生的屬性名稱。 有時候，當 FooDelegate 類別方法的名稱對委派類別很有意義，但在主機類別中看起來像是屬性時，這會很有用。

此外，當您有兩個以上的多載方法可讓它們以 FooDelegate 類別命名，但您想要以更好的指定名稱在主機類別中公開這些多載方法時，這非常有用 (而且需要) 。

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

對於採用多個參數的事件 (在目標 C 中，慣例是委派類別中的第一個參數是傳送者物件的實例) 您必須提供所產生的 EventArgs 類別所需的名稱。 這是使用 `[EventArgs]` 類別中的方法宣告上的屬性來完成 `Model` 。

例如：

```csharp
[BaseType (typeof (UINavigationControllerDelegate))]
[Model][Protocol]
public interface UIImagePickerControllerDelegate {
    [Export ("imagePickerController:didFinishPickingImage:editingInfo:"), EventArgs ("UIImagePickerImagePicked")]
    void FinishedPickingImage (UIImagePickerController picker, UIImage image, NSDictionary editingInfo);
}
```

上述宣告會產生 `UIImagePickerImagePickedEventArgs` 衍生自 EventArgs 的類別，並將兩個參數（和）封裝在一起 `UIImage` `NSDictionary` 。 產生器會產生下列結果：

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

這個屬性是用來允許產生器變更在類別中產生的事件或屬性的名稱。 當模型類別方法的名稱對模型類別有意義時，有時會很有用，但在原始類別中會看起來像是事件或屬性。

例如，會 `UIWebView` 使用中的下列位 `UIWebViewDelegate` ：

```csharp
[Export ("webViewDidFinishLoad:"), EventArgs ("UIWebView"), EventName ("LoadFinished")]
void LoadingFinished (UIWebView webView);
```

上述的會公開 `LoadingFinished` 為中的方法 `UIWebViewDelegate` ，但 `LoadFinished` 做為連接到中的事件 `UIWebView` ：

```csharp
var webView = new UIWebView (...);
webView.LoadFinished += delegate { Console.WriteLine ("done!"); }
```

<a name="ModelAttribute"></a>

### <a name="modelattribute"></a>ModelAttribute

當您將 `[Model]` 屬性套用至合約 API 中的型別定義時，如果使用者已覆寫類別中的方法，則執行時間會產生特殊的程式碼，只會在類別中將調用呈現給方法。 這個屬性通常會套用至包裝目標 C 委派類別的所有 Api。

<a name="NoDefaultValueAttribute"></a>

### <a name="nodefaultvalueattribute"></a>NoDefaultValueAttribute

指定模型上的方法不提供預設的傳回值。

這適用于目標 c 執行時間，方法是回應 `false` 目標 c 執行時間要求，以判斷指定的選取器是否在這個類別中執行。

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface CameraDelegate {
    [Export ("shouldDisplayPopup"), NoDefaultValue]
    bool ShouldUploadToServer ();
}
```

另請參閱： [`[DefaultValue]`](#DefaultValueAttribute) 、 [`[DefaultValueFromArgument]`](#DefaultValueFromArgumentAttribute)  

<a name="ProtocolAttribute"></a>

## <a name="protocols"></a>通訊協定

C # 中沒有真正存在的目標 C 通訊協定概念。 通訊協定類似于 c # 介面，但不同之處在于，通訊協定中宣告的所有方法和屬性都必須由採用它的類別來執行。 相反地，某些方法和屬性是選擇性的。

某些通訊協定通常用來做為模型類別，這些類別應該使用屬性來系結 [`[Model]`](#ModelAttribute) 。

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

從 7.0 Xamarin 開始，已合併新的和改良的通訊協定系結功能。  任何包含屬性的定義 `[Protocol]` 實際上都會產生三個支援類別，以大幅改善取用通訊協定的方式：

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

**類別實** 提供完整的抽象類別，您可以覆寫的個別方法，並取得完整的型別安全。 但由於 c # 不支援多重繼承，因此在某些情況下，您可能需要不同的基類，但仍想要執行介面。

這是產生之 **介面定義** 的來源。  它是一個介面，其中包含通訊協定所需的所有方法。  這可讓想要執行您的通訊協定的開發人員只執行介面。  執行時間會自動將型別註冊為採用通訊協定。

請注意，介面只會列出必要的方法，且會公開選擇性方法。   這表示採用此通訊協定的類別會針對所需的方法取得完整的類型檢查，但必須使用匯出屬性 (手動使用匯出屬性，並比對選擇性通訊協定方法的簽章) 。

為了方便使用使用通訊協定的 API，系結工具也會產生可公開所有選用方法的擴充方法類別。   這表示只要您使用 API，您就可以將通訊協定視為具有所有方法。

如果您想要在您的 API 中使用通訊協定定義，您必須在您的 API 定義中撰寫基本架構空白介面。   如果您想要在 API 中使用 MyProtocol，您必須執行下列動作：

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

需要上述的原因是因為在系結時 `IMyProtocol` 不存在，因此您必須提供空的介面。

### <a name="adopting-protocol-generated-interfaces"></a>採用通訊協定產生的介面

當您執行為通訊協定產生的其中一個介面時（如下所示）：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

必要介面方法的執行會以正確的名稱匯出，因此相當於：

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

這適用于所有必要的通訊協定成員，但是有特殊的案例，選擇性的選擇器必須留意。

使用基類時，選擇性的通訊協定成員會視為相同：

```
public class UrlSessionDelegate : NSUrlSessionDownloadDelegate {
    public override void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
```

但是，在使用通訊協定介面時，必須新增 [Export]。 當您新增以覆寫開頭的自動完成時，IDE 會將它新增至自動完成。 

```
public class UrlSessionDelegate : NSObject, INSUrlSessionDownloadDelegate {
    [Export ("URLSession:downloadTask:didWriteData:totalBytesWritten:totalBytesExpectedToWrite:")]
    public void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
```

在執行時間，兩者之間會有些許的行為差異：

- 在) 範例中，基類 (NSUrlSessionDownloadDelegate 的使用者會提供所有必要和選擇性的選取器，並傳回合理的預設值。
- 在範例中， (INSUrlSessionDownloadDelegate 介面的使用者) 只會回應所提供的確切選取器。

有些罕見的類別在這裡可能會有不同的行為。 但在幾乎所有情況下，都可以放心使用。

### <a name="protocol-inlining"></a>通訊協定內嵌

當您系結的現有目標 C 類型已宣告為採用通訊協定時，您會想要直接內嵌通訊協定。 若要這樣做，只需將您的通訊協定宣告為沒有任何屬性的介面 [`[BaseType]`](#BaseTypeAttribute) ，並在介面的基底介面清單中列出通訊協定。

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

此區段中的屬性會套用至類型的個別成員：屬性和方法宣告。

### <a name="alignattribute"></a>AlignAttribute

用來指定屬性傳回類型的對齊值。 某些屬性會採用必須在 Xamarin (特定界限對齊的位址指標，例如，某些 `GLKBaseEffect` 屬性必須是16位元組對齊的) 。 您可以使用這個屬性來裝飾 getter，並使用對齊值。 `OpenTK.Vector4` `OpenTK.Matrix4` 當與目標 C api 整合時，這通常會搭配和類型使用。

範例：

```csharp
public interface GLKBaseEffect {
    [Export ("constantColor")]
    Vector4 ConstantColor { [Align (16)] get; set;  }
}
```

### <a name="appearanceattribute"></a>AppearanceAttribute

此 `[Appearance]` 屬性僅限 iOS 5，其中引進了外觀管理員。

`[Appearance]`屬性可以套用至任何參與架構的方法或屬性 `UIAppearance` 。 當這個屬性套用至類別中的方法或屬性時，它會指示系結產生器建立強型別外觀類別，用來為這個類別的所有實例或符合特定準則的實例進行樣式。

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

上述程式碼會在 UIToolbar 中產生下列程式碼：

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

### <a name="autoreleaseattribute-xamarinios-54"></a>AutoReleaseAttribute () 的 iOS 5。4

使用 `[AutoReleaseAttribute]` on 方法和屬性，將方法調用包裝在中的方法 `NSAutoReleasePool` 。

在目標 C 中，有一些方法會傳回已新增至預設值的值 `NSAutoReleasePool` 。 根據預設，這些會移至您的執行緒 `NSAutoReleasePool` ，但因為 Xamarin 也會保留物件的參考，只要 managed 物件存在，您可能不會想要在中保留額外的參考， `NSAutoReleasePool` 只會在您的執行緒將控制權傳給下一個執行緒之前耗盡，或回到主要迴圈。

這個屬性會在大量屬性上套用，例如，傳回已 `UIImage.FromFile` 加入至預設值之物件的) 範例 (`NSAutoReleasePool` 。 如果沒有這個屬性，只要您的執行緒沒有將控制權傳回給主要迴圈，就會保留影像。 如果您的往來文章是某種背景下載程式，而且一律處於作用中並等待工作，則不會釋出這些映射。

### <a name="forcedtypeattribute"></a>ForcedTypeAttribute

`[ForcedTypeAttribute]`會用來強制建立 managed 型別，即使傳回的非受控物件與系結定義中所述的型別不相符。

當標頭中所述的型別不符合原生方法的傳回型別時，這非常有用，例如，採用下列的目標 C 定義 `NSURLSession` ：

`- (NSURLSessionDownloadTask *)downloadTaskWithRequest:(NSURLRequest *)request`

它會清楚指出它會傳回 `NSURLSessionDownloadTask` 實例，但它會傳回 **returns** `NSURLSessionTask` ，這是一個超級類別，因此無法轉換成 `NSURLSessionDownloadTask` 。 由於我們是在型別安全的內容中，因此 `InvalidCastException` 會發生。

為了符合標頭描述並避免 `InvalidCastException` `[ForcedTypeAttribute]` 使用，會使用。

```csharp
[BaseType (typeof (NSObject), Name="NSURLSession")]
interface NSUrlSession {

    [Export ("downloadTaskWithRequest:")]
    [return: ForcedType]
    NSUrlSessionDownloadTask CreateDownloadTask (NSUrlRequest request);
}
```

`[ForcedTypeAttribute]`也會接受名為 `Owns` 且預設為的布林值 `false` `[ForcedType (owns: true)]` 。 擁有參數是用來遵循 **Core Foundation** 物件的 [擁有權原則](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html)。

`[ForcedTypeAttribute]`只有在參數、屬性和傳回值上才有效。

<a name="BindAsAttribute"></a>

### <a name="bindasattribute"></a>BindAsAttribute

`[BindAsAttribute]`允許系結 `NSNumber` ， `NSValue` 以及 `NSString` (列舉) 成更精確的 c # 類型。 屬性可以用來透過原生 API 建立更好、更精確的 .NET API。

您可以使用來裝飾傳回值) 、參數和屬性 (的方法 `BindAs` 。 唯一的限制是您的成員 **不得** 在 `[Protocol]` 或 [`[Model]`](#ModelAttribute) 介面內。

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

在內部，我們會進行 `bool?`  <->  `NSNumber` 和 `CGRect`  <->  `NSValue` 轉換。

目前支援的封裝類型為：

* `NSValue`
* `NSNumber`
* `NSString`

#### <a name="nsvalue"></a>NSValue

下列 c # 資料類型支援封裝自/into `NSValue` ：

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

下列 c # 資料類型支援封裝自/into `NSNumber` ：

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

[`[BindAs]`](#BindAsAttribute) 在 conjuntion 中搭配 [NSString 常數所支援](#enum-attributes) 的列舉，因此您可以建立更好的 .net API，例如：

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

`enum`  <->  `NSString` 只有當提供的列舉類型 [`[BindAs]`](#BindAsAttribute) 是[由 NSString 常數所支援](#enum-attributes)時，才會處理轉換。

#### <a name="arrays"></a>陣列

[`[BindAs]`](#BindAsAttribute) 也支援任何支援類型的陣列，您可以使用下列 API 定義作為範例：

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

`rects`參數將封裝至 `NSArray` ，其中包含 `NSValue` 每個 `CGRect` 和中的，而您將會取得 `CAScroll?` 使用傳回的包含之值所建立的陣列 `NSArray` `NSStrings` 。

<a name="BindAttribute"></a>

### <a name="bindattribute"></a>BindAttribute

`[Bind]`當屬性套用至方法或屬性宣告，而另一個套用至屬性中的個別 getter 或 setter 時，屬性有兩種用法。

用於方法或屬性時，屬性的效果 `[Bind]` 會產生叫用指定之選取器的方法。 但產生的產生的方法不會以 [`[Export]`](#ExportAttribute) 屬性裝飾，這表示它無法參與方法覆寫。 這通常會與用 `[Target]` 來執行目標 C 擴充方法的屬性搭配使用。

例如：

```csharp
public interface UIView {
    [Bind ("drawAtPoint:withFont:")]
    SizeF DrawString ([Target] string str, CGPoint point, UIFont font);
}
```

在 getter 或 setter 中使用時，在 `[Bind]` 產生屬性的 getter 和 Setter 目標-C 選取器名稱時，會使用屬性來改變程式碼產生器所推斷的預設值。 依預設，當您以名稱來標記屬性時 `fooBar` ，產生器會 `fooBar` 針對 getter 和 setter 產生匯出 `setFooBar:` 。 在少數情況下，目標 C 不會遵循此慣例，通常會將 getter 名稱變更為 `isFooBar` 。
您可以使用這個屬性來通知產生器。

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

僅適用于 Xamarin 6.3 和更新版本。

這個屬性可以套用至將完成處理常式做為最後一個引數的方法。

您可以 `[Async]` 在其最後一個引數為回呼的方法上使用屬性。  當您將此套用至方法時，系結產生器會產生具有尾碼的該方法版本 `Async` 。  如果回呼不接受任何參數，則傳回值會是 `Task` ，如果回呼接受參數，則結果會是 `Task<T>` 。

```csharp
[Export ("upload:complete:")]
[Async]
void LoadFile (string file, NSAction complete)
```

下列會產生此非同步方法：

```csharp
Task LoadFileAsync (string file);
```

如果回呼接受多個參數，您應該將 `ResultType` 或設 `ResultTypeName` 為，以指定將保留所有屬性之產生型別的預期名稱。

```csharp
delegate void OnComplete (string [] files, nint byteCount);

[Export ("upload:complete:")]
[Async (ResultTypeName="FileLoading")]
void LoadFiles (string file, OnComplete complete)
```

下列會產生這個非同步方法，其中 `FileLoading` 包含可同時存取和的 `files` 屬性 `byteCount` ：

```csharp
Task<FileLoading> LoadFile (string file);
```

如果回呼的最後一個參數是，則 `NSError` 產生的 `Async` 方法會檢查值是否不是 null，如果是這種情況，則產生的非同步方法會設定工作例外狀況。

```csharp
[Export ("upload:onComplete:")]
[Async]
void Upload (string file, Action<string,NSError> onComplete);
```

上述動作會產生下列非同步方法：

```csharp
Task<string> UploadAsync (string file);
```

發生錯誤時，產生的工作會將例外狀況設定為 `NSErrorException` 包裝產生的 `NSError` 。

#### <a name="asyncattributeresulttype"></a>AsyncAttribute

您可以使用這個屬性來指定傳回物件的值 `Task` 。   此參數會採用現有的型別，因此必須在其中一個核心 api 定義中定義。

#### <a name="asyncattributeresulttypename"></a>AsyncAttribute.ResultTypeName

您可以使用這個屬性來指定傳回物件的值 `Task` 。   此參數會採用您所需的型別名稱，產生器會產生一系列的屬性，回呼所採用的每個參數都有一個。

#### <a name="asyncattributemethodname"></a>AsyncAttribute。方法名稱

使用這個屬性可自訂產生的非同步方法名稱。   預設值是使用方法的名稱並附加 "Async" 文字，您可以使用它來變更這個預設值。

<a name="DesignatedInitializerAttribute"></a>

### <a name="designatedinitializerattribute"></a>DesignatedInitializerAttribute

當這個屬性套用至函式時，它會 `[DesignatedInitializer]` 在最後一個平臺元件中產生相同的。 這是為了協助 IDE 指出應該在子類別中使用哪一個函數。

這應該對應至的目標-C/clang 使用 `__attribute__((objc_designated_initializer))` 。

<a name="DisableZeroCopyAttribute"></a>

### <a name="disablezerocopyattribute"></a>DisableZeroCopyAttribute

這個屬性會套用至字串參數或字串屬性，並指示程式碼產生器不會針對此參數使用零複製字串封送處理，而是從 c # 字串建立新的 NSString 實例。
只有當您使用 `--zero-copy` 命令列選項或設定元件層級屬性，指示產生器使用零複製字串封送處理時，才需要在字串上使用此屬性 `ZeroCopyStringsAttribute` 。

在以目標 C 將屬性宣告為 `retain` 或 `assign` 屬性（而非屬性）的情況下，這是必要的 `copy` 。 這些通常會發生在協力廠商程式庫中，開發人員誤「優化」。 一般而言， `retain` 或 `assign` `NSString` 屬性不正確，因為 `NSMutableString` 或的使用者衍生類別 `NSString` 可能會改變字串的內容，而不需要程式庫程式碼，而會稍微中斷應用程式。 這通常是因為過早的優化而發生。

以下顯示目標 C 中的兩個這類屬性：

```csharp
@property(nonatomic,retain) NSString *name;
@property(nonatomic,assign) NSString *name2;
```

<a name="DisposeAttribute"></a>

### <a name="disposeattribute"></a>DisposeAttribute

當您將套用 `[DisposeAttribute]` 至類別時，您會提供將加入至 `Dispose()` 類別之方法執行的程式碼片段。

因為 `Dispose` 方法是由和工具自動產生 `bmac-native` `btouch-native` ，所以您必須使用 `[Dispose]` 屬性在產生的方法執行中插入一些程式碼 `Dispose` 。

例如：

```csharp
[BaseType (typeof (NSObject))]
[Dispose ("if (OpenConnections > 0) CloseAllConnections ();")]
interface DatabaseConnection {
}
```

<a name="ExportAttribute"></a>

### <a name="exportattribute"></a>ExportAttribute

`[Export]`屬性是用來將方法或屬性標示為要公開至目標 C 執行時間。 這個屬性是在系結工具與實際的 Xamarin 和 Xamarin 執行時間之間共用。 針對方法，會將參數逐字傳遞給產生的程式碼，而針對屬性，會根據基底宣告產生 getter 和 setter 匯出 (如需有關 [`[BindAttribute]`](#BindAttribute) 如何改變系結工具) 的行為的詳細資訊，請參閱中的一節。

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

[選取器](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Selector.html)代表所系結之基礎目標 C 方法或屬性的名稱。

#### <a name="exportattributeargumentsemantic"></a>ExportAttribute.ArgumentSemantic

<a name="FieldAttribute"></a>

### <a name="fieldattribute"></a>FieldAttribute

這個屬性是用來將 C 全域變數公開為隨選載入的欄位，並公開給 c # 程式碼。 通常，若要取得 C 或目標 C 中定義的常數值，而且可能是某些 Api 中所使用的標記，或是其值不透明，而且必須依使用者程式碼使用的值，通常是必要的。

語法：

```csharp
public class FieldAttribute : Attribute {
    public FieldAttribute (string symbolName);
    public FieldAttribute (string symbolName, string libraryName);
    public string SymbolName { get; set; }
    public string LibraryName { get; set; }
}
```

`symbolName`是要連結的 C 符號。 根據預設，這會從已從定義類型之命名空間推斷出其名稱的程式庫載入。 如果這不是已查閱符號的程式庫，您應該傳遞 `libraryName` 參數。 如果您要連結靜態程式庫，請使用 `__Internal` 做為 `libraryName` 參數。

產生的屬性一律為靜態。

以 Field 屬性標記的屬性可以是下列類型：

* `NSString`
* `NSArray`
* `nint` / `int` / `long`
* `nuint` / `uint` / `ulong`
* `nfloat` / `float`
* `double`
* `CGSize`
* `System.IntPtr`
* 列舉

[NSString 常數](#enum-attributes)所支援的列舉不支援 setter，但可以視需要手動系結。

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

`[Internal]`屬性可以套用至方法或屬性，而且其效果是以 c # 關鍵字標示產生的程式碼， `internal` 讓程式碼只能在產生的元件中程式碼存取。 這通常是用來隱藏太低層級的 Api，或提供您想要改善的 api，或針對產生器不支援的 Api，並需要一些手動編碼的 api。

當您設計系結時，通常會使用這個屬性來隱藏方法或屬性，並為方法或屬性提供不同的名稱，然後在您的 c # 互補支援檔案中，您可以加入公開基礎功能的強型別包裝函式。

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

這個屬性會將屬性的支援欄位標示為具有 .NET 屬性的批註 `[ThreadStatic]` 。 如果欄位是執行緒靜態變數，這就很有用。

### <a name="marshalnativeexceptions-xamarinios-606"></a>MarshalNativeExceptions (Xamarin 6.0.6) 

這個屬性會讓方法支援原生 (目標-C) 例外狀況。
`objc_msgSend`調用會透過自訂 trampoline 來攔截 ObjectiveC 例外狀況，並將其封送處理為 managed 例外狀況，而不是直接呼叫。

目前只支援幾個簽章 `objc_msgSend` (您會發現當使用系結之應用程式的原生連結失敗，並) 缺少的 monotouch_ *_objc_msgSend* 符號時，不支援簽章，但您可以在要求中新增更多的簽名碼。

### <a name="newattribute"></a>NewAttribute

這個屬性會套用至方法和屬性，讓產生器在宣告 `new` 前面產生關鍵字。

當在基類中已經存在的子類別中引進相同的方法或屬性名稱時，會使用它來避免編譯器警告。

<a name="NotificationAttribute"></a>

### <a name="notificationattribute"></a>NotificationAttribute

您可以將此屬性套用至欄位，讓產生器產生強型別協助程式通知類別。

您可以使用這個屬性，而不會有不含承載之通知的引數，或您可以指定 `System.Type` 參考 API 定義中的另一個介面（通常名稱結尾為 "EventArgs"）。 產生器會將介面轉換為子類別的類別， `EventArgs` 並包含此處列出的所有屬性。 此 [`[Export]`](#ExportAttribute) 屬性應該用於 `EventArgs` 類別中，以列出用來查閱目標 C 字典以提取值的索引鍵名稱。

例如：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

上述程式碼將會使用下列方法來產生嵌套類別 `MyClass.Notifications` ：

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
      public static NSObject ObserveDidStart (NSObject objectToObserve, EventHandler<NSNotificationEventArgs> handler)
   }
}
```

然後，您程式碼的使用者可以使用如下的程式碼，輕鬆地訂閱張貼至 [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) 的通知：

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

或者，您可以呼叫 [NSNotification DefaultCenter](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) ，並傳遞權杖。 如果您的通知包含參數，您應該指定 helper `EventArgs` 介面，如下所示：

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

上述程式會產生 `MyScreenChangedEventArgs` 具有和屬性的類別， `ScreenX` 該類別 `ScreenY` 會使用索引鍵和分別從 [NSNotification](xref:Foundation.NSNotification.UserInfo) 中提取資料， `ScreenXKey` 並套用 `ScreenYKey` 適當的轉換。 `[ProbePresence]`如果已在中設定索引鍵，則會使用屬性來探查 `UserInfo` ，而不是嘗試將值解壓縮。 這適用于索引鍵是否存在的情況下， (通常會) 布林值的值。

這可讓您撰寫如下的程式碼：

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

在某些情況下，沒有與字典上傳遞的值相關聯的常數。  Apple 有時候會使用公用符號常數，有時會使用字串常數。  根據預設， [`[Export]`](#ExportAttribute) 您提供之類別中的屬性 `EventArgs` 會使用指定的名稱做為要在執行時間查閱的公用符號。  如果不是這種情況，則應該以字串常數的形式來查閱，然後將 `ArgumentSemantic.Assign` 值傳遞至 Export 屬性。

**Xamarin. iOS 8.4 的新功能**

有時候，通知的存留期不會有任何引數，因此 [`[Notification]`](#NotificationAttribute) 可以接受不含引數的使用。  但有時候會引進通知的參數。  若要支援此案例，可以多次套用此屬性。

如果您正在開發系結，而您想要避免中斷現有的使用者程式碼，您可以從下列程式碼中開啟現有的通知：

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}
```

在列出通知屬性兩次的版本中，如下所示：

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

當此屬性套用至屬性時，會將屬性標示為允許將值指派給該屬性 `null` 。 這只適用于參考型別。

當它套用至方法簽章中的參數時，表示指定的參數可以是 null，而且不應該執行任何檢查來傳遞 `null` 值。

如果參考型別沒有這個屬性（attribute），系結工具將會針對所指派的值產生檢查，然後將其傳遞至目標 C，並且會產生檢查， `ArgumentNullException` 如果指派的值為，則會擲回 `null` 。

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

您可以使用這個屬性來指示系結產生器，這個特定方法的系結應以 `override` 關鍵字標記。

### <a name="presnippetattribute"></a>PreSnippetAttribute

您可以使用這個屬性來插入一些程式碼，這些程式碼會在輸入參數經過驗證之後，但在程式碼呼叫至目標 C 之前插入。

範例：

```csharp
[Export ("demo")]
[PreSnippet ("var old = ViewController;")]
void Demo ();
```

### <a name="prologuesnippetattribute"></a>PrologueSnippetAttribute

您可以使用這個屬性，插入要在產生的方法中驗證任何參數之前插入的一些程式碼。

範例：

```csharp
[Export ("demo")]
[Prologue ("Trace.Entry ();")]
void Demo ();
```

### <a name="postgetattribute"></a>PostGetAttribute

指示系結產生器從這個類別叫用指定的屬性，以從中提取值。

這個屬性通常用來重新整理指向參考物件的快取，而這些物件會保持參考物件圖形。 通常會顯示在具有新增/移除等作業的程式碼中。 使用這個方法，如此一來，加入或移除內部快取之後，就會更新內部快取，以確保我們會將 managed 參考保留給實際使用中的物件。 這是可能的，因為系結工具會為指定系結中的所有參考物件產生支援欄位。

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

在此情況下， `Dependencies` 將會在新增或移除物件的相依性之後叫用屬性 `NSOperation` ，以確保我們有代表實際載入物件的圖表，以避免記憶體流失和記憶體損毀。

### <a name="postsnippetattribute"></a>PostSnippetAttribute

您可以使用這個屬性插入一些 c # 原始程式碼，以在程式碼叫用基礎 C 方法之後插入

範例：

```csharp
[Export ("demo")]
[PostSnippet ("if (old != null) old.DemoComplete ();")]
void Demo ();
```

### <a name="proxyattribute"></a>ProxyAttribute

這個屬性會套用至傳回值，以將它們標示為 proxy 物件。 某些目標 C Api 會傳回無法與使用者系結區別的 proxy 物件。 這個屬性的效果是將物件標示為 `DirectBinding` 物件。 在 Xamarin 的案例中，您可以看到 [有關此錯誤的討論](https://bugzilla.novell.com/show_bug.cgi?id=670844)。

### <a name="retainlistattribute"></a>RetainListAttribute

指示產生器保留參數的 managed 參考，或移除參數的內部參考。 這是用來保留參考的物件。

語法：

```csharp
public class RetainListAttribute: Attribute {
     public RetainListAttribute (bool doAdd, string listName);
}
```

如果的值 `doAdd` 是 true，則會將參數加入至 `__mt_{0}_var List<NSObject>;` 。 其中 `{0}` 會以指定的取代 `listName` 。 您必須在 API 的互補部分類別中，宣告此支援欄位。

如需範例，請參閱 [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) 和 [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="releaseattribute-xamarinios-60"></a>ReleaseAttribute () 的 iOS 6。0

這可以套用至傳回型別，以表示產生器應該 `Release` 先在物件上呼叫，再將它傳回。 只有當方法提供您保留的物件 (而不是 autoreleased 物件（這是最常見的案例）時，才需要這麼做) 

範例：

```csharp
[Export ("getAndRetainObject")]
[return: Release ()]
NSObject GetAndRetainObject ();
```

此外，此屬性會傳播至產生的程式碼，因此，在從這類函式傳回至目標的情況下，Xamarin iOS 執行時間知道它必須保留物件。

### <a name="sealedattribute"></a>SealedAttribute

指示產生器將產生的方法標示為密封。 如果未指定此屬性，則預設值是根據其他屬性的使用方式， (虛擬方法、抽象方法或覆寫來產生虛擬方法) 。

<a name="StaticAttribute"></a>

### <a name="staticattribute"></a>StaticAttribute

當 `[Static]` 屬性套用至方法或屬性時，會產生靜態方法或屬性。 如果未指定此屬性，則產生器會產生實例方法或屬性。

### <a name="transientattribute"></a>TransientAttribute

您可以使用此屬性來旗標其值為暫時性的屬性，也就是由 iOS 暫時建立但不長期存留的物件。 當這個屬性套用至屬性時，產生器不會建立這個屬性的支援欄位，這表示 managed 類別不會保留物件的參考。

<a name="WrapAttribute"></a>

### <a name="wrapattribute"></a>WrapAttribute

在 [Xamarin]/[Xamarin] 系結的設計中， `[Wrap]` 屬性是用來包裝弱型別物件與強型別物件。 大部分的情況下都是使用目標 C 委派物件，這些物件通常宣告為型別 `id` 或 `NSObject` 。 Xamarin 和 Xamarin 所使用的慣例是將這些委派或資料來源公開為類型 `NSObject` ，並使用「弱式」和公開名稱的慣例來命名。 `id delegate`來自目標 C 的屬性會公開為 `NSObject WeakDelegate { get; set; }` API 合約檔案中的屬性。

但指派給此委派的值通常是強型別，因此，我們會呈現強型別並套用 `[Wrap]` 屬性，這表示使用者可以選擇使用弱式型別，如果他們需要一些精細的控制，或者他們需要對低層級的訣竅進行操作，也可以針對大部分的工作使用強型別屬性。

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

這就是使用者使用強型別版本的方式，請注意，使用者會利用 c # 的型別系統，並使用 override 關鍵字來宣告他的意圖，而不需要以手動方式裝飾方法 `[Export]` ，因為我們是在使用者的系結中工作：

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

當屬性套用至 `[Wrap]` 以屬性裝飾之類型內的方法時 `[Category]` ，您必須包含 `This` 做為第一個引數，因為正在產生擴充方法。 例如：

```csharp
[Wrap ("Write (This, image, options?.Dictionary, out error)")]
bool Write (CIImage image, CIImageRepresentationOptions options, out NSError error);
```

`[Wrap]` `virtual` 如果您需要成員，您可以將所產生的成員 `virtual` 設定為 `true` 選擇性參數，而不是預設所產生的成員 `isVirtual` 。

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
如此一來，就可以完全掌控它們，並且視需要調整程式碼。
例如，請考慮使用智慧型列舉的下列 API 定義：

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

本節說明您可以套用至方法定義中之參數的屬性，以及 `[NullAttribute]` 套用至整個屬性的。

<a name="BlockCallback"></a>

### <a name="blockcallback"></a>BlockCallback

這個屬性會套用至 c # 委派宣告中的參數類型，以通知系結器，問題中的參數符合目標 C 區塊呼叫慣例，而且應該以這種方式封送處理。

這通常用於在目標 C 中定義如下的回呼：

```objc
typedef returnType (^SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另請參閱： [CCallback](#CCallback)。

<a name="CCallback"></a>

### <a name="ccallback"></a>CCallback

這個屬性會套用至 c # 委派宣告中的參數類型，以通知系結器，問題中的參數符合 C ABI 函式指標呼叫慣例，而且應該以這種方式封送處理。

這通常用於在目標 C 中定義如下的回呼：

```objc
typedef returnType (*SomeTypeDefinition) (int parameter1, NSString *parameter2);
```

另請參閱： [BlockCallback](#BlockCallback)。

### <a name="params"></a>Params

您可以使用 `[Params]` 方法定義之最後一個陣列參數上的屬性，讓產生器在定義中插入「params」。   這可讓系結輕鬆地允許選擇性參數。

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

這樣做的優點是，它不會要求使用者只為了傳遞元素而建立陣列。

<a name="plainstring"></a>

### <a name="plainstring"></a>PlainString

您可以 `[PlainString]` 在字串參數前面使用屬性，指示系結產生器將字串傳遞為 C 字串，而不是將參數傳遞為 `NSString` 。

大部分的目標 C Api 都會使用 `NSString` 參數，但少數的 api 會公開 `char *` 傳遞字串的 api，而不是 `NSString` 變化。
`[PlainString]`在這些情況下使用。

例如，下列的目標 C 宣告：

```csharp
- (void) setText: (NSString *) theText;
- (void) logMessage: (char *) message;
```

應系結如下：

```csharp
[Export ("setText:")]
void SetText (string theText);

[Export ("logMessage:")]
void LogMessage ([PlainString] string theText);
```

### <a name="retainattribute"></a>RetainAttribute

指示產生器保留指定參數的參考。 產生器會提供此欄位的備份存放區，您也可以指定名稱 (`WrapName` 要儲存值的) 。 這對於保存傳遞為參數的 managed 物件的參考，以及當您知道目標 C 只會保存物件的複本時很有用。 例如，類似的 API `SetDisplay (SomeObject)` 會使用此屬性，因為 SetDisplay 可能一次只能顯示一個物件。 如果您需要追蹤一個以上的物件 (例如，針對類似堆疊的 API) 您會使用 `[RetainList]` 屬性。

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

如果的值 `doAdd` 是 true，則會將參數加入至 `__mt_{0}_var List<NSObject>` 。 其中 `{0}` 會以指定的取代 `listName` 。 您必須在 API 的互補部分類別中，宣告此支援欄位。

如需範例，請參閱 [foundation.cs](https://github.com/mono/maccore/blob/master/src/foundation.cs) 和 [NSNotificationCenter.cs](https://github.com/mono/maccore/blob/master/src/Foundation/NSNotificationCenter.cs)

### <a name="transientattribute"></a>TransientAttribute

這個屬性會套用至參數，而且只會在從目標 C 轉換成 c # 時使用。  在這些轉換期間，會將不同的目標 C `NSObject` 參數包裝成物件的 managed 標記法。

執行時間會取得原生物件的參考，並保留參考直到物件的最後一個 managed 參考消失，而且 GC 有機會執行。

在少數的情況下，c # 執行時間不需要保留原生物件的參考，是很重要的。  當基礎機器碼將特殊行為附加至參數的生命週期時，有時會發生這種情況。  例如：參數的函式會執行一些清除動作，或處置一些寶貴的資源。

這個屬性會告知執行時間，您想要在可能的情況下，從您覆寫的方法傳回目標 C 時處置物件。

規則很簡單：如果執行時間必須從原生物件建立新的 managed 標記法，則在函式的結尾，將會卸載原生物件的保留計數，且會清除受管理物件的控制碼屬性。   這表示，如果您保留受管理物件的參考，該參考將會變得無用 (在其上叫用方法將會擲回例外狀況) 。

如果未建立傳遞的物件，或是物件已經有未處理的 managed 標記法，就不會進行強制處置。 

## <a name="property-attributes"></a>Property 屬性

<a name="NotImplementedAttribute"></a>

### <a name="notimplementedattribute"></a>NotImplementedAttribute

這個屬性是用來支援目標 C 用法，其中具有 getter 的屬性是在基類中導入的，而且可變動的子類別會引進 setter。

因為 c # 不支援此模型，所以基類必須同時具有 setter 和 getter，而且子類別可以使用 [OverrideAttribute](#OverrideAttribute)。

這個屬性只會在屬性 setter 中使用，並且用來支援以 C 為目標的可變用法。

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

將 `NSString` 常數對應至列舉值是建立更好的 .NET API 的簡單方法。 強烈

* 藉由 **只** 顯示 API 的正確值，讓程式碼自動完成更有用。
* 加入型別安全，您無法 `NSString` 在不正確的內容中使用另一個常數; 以及
* 允許隱藏一些常數，使程式碼完成顯示較短的 API 清單，而不會遺失功能。

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

從上述系結定義中，產生器會建立 `enum` 本身，也會建立 `*Extensions` 靜態型別，其中包含列舉值與常數之間的雙向轉換方法 `NSString` 。 這表示即使開發人員不是 API 的一部分，它們仍可供開發人員使用。

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

您可以使用這個屬性裝飾 **一個** 列舉值。 如果列舉值不知道，這將會成為傳回的常數。

從上述範例：

```csharp
var x = (NSRunLoopMode) 99;
Call (x.GetConstant ()); // NSDefaultRunLoopMode will be used
```

如果沒有裝飾的列舉值，則 `NotSupportedException` 會擲回。

### <a name="errordomainattribute"></a>ErrorDomainAttribute

錯誤碼會系結為列舉值。 其中通常會有一個錯誤網域，且不一定會很容易找出哪一個是套用 (，或者即使) 有一個。

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

然後您可以呼叫擴充方法 `GetDomain` ，以取得任何錯誤的網域常數。

### <a name="fieldattribute"></a>FieldAttribute

這是在 `[Field]` 類型內用於常數的相同屬性。 它也可以在列舉內部使用，以對應具有特定常數的值。

`null`如果 `null` `NSString` 指定了常數，則可以使用值來指定應該傳回的列舉值。

從上述範例：

```csharp
var constant = NSRunLoopMode.NewInWatchOS3; // will be null in watchOS 2.x
Call (NSRunLoopModeExtensions.GetValue (constant)); // will return 1000
```

如果不 `null` 存在任何值，則 `ArgumentNullException` 會擲回。

## <a name="global-attributes"></a>全域屬性

全域屬性可以使用 `[assembly:]` 屬性修飾詞（例如）來套用 [`[LinkWithAttribute]`](#LinkWithAttribute) ，也可以使用於任何地方，例如 [`[Lion]`](#SinceAndLionAttributes) 和 [`[Since]`](#SinceAndLionAttributes) 屬性。

<a name="LinkWithAttribute"></a>

### <a name="linkwithattribute"></a>LinkWithAttribute

這是元件層級的屬性，可讓開發人員指定重複使用系結程式庫所需的連結旗標，而不需要強制程式庫的取用者手動設定 gcc_flags 和傳遞至程式庫的額外 mtouch 引數。

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

這個屬性會在元件層級套用，例如，這是 [CorePlot](https://github.com/mono/monotouch-bindings/tree/master/CorePlot) 系結所使用的內容：

```csharp
[assembly: LinkWith ("libCorePlot-CocoaTouch.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, Frameworks = "CoreGraphics QuartzCore", ForceLoad = true)]
```

當您使用屬性時， `[LinkWith]` 會將指定的 `libraryName` 內嵌到產生的元件中，讓使用者可以傳送包含非受控相依性的單一 DLL，以及適當地從 Xamarin 使用程式庫所需的命令列旗標。

您也可以不提供 `libraryName` ，在這種情況下， `LinkWith` 屬性只能用來指定其他連結器旗標：

``` csharp
[assembly: LinkWith (LinkerFlags = "-lsqlite3")]
```

#### <a name="linkwithattribute-constructors"></a>LinkWithAttribute 函式

這些函式可讓您指定程式庫以連結並內嵌至結果元件、程式庫支援的支援目標，以及程式庫所需的任何選用程式庫旗標。

請注意， `LinkTarget` 引數是由 Xamarin 推斷，不需要設定。

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

`ForceLoad`屬性是用來決定是否要 `-force_load` 使用連結旗標來連結原生程式庫。 目前，這應該一律為 true。

#### <a name="linkwithattributeframeworks"></a>LinkWithAttribute 架構

如果要系結的程式庫對任何架構都是必要的， (除了 `Foundation` 和 `UIKit`) 之外，您應該將 `Frameworks` 屬性設定為包含以空格分隔的必要平臺架構清單的字串。 例如，如果您要系結需要和的連結 `CoreGraphics` 庫 `CoreText` ，您可以將 `Frameworks` 屬性設定為 `"CoreGraphics CoreText"` 。

#### <a name="linkwithattributeiscxx"></a>LinkWithAttribute.IsCxx

如果產生的可執行檔需要使用 c + + 編譯器來編譯，而不是使用預設值（C 編譯器），請將此屬性設定為 true。 如果您要系結的程式庫是以 c + + 撰寫的，請使用此程式庫。

#### <a name="linkwithattributelibraryname"></a>LinkWithAttribute. >libraryname

要組合的非受控程式庫名稱。 這是副檔名為 ". a" 的檔案，而且可以包含多個平臺的物件程式碼 (例如，適用于模擬器的 ARM 和 x86) 。

舊版的 Xamarin `LinkTarget` 會檢查屬性，以判斷程式庫支援的平臺，但現在會自動偵測到，而且會 `LinkTarget` 忽略屬性。

#### <a name="linkwithattributelinkerflags"></a>LinkWithAttribute.LinkerFlags

`LinkerFlags`字串會提供方法，讓作者在將原生程式庫連結至應用程式時，指定所需的任何其他連結器旗標。

例如，如果原生程式庫需要 libxml2 和 zlib，您可以將 `LinkerFlags` 字串設定為 `"-lxml2 -lz"` 。

#### <a name="linkwithattributelinktarget"></a>LinkWithAttribute. LinkTarget

舊版的 Xamarin `LinkTarget` 會檢查屬性，以判斷程式庫支援的平臺，但現在會自動偵測到，而且會 `LinkTarget` 忽略屬性。

#### <a name="linkwithattributeneedsgccexceptionhandling"></a>LinkWithAttribute.NeedsGccExceptionHandling

如果您要連結的程式庫需要 GCC 例外狀況處理程式庫 (gcc_eh，請將此屬性設定為 true) 

#### <a name="linkwithattributesmartlink"></a>LinkWithAttribute. SmartLink

`SmartLink`屬性應該設定為 true，讓 Xamarin 能判斷是否 `ForceLoad` 需要。

#### <a name="linkwithattributeweakframeworks"></a>LinkWithAttribute.WeakFrameworks

`WeakFrameworks`屬性的運作方式與 `Frameworks` 屬性相同，不同之處在于在連結時， `-weak_framework` 會針對每個列出的架構將規範傳遞給 gcc。

`WeakFrameworks` 讓程式庫和應用程式可以弱連結平臺架構，讓它們可以選擇性地使用它們（如果有的話），但如果您的程式庫是要在較新版本的 iOS 上新增額外的功能，這會很有用。 如需弱式連結的詳細資訊，請參閱 Apple 的 [弱式連結](https://developer.apple.com/library/mac/#documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WeakLinking.html)檔。

弱式連結的絕佳候選項目就 `Frameworks` 像是帳戶、 `CoreBluetooth` 、、和， `CoreImage` `GLKit` `NewsstandKit` `Twitter` 因為它們只能在 iOS 5 中使用。

<a name="SinceAndLionAttributes"></a>

### <a name="sinceattribute-ios-and-lionattribute-macos"></a>SinceAttribute (iOS) 和 LionAttribute (macOS) 

您可以使用 `[Since]` 屬性來標示 api，使其在特定時間點引進。 只有當基礎類別、方法或屬性無法使用時，屬性才會用來標記可能會造成執行時間問題的類型和方法。

語法：

```csharp
public SinceAttribute : Attribute {
     public SinceAttribute (byte major, byte minor);
     public byte Major, Minor;
}
```

它通常不會套用至列舉、條件約束或新結構，因為它們在具有舊版作業系統的裝置上執行時，不會造成執行階段錯誤。

套用至類型的範例：

```csharp
// Type introduced with iOS 4.2
[Since (4,2)]
[BaseType (typeof (UIPrintFormatter))]
interface UIViewPrintFormatter {
    [Export ("view")]
    UIView View { get; }
}
```

套用至新成員的範例：

```csharp
[BaseType (typeof (UIViewController))]
public interface UITableViewController {
    [Export ("tableView", ArgumentSemantic.Retain)]
    UITableView TableView { get; set; }

    [Since (3,2)]
    [Export ("clearsSelectionOnViewWillAppear")]
    bool ClearsSelectionOnViewWillAppear { get; set; }
```

屬性的套用 `[Lion]` 方式與 Lion 所引進的類型相同。 使用的原因 `[Lion]` 與 ios 中使用的特定版本號碼相同，因為 ios 的修改頻率很低，而主要 OS X 版本很少發生，而且比起其版本號碼，codename 作業系統的方式更容易記住

### <a name="adviceattribute"></a>AdviceAttribute

使用此屬性可為開發人員提供有關其他 Api 的提示，這些 Api 可能更方便他們使用。   例如，如果您提供強型別版本的 API，您可以在弱型別屬性上使用此屬性，將開發人員導向至更好的 API。

這項屬性的資訊會顯示在檔和工具中，可為使用者提供如何改進的建議。

### <a name="requiressuperattribute"></a>RequiresSuperAttribute

這是屬性的特製化子類別 `[Advice]` ，可以用來提示開發人員覆寫方法， **需要** 呼叫基底 (覆寫) 方法。

這會對應到 `clang`[`__attribute__((objc_requires_super))`](https://clang.llvm.org/docs/AttributeReference.html#objc-requires-super)

### <a name="zerocopystringsattribute"></a>ZeroCopyStringsAttribute

僅適用于 Xamarin 5.4 和更新版本。

此屬性會指示產生器，此特定程式庫的系結 (如果搭配 `[assembly:]`) 或類型套用時，應使用快速的零複製字串封送處理。 這個屬性相當於將命令列選項傳遞給產生器 `--zero-copy` 。

針對字串使用零複製時，產生器會有效地使用與目標 C 相同的 c # 字串，而不會建立新的 `NSString` 物件，並避免將資料從 c # 字串複製到目標 C 字串。 使用零份複製字串的唯一缺點是，您必須確定您所包裝的任何字串屬性（property）會標示為 `retain` 或 `copy` 已 `[DisableZeroCopy]` 設定屬性（attribute）。 這是必要的，因為零複製字串的控制碼是在堆疊上配置的，而且在函式傳回時無效。

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

使用 Xamarin. iOS 8.0，我們引進了可輕鬆建立包裝的強型別類別的支援 `NSDictionaries` 。

雖然一律可以搭配使用 [DictionaryContainer](xref:Foundation.DictionaryContainer) 資料類型與手動 API，但這麼做會比較簡單。  如需詳細資訊，請參閱 [呈現強型別](~/cross-platform/macios/binding/objective-c-libraries.md#Surfacing_Strong_Types)。

<a name="StrongDictionary"></a>

### <a name="strongdictionary"></a>StrongDictionary

當這個屬性套用至介面時，產生器會產生與衍生自 [DictionaryContainer](xref:Foundation.DictionaryContainer) 之介面相同名稱的類別，並將介面中定義的每個屬性轉換成字典的強型別 getter 和 setter。

這會自動產生可以從現有的 `NSDictionary` 或已建立新的類別來具現化的類別。

這個屬性會接受一個參數，也就是類別的名稱，其中包含用來存取字典上元素的索引鍵。   依預設，介面中的每個屬性（attribute）都會查閱指定型別中具有尾碼 "Key" 之名稱的成員。

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

在上述案例中， `MyOption` 類別會產生的字串屬性 `Name` ，將使用 `MyOptionKeys.NameKey` 做為字典中的索引鍵來取出字串。   和會使用 `MyOptionKeys.AgeKey` 做為字典中的索引鍵，以取得 `NSNumber` 包含 int 的。

如果您想要使用不同的金鑰，您可以在屬性上使用 export 屬性，例如：

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

|C # 介面類別型|`NSDictionary` 儲存體類型|
|---|---|
|`bool`|`Boolean` 儲存在 `NSNumber`|
|列舉值|儲存在中的整數 `NSNumber`|
|`int`|儲存在中的32位整數 `NSNumber`|
|`uint`|32位不帶正負號的整數，儲存在 `NSNumber`|
|`nint`|`NSInteger` 儲存在 `NSNumber`|
|`nuint`|`NSUInteger` 儲存在 `NSNumber`|
|`long`|儲存在中的64位整數 `NSNumber`|
|`float`|32位整數，儲存為 `NSNumber`|
|`double`|64位整數，儲存為 `NSNumber`|
|`NSObject` 和子類別|`NSObject`|
|`NSDictionary`|`NSDictionary`|
|`string`|`NSString`|
|`NSString`|`NSString`|
|C `Array` # `NSObject`|`NSArray`|
|C # `Array` 的列舉|`NSArray` 包含 `NSNumber` 值|
