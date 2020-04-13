---
title: 在 Xamarin.Mac 應用程式中偵錯原生損毀
description: 本文件描述如何針對源自 Objective-C 執行階段的例外狀況進行偵錯。 並討論判斷提示失敗、回呼問題、例外狀況反昇等等。
ms.prod: xamarin
ms.assetid: B0C0CE31-2737-4969-8EA5-D39D3333E9C2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: 40d849ad403f2f47c00be9d3da7b59fc27ce8002
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725489"
---
# <a name="debugging-a-native-crash-in-a-xamarinmac-app"></a>在 Xamarin.Mac 應用程式中偵錯原生損毀

## <a name="overview"></a>概觀

有時程式設計錯誤會造成原生 Objective-C 執行階段發生損毀。 不同於 C# 例外狀況，這些並不會指向可供您查看來修正的特定程式碼行。 有時它們可能太過瑣細而不易找出並修正，或是極難以追蹤。

我們將逐步解說幾個真實的原生損毀範例來探究一下。

## <a name="example-1-assertion-failure"></a>範例 1：判斷提示失敗

以下是一個簡單測試應用程式中的前幾行損毀程式碼 (此資訊將會在**應用程式輸出**面板中)：

```csharp
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.381 NSOutlineViewHottness[79111:1304993] (
  0   CoreFoundation                      0x91888343 __raiseError + 195
  1   libobjc.A.dylib                     0x9a5e6a2a objc_exception_throw + 276
  2   CoreFoundation                      0x918881ca +[NSException raise:format:arguments:] + 138
  3   Foundation                          0x950742b1 -[NSAssertionHandler handleFailureInMethod:object:file:lineNumber:description:] + 118
  4   AppKit                              0x975db476 -[NSTableView _uncachedRectHeightOfRow:] + 373
  5   AppKit                              0x975db2f8 -[_NSTableRowHeightStorage _uncachedRectHeightOfRow:] + 143
  6   AppKit                              0x975db206 -[_NSTableRowHeightStorage _cacheRowHeights] + 167
  7   AppKit                              0x975db130 -[_NSTableRowHeightStorage _createRowHeightsArray] + 226
  8   AppKit                              0x975b5851 -[_NSTableRowHeightStorage _ensureRowHeights] + 73
  9   AppKit                              0x975b5790 -[_NSTableRowHeightStorage computeTableHeightForNumberOfRows:] + 89
  10  AppKit                              0x975b4c38 -[NSTableView _totalHeightOfTableView] + 220
```

前面加上數字的行是原生堆疊追蹤。 您可以從中看出損毀發生在處理資料列高度的 `NSTableView` 內。 接著 `NSAssertionHandler` 引發 `NSException (objc_exception_throw)`，然後我們就看到判斷提示失敗：

```csharp
rowHeight error: The value must be > 0 for row 0, but the delegate
<NSOutlineView_ViewDelegate: 0xaa01860> gave -1.000
```

一旦您看到此情況，很顯然就是有某個 `NSOutlineViewDelegate` 方法傳回負數。 這就是問題所在：

```csharp
public override nfloat GetRowHeight (NSTableView tableView, nint row)
{
    return -1;
}
```

## <a name="example-2-callback-jumped-into-middle-of-nowhere"></a>範例 2：回呼不知跳到哪裡去

