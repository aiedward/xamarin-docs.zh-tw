---
title: 在 Xamarin.iOS 中封送處理的例外狀況
description: 本文件說明如何使用 Xamarin.iOS 應用程式中的原生和 managed 例外狀況。 它討論可能發生的問題，以及這些問題的解決方案。
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/05/2017
ms.openlocfilehash: 167d6ac421bdd2652e7f8474e1ea21bd9040723f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114297"
---
# <a name="exception-marshaling-in-xamarinios"></a>在 Xamarin.iOS 中封送處理的例外狀況

_Xamarin.iOS 將包含新的事件，以協助回應例外狀況，尤其是在原生程式碼。_

Managed 程式碼和 OBJECTIVE-C 有支援執行階段例外狀況 （try/catch/finally 子句）。

不過，它們的實作不相同，也就是說，執行階段程式庫 （Mono 執行階段和 OBJECTIVE-C 執行階段程式庫） 時遇到問題，他們必須處理的例外狀況，並接著執行以其他語言撰寫的程式碼。

本文件說明可能會發生的問題和可能的解決方案。

它也包含範例專案[例外狀況封送處理](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)，可用來測試不同的案例和解決方案。

## <a name="problem"></a>問題

當例外狀況擲回，且在堆疊回溯的畫面格期間遇到不符合所擲回的例外狀況的類型時，就會發生問題。

Xamarin.iOS 或 Xamarin.Mac 的典型範例是原生 API 會擲回的 Objective C 例外狀況，而且然後 Objective C 例外狀況必須以某種方式處理當堆疊回溯處理序達到一個 managed 的框架。

預設動作是不執行任何動作。 上述範例中，這表示讓 OBJECTIVE-C 執行階段受管理的回溯框架。 這是有問題，因為 OBJECTIVE-C 執行階段不知道如何回溯受控的框架;比方說，它將不會執行任何`catch`或`finally`框架中的子句。

### <a name="broken-code"></a>中斷的程式碼

請考慮下列程式碼範例：

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

這將會擲回的 Objective C NSInvalidArgumentException，原生程式碼：

    NSInvalidArgumentException *** setObjectForKey: key cannot be nil

和堆疊追蹤會類似如下：

    0   CoreFoundation          __exceptionPreprocess + 194
    1   libobjc.A.dylib         objc_exception_throw + 52
    2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
    3   libobjc.A.dylib         objc_msgSend + 102
    4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
    5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
    6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()

框架 0-3 是原生框架和 OBJECTIVE-C 執行階段中的堆疊 unwinder_可以_回溯框架。 特別是，它會執行任何 OBJECTIVE-C`@catch`或`@finally`子句。

不過，OBJECTIVE-C 堆疊回溯器會_不_能夠正確回溯 （框架 4-6） 中的 managed 的框架，框架會回溯，但不會執行的 managed 例外狀況邏輯。

這表示，它通常是不可能以下列方式攔截這些例外狀況：

```csharp
try {
    var dict = new NSMutableDictionary ();
    dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero);
} catch (Exception ex) {
    Console.WriteLine (ex);
} finally {
    Console.WriteLine ("finally");
}
```

這是因為 Objective C 的堆疊 unwinder 不知道 managed`catch`子句，而且未將`finally`子句會執行。

當上述的程式碼範例_是_有效，它是因為 Objective C 的未處理的 Objective C 例外狀況的通知方法 [`NSSetUncaughtExceptionHandler`][2]，Xamarin.iOS 和 Xamarin.Mac 使用，並在該點嘗試 managed 例外狀況轉換成 Objective C 中的任何例外狀況。

## <a name="scenarios"></a>案例

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>案例 1-攔截與受管理的 catch 處理常式的 Objective C 例外狀況

在下列案例中，就可以攔截 Objective C 例外狀況使用 managed`catch`處理常式：

1. Objective C 例外狀況會擲回。
2. OBJECTIVE-C 執行階段查核堆疊 （但不是回溯），尋找原生`@catch`可處理的例外狀況處理常式。
3. OBJECTIVE-C 執行階段沒有找到任何`@catch`處理常式時，會呼叫`NSGetUncaughtExceptionHandler`，並叫用 Xamarin.iOS/Xamarin.Mac 所安裝的處理常式。
4. Xamarin.iOS/Xamarin.Mac's 處理常式會將 OBJECTIVE-C 例外狀況轉換成 managed 例外狀況，並擲回它。 因為 Objective C 執行階段未回溯堆疊 （僅限檢視過它）、 目前的框架是同一個擲回的 Objective C 例外狀況的位置。

