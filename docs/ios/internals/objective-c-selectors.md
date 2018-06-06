---
title: Objective C Xamarin.iOS 中的選取器
description: 本文將討論如何互動 Objective C 選取器，從 C#。 說明如何叫用的選取器和技術考量這麼做時必須採取納入考量。
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 25276851879ba828361d3236cbf7896cf748588c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787038"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Objective C Xamarin.iOS 中的選取器

Objective C 語言根據*選取器*。 選取器是可以傳送至物件的訊息或*類別*。 [Xamarin.iOS](~/ios/internals/api-design/index.md)對應執行個體選擇器執行個體方法和類別的靜態方法的選取器。

不同於標準 C 函式 （和 c + + 成員函式一樣)，您無法直接叫用選取器使用[P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/)。
(*擱置在一旁*： 理論上您可以使用 P/Invoke 非虛擬的 c + + 成員函式，但您需要擔心每個編譯器名稱修飾 （name-mangling），這是更忽略痛苦世界。)相反地，會傳送至 OBJECTIVE-C 類別選取器，或執行個體使用[`objc_msgSend`函式](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend)。

您可能會發現[上 OBJECTIVE-C 傳訊此有幫助指南](http://developer.apple.com/iphone/library/documentation/cocoa/conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html)很有用。

<a name="Example" />

## <a name="example"></a>範例

假設您想要叫用[-[NSString sizeWithFont:forWidth:lineBreakMode:]](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:)選取器。
（從 Apple 文件） 宣告為：

```csharp
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

-  傳回型別是*CGSize*統一的 api。
-  *字型*參數是[UIFont](https://developer.xamarin.com/api/type/UIKit.UIFont/) (和類型 （間接） 衍生自[NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) )，因此會對應到和[System.IntPtr](https://developer.xamarin.com/api/type/System.IntPtr/) 。
-  *寬度*參數， *CGFloat* ，對應至*nfloat*。
-  *LineBreakMode*參數， *UILineBreakMode* ，具有已繫結中做為 Xamarin.iOS [UILineBreakMode 列舉](https://developer.xamarin.com/api/type/UIKit.UILineBreakMode/)。


全部整合，以及我們想要符合的 objc_msgSend 宣告：

```csharp
CGSize objc_msgSend(IntPtr target, IntPtr selector,
    IntPtr font, nfloat width, UILineBreakMode mode);
```

我們需要將它宣告為：

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

一旦宣告之後，我們可以叫用它一旦有適當的參數：

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode);
```

傳回的值便是大小小於 8 位元組結構 (喜歡舊版`SizeF`使用，之後再切換至統一的 Api) 方式但損毀裝置模擬器上執行上述程式碼。 因此，要呼叫傳回的值少於 8 位元的大小，因此我們選取器*也*需要宣告`objc_msgSend_stret()`函式：

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

就會成為引動過程：

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode);
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode);
```


<a name="Invoking_a_Selector" />

## <a name="invoking-a-selector"></a>叫用選取器

叫用選取器有三個步驟：

1.  取得選取器目標。
1.  取得選取器名稱。
1.  呼叫 objc_msgSend() 與適當的引數。


<a name="Selector_Targets" />

### <a name="selector-targets"></a>選取器目標

物件執行個體或 OBJECTIVE-C 類別選取器目標。 如果目標執行個體，並且繫結的 Xamarin.iOS 類型的來源，使用[ObjCRuntime.INativeObject.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/)屬性。

如果目標類別，請使用[ObjCRuntime.Class](https://developer.xamarin.com/api/type/ObjCRuntime.Class/)若要取得類別的執行個體的參考，然後使用[Class.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/)屬性。


<a name="Selector_Names" />

### <a name="selector-names"></a>選取器名稱

選取器名稱會在 Apple 文件中列出的。 例如， [UIKit NSString 擴充方法](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html)包含[sizeWithFont:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:)和[sizeWithFont:forWidth:lineBreakMode:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:)。 內嵌和尾端冒號都很重要，並選取器名稱的一部分。

選取器名稱之後，您可以建立[ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/)為它的執行個體。


<a name="Calling_objc_msgSend()" />

### <a name="calling-objcmsgsend"></a>呼叫 objc_msgSend()

 `objc_msgSend()` 用來傳送訊息 （選擇器） 的物件。 這一系列的函式會採用兩個以上的必要引數： 選取器目標 （執行個體或處理的類別）、 選取器本身，然後所需的特定選取器的任何引數。 執行個體和選取器引數必須是`System.IntPtr`，而且所有其餘的引數必須符合選取器所預期，例如類型`nint`如`int`，或`System.IntPtr`所有`NSObject`-衍生型別。 使用[NSObject.Handle](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/)屬性，以取得`IntPtr`Objective C 類型執行個體。

不幸的是，有一個以上`objc_msgSend()`函式。

使用[ `objc_msgSend_stret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret)針對傳回結構的選取器。
為了讓事情變 「 有趣 」，在 ARM 上這包含所有傳回的型別*不*列舉型別或任何 C 內建類型 (char、 short、 int、 long、 float、 double)。 On x86 （模擬），需要這個選項可用於所有結構大於 8 個位元組大小。 (CGSize-用於上例-正是 8 個位元組，並因此不會使用`objc_msgSend_stret()`在模擬器中。)