```csharp
Stacktrace:

 at <unknown> <0xffffffff>
 at (wrapper managed-to-native) MonoMac.AppKit.NSApplication.NSApplicationMain (int,string[]) <IL 0x000a4, 0xffffffff>
 at MonoMac.AppKit.NSApplication.Main (string[]) [0x00041] in /Users/donblas/Programming/xamcore-master/src/AppKit/NSApplication.cs:107
 at NSOutlineViewHottness.MainClass.Main (string[]) [0x00007] in /Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/Main.cs:14
 at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr) <IL 0x00050, 0xffffffff>

Native stacktrace:

Debug info from gdb:

(lldb) command source -s 0 '/tmp/mono-gdb-commands.qrHllW'
Executing commands in '/private/tmp/mono-gdb-commands.qrHllW'.
(lldb) process attach --pid 79229
Process 79229 stopped
Executable module set to "/Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/bin/Debug/NSOutlineViewHottness.app/Contents/MacOS/NSOutlineViewHottness".
Architecture set to: i386-apple-macosx.
(lldb) thread list
Process 79229 stopped
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 thread #2: tid = 0x142790, 0x9af768d2 libsystem_kernel.dylib`kevent64 + 10, queue = 'com.apple.libdispatch-manager'
 thread #3: tid = 0x142792, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #4: tid = 0x142794, 0x9af6fa6a libsystem_kernel.dylib`semaphore_wait_trap + 10
 thread #5: tid = 0x142795, 0x9af75772 libsystem_kernel.dylib`__recvfrom + 10
 thread #6: tid = 0x142799, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #7: tid = 0x14279a, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #8: tid = 0x14279b, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #9: tid = 0x1427f8, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #10: tid = 0x1427fe, 0x9af6fa2e libsystem_kernel.dylib`mach_msg_trap + 10
(lldb) thread backtrace all
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 * frame #0: 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10
   frame #1: 0x986bfb25 libsystem_c.dylib`waitpid$UNIX2003 + 48
   frame #2: 0x028ba36d libmono-2.0.dylib`mono_handle_native_sigsegv(signal=11, ctx=0x03115fe0) + 541 at mini-exceptions.c:2323
   frame #3: 0x0290a8bb libmono-2.0.dylib`mono_arch_handle_altstack_exception(sigctx=<unavailable>, fault_addr=<unavailable>, stack_ovf=0) + 155 at exceptions-x86.c:1159
   frame #4: 0x0280b4fd libmono-2.0.dylib`mono_sigsegv_signal_handler(_dummy=<unavailable>, info=<unavailable>, context=<unavailable>) + 445 at mini.c:6861
   frame #5: 0x91ef403b libsystem_platform.dylib`_sigtramp + 43
   frame #6: 0x9a5dd0bd libobjc.A.dylib`objc_msgSend + 45
   frame #7: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #8: 0x9773ba91 AppKit`-[NSApplication sendAction:to:from:] + 548
   frame #9: 0x9773b82d AppKit`-[NSControl sendAction:to:] + 102
   frame #10: 0x97934d36 AppKit`__26-[NSCell _sendActionFrom:]_block_invoke + 176
   frame #11: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #12: 0x97787975 AppKit`-[NSCell _sendActionFrom:] + 161
   frame #13: 0x979188ea AppKit`-[NSButtonCell _sendActionFrom:] + 55
   frame #14: 0x979366e6 AppKit`__48-[NSCell trackMouse:inRect:ofView:untilMouseUp:]_block_invoke965 + 43
   frame #15: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #16: 0x977a3d00 AppKit`-[NSCell trackMouse:inRect:ofView:untilMouseUp:] + 2815
   frame #17: 0x977a2df4 AppKit`-[NSButtonCell trackMouse:inRect:ofView:untilMouseUp:] + 524
   frame #18: 0x977a233b AppKit`-[NSControl mouseDown:] + 762
   frame #19: 0x97cbc112 AppKit`-[_NSThemeWidget mouseDown:] + 378
   frame #20: 0x97d36d74 AppKit`-[NSWindow _reallySendEvent:] + 12353
   frame #21: 0x977201f9 AppKit`-[NSWindow sendEvent:] + 409
   frame #22: 0x976cdc67 AppKit`-[NSApplication sendEvent:] + 4679
   frame #23: 0x9754807c AppKit`-[NSApplication run] + 1003