另一個問題就會發生在這裡，Mono 執行階段不知道如何正確回溯 OBJECTIVE-C 框架。

Xamarin.iOS' 無法攔截的 Objective C 例外狀況回呼呼叫時，堆疊是像這樣：

     0 libxamarin-debug.dylib   exception_handler(exc=name: "NSInvalidArgumentException" - reason: "*** setObjectForKey: key cannot be nil")
     1 CoreFoundation           __handleUncaughtException + 809
     2 libobjc.A.dylib          _objc_terminate() + 100
     3 libc++abi.dylib          std::__terminate(void (*)()) + 14
     4 libc++abi.dylib          __cxa_throw + 122
     5 libobjc.A.dylib          objc_exception_throw + 337
     6 CoreFoundation           -[__NSDictionaryM setObject:forKey:] + 1015
     7 libxamarin-debug.dylib   xamarin_dyn_objc_msgSend + 102
     8 TestApp                  ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
     9 TestApp                  Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr) [0x00000]
    10 TestApp                  ExceptionMarshaling.Exceptions.ThrowObjectiveCException () [0x00013]

在這裡，唯一的受控的框架框架 8-10，但框架 0 中擲回 managed 例外狀況。 這表示，Mono 執行階段必須回溯原生框架 0-7，這會導致問題相當於上面所討論的問題： Mono 執行階段會回溯原生框架，雖然它不會執行任何 OBJECTIVE-C`@catch`或`@finally`子句.

程式碼範例：

```objc
-(id) setObject: (id) object forKey: (id) key
{
    @try {
        if (key == nil)
            [NSException raise: @"NSInvalidArgumentException"];
    } @finally {
        NSLog (@"This will not be executed");
    }
}
```

和`@finally`子句就不會執行，因為回溯至此框架 Mono 執行階段不知道它。

這個變化會擲回 managed 例外狀況在 managed 程式碼，並接著透過原生框架，以取得回溯至第一個管理`catch`子句：

```csharp
class AppDelegate : UIApplicationDelegate {
    public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
    {
        throw new Exception ("An exception");
    }
    static void Main (string [] args)
    {
        try {
            UIApplication.Main (args, null, typeof (AppDelegate));
        } catch (Exception ex) {
            Console.WriteLine ("Managed exception caught.");
        }
    }
}
```

Managed`UIApplication:Main`方法會呼叫原生`UIApplicationMain`方法，然後按一下 iOS 會進行之前最後呼叫 受管理的原生程式碼執行的許多`AppDelegate:FinishedLaunching`方法中，仍有許多 managed 例外狀況時在堆疊上的原生框架擲回：

     0: TestApp                 ExceptionMarshaling.IOS.AppDelegate:FinishedLaunching (UIKit.UIApplication,Foundation.NSDictionary)
     1: TestApp                 (wrapper runtime-invoke) <Module>:runtime_invoke_bool__this___object_object (object,intptr,intptr,intptr) 
     2: libmonosgen-2.0.dylib   mono_jit_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
     3: libmonosgen-2.0.dylib   do_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
     4: libmonosgen-2.0.dylib   mono_runtime_invoke [inlined] mono_runtime_invoke_checked(method=<unavailable>, obj=<unavailable>, params=<unavailable>, error=0xbff45758)
     5: libmonosgen-2.0.dylib   mono_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>)
     6: libxamarin-debug.dylib  xamarin_invoke_trampoline(type=<unavailable>, self=<unavailable>, sel="application:didFinishLaunchingWithOptions:", iterator=<unavailable>), context=<unavailable>)
     7: libxamarin-debug.dylib  xamarin_arch_trampoline(state=0xbff45ad4)
     8: libxamarin-debug.dylib  xamarin_i386_common_trampoline
     9: UIKit                   -[UIApplication _handleDelegateCallbacksWithOptions:isSuspended:restoreState:]
    10: UIKit                   -[UIApplication _callInitializationDelegatesForMainScene:transitionContext:]
    11: UIKit                   -[UIApplication _runWithMainScene:transitionContext:completion:]
    12: UIKit                   __84-[UIApplication _handleApplicationActivationWithScene:transitionContext:completion:]_block_invoke.3124
    13: UIKit                   -[UIApplication workspaceDidEndTransaction:]
    14: FrontBoardServices      __37-[FBSWorkspace clientEndTransaction:]_block_invoke_2
    15: FrontBoardServices      __40-[FBSWorkspace _performDelegateCallOut:]_block_invoke
    16: FrontBoardServices      __FBSSERIALQUEUE_IS_CALLING_OUT_TO_A_BLOCK__
    17: FrontBoardServices      -[FBSSerialQueue _performNext]
    18: FrontBoardServices      -[FBSSerialQueue _performNextFromRunLoopSource]
    19: FrontBoardServices      FBSSerialQueueRunLoopSourceHandler
    20: CoreFoundation          __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
    21: CoreFoundation          __CFRunLoopDoSources0
    22: CoreFoundation          __CFRunLoopRun
    23: CoreFoundation          CFRunLoopRunSpecific
    24: CoreFoundation          CFRunLoopRunInMode
    25: UIKit                   -[UIApplication _run]
    26: UIKit                   UIApplicationMain
    27: TestApp                 (wrapper managed-to-native) UIKit.UIApplication:UIApplicationMain (int,string[],intptr,intptr)
    28: TestApp                 UIKit.UIApplication:Main (string[],intptr,intptr)
    29: TestApp                 UIKit.UIApplication:Main (string[],string,string)
    30: TestApp                 ExceptionMarshaling.IOS.Application:Main (string[])

