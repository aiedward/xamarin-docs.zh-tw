---
title: 組建優化
description: 本檔說明在 build time 為 Xamarin. iOS 和 Xamarin. Mac 應用程式所套用的各種優化。
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: conceptdev
ms.author: crdun
ms.date: 04/16/2018
ms.openlocfilehash: 4fdc40a8aed4b3137e418d6123fc000c2b36b6dd
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226199"
---
# <a name="build-optimizations"></a>組建優化

本檔說明在 build time 為 Xamarin. iOS 和 Xamarin. Mac 應用程式所套用的各種優化。

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>移除 UIApplication. EnsureUIThread/NSApplication. EnsureUIThread

移除對[UIApplication. EnsureUIThread][1] (適用于 xamarin) 或`NSApplication.EnsureUIThread` (適用于 xamarin) 的呼叫。

這項優化會變更下列類型的程式碼:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

如下所示:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

此優化需要啟用連結器, 而且只會套用至具有屬性的`[BindingImpl (BindingImplOptions.Optimizable)]`方法。

根據預設, 它會針對發行組建啟用。

藉由傳遞`--optimize=[+|-]remove-uithread-checks`至 mtouch/mmp, 可以覆寫預設行為。

[1]: https://docs.microsoft.com/dotnet/api/UIKit.UIApplication.EnsureUIThread

## <a name="inline-intptrsize"></a>內嵌 IntPtr. 大小

`IntPtr.Size`根據目標平臺內嵌的常數值。

這項優化會變更下列類型的程式碼:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

在下列 (為64位平臺建立時):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

此優化需要啟用連結器, 而且只會套用至具有屬性的`[BindingImpl (BindingImplOptions.Optimizable)]`方法。

根據預設, 如果是以單一架構為目標, 或是針對平臺元件 ( **TVOS**、 **WatchOS** .dll 或**xamarin**), 則會啟用此功能。

如果以多個架構為目標, 此優化會針對32位版本和64位版本的應用程式建立不同的元件, 而且這兩個版本都必須包含在應用程式中, 以有效地增加最終應用程式大小, 而不是減少這樣.

藉由傳遞`--optimize=[+|-]inline-intptr-size`至 mtouch/mmp, 可以覆寫預設行為。

## <a name="inline-nsobjectisdirectbinding"></a>內嵌 NSObject。 IsDirectBinding

`NSObject.IsDirectBinding`這是一個實例屬性, 可判斷特定實例是否為包裝函式類型 (包裝函式類型是對應至原生類型的 managed 類型; 例如, managed `UIKit.UIView`類型會對應至原生`UIView`類型, 相反的是使用者類型在此情況下`class MyUIView : UIKit.UIView` , 這會是使用者類型)。

`IsDirectBinding`當呼叫目標-C 時, 必須知道的值, 因為值會決定要使用哪個版本的`objc_msgSend` 。

僅提供下列程式碼:

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

我們可以判斷在的`UIView.SomeProperty` `IsDirectBinding`值中, 不是常數, 而且無法內嵌:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