```

這是一個追蹤難度高出許多的問題。 當您在受控堆疊追蹤頂端看到 `at <unknown> <0xffffffff>` 或 `MonoMac.ObjCRuntime.Runtime.GetNSObject (IntPtr ptr)` 時，即暗示了我們正嘗試搭配某個已被執行記憶體回收的物件執行某些受控程式碼。 原生堆疊追蹤顯示 `trackMouse:inRect:ofView:untilMouseUp` 進入 `NSCell _sendActionFrom`，因此我們處理的是點選事件，其中嘗試回呼 C# 但卻結束。

一般而言，這類錯誤很難以追蹤。 我在按鈕處理常式新增了 `GC.Collect(2)` 來協助追蹤此問題 (強制執行記憶體回收)，以便讓問題能夠重現。 在將範例暫時分為兩部分、移除程式碼區段直到問題消失之後，證明是[這個 Bug](https://bugzilla.xamarin.com/show_bug.cgi?id=23378)：

```csharp
mainWindowController.Window.StandardWindowButton (NSWindowButton.CloseButton).Activated += HandleActivated;
```

`StandardWindowButton()` 所傳回的 `NSButton` 雖然有註冊的事件 (這就是錯誤 Bug 所在)，但卻被回收。 當我們嘗試藉由點選來呼叫該事件時，如果按鈕已被執行記憶體回收，就會發生損毀。

儘管並非此特定問題的根本原因，但造成這類堆疊追蹤的原因也可能是藉由 `[Export]` 匯出至 Objective-C 的函式中有不正確的方法簽章。 例如，如果方法預期參數是 `out string`，而您將它輸入成 `string`，就會造成同樣的損毀。

## <a name="example-3-callbacks-and-managed-objects"></a>範例 3：回呼和受控物件

在許多 Cocoa API 中，當發生某個事件或需要某些資料來執行工作時，都會有程式庫呼叫 API 的情況，讓您能夠有機會回應。 雖然您可能主要考慮 **Delegate** 和 **DataSource** 模式，但有許多 API 都是以此方式運作。 例如，當您覆寫 `NSView` 的方法，然後將它插入到視覺化樹狀結構時，您會預期 AppKit 在特定事件發生時回呼您。

幾乎在所有情況下，只要仍可回呼這些回呼的受控物件目標，Xamarin.Mac 都會正確地防止對這些目標執行資源回收。 不過，在罕見的情況下，繫結中的 Bug 可能會中斷此狀態。 當發生這種情況時，您就會看到類似下方令人不愉快的損毀：

```csharp
Thread 0 Crashed:: Dispatch queue: com.apple.main-thread

0   libsystem_kernel.dylib              0x98c2f69a __pthread_kill + 10
1   libsystem_pthread.dylib             0x90341f19 pthread_kill + 101
2   libsystem_c.dylib                   0x9453feee abort + 156
3   libmonosgen-2.0.dylib               0x020bfba5 mono_handle_native_sigsegv + 757
4   libmonosgen-2.0.dylib               0x0210b812 mono_arch_handle_altstack_exception + 162
5   libmonosgen-2.0.dylib               0x0200c55e mono_sigsegv_signal_handler + 446
6   libsystem_platform.dylib            0x9513003b _sigtramp + 43
7   ???                                 0xffffffff 0 + 4294967295
8   libmonosgen-2.0.dylib               0x0200c3a0 mono_sigill_signal_handler + 48
9   com.apple.AppKit                    0x99f76041 -[NSView setFrame:] + 448
10  com.apple.AppKit                    0x9a1fd4ea -[NSToolbarView adjustToWindow:attachedToEdge:] + 198
11  com.apple.AppKit                    0x9a1fd414 -[NSToolbar _adjustViewToWindow] + 68
12  com.apple.AppKit                    0x9a01eb0d -[NSToolbar _windowWillShowToolbar] + 79