畫面格 0-1 和 27-30 進行管理，而所有這些之間都是原生。 如果 Mono 回溯透過這些框架，Objective C`@catch`或`@finally`子句將會執行。

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>案例 2-無法攔截 Objective C 例外狀況

在下列案例中，很_未_能夠攔截 Objective C 例外狀況的使用受管理`catch`處理常式因為 Objective C 例外狀況已處理另一種方式：

1. Objective C 例外狀況會擲回。
2. OBJECTIVE-C 執行階段查核堆疊 （但不是回溯），尋找原生`@catch`可處理的例外狀況處理常式。
3. OBJECTIVE-C 執行階段會尋找`@catch`處理常式，回溯堆疊，以及開始執行`@catch`處理常式。

此案例中是常見的 Xamarin.iOS 應用程式，因為在主執行緒上通常會像這樣的程式碼：

``` objective-c
void UIApplicationMain ()
{
    @try {
        while (true) {
            ExecuteRunLoop ();
        }
    } @catch (NSException *ex) {
        NSLog (@"An unhandled exception occured: %@", exc);
        abort ();
    }
}

```

這表示，在主執行緒上其實永遠不會處理的 Objective C 例外狀況，因此絕不會呼叫我們將 OBJECTIVE-C 例外狀況轉換成 managed 例外狀況的回呼。

這也是相當常見較早的 macOS 版本高於 Xamarin.Mac 支援，因為檢查偵錯工具中的大部分 UI 物件將會嘗試擷取到不存在的選取器的屬性會對應上執行的平台 （Xamarin.Mac 應用程式進行偵錯時由於 Xamarin.Mac 包含更高版本的 macOS 版本支援）。 呼叫這類的選取器將會擲回`NSInvalidArgumentException`（"無法辨識的選取器傳送到..."），最終就會造成損毀的程序。

總結來說，讓它們不去的 Mono 執行階段回溯框架或 OBJECTIVE-C 執行階段控制代碼可能會導致未定義的行為，例如損毀，記憶體流失和其他類型的行為無法預測 (mis)。

## <a name="solution"></a>方案

在 Xamarin.iOS 10 和 Xamarin.Mac 2.10，我們已新增來攔截任何 managed 原生界限上的受管理和 OBJECTIVE-C 例外狀況，並將該例外狀況轉換成其他類型的支援。

在虛擬程式碼，它看起來像這樣：

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

P/Invoke 來 objc_msgSend 會被攔截，以及這會改為呼叫：

``` objective-c
void
xamarin_dyn_objc_msgSend (id obj, SEL sel)
{
    @try {
        objc_msgSend (obj, sel);
    } @catch (NSException *ex) {
        convert_to_and_throw_managed_exception (ex);
    }
}
```

且類似反向 （封送處理成 Objective C 例外狀況的 managed 例外狀況） 的情況下進行。

Managed 原生界限攔截例外狀況不是免費的因此它不一定預設會啟用：

- Xamarin.iOS/tvOS： 在模擬器中，已啟用攔截的 Objective C 例外狀況。
- Xamarin.watchOS： 攔截會強制執行在所有情況下，因為讓受管理的 Objective C 執行階段回溯框架會混淆記憶體回收行程，並讓它停止回應或損毀。
- Xamarin.Mac： 針對偵錯組建，已啟用攔截的 Objective C 例外狀況。

