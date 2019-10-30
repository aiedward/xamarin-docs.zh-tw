---
title: Xamarin 中的目標-C 選取器
description: 本檔討論如何從C#與目標-C 選取器互動。 其中說明如何叫用選取器，以及在這麼做時必須考慮的技術考慮。
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/12/2017
ms.openlocfilehash: 79f226c137c3ab6b1dd2de9f92cb868056aa9d59
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022280"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Xamarin 中的目標-C 選取器

目標 C 語言是以*選取器*為基礎。 「選取器」是可以傳送至物件或*類別*的訊息。 [ [Xamarin](~/ios/internals/api-design/index.md) ] 會將實例選取器對應至實例方法，並將類別選取器對應至靜態方法。

與一般 C 函式（和C++ like 成員函式）不同的是，您無法使用[P/invoke](https://www.mono-project.com/docs/advanced/pinvoke/)直接叫用選取器，而是使用[`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)將選取器傳送至目標 C 類別或實例。
函數.

如需有關目標-C 中訊息的詳細資訊，請參閱 Apple 的[使用物件](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2)指南。

## <a name="example"></a>範例

假設您想要叫用[`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
[`NSString`](https://developer.apple.com/documentation/foundation/nsstring)上的選取器。
宣告（來自 Apple 的檔）是：

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

此 API 具有下列特性：

- Unified API 的傳回型別是 `CGSize`。
- `font` 參數是[UIFont](xref:UIKit.UIFont) （以及衍生自[NSObject](xref:Foundation.NSObject)的類型（間接），而且會對應至[system.object](xref:System.IntPtr)。
- `width` 參數（`CGFloat`）會對應至 `nfloat`。
- `lineBreakMode` 參數（ [`UILineBreakMode`](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc)）已在 Xamarin iOS 中系結為[`UILineBreakMode`](xref:UIKit.UILineBreakMode)
枚舉.

將它全部放在一起，`objc_msgSend` 宣告應該符合：

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

將其宣告如下：

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

若要呼叫此方法，請使用如下所示的程式碼：

```csharp
NSString target = ...
Selector selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont font = ...
nfloat width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, 
    selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode
);
```

傳回的值是小於8個位元組大小的結構（例如，切換到整合 Api 之前使用的舊版 `SizeF`），上述程式碼會在模擬器上執行，但會在裝置上損毀。 若要呼叫會傳回小於8位大小之值的選取器，請宣告 `objc_msgSend_stret` 函式：

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

若要呼叫此方法，請使用如下所示的程式碼：

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, 
        selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode
    );
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode
    );
```

## <a name="invoking-a-selector"></a>叫用選取器

叫用選取器有三個步驟：

1. 取得選取器目標。
2. 取得選取器名稱。
3. 使用適當的引數呼叫 `objc_msgSend`。

### <a name="selector-targets"></a>選取器目標

選取器目標可以是物件實例或目標 C 類別。 如果目標是實例，而且來自已系結的 Xamarin. iOS 類型，請使用[`ObjCRuntime.INativeObject.Handle`](xref:ObjCRuntime.INativeObject.Handle)屬性。

如果目標是類別，請使用[`ObjCRuntime.Class`](xref:ObjCRuntime.Class)來取得類別實例的參考，然後使用[`Class.Handle`](xref:ObjCRuntime.Class.Handle)屬性。

### <a name="selector-names"></a>選取器名稱

選取器名稱會列在 Apple 的檔中。 例如， [`NSString`](https://developer.apple.com/documentation/foundation/nsstring?language=objc)包括[`sizeWithFont:`](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc)和[`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc)選取器。 內嵌和尾端的冒號是選取器名稱的一部分，而且不能省略。

一旦有了選取器名稱，您就可以為它建立[`ObjCRuntime.Selector`](xref:ObjCRuntime.Selector)實例。

### <a name="calling-objc_msgsend"></a>呼叫 objc_msgSend

`objc_msgSend` 將訊息（選取器）傳送至物件。 這一系列的函式至少需要兩個必要的引數：選取器目標（實例或類別控制碼）、選取器本身，以及選取器所需的任何引數。 實例和選取器引數必須 `System.IntPtr`，而且所有剩餘的引數都必須符合選取器所預期的類型，例如 `int`的 `nint`，或所有 `NSObject`衍生類型的 `System.IntPtr`。 使用[`NSObject.Handle`](xref:Foundation.NSObject.Handle)
用以取得目標 C 類型實例 `IntPtr` 的屬性。

有一個以上的 `objc_msgSend` 函數：

- 針對傳回結構的選取器，請使用[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) 。 在 ARM 上，這包括不是列舉或任何 C 內建類型（`char`、`short`、`int`、`long`、`float`、`double`）的所有傳回類型。 在 x86 （模擬器）上，此方法必須用於大小大於8個位元組的所有結構（`CGSize` 為8個位元組，而且不會在模擬器中使用 `objc_msgSend_stret`）。 
- 僅針對在 x86 上傳回浮點值的選取器使用[`objc_msgSend_fpret`](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) 。 此函式不需要在 ARM 上使用;請改用 `objc_msgSend`。 
- 主要[objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)函數會用於所有其他選取器。

一旦您決定需要呼叫的 `objc_msgSend` 函式（模擬器和裝置可能需要不同的方法），您可以使用一般的[`[DllImport]`](xref:System.Runtime.InteropServices.DllImportAttribute)方法來宣告函式以供稍後的叫用。

在 `ObjCRuntime.Messaging`中可以找到一組預先建立的 `objc_msgSend` 宣告。

## <a name="different-invocations-on-simulator-and-device"></a>模擬器和裝置上的不同調用

如上所述，目標-C 有三種 `objc_msgSend` 方法：一個用於一般調用，一個用於傳回浮點值的調用（僅限 x86），另一個用於傳回結構值的調用。 後者會在 `ObjCRuntime.Messaging`中包含尾碼 `_stret`。

如果您叫用的方法會傳回特定結構（如下所述的規則），您必須使用傳回值當做第一個參數來叫用方法，做為 `out` 值：

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

在 x86 和 ARM 上使用 `_stret_` 方法的時機規則會有所不同。
如果您想要系結同時在模擬器和裝置上工作，請新增如下的程式碼：

```csharp
if (Runtime.Arch == Arch.DEVICE)
{
    PointF ret;
    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);
    return ret;
} 
else
{
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
}
```

### <a name="using-the-objc_msgsend_stret-method"></a>使用 objc_msgSend_stret 方法

針對 ARM 建立時，請使用[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
針對任何不是列舉的實值型別，或列舉的任何基底類型（`int`、`byte`、`short`、`long`、`double`、`float`）。

針對 x86 建立時，請使用[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
針對不是列舉的任何實數值型別，或列舉的任何基底類型（`int`、`byte`、`short`、`long`、`double`、`float`），且其原生大小大於8個位元組。

### <a name="creating-your-own-signatures"></a>建立您自己的簽章

如有需要，可以使用下列[gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c)來建立您自己的簽章。

## <a name="related-links"></a>相關連結

- [目標-C 選取器](https://developer.xamarin.com/samples/mac-ios/Objective-C/)範例