不過, 您可以查看應用程式中的所有類型, 並判斷沒有繼承自`NSUrl`的類型, 因此可以安全地將`IsDirectBinding`值內嵌至常數`true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

特別是, 這項優化會變更下列類型的程式碼 (這是的系結`NSUrl.AbsoluteUrl`程式碼):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

到下列 (當它可以判斷應用程式`NSUrl`中沒有任何子類別時):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

此優化需要啟用連結器, 而且只會套用至具有屬性的`[BindingImpl (BindingImplOptions.Optimizable)]`方法。

根據預設, 在 xamarin 中一律會啟用此功能, 而且在 Xamarin 中一律預設為停用 (因為可以在 Xamarin 中以動態方式載入元件, 因此無法判斷特定類別永遠不會被子系)。

藉由傳遞`--optimize=[+|-]inline-isdirectbinding`至 mtouch/mmp, 可以覆寫預設行為。

## <a name="inline-runtimearch"></a>內嵌執行時間。

這項優化會變更下列類型的程式碼:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

到下列 (為裝置建立時):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

此優化需要啟用連結器, 而且只會套用至具有屬性的`[BindingImpl (BindingImplOptions.Optimizable)]`方法。

根據預設, 在 Xamarin 中一律會啟用此功能 (Xamarin. Mac 無法使用它)。

傳遞`--optimize=[+|-]inline-runtime-arch`至 mtouch 可以覆寫預設行為。

## <a name="dead-code-elimination"></a>停止程式碼排除

這項優化會變更下列類型的程式碼:

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

登錄

```csharp
Console.WriteLine ("Doing this");
```

它也會評估常數比較, 如下所示:

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

並判斷運算式`8 == 8`一律為 true, 並將其減少為:

```csharp
Console.WriteLine ("Doing this");
```

這是與內嵌優化搭配使用時的強大優化, 因為它可以轉換下列類型的程式碼 (這是的系結程式`NFCIso15693ReadMultipleBlocksConfiguration.Range`代碼):

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

至此 (為64位裝置建立時, 以及同時能夠確保`NFCIso15693ReadMultipleBlocksConfiguration`應用程式中沒有子類別):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

AOT 編譯器已經可以排除這類不好的程式碼, 但這項優化是在連結器內完成, 這表示連結器可以查看有多個未使用的方法, 因此可能會移除 (除非在其他地方使用):

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

此優化需要啟用連結器, 而且只會套用至具有屬性的`[BindingImpl (BindingImplOptions.Optimizable)]`方法。

預設會一律啟用 (當連結器啟用時)。

藉由傳遞`--optimize=[+|-]dead-code-elimination`至 mtouch/mmp, 可以覆寫預設行為。

## <a name="optimize-calls-to-blockliteralsetupblock"></a>優化對 BlockLiteral 的呼叫。 SetupBlock

在建立 managed 委派的目標 C 區塊時, 您必須知道此 Xamarin iOS/Mac 執行時間的區塊簽章。 這可能是相當耗費資源的作業。 此優化會在組建階段計算區塊簽章, 並修改 IL 以呼叫`SetupBlock`接受簽章做為引數的方法。 這麼做可避免在執行時間計算簽章的需求。

效能評定會顯示, 這會加快以10到15的因素來呼叫區塊的速度。

它會轉換下列程式[代碼](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

登錄

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

此優化需要啟用連結器, 而且只會套用至具有屬性的`[BindingImpl (BindingImplOptions.Optimizable)]`方法。

使用靜態註冊機構時, 預設會啟用此功能 (在 Xamarin. iOS 中, 裝置組建的靜態註冊機構預設為啟用, 而在 Xamarin 中, 預設會針對發行組建啟用靜態報名者)。

藉由傳遞`--optimize=[+|-]blockliteral-setupblock`至 mtouch/mmp, 可以覆寫預設行為。

## <a name="optimize-support-for-protocols"></a>將通訊協定的支援優化

如需管理類型如何執行目標 C 通訊協定的相關資訊, 則需要執行 Xamarin/Mac 執行時間。 這項資訊會儲存在介面中 (以及這些介面上的屬性), 這不是非常有效率的格式, 也不是連結器易記的。

其中一個範例是, 這些介面會儲存`[ProtocolMember]`屬性中所有通訊協定成員的相關資訊, 而其他專案則包含這些成員之參數類型的參考。 這表示只要執行此類介面, 連結器就會保留該介面中使用的所有類型, 即使應用程式永遠不會呼叫或實行選擇性成員也一樣。

這項優化會使靜態註冊機構以有效率的格式儲存任何必要的資訊, 以使用可在執行時間輕鬆且快速尋找的小型記憶體。

它也會告訴連結器, 它不一定需要保留這些介面, 也不會有任何相關的屬性。

這項優化需要同時啟用連結器和靜態註冊機構。

在 Xamarin. iOS 上, 當連結器和靜態註冊機構都已啟用時, 預設會啟用這項優化。

在 Xamarin 上, 預設一律不會啟用這項優化, 因為 Xamarin 支援以動態方式載入元件, 而且這些元件可能不會在組建階段得知 (因此不會優化)。

藉由傳遞`--optimize=-register-protocols`至 mtouch/mmp, 可以覆寫預設行為。

## <a name="remove-the-dynamic-registrar"></a>移除動態註冊機構

Xamarin 和 Xamarin 都包含以目標-C 執行時間[註冊 managed 類型](~/ios/internals/registrar.md)的支援。 您可以在組建階段或執行時間 (或部分于執行時間) 完成此作業, 但如果它是在組建階段完成, 則可以移除在執行時間執行的支援程式碼。 這會大幅減少應用程式大小, 特別是針對較小的應用程式 (例如擴充功能或 watchOS 應用程式)。

這項優化需要同時啟用靜態註冊機構和連結器。

連結器會嘗試判斷是否可以安全地移除動態註冊機構, 如果是的話, 則會嘗試將它移除。

因為 Xamarin 支援在執行時間以動態方式載入元件 (在組建階段未知), 所以無法在組建時間判斷這是否為安全的優化。 這表示在 Xamarin. Mac 應用程式中, 預設一律不會啟用這項優化。

藉由傳遞`--optimize=[+|-]remove-dynamic-registrar`至 mtouch/mmp, 可以覆寫預設行為。

如果覆寫預設值以移除動態註冊機構, 則連結器會在偵測到不安全時發出警告 (但仍會移除動態註冊機構)。

## <a name="inline-runtimedynamicregistrationsupported"></a>內嵌執行時間。 DynamicRegistrationSupported

內嵌在組建時間`Runtime.DynamicRegistrationSupported`決定的值。

如果已移除動態註冊機構 (請參閱[移除動態註冊機構](#remove-the-dynamic-registrar)優化), 這是常`false`數值, 否則為常`true`數值。

這項優化會變更下列類型的程式碼:

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

移除動態註冊機構時的下列內容:

```csharp
throw new Exception ("dynamic registration is not supported");
```

當動態註冊機構未移除時, 如下所示:

```csharp
Console.WriteLine ("do something");
```

此優化需要啟用連結器, 而且只會套用至具有屬性的`[BindingImpl (BindingImplOptions.Optimizable)]`方法。

預設會一律啟用 (當連結器啟用時)。

藉由傳遞`--optimize=[+|-]inline-dynamic-registration-supported`至 mtouch/mmp, 可以覆寫預設行為。

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>為目標-C 區塊建立受控委派的預先計算方法

當目標-C 呼叫接受區塊做為參數的選取器, 然後 managed 程式碼已覆寫該方法時, 則 Xamarin. iOS/Xamarin 執行時間必須建立該區塊的委派。

系結產生器所產生的系結程式碼`[BlockProxy]`將會包含一個屬性, 它會`Create`使用可執行此動作的方法來指定類型。

假設有下列目標-C 程式碼:

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

和下列系結程式碼:

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

產生器將會產生:

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

當目標-C 呼叫`[ObjCBlockTester callClassCallback]`時, 就會查看參數上的`[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]`屬性。 然後, 它會在該`Create`類型上查詢方法, 並呼叫該方法來建立委派。

這種優化會在`Create`組建階段尋找方法, 而靜態註冊機構會產生程式碼, 以使用元資料標記在執行時間查閱方法, 而不是使用屬性和反映 (這會更快, 而且也允許連結器若要移除對應的執行時間程式碼, 請讓應用程式變得更小。

如果 mmp/mtouch 找不到`Create`方法, 則會顯示 MT4174/MM4174 警告, 而查閱會改為在執行時間執行。
最可能的原因是手動撰寫系結程式碼, `[BlockProxy]`但沒有必要的屬性。

此優化需要啟用靜態註冊機構。

根據預設, 一律會啟用此功能 (只要已啟用靜態註冊機構)。

藉由傳遞`--optimize=[+|-]static-delegate-to-block-lookup`至 mtouch/mmp, 可以覆寫預設行為。