[建置階段旗標](#build_time_flags)章節將說明如何啟用攔截時預設不啟用。

## <a name="events"></a>事件

有兩個新的事件引發之後攔截例外狀況：`Runtime.MarshalManagedException`和`Runtime.MarshalObjectiveCException`。

這兩個事件會傳遞`EventArgs`物件，其中包含原始擲回的例外狀況 (`Exception`屬性)，和`ExceptionMode`屬性來定義如何例外狀況應該封送處理。

`ExceptionMode`屬性可以變更在事件處理常式，若要變更根據處理常式中完成的任何自訂處理行為。 其中一個範例是一個特定的例外狀況發生時中止程序。

變更`ExceptionMode`屬性會套用至單一事件，它不會影響未來攔截任何例外狀況。

以下為可用模式：

- `Default`： 預設值是根據平台而異。 很`ThrowObjectiveCException`如果 GC 是合作式模式 (watchOS)，和`UnwindNativeCode`否則 (iOS / watchOS / macOS)。 預設值可能會在未來變更。
- `UnwindNativeCode`： 這是上一個 （未定義） 的行為。 未提供此使用合作式模式 GC 時 （這是唯一的選項，在 watchOS; 因此，這不是有效的選項，在 watchOS），但它是所有其他平台的預設選項。
- `ThrowObjectiveCException`： 將 managed 例外狀況轉換成 Objective C 例外狀況並擲回的 Objective C 例外狀況。 這是 watchOS 上的預設值。
- `Abort`： 中止程序。
- `Disable`： 停用例外狀況攔截，因此沒有任何事件處理常式中設定此值的意義，但引發事件之後就無法再將它停用。 在任何情況下，如果設定，它會做`UnwindNativeCode`。

封送處理至 managed 程式碼的 Objective C 例外狀況，以下為可用模式：

- `Default`： 預設值是根據平台而異。 很`ThrowManagedException`如果 GC 是合作式模式 (watchOS)，和`UnwindManagedCode`否則 (iOS / tvOS / macOS)。 預設值可能會在未來變更。
- `UnwindManagedCode`： 這是上一個 （未定義） 的行為。 未提供此使用合作式模式 GC 時 （這是唯一有效的 GC 模式在 watchOS; 因此這不是有效的選項，在 watchOS），但它是所有其他平台的預設值。
- `ThrowManagedException`： 將 OBJECTIVE-C 例外狀況轉換成 managed 例外狀況並擲回 managed 例外狀況。 這是 watchOS 上的預設值。
- `Abort`： 中止程序。
- `Disable`： 停用例外狀況攔截，因此沒有任何意義，若要設定此值在事件處理常式，但一次事件，就會引發時，就無法再將它停用。 在任何情況下如果設定，就會中止程序。

因此，若要查看每次例外狀況會封送處理，您可以這樣做：

``` csharp
Runtime.MarshalManagedException += (object sender, MarshalManagedExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling managed exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
    
};
Runtime.MarshalObjectiveCException += (object sender, MarshalObjectiveCExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling Objective-C exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
};
```

<a name="build_time_flags" />

## <a name="build-time-flags"></a>建置階段旗標

您可將下列選項來**mtouch** （適用於 Xamarin.iOS 應用程式） 和**mmp** （適用於 Xamarin.Mac 應用程式），以判斷是否已啟用例外狀況攔截，並設定預設動作應該會發生：

- `--marshal-managed-exceptions=`
  - `default`
  - `unwindnativecode`
  - `throwobjectivecexception`
  - `abort`
  - `disable`

- `--marshal-objectivec-exceptions=`
  - `default`
  - `unwindmanagedcode`
  - `throwmanagedexception`
  - `abort`
  - `disable`

除了`disable`，這些值會等於`ExceptionMode`值傳遞給`MarshalManagedException`和`MarshalObjectiveCException`事件。

`disable`選項會_大部分_停用攔截，但我們仍會攔截例外狀況，當它沒有任何執行額外負荷。 針對這些例外狀況，與正在執行的平台的預設模式的預設模式仍然引發封送處理的事件。

## <a name="limitations"></a>限制

我們只會攔截 P/Invokes 移到`objc_msgSend`系列函式嘗試攔截 Objective C 例外狀況時。 這表示 P/Invoke 至另一個 C 函式，則會擲回任何 Objective C 例外狀況，其仍會執行 （這可以改善未來） 的舊的和未定義行為。

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>相關連結

- [例外狀況封送處理 （範例）](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
