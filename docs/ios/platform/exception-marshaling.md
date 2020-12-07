---
title: Xamarin 中的例外狀況封送處理
description: 本檔說明如何在 Xamarin iOS 應用程式中使用原生和受控例外狀況。 它會討論可能發生的問題，以及這些問題的解決方案。
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/05/2017
ms.openlocfilehash: a54a0012f7b5ed3d147242e3ee02b2ed6fe890bf
ms.sourcegitcommit: 0a41c4aa6db72cd2d0cecbe0dc893024cecac71d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749874"
---
# <a name="exception-marshaling-in-xamarinios"></a>Xamarin 中的例外狀況封送處理

_Xamarin 包含新的事件，可協助回應例外狀況，尤其是在原生程式碼中。_

Managed 程式碼和目標 C 都支援執行時間例外狀況， (try/catch/finally 子句) 。

不過，其實作為不同之處，這表示 (Mono 執行時間的執行時間程式庫和目標 C 執行時間程式庫) 在必須處理例外狀況，然後執行以其他語言撰寫的程式碼時，才會發生問題。

本檔說明可能發生的問題，以及可能的解決方案。

它也包含範例專案、 [例外狀況封送處理](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)，可用來測試不同的案例及其解決方案。

## <a name="problem"></a>問題

擲回例外狀況時，就會發生此問題，而在堆疊回溯期間，遇到的框架不符合擲回的例外狀況類型。

這是適用于 Xamarin 或 Xamarin 的典型範例，就是當原生 API 擲回目標 C 例外狀況，然後當堆疊回溯進程到達受控框架時，該目標 C 例外狀況必須以某種方式處理。

預設動作是不執行任何動作。 在上述範例中，這表示讓目標 C 執行時間回溯受控框架。 這有問題，因為目標 C 執行時間不知道如何回溯 managed 框架;例如，它不會 `catch` `finally` 在該框架中執行任何或子句。

### <a name="broken-code"></a>中斷的程式碼

請考慮下列程式碼範例：

``` csharp
var dict = new NSMutableDictionary ();
dict.LowlevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

這將會在機器碼中擲回目標 C NSInvalidArgumentException：

```
NSInvalidArgumentException *** setObjectForKey: key cannot be nil
```

堆疊追蹤會像這樣：

```
0   CoreFoundation          __exceptionPreprocess + 194
1   libobjc.A.dylib         objc_exception_throw + 52
2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
3   libobjc.A.dylib         objc_msgSend + 102
4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()
```

框架0-3 是原生框架，而目標 C 執行時間中的堆疊回溯器 _可以_ 回溯這些畫面格。 尤其是，它會執行任何目標 C `@catch` 或 `@finally` 子句。

不過，目標 C 堆疊回溯器 _無法_ 正確回溯 managed 框架 (框架 4-6) ，因為畫面格將會被回溯，但不會執行 managed 例外狀況邏輯。

這表示通常無法以下列方式攔截這些例外狀況：

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

這是因為目標 C 堆疊回溯器不知道 managed `catch` 子句，而且子句都不會 `finally` 執行。

上述 _程式碼範例生效_ 時，是因為目標 c 有一種方法可以通知未處理的目標 c 例外狀況，也就是 [`NSSetUncaughtExceptionHandler`][2] xamarin. iOS 和 xamarin. Mac 使用的方法，而此時會嘗試將任何目標 c 例外狀況轉換為 managed 例外狀況。

## <a name="scenarios"></a>案例

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>案例 1-使用 managed catch 處理常式捕捉目標 C 例外狀況

在下列案例中，您可以使用 managed 處理常式來攔截目標 C 例外狀況 `catch` ：

1. 擲回目標 C 例外狀況。
2. 目標 C 執行時間會將堆疊 (，但不會將它) ，而是尋找可以處理例外狀況的原生 `@catch` 處理常式。
3. 目標 C 執行時間找不到任何 `@catch` 處理常式、呼叫 `NSGetUncaughtExceptionHandler` ，以及叫用由 Xamarin. IOS/xamarin 所安裝的處理常式。
4. Xamarin/Xamarin 的處理常式會將目標 C 例外狀況轉換成 managed 例外狀況，並加以擲回。 由於目標 C 執行時間不會回溯堆疊 (只會將它) ，因此目前的框架與擲回目標 C 例外狀況的框架相同。

另一個問題發生在此處，因為 Mono 執行時間不知道如何正確地回溯目標 C 框架。

當 Xamarin. iOS 無法攔截到目標 C 例外狀況回呼時，堆疊就像這樣：

```
 0 libxamarin-debug.dylib   exception_handler(exc=name: "NSInvalidArgumentException" - reason: "**_ setObjectForKey: key cannot be nil")
 1 CoreFoundation           __handleUncaughtException + 809
 2 libobjc.A.dylib          _objc_terminate() + 100
 3 libc++abi.dylib          std::__terminate(void (_)()) + 14
 4 libc++abi.dylib          __cxa_throw + 122
 5 libobjc.A.dylib          objc_exception_throw + 337
 6 CoreFoundation           -[__NSDictionaryM setObject:forKey:] + 1015
 7 libxamarin-debug.dylib   xamarin_dyn_objc_msgSend + 102
 8 TestApp                  ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
 9 TestApp                  Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr) [0x00000]
