---
title: 組建優化
description: 本檔說明在組建期間適用于 Xamarin 和 Xamarin 應用程式的各種優化。
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: davidortinau
ms.author: daortin
ms.date: 04/16/2018
ms.openlocfilehash: d3076e35d6e9b7e44ec2f9537f250295eaff7e2e
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453554"
---
# <a name="build-optimizations"></a>組建優化

本檔說明在組建期間適用于 Xamarin 和 Xamarin 應用程式的各種優化。

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>移除 UIApplication. EnsureUIThread/NSApplication. EnsureUIThread

移除適用于 Xamarin 的 [UIApplication. EnsureUIThread][1] () 或 `NSApplication.EnsureUIThread` 適用于 xamarin) 的 (。

這項優化將會變更下列類型的程式碼：

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

如下所示：

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

這項優化需要啟用連結器，而且只會套用至屬性為的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

依預設，它會針對發行組建啟用。

傳遞至 mtouch/mmp 可覆寫預設行為 `--optimize=[+|-]remove-uithread-checks` 。

[1]: /dotnet/api/UIKit.UIApplication.EnsureUIThread

## <a name="inline-intptrsize"></a>內嵌 IntPtr. 大小

根據目標平臺內嵌的常數值 `IntPtr.Size` 。

這項優化將會變更下列類型的程式碼：

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

在建立64位平臺) 時，會進入下列 (：

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

這項優化需要啟用連結器，而且只會套用至屬性為的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

根據預設，如果以單一架構為目標，或針對平臺元件 (**Xamarin.iOS.dll**、 **Xamarin.TVOS.dll**、 **Xamarin.WatchOS.dll** 或 **Xamarin.Mac.dll**) ，則會啟用它。

如果以多個架構為目標，此優化將會為32位版本和64位版本的應用程式建立不同的元件，而這兩個版本都必須包含在應用程式中，以有效地增加最終應用程式大小，而不是減少。

傳遞至 mtouch/mmp 可覆寫預設行為 `--optimize=[+|-]inline-intptr-size` 。

## <a name="inline-nsobjectisdirectbinding"></a>內嵌 NSObject. IsDirectBinding

`NSObject.IsDirectBinding` 這是實例屬性，可判斷特定實例是否為包裝函式類型，或不 (包裝函式類型是否為對應至原生類型的 managed 類型;例如，managed 型別會 `UIKit.UIView` 對應至原生 `UIView` 型別，而相反的是使用者類型，在此案例中是 `class MyUIView : UIKit.UIView` 使用者類型) 。

`IsDirectBinding`當呼叫至目標 C 時，必須知道的值，因為該值會決定要使用哪一個版本 `objc_msgSend` 。

僅提供下列程式碼：

```csharp
class UIView : NSObject {
    public virtual string SomeProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class NSUrl : NSObject {
    public virtual string SomeOtherProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class MyUIView : UIView {
}
```

我們可以判斷在的 `UIView.SomeProperty` 值中 `IsDirectBinding` 不是常數，而且無法內嵌：

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

不過，您可以查看應用程式中的所有類型，並判斷沒有繼承自的型別 `NSUrl` ，因此可以放心將 `IsDirectBinding` 值內嵌至常數 `true` ：

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

特別是，這項優化會變更下列程式碼類型 (這是) 的系結程式碼 `NSUrl.AbsoluteUrl` ：

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

在下列 (中，可以判斷 `NSUrl` 應用程式) 中沒有任何子類別：

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

這項優化需要啟用連結器，而且只會套用至屬性為的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

依預設，針對 xamarin 預設會啟用，而且基於 Xamarin (預設一律停用，因為您可以動態載入 Xamarin 中的元件，所以無法判斷特定類別永遠不會) 子類別。

傳遞至 mtouch/mmp 可覆寫預設行為 `--optimize=[+|-]inline-isdirectbinding` 。

## <a name="inline-runtimearch"></a>內嵌執行時間。

這項優化將會變更下列類型的程式碼：

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

針對裝置) 建立時，會進入下列 (：

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

這項優化需要啟用連結器，而且只會套用至屬性為的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

針對 Xamarin (預設一律會啟用，因為 Xamarin) 無法使用它。

傳遞至 mtouch 可以覆寫預設行為 `--optimize=[+|-]inline-runtime-arch` 。

## <a name="dead-code-elimination"></a>無作用程式碼排除

這項優化將會變更下列類型的程式碼：

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

到：

```csharp
Console.WriteLine ("Doing this");
```

它也會評估常數比較，如下所示：

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

並判斷運算式一律為 `8 == 8` true，並將其縮減為：

```csharp
Console.WriteLine ("Doing this");
```

這是搭配內嵌優化使用時的強大優化，因為它可以轉換下列類型的程式碼 (這是) 的系結程式碼 `NFCIso15693ReadMultipleBlocksConfiguration.Range` ：

```csharp
NSRange ret;
if (IsDirectBinding) {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret (out ret, this.Handle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    }
} else {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret (out ret, this.SuperHandle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    }
}
return ret;
```

在此 (建立64位裝置時，以及在應用程式) 中也能確保沒有子 `NFCIso15693ReadMultipleBlocksConfiguration` 類別時：

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

AOT 編譯器已經可以消除像這樣的無效程式碼，但是這項優化是在連結器內完成，這表示連結器可以看到有多個方法不再使用，因此除非在其他地方) 使用，否則可能會被移除 (：

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

這項優化需要啟用連結器，而且只會套用至屬性為的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

預設會在啟用連結器) 時 (預設為啟用。

傳遞至 mtouch/mmp 可覆寫預設行為 `--optimize=[+|-]dead-code-elimination` 。

## <a name="optimize-calls-to-blockliteralsetupblock"></a>優化對 BlockLiteral 的呼叫。 SetupBlock

當您建立 managed 委派的目標 C 區塊時，需要知道區塊簽章。 這可能是相當昂貴的作業。 這種優化會在組建階段計算區塊簽章，並修改 IL 以呼叫接受簽章 `SetupBlock` 做為引數的方法。 這麼做可避免在執行時間計算簽章的需求。

基準測試顯示這會加速以10到15的因數來呼叫區塊。

它會轉換下列程式 [代碼](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211)：

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

到：

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

這項優化需要啟用連結器，而且只會套用至屬性為的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

在 Xamarin 中使用靜態註冊機構 (時，預設會啟用此功能。 iOS 預設會針對裝置組建啟用靜態註冊機構，而在 Xamarin 中，預設會為發行組建) 啟用靜態註冊機構。

傳遞至 mtouch/mmp 可覆寫預設行為 `--optimize=[+|-]blockliteral-setupblock` 。

## <a name="optimize-support-for-protocols"></a>優化對通訊協定的支援

Xamarin. iOS/Mac 執行時間需要 managed 類型如何實作為目標 C 通訊協定的相關資訊。 這項資訊會儲存在介面 (以及這些介面上的屬性) （這不是非常有效率的格式），也不能讓連結器更容易使用。

其中一個範例是，這些介面會將所有通訊協定成員的相關資訊儲存在 `[ProtocolMember]` 屬性中，而其他專案則包含這些成員之參數類型的參考。 這表示，只要執行這類介面，連結器就會保留該介面中使用的所有類型，即使是應用程式永遠不會呼叫或實行的選擇性成員也一樣。

這項優化會讓靜態註冊機構以有效率的格式儲存任何所需的資訊，而這些資訊會在執行時間使用很簡單快速的記憶體。

此外，它也會教連結器不一定需要保留這些介面，也不需要任何相關的屬性。

這項優化需要同時啟用連結器和靜態註冊機構。

在 Xamarin 上，啟用連結器和靜態註冊機構時，預設會啟用這項優化。

在 Xamarin 上，預設永遠不會啟用這項優化，因為 Xamarin 支援以動態方式載入元件，而且這些元件在組建階段可能不會 (，因此不會) 優化。

傳遞至 mtouch/mmp 可覆寫預設行為 `--optimize=-register-protocols` 。

## <a name="remove-the-dynamic-registrar"></a>移除動態註冊機構

Xamarin 和 Xamarin 執行時間都包含使用目標-C 執行時間來 [註冊 managed 類型](~/ios/internals/registrar.md) 的支援。 它可以在組建階段或執行時間執行，或在執行時間 (或部分于執行時間) ，但如果在組建階段完全完成，則表示可以移除在執行時間執行此程式碼的支援程式碼。 這會大幅減少應用程式的大小，特別是較小的應用程式，例如延伸模組或 watchOS 應用程式。

這項優化需要啟用靜態註冊機構和連結器。

連結器會嘗試判斷是否可以安全地移除動態註冊機構，如果是，則會嘗試移除它。

由於 Xamarin 支援在執行時間以動態方式載入元件 (在組建階段並不知道) ，因此在組建時無法判斷這是否為安全的優化。 這表示 Xamarin 的 Mac 應用程式預設不會啟用這項優化。

傳遞至 mtouch/mmp 可覆寫預設行為 `--optimize=[+|-]remove-dynamic-registrar` 。

如果覆寫預設值以移除動態註冊機構，連結器會在偵測到它不安全 (時發出警告，但動態註冊機構仍會) 移除。

## <a name="inline-runtimedynamicregistrationsupported"></a>內嵌執行時間。 DynamicRegistrationSupported

內嵌在 `Runtime.DynamicRegistrationSupported` 組建階段決定的值。

如果已移除動態註冊機構 (請參閱 [移除動態註冊機構](#remove-the-dynamic-registrar) 優化) ，這是常 `false` 數值，否則為常 `true` 數值。

這項優化將會變更下列類型的程式碼：

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

移除動態註冊機構時的下列內容：

```csharp
throw new Exception ("dynamic registration is not supported");
```

當動態註冊機構未移除時為下列內容：

```csharp
Console.WriteLine ("do something");
```

這項優化需要啟用連結器，而且只會套用至屬性為的方法 `[BindingImpl (BindingImplOptions.Optimizable)]` 。

預設會在啟用連結器) 時 (預設為啟用。

傳遞至 mtouch/mmp 可覆寫預設行為 `--optimize=[+|-]inline-dynamic-registration-supported` 。

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>針對目標 C 區塊建立受控委派的預先計算方法

當目標-C 呼叫接受區塊作為參數的選取器，然後 managed 程式碼已覆寫該方法時，就必須建立該區塊的委派。

系結產生器所產生的系結程式碼將包含 `[BlockProxy]` 屬性，此屬性會指定具有 `Create` 可進行此動作之方法的型別。

假設有下列的目標 C 程式碼：

```objc
@interface ObjCBlockTester : NSObject {
}
-(void) classCallback: (void (^)())completionHandler;
-(void) callClassCallback;
@end

@implementation ObjCBlockTester
-(void) classCallback: (void (^)())completionHandler
{
}

-(void) callClassCallback
{
    [self classCallback: ^()
    {
        NSLog (@"called!");
    }];
}
@end
```

以及下列系結程式碼：

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

產生器將會產生：

```csharp
[Register("ObjCBlockTester", true)]
public unsafe partial class ObjCBlockTester : NSObject {
    // unrelated code...

    [Export ("callClassCallback")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public virtual void CallClassCallback ()
    {
        if (IsDirectBinding) {
            ApiDefinition.Messaging.void_objc_msgSend (this.Handle, Selector.GetHandle ("callClassCallback"));
        } else {
            ApiDefinition.Messaging.void_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("callClassCallback"));
        }
    }

    [Export ("classCallback:")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public unsafe virtual void ClassCallback ([BlockProxy (typeof (Trampolines.NIDActionArity1V0))] System.Action completionHandler)
    {
        // ...

    }
}

static class Trampolines
{
    [UnmanagedFunctionPointerAttribute (CallingConvention.Cdecl)]
    [UserDelegateType (typeof (System.Action))]
    internal delegate void DActionArity1V0 (IntPtr block);

    static internal class SDActionArity1V0 {
        static internal readonly DActionArity1V0 Handler = Invoke;

        [MonoPInvokeCallback (typeof (DActionArity1V0))]
        static unsafe void Invoke (IntPtr block) {
            var descriptor = (BlockLiteral *) block;
            var del = (System.Action) (descriptor->Target);
            if (del != null)
                del (obj);
        }
    }

    internal class NIDActionArity1V0 {
        IntPtr blockPtr;
        DActionArity1V0 invoker;

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe NIDActionArity1V0 (BlockLiteral *block)
        {
            blockPtr = _Block_copy ((IntPtr) block);
            invoker = block->GetDelegateForBlock<DActionArity1V0> ();
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        ~NIDActionArity1V0 ()
        {
            _Block_release (blockPtr);
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe static System.Action Create (IntPtr block)
        {
            if (block == IntPtr.Zero)
                return null;
            if (BlockLiteral.IsManagedBlock (block)) {
                var existing_delegate = ((BlockLiteral *) block)->Target as System.Action;
                if (existing_delegate != null)
                    return existing_delegate;
            }
            return new NIDActionArity1V0 ((BlockLiteral *) block).Invoke;
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        unsafe void Invoke ()
        {
            invoker (blockPtr);
        }
    }
}
```

當目標 C 呼叫時 `[ObjCBlockTester callClassCallback]` ，Xamarin （xamarin）執行時間會查看 `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` 參數上的屬性（attribute）。 然後，它會 `Create` 在該型別上查閱方法，並呼叫該方法來建立委派。

這項優化會 `Create` 在組建階段尋找方法，而靜態註冊機構會產生程式碼，以使用元資料標記在執行時間查詢方法，而不是使用屬性和反映 (這會更快，也可讓連結器移除對應的執行時間程式碼，讓應用程式變得更小) 。

如果 mmp/mtouch 找不到 `Create` 方法，則會顯示 MT4174/MM4174 警告，而會改為在執行時間執行查閱。
最可能的原因是手動撰寫系結程式碼，但沒有必要的 `[BlockProxy]` 屬性。

這種優化需要啟用靜態註冊機構。

只要已啟用靜態註冊機構) ，預設一律會啟用 (。

傳遞至 mtouch/mmp 可覆寫預設行為 `--optimize=[+|-]static-delegate-to-block-lookup` 。