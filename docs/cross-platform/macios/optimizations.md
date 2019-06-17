---
title: 組建最佳化
description: 本文件說明在 Xamarin.iOS 和 Xamarin.Mac 的應用程式的建置階段套用的各種最佳化功能。
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: conceptdev
ms.author: crdun
ms.date: 04/16/2018
ms.openlocfilehash: f1aa805b9b7a16ad1e8af573cf4170f885eb0197
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261196"
---
# <a name="build-optimizations"></a>組建最佳化

本文件說明在 Xamarin.iOS 和 Xamarin.Mac 的應用程式的建置階段套用的各種最佳化功能。

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>移除 UIApplication.EnsureUIThread / NSApplication.EnsureUIThread

移除對[UIApplication.EnsureUIThread] [ 1] （適用於 Xamarin.iOS) 或`NSApplication.EnsureUIThread`（適用於 Xamarin.Mac)。

此最佳化會變更下列類型的程式碼：

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

方式如下：

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

此最佳化需要啟用，連結器僅會套用到具有方法和`[BindingImpl (BindingImplOptions.Optimizable)]`屬性。

依預設啟用的發行組建。

可以覆寫預設行為，藉由傳遞`--optimize=[+|-]remove-uithread-checks`mtouch/mmp 到。

[1]: https://docs.microsoft.com/dotnet/api/UIKit.UIApplication.EnsureUIThread

## <a name="inline-intptrsize"></a>Inline IntPtr.Size

內嵌的常數值的`IntPtr.Size`根據目標平台。

此最佳化會變更下列類型的程式碼：

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

為下列 （當建置適用於 64 位元平台）：

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

此最佳化需要啟用，連結器僅會套用到具有方法和`[BindingImpl (BindingImplOptions.Optimizable)]`屬性。

根據預設，如果目標設為單一的架構，它會啟用，或平台組件 (**Xamarin.iOS.dll**， **Xamarin.TVOS.dll**， **Xamarin.WatchOS.dll**或**Xamarin.Mac.dll**)。

如果以多個架構為目標，此最佳化就會建立不同的組件，32 位元和 64 位元版本的應用程式，而且這兩個版本必須包含在應用程式中，有效地將最終的應用程式大小增加而不減少它。

可以覆寫預設行為，藉由傳遞`--optimize=[+|-]inline-intptr-size`mtouch/mmp 到。

## <a name="inline-nsobjectisdirectbinding"></a>內嵌 NSObject.IsDirectBinding

`NSObject.IsDirectBinding` 是決定特定的執行個體是否將包裝函式型別的執行個體屬性 (包裝函式型別是對應至原生類型的 managed 的類型; 的執行個體 managed`UIKit.UIView`類型會對應至原生`UIView`類型-相反是使用者類型在此情況下`class MyUIView : UIKit.UIView`會是使用者型別)。

就必須知道的值`IsDirectBinding`時呼叫 Objective C，因為值會決定哪一個版本的`objc_msgSend`使用。

假設下列程式碼：

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

我們可以判斷，在`UIView.SomeProperty`的值`IsDirectBinding`不是常數，不能是內嵌：

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