10 TestApp                  ExceptionMarshaling.Exceptions.ThrowObjectiveCException () [0x00013]
```

在這裡，唯一的受控框架是畫面格8-10，但在框架0中擲回 managed 例外狀況。 這表示 Mono 執行時間必須回溯原生框架0-7，這會造成相當於上述問題的問題：雖然 Mono 執行時間會回溯原生框架，但不會執行任何目標 C `@catch` 或 `@finally` 子句。

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

和子句不會 `@finally` 執行，因為回溯此框架的 Mono 執行時間並不知道它。

這種情況的變化是擲回 managed 程式碼中的 managed 例外狀況，然後透過原生框架回溯以取得第一個 managed `catch` 子句：

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

Managed `UIApplication:Main` 方法會呼叫原生 `UIApplicationMain` 方法，然後 iOS 會在最後呼叫 managed 方法之前執行許多機器碼執行，而且在擲回 `AppDelegate:FinishedLaunching` managed 例外狀況時，堆疊上仍有許多原生框架：

```
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
```

系統會管理框架0-1 和27-30，而兩者之間的所有都是原生的。 如果 Mono 流經這些框架，將不會執行任何目標 C `@catch` 或 `@finally` 子句。

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>案例 2-無法攔截目標 C 例外狀況

在下列案例中， _無法_ 使用 managed 處理常式來攔截目標 c 例外狀況， `catch` 因為目標 c 例外狀況是以另一種方式處理：

1. 擲回目標 C 例外狀況。
2. 目標 C 執行時間會將堆疊 (，但不會將它) ，而是尋找可以處理例外狀況的原生 `@catch` 處理常式。
3. 目標 C 執行時間會尋找 `@catch` 處理常式、回溯堆疊，然後開始執行 `@catch` 處理常式。

此案例通常會在 Xamarin iOS 應用程式中找到，因為在主執行緒上，通常會有類似如下的程式碼：

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

這表示，在主執行緒上，絕對不會有未處理的目標 C 例外狀況，因此永遠不會呼叫將目標 C 例外狀況轉換為 managed 例外狀況的回呼。

在比 Xamarin 更早的 macOS 版本上進行 Xamarin. Mac 應用程式的偵錯工具時，這種情況也相當常見，因為檢查偵錯工具中大部分的 UI 物件將會嘗試提取與執行 (平臺上不存在之選取器對應的屬性，因為 Xamarin 支援較高的 macOS 版本) 。 呼叫這類別選取器會擲回 `NSInvalidArgumentException` ( 「無法辨識的選取器傳送到 ...」 ) ，最後會導致程式損毀。

總而言之，將目標 C 執行時間或 Mono 執行時間回溯框架視為未進行程式設計來處理，可能會導致未定義的行為，例如當機、記憶體流失，以及其他類型的無法預期的行為 (錯誤) 行為。

## <a name="solution"></a>解決方案

在 Xamarin 10 和 Xamarin 2.10 中，我們新增了在任何 managed 原生界限上攔截 managed 和目標 C 例外狀況，以及將該例外狀況轉換成另一種類型的支援。

在虛擬程式碼中，它看起來像這樣：

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

Objc_msgSend 的 P/Invoke 被攔截，而這會改為呼叫：

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

相反地，在反向案例中， (將 managed 例外狀況封送處理至目標 C 例外狀況) 。

攔截 managed 原生界限上的例外狀況並不是無成本的，因此預設不會啟用：

- TvOS：攔截目標-C 例外狀況會在模擬器中啟用。
- 在所有情況下都會強制執行 watchOS：攔截，因為讓目標-C 執行時間回溯 managed 框架將會混淆垃圾收集行程，並使其停止回應或損毀。
- Xamarin：已針對 debug 組建啟用目標-C 例外狀況攔截。

[ [組建時間旗標](#build_time_flags) ] 區段說明如何在預設未啟用的情況下啟用攔截。

## <a name="events"></a>事件

攔截到例外狀況時，會引發兩個新的事件： `Runtime.MarshalManagedException` 和 `Runtime.MarshalObjectiveCException` 。

這兩個事件都會傳遞一個 `EventArgs` 物件，其中包含 (屬性) 所擲回的原始例外狀況 `Exception` ，以及 `ExceptionMode` 定義如何封送處理例外狀況的屬性。

您 `ExceptionMode` 可以在事件處理常式中變更屬性，以根據處理常式中完成的任何自訂處理來變更行為。 其中一個範例是在發生特定例外狀況時中止進程。

變更 `ExceptionMode` 屬性會套用至單一事件，而不會影響未來攔截到的任何例外狀況。

可用的模式如下：

- `Default`：預設會因平臺而異。 `ThrowObjectiveCException`如果 GC 處於合作模式 (watchOS) ，則為， `UnwindNativeCode` 否則 (IOS/WatchOS/macOS) 。 預設值可能會在未來變更。
- `UnwindNativeCode`：這是先前的 (未定義) 行為。 當在合作模式中使用 GC (這是 watchOS 上唯一的選項時，無法使用此選項;因此，這不是 watchOS) 的有效選項，但它是所有其他平臺的預設選項。
- `ThrowObjectiveCException`：將 managed 例外狀況轉換為目標 C 例外狀況，並擲回目標 C 例外狀況。 這是 watchOS 上的預設值。
- `Abort`：中止進程。
- `Disable`：停用例外狀況攔截，因此在事件處理常式中設定這個值並不合理，但是一旦引發事件，就太晚停用它。 在任何情況下，如果設定，它的行為會是 `UnwindNativeCode` 。

若要將目標 C 例外狀況封送處理至 managed 程式碼，則可以使用下列模式：

- `Default`：預設會因平臺而異。 `ThrowManagedException`如果 GC 處於合作模式 (watchOS) ，則為， `UnwindManagedCode` 否則 (IOS/TvOS/macOS) 。 預設值可能會在未來變更。
- `UnwindManagedCode`：這是先前的 (未定義) 行為。 當在合作模式下使用 GC 時，此功能無法使用 (這是 watchOS 上唯一有效的 GC 模式;因此，這不是 watchOS) 的有效選項，但它是所有其他平臺的預設值。
- `ThrowManagedException`：將目標 C 例外狀況轉換為 managed 例外狀況，並擲回 managed 例外狀況。 這是 watchOS 上的預設值。
- `Abort`：中止進程。
- `Disable`:D isables 例外狀況攔截，因此在事件處理常式中設定這個值並不合理，但是一旦引發事件之後，就太晚停用它。 在任何情況下，如果設定，則會中止進程。

因此，若要查看每次封送處理例外狀況，您可以執行下列動作：

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

<a name="build_time_flags"></a>

## <a name="build-time-flags"></a>Build-Time 旗標

您可以將下列選項傳遞至適用于 xamarin 的 **mtouch** () 和 xamarin 應用程式的 **mmp** () ，它會判斷是否已啟用例外狀況攔截，並設定應該發生的預設動作：

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

除了以外 `disable` ，這些值與 `ExceptionMode` 傳遞給和事件的值相同 `MarshalManagedException` `MarshalObjectiveCException` 。

`disable`選項 _大部分_ 會停用攔截，但我們仍會在未新增任何執行額外負荷的情況下攔截例外狀況。 這些例外狀況仍會引發封送處理事件，而預設模式則是執行平臺的預設模式。

## <a name="limitations"></a>限制

在嘗試攔截目標 C 例外狀況時，我們只會攔截 P/Invoke 至函 `objc_msgSend` 式系列。 這表示對另一個 C 函式的 P/Invoke （接著會擲回任何目標 C 例外狀況）仍會遇到舊的和未定義的行為 (這可能會在未來的) 中改善。

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc

## <a name="related-links"></a>相關連結

- [例外狀況封送處理 (範例) ](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