```

此指南可協助您在發生此種現象時，追蹤這類 Bug、正確地進行回報，同時在您的程式碼中避開它們直到錯誤修復為止。

### <a name="locating"></a>找出問題

在幾乎每個有此性質之 Bug 的情況中，主要的徵兆都是原生損毀，通常在堆疊頂端的框架中，會有類似 `mono_sigsegv_signal_handler` 或 `_sigtrap` 的行。 Cocoa 嘗試回呼您的 C# 程式碼，但卻遇到已被執行記憶體回收的物件，然後就發生損毀。 不過，並非每個具有這些表徵的損毀都是由這類繫結問題所造成，您將必須進行一些額外的探究，才能確認這是問題所在。

導致這些 Bug 難以追蹤的原因在於，只有在記憶體回收作業已處置所提到的物件**之後**，這些 Bug 才會發生。 如果您確信您已遇到這其中一個 Bug，請在您的啟動序列中新增下列程式碼：

```csharp
new System.Threading.Thread (() =>
{
    while (true) {
         System.Threading.Thread.Sleep (1000);
         GC.Collect ();
    }
}).Start ();
```

這會強制您的應用程式每秒都執行記憶體回收行程。 請重新執行您的應用程式並嘗試重現 Bug。 如果立即發生損毀，或持續不斷發生而不是隨機發生，即表示您的做法正確。

### <a name="reporting"></a>報告

下一步是要將問題回報給 Xamarin，以便在未來的版本中修正此繫結。 如果您是商務版或企業版授權的持有者，請透過下列方式建立票證

[visualstudio.microsoft.com/vs/support/](https://visualstudio.microsoft.com/vs/support/)

否則，請搜尋現有的問題：

- 查看 [Xamarin.Mac 論壇](https://forums.xamarin.com/categories/xamarin-mac) \(英文\)
- 搜尋[問題存放庫](https://github.com/xamarin/xamarin-macios/issues) \(英文\)
- 在切換到 GitHub 問題之前，Xamarin 問題的追蹤是在 [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi) \(英文\) 上進行的。 請從該處搜尋相符的問題。
- 如果您找不到相符的問題，請在 [GitHub 問題存放庫](https://github.com/xamarin/xamarin-macios/issues/new) \(英文\) 中提出新的問題。

GitHub 問題全都是公開的。 無法隱藏意見或附件。

請儘量包含下列資訊：

- 一個可重現問題的簡單範例。 這是**非常寶貴的**，請盡您所能提供。
- 損毀狀況的完整堆疊追蹤。
- 損毀狀況周圍的 C# 程式碼。   

### <a name="working-around"></a>解決問題

追蹤到問題之後，採用因應措施來修補問題，直到修正繫結為止，可以是一個相當簡單的過程。 目標是要藉由保留一個開啟的參考，防止將遭到以錯誤方式處理的物件 (**View****Delegate****DataSource**) 自記憶體中移除。

針對只有單一物件執行個體的簡單案例，請將程式碼從這樣：

```csharp
void AddObject ()
{
    item.View = new MyView ();
    ...
}
```

變更成使用靜態變數，例如這樣：

```csharp
static NSObject view;
...

void AddObject ()
{
    view = new MyView ();
    item.View = view;
    ...
}
```

在可能建立多個執行個體的案例中，可以採用靜態 `HashSet`：

```csharp
static HashSet<NSObject> collection = new HashSet<NSObject> ();
...

void AddObject ()
{
    item.View = new MyView ();
    collection.Add (item.View );
    ...
}
```

修正繫結且您已升級至包含該項修正的 Xamarin.Mac 版本之後，即可將因應措施程式碼移除。

## <a name="exception-bubbling-and-objective-c"></a>例外狀況事件反昇和 Objective-C

您應該一律不允許 C# 例外狀況將受控碼「逸出」至呼叫端 Objective-C 方法。 如果您這麼做，結果並不明確，但通常會涉及損毀。 一般而言，我們會竭盡所能提供實用的原生和受控損毀資訊，來協助您快速解決問題。

如果不過於深究技術原因，設定基礎結構以在每個受控/原生界限攔截受控例外狀況，不僅成本非常高昂，而且還會有「許多」__ 發生在眾多常見作業中的轉換。 許多作業 (特別是涉及 UI 執行緒的作業) 必須快速完成，否則您的應用程式執行將會斷斷續續，或是效能讓人難以接受。 這些回呼中有許多都是執行不太可能擲回例外狀況的非常簡單工作，所以在這些案例中，這個額外負荷不僅成本太高，也沒有必要性。

因此，我們不會為您設定這些 try/catch。 針對程式碼執行非瑣碎工作 (例如不僅僅只是傳回布林值或執行簡單比對) 的情況，您可以自行嘗試 try catch。