不過，就可以查看所有類型的應用程式中，並判斷是否繼承自任何型別`NSUrl`，因此是安全地內嵌`IsDirectBinding`常數的值`true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

特別是，此最佳化會變更下列類型的程式碼 (這是繫結程式碼`NSUrl.AbsoluteUrl`):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

為下列 (當它可以判斷，有的任何子類別`NSUrl`應用程式中):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

此最佳化需要啟用，連結器僅會套用到具有方法和`[BindingImpl (BindingImplOptions.Optimizable)]`屬性。

它是預設永遠啟用適用於 Xamarin.iOS，並一律停用預設 xamarin.mac （因為它可能會有以動態方式載入 Xamarin.Mac 中的組件，不可能以判斷特定類別永遠不會形成子類別）。

可以覆寫預設行為，藉由傳遞`--optimize=[+|-]inline-isdirectbinding`mtouch/mmp 到。

## <a name="inline-runtimearch"></a>內嵌 Runtime.Arch

此最佳化會變更下列類型的程式碼：

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

為下列 （當建置裝置適用）：

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

此最佳化需要啟用，連結器僅會套用到具有方法和`[BindingImpl (BindingImplOptions.Optimizable)]`屬性。

它是預設永遠啟用適用於 Xamarin.iOS （它不適用於 Xamarin.Mac）。

可以覆寫預設行為，藉由傳遞`--optimize=[+|-]inline-runtime-arch`mtouch 到。

## <a name="dead-code-elimination"></a>無作用程式碼刪除

此最佳化會變更下列類型的程式碼：

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

此外，它也會評估常數的比較，就像這樣：

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

並判斷運算式`8 == 8`是一律 true，並降低它：

```csharp
Console.WriteLine ("Doing this");
```

這是功能強大的最佳化與內嵌的最佳化，一起使用時，因為它可以轉換下列類型的程式碼 (這是繫結程式碼`NFCIso15693ReadMultipleBlocksConfiguration.Range`):

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

到這個 (64 位元裝置，建置和時也能夠確定沒有任何`NFCIso15693ReadMultipleBlocksConfiguration`應用程式中的子類別):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

AOT 編譯器已可以執行排除無作用程式碼，就像這樣，但這種最佳化是在連結器，這表示可以看到有多個方法，不會使用，而且也因此會移除 （除非使用了其他地方） 連結器:

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

此最佳化需要啟用，連結器僅會套用到具有方法和`[BindingImpl (BindingImplOptions.Optimizable)]`屬性。

它是預設永遠啟用 （如果已啟用連結器）。

可以覆寫預設行為，藉由傳遞`--optimize=[+|-]dead-code-elimination`mtouch/mmp 到。

## <a name="optimize-calls-to-blockliteralsetupblock"></a>最佳化 BlockLiteral.SetupBlock 呼叫

Xamarin.iOS/Mac 執行階段必須知道區塊簽章時建立的 OBJECTIVE-C 區塊，managed 委派。 這可能是相當耗費資源的作業。 此最佳化會計算區塊簽章在建置階段，並修改呼叫 IL`SetupBlock`改為接受做為引數的簽章的方法。 如此一來可避免計算在執行階段的簽章。

基準測試顯示，加快 10 至 15 倍呼叫區塊。

它會將轉換下列[程式碼](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

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

此最佳化需要啟用，連結器僅會套用到具有方法和`[BindingImpl (BindingImplOptions.Optimizable)]`屬性。

使用靜態的註冊機構 （以靜態的註冊機構針對裝置組建，預設會啟用，並在 Xamarin.Mac 版本預設會啟用靜態註冊機構中建置的 Xamarin.iOS) 時，它會啟用預設。

可以覆寫預設行為，藉由傳遞`--optimize=[+|-]blockliteral-setupblock`mtouch/mmp 到。

## <a name="optimize-support-for-protocols"></a>最佳化通訊協定的支援

Xamarin.iOS/Mac 執行階段需要解受管理的類型會實作 OBJECTIVE-C 通訊協定的相關資訊。 這項資訊會儲存在介面 （並對這些介面的屬性），這不是非常有效率的格式，也不是連結器友善。

其中一個範例是，這些介面會儲存資訊中的所有通訊協定成員`[ProtocolMember]`屬性，以及其他項目包含這些成員的參數類型的參考。 這表示只要實作這類介面可讓連結器保留在該介面中使用的所有類型，甚至針對選擇性成員應用程式永遠不會呼叫或實作。

此最佳化會讓靜態的註冊機構會使用簡單且快速地在執行階段尋找一些記憶體有效率的格式儲存任何必要的資訊。

它也會告訴連結器，它不一定需要保留這些介面，或任何相關的屬性。

此最佳化需要連結器和靜態的註冊機構會啟用。

在 Xamarin.iOS 上此最佳化會啟用根據預設，啟用連結器和靜態的註冊機構時。

在 Xamarin.Mac 此最佳化是永遠不會啟用預設的情況下，由於 Xamarin.Mac 支援，以動態方式載入組件和組件可能會不有已在建置階段已知 （並因此不會最佳化）。

可以覆寫預設行為，藉由傳遞`--optimize=-register-protocols`mtouch/mmp 到。

## <a name="remove-the-dynamic-registrar"></a>移除動態註冊機構

Xamarin.iOS 和 Xamarin.Mac 執行階段包含對[註冊 managed 型別](https://developer.xamarin.com/guides/ios/advanced_topics/registrar/)OBJECTIVE-C 執行階段使用。 也可以是在建置階段或在執行階段 （或部分在建置階段和在執行階段的其餘部分），但如果它完全是在建置階段，則表示可以移除支援的程式碼，在執行階段進行模擬。 這會導致大幅降低應用程式的大小，特別是較小的應用程式，例如延伸模組或 watchOS 應用程式。

此最佳化需要靜態的註冊機構和連結器啟用。

連結器會嘗試判斷它是否安全地移除動態註冊機構，若是，會嘗試將它移除。

由於 Xamarin.Mac 支援以動態方式載入組件，在執行階段 （這不知道在建置階段），就無法在建置階段判斷是否這是安全的最佳化。 這表示預設為 Xamarin.Mac 應用程式永遠不會啟用此最佳化。

可以覆寫預設行為，藉由傳遞`--optimize=[+|-]remove-dynamic-registrar`mtouch/mmp 到。

如果它偵測到，如果預設值會覆寫，以移除動態註冊機構，連結器就會發出警告並不安全 （但仍會移除動態註冊機構）。

## <a name="inline-runtimedynamicregistrationsupported"></a>內嵌 Runtime.DynamicRegistrationSupported

內嵌值的`Runtime.DynamicRegistrationSupported`決定在建置階段。

如果已移除動態註冊機構 (請參閱[移除動態註冊機構](#remove-the-dynamic-registrar)最佳化)，這是常數`false`值，否則就是一個常數`true`值。

此最佳化會變更下列類型的程式碼：

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

為下列時動態的註冊機構會移除：

```csharp
throw new Exception ("dynamic registration is not supported");
```

為下列時不會移除動態註冊機構：

```csharp
Console.WriteLine ("do something");
```

此最佳化需要啟用，連結器僅會套用到具有方法和`[BindingImpl (BindingImplOptions.Optimizable)]`屬性。

它是預設永遠啟用 （如果已啟用連結器）。

可以覆寫預設行為，藉由傳遞`--optimize=[+|-]inline-dynamic-registration-supported`mtouch/mmp 到。

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>預先計算的方法，來建立受管理的委派，OBJECTIVE-C 區塊

當 OBJECTIVE-C 呼叫的選取器，不會採用區塊，以做為參數，而且然後 managed 程式碼覆寫該方法時，Xamarin.iOS / Xamarin.Mac 執行階段必須建立該區塊的委派。

繫結產生器所產生的繫結程式碼會包含`[BlockProxy]`屬性會指定具有類型`Create`可以執行這項操作的方法。

指定下列的 OBJECTIVE-C 程式碼：

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

以及下列繫結程式碼：

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

產生器會產生：

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

當呼叫 Objective C `[ObjCBlockTester callClassCallback]`，Xamarin.iOS / Xamarin.Mac 執行階段將探討`[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]`參數上的屬性。 它接著會查閱`Create`方法，在該型別，並呼叫該方法來建立委派。

此最佳化會發現`Create`在建置階段，以及靜態的註冊機構的方法會產生查詢的方法，在執行階段使用中繼資料語彙基元，改為使用的屬性和反映 （這是快多了，而且也可讓連結器程式碼若要移除對應的執行階段程式碼，讓較小的應用程式）。

如果找不到 mmp/mtouch`Create`方法，然後 MT4174/MM4174 警告將會顯示，並查閱將執行在執行階段。
最可能的原因手動撰寫且沒有必要的繫結程式碼`[BlockProxy]`屬性。

此最佳化需要啟用靜態的註冊機構。

它是預設永遠啟用 （只要已啟用靜態註冊機構）。

可以覆寫預設行為，藉由傳遞`--optimize=[+|-]static-delegate-to-block-lookup`mtouch/mmp 到。