使用[ `objc_msgSend_fpret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_fpret)選取器傳回浮點點值，僅限 x86 上。 不需要在 ARM; 上使用此函式請改用`objc_msgSend()`。

主要[objc_msgSend()](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend)函式用於其他所有的選取器。

當您決定其中`objc_msgSend()`函式需要呼叫 （及以上其中一個，例如，用於模擬器和行動裝置），您可以使用一般[ `[DllImport]` ](https://developer.xamarin.com/api/type/System.Runtime.InteropServices.DllImportAttribute/)宣告稍後叫用的函式的方法。

一組預先製作`objc_msgSend()`宣告可以位於[ `ObjCRuntime.Messaging` ](https://developer.xamarin.com/api/type/ObjCRuntime.Messaging/)。


<a name="ugly" />

## <a name="the-ugly"></a>優劣

Objective C 有三種類型的`objc_msgSend`方法： 一個用於規則的引動過程，一個用於傳回浮點值 (僅限 x86)，其中引動過程，一個用於傳回結構值的引動過程。 後者包含尾碼`_stret`中`ObjCRuntime.Messaging`。

如果您叫用這個方法會傳回 （規則是下面所述的） 某些結構，您必須為 out 的值，就像這樣的第一個參數來叫用具有傳回值的方法：

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

項目太這裡好看，而且因為，當您必須使用規則_stret_不同在 X86 和 ARM，如果您想要您的繫結處理模擬器和裝置，您必須加入程式碼看起來像這樣：

```csharp
if (Runtime.Arch == Arch.DEVICE){
    PointF ret;

    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);

    return ret;
} else
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
```

### <a name="using-the-objcmsgsendstret-method"></a>使用 objc\_msgSend\_stret 方法

規則的使用時機[ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret)會像這樣的**ARM**:

-  任何實值類型不是列舉型別或任何列舉類型的基底類型 (int、 byte、 short、 long、 double、 float)。


規則的使用時機[ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret)會像這樣的**X86**:

-  任何實值類型不是列舉型別，或任何列舉類型的基底類型 (int、 byte、 short、 long、 double、 float) 和原生大小大於 8 個位元組。


### <a name="creating-your-own-signatures"></a>建立您自己的簽章。

下列[gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c)可以用來建立您自己的簽章中，視需要。



## <a name="related-links"></a>相關連結

- [選取器範例](https://developer.xamarin.com/samples/mac-ios/Objective-C/Selectors/)
