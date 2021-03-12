---
title: 適用于 Xamarin 的疑難排解秘訣
description: 本檔提供在開發 Xamarin iOS 應用程式期間進行疑難排解的各種秘訣。 它描述特定的錯誤訊息和其他潛在問題。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/10/2020
ms.openlocfilehash: 0f9685ba19d634fb13321342ab8265f039c4d642
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602875"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>適用于 Xamarin 的疑難排解秘訣

## <a name="xamarinios-cant-resolve-systemvaluetuple"></a>Xamarin 無法解析 System. ValueTuple

發生此錯誤的原因是 Visual Studio 不相容。

- **Visual Studio 2017 Update 1** (15.1 版或更舊的) 只與 **ValueTuple NuGet 4.3.0** (或較舊的) 相容。

- **Visual Studio 2017 Update 2** (15.2 版或更新版本的) 僅相容于 **ValueTuple NuGet 4.3.1** 或更新版本。

選擇與您的 Visual Studio 2017 安裝對應的正確 ValueTuple NuGet。

## <a name="receiving-error-retrieving-update-information-error-message"></a>收到「正在抓取更新資訊時發生錯誤」錯誤訊息

嘗試更新軟體並出現此錯誤訊息時，請嘗試重新開機您的 IDE。 如果失敗，請嘗試登出，然後再登入您在 IDE 內的帳戶。

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>如何使用 Interface Builder 建立輸出或動作？

如需在 IB 中使用輸出和動作的詳細資訊，請參閱 Apple 的 [輸出](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) 和 [操作](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html) 指南。

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>>encoding.getencoding 擲回 NotSupportedException

您可以使用預設不會新增的編碼。 請查看 [  [國際化](~/ios/app-fundamentals/localization/index.md) ] 頁面，以瞭解如何新增更多編碼的支援。

## <a name="systemmissingmethodexception-anything-else"></a>MissingMethodException (任何其他) 

連結器可能已移除該成員，且在執行時間中不存在於元件中。  此錯誤有幾個解決方案：

- 將 [`[Preserve]`](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute) 屬性加入至成員。  這個屬性會讓連結器無法移除它。
- 叫用 [**mtouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29)時，請使用 **-nolink** 或 **-linksdkonly** 選項：
  - **-nolink** 會停用所有連結。
  - **-linksdkonly** 只會連結 Xamarin. iOS 提供的元件（例如 **xamarin.ios.dll**），同時保留使用者建立元件中的所有類型 (也就是您的應用程式專案) 。

元件會連結，讓產生的可執行檔變得較小。
停用連結可能會導致比預期更大的可執行檔。

## <a name="youre-getting-a-modelnotimplementedexception"></a>您正在 ModelNotImplementedException

這個例外狀況表示您正在呼叫基底。方法 () 在覆寫模型的類別上。 您不需要在類別中呼叫基底方法，以取得以 [Model] 屬性標示 (類別的模型) 。

## <a name="this-class-isnt-key-value-coding-compliant-for-the-key-xxxx"></a>此類別不符合金鑰值的索引鍵值編碼

如果您在載入筆尖檔案時收到此錯誤，表示在您的 managed 類別上找不到值 XXXX。 您遺漏了如下的宣告：

```csharp
[Connect]
TypeName XXXX {
   get {
       return (TypeName) GetNativeField ("XXXX");
   }
   set {
       SetNativeField ("XXXX", value);
   }
}
```

Visual Studio for Mac 會自動產生上述定義，適用于您在檔案中新增至 Visual Studio for Mac 的任何 XIB 檔 `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` 。

此外，包含上述程式碼的類型必須是 [NSObject](xref:Foundation.NSObject)的子類別。  如果包含型別是在命名空間內，它也應該有 [[Register]](xref:Foundation.RegisterAttribute) 屬性，它會提供不含命名空間的類型名稱 (因為 Interface Builder 不支援類型) 中的命名空間：

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Interface Builder 檔案中有未知的類別 XXXX

在介面產生器檔案中定義類別，但不包括 c # 程式碼中的實際執行時，就會發生這個錯誤。

新增如下所示的程式碼：

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```

## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>MissingMethodException：找不到 Foo：： ctor (System.object) 的任何函式

當具現化從介面產生器檔案參考之類別的實例時，在執行時間會發生這個錯誤。 此錯誤表示您遺漏了接受單一 IntPtr 作為參數的函式。

具有 IntPtr 控制碼的函式是用來系結 managed 物件與其非受控表示。

若要修正這個錯誤，請將下列程式程式碼新增至類別 Foo. Bar：

```csharp
public Bar (IntPtr handle) : base (handle) { }
```

## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>類型 {Foo} 未包含的定義 `GetNativeField` ，而且 `GetNativeField` 找不到類型為 {Foo} 的擴充方法

如果您在設計工具產生的檔案中收到此錯誤 ( *. xib.designer.cs) ，這代表兩件事之一：

 **1) 遺漏部分類別或基類**

設計工具產生的部分類別在使用者程式碼中必須有對應的部分類別，而這些類別通常會繼承自的某些子類別 `NSObject` `UIViewController` 。 確定您有提供錯誤的類型類別。

 **2) 預設命名空間已變更**

設計工具檔案是使用您專案的預設命名空間設定產生的。 如果您變更了這些設定或重新命名專案，則在部分類別中產生的命名空間可能會變更。 產生的部分類別可能不再與使用者程式碼的對應專案位在相同的命名空間中。

您可以在 [專案選項] 對話方塊中找到命名空間設定。 預設命名空間位於 **一般 >的主要設定** 區段中。 如果您的專案是空白的，則會使用專案的名稱作為預設值。 [ **原始程式碼-> .Net 命名原則** ] 區段中有更多的 advanced 命名空間設定。

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>動作警告：私用方法 ' Foo ' 從未使用過。  (CS0169) 

介面產生器檔案的動作會在執行時間依反映連接至 widget，因此必須要有此警告。

如果您只想要針對這些方法隱藏此警告，您可以使用「#pragma 警告停用0169」」 #pragma 警告啟用0169」。
如果您想要針對整個專案停用它 (不建議) ，請將0169新增至編譯器選項中的 [忽略警告] 欄位。

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch 失敗，並出現下列訊息：無法開啟元件 '/path/to/yourproject.exe '

如果您看到此錯誤訊息，通常問題是您專案的絕對路徑包含空格。 這個問題將在未來的 Xamarin 版本中修正。 您可以藉由將專案移至沒有空格的資料夾來解決此問題。

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>您的 >db.sqlite3 版本已過時-請升級至至少 v 3.5.0！

當下列所有條件都成立時，就會出現此訊息：

1. 您可以參考專案中的 Mono. Sqlite
1. 您的 Mac 正在執行 macOS X Leopard (10.5) 
1. 在模擬器中執行您的應用程式。

問題在於 Mono 是挑選 OS X `libsqlite3.dylib` ，而不是 iPhoneSimulator 的檔案 `libsqlite3.dylib` 。 您的應用程式 *會* 在裝置上運作，而不是在您的模擬器上運作。

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>部署至裝置失敗，發生系統。例外狀況： AMDeviceInstallApplication 傳回3892346901

此錯誤表示您的憑證或套件組合識別碼的程式碼簽署設定不符合裝置上安裝的布建設定檔。 確認您已在 [專案選項] 中選取適當的憑證->iPhone 套件組合簽署，以及在 [專案選項->iPhone 應用程式] 中指定正確的組合識別碼。

## <a name="code-completion-isnt-working-in-visual-studio-for-mac"></a>Visual Studio for Mac 中的程式碼完成無法運作

確定您使用的是最新版本的 Visual Studio for Mac 和 Xamarin

如果問題仍然存在，請提出 [bug](https://monodevelop.com/Developers#Reporting_Bugs)，並附加 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**、 **AndroidTools-{timestamp} .LOG** 和 **Components-{TIMESTAMP} .log** 記錄檔。

如果所有其他作業失敗，您可以嘗試移除程式碼完成快取，使其重新產生：

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

請小心輸入此命令，否則您可能會不小心地移除重要的檔案。

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>當您複製文字時，Visual Studio for Mac 損毀

熱門的 Mac 公用程式 QuickSilver、Google 工具列和啟動列具有剪貼簿功能，導致 Visual Studio for Mac 的記憶體損毀。 在其選項中，您可以將 Visual Studio for Mac 列為不應該干擾的進程。

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>需要 Mono 2.4 的 Visual Studio for Mac 抱怨

如果您已更新 Visual Studio for Mac，並嘗試重新開機 Visual Studio for Mac 時，您會收到有關 Mono 2.4 不存在的訊息，請[升級您的 mono 2.4 安裝](http://www.go-mono.com/mono-downloads/download.html)。

Mono 2.4.2.3 _6 修正了一些重要的問題，導致 Visual Studio for Mac 無法可靠地執行，有時會在啟動時使 Visual Studio for Mac 停止運作，或無法產生程式碼完成資料庫。

當您安裝新 Mono 之後，Visual Studio for Mac 就會如預期般啟動。

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>判斷提示位於。/../../../mono/metadata/generic-sharing.c：704，條件 ' oti ' 不符合

如果您收到下列堆疊追蹤：

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

這表示您要將以 thumb 程式碼編譯的靜態程式庫連結至您的專案。 在此撰寫) 的 iPhone SDK 版本 3.1 (或更高版本中，當您將非 Thumb 程式)  (代碼與您的靜態程式庫 () 中的程式碼時，Apple 在連結器中引進了錯誤。 您將需要與靜態程式庫的非 Thumb 版本連結，以減輕此問題。

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1get_count-"></a>System.ExecutionEngineException：嘗試 JIT 編譯方法 (包裝函式 managed 至 managed) Foo []： System.object ' 1.get_Count () 

[] 尾碼指出您或類別庫透過泛型集合（例如 IEnumerable<>、ICollection<> 或 IList<>）在陣列上呼叫方法。 因應措施是，您可以明確地強制 AOT 編譯器透過呼叫方法來包含方法。 請確定此程式碼會在觸發例外狀況的呼叫之前執行。 在此情況下，您可以撰寫：

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

這會強制 AOT 編譯器包含 get_Count 方法。

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio for Mac 來源編輯器的速度非常慢

Visual Studio for Mac 來源編輯器有時會變得非常緩慢，似乎會在鍵入字元之間有幾秒鐘的時間停止回應。

這個問題很罕見，而且難以重現。 重新開機 Visual Studio for Mac 之後，通常無法在同一部電腦上重現。 基於這個理由，如果您可以在重新開機 Visual Studio for Mac 之前執行數個偵錯工具，並將結果傳送給我們，我們會很高興。

1. 請嘗試關閉 [編輯器] 索引標籤，然後重新開啟它。 它是否需要進行一些編輯或移動插入點，直到發生緩慢的情況？
1. 使用「Quartz Debug」開發人員工具停用「橫樑同步」 (您可以使用 [焦點) ] 來尋找，並檢查是否已將 [來源編輯器] 效能還原為 [正常]。
1. 嘗試重複步驟 (1) ，其中的橫樑同步仍停用。
1. 如果編輯器停止回應超過幾秒鐘，請嘗試在終端機中執行 "killall-QUIT [Visual Studio for Mac]"。 當編輯器沒有回應時，可能很難進行 kill 命令，但這是不可或缺的。 此命令會強制 Mono 將所有線程的堆疊追蹤寫入 MD 記錄檔。 您可以使用此記錄來探索執行緒在 Visual Studio for Mac 無回應時的狀態。

附加 XS 記錄、 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**、 **ANDROIDTOOLS-{timestamp} .log** 和 **元件-{timestamp}. log** (在舊版 XS/MonoDevelop 中，只需傳送 **~/Library/Logs/MonoDevelop- (3.0 | 2.8 | 2.6) /MonoDevelop.log**) 。

> [!NOTE]
> 上述問題已在 XS 2.2 最終 * * 中修正

## <a name="compiled-application-is-very-large"></a>編譯的應用程式很大

為了支援偵錯工具，debug 組建包含額外的程式碼。 內建的發行模式專案是大小的一小部分。

從 Xamarin. iOS 1.3 開始，debug 組建包含每一個 Mono 元件的偵錯工具支援 (每個架構類別中的每個方法) 。

使用 Xamarin. iOS 1.4，我們將介紹更精細的偵錯工具方法。
預設會為您的程式碼和程式庫提供偵錯工具，而不是所有 [Mono 元件](~/cross-platform/internals/available-assemblies.md)的偵測。
您必須選擇為所有 [Mono 元件](~/cross-platform/internals/available-assemblies.md)提供偵錯工具檢測。

## <a name="installation-hangs"></a>安裝停止回應

如果您的 iPhone 模擬器正在執行，則 Mono 和 Xamarin 安裝程式都會停止回應。 此問題不限於 Mono 或 Xamarin。 如果 iPhone 模擬器是在安裝期間執行，則在任何嘗試在 macOS 雪 Leopard 上安裝軟體的軟體，此問題都會一致。

請確定您已結束 iPhone 模擬器，然後重試安裝。

## <a name="debugger-is-unable-to-connect-with-the-device"></a>偵錯工具無法與裝置連接

當您開始錯裝置設定時，您會看到偵錯工具顯示對話方塊，指出正在嘗試連接到應用程式。 有幾個原因可能是偵錯工具可能無法連線到應用程式，這取決於您用來連接 (USB 或 WiFi) 的模式。

 **如果裝置和偵錯工具主機位於不同的網路上**，防火牆或私人網路可能會防止應用程式以 WiFi 模式連接到偵錯工具主機。

 **Visual Studio For Mac 可能無法查詢正確的主機 IP**。 在 WiFi 模式中，Visual Studio for Mac 會為應用程式提供可在主機上找到的所有 Ip。 應用程式會嘗試使用它們來連線到 Visual Studio for Mac。

 **另一個裝置連接至主機上的 USB 埠。** 在少數情況下，連線到主機上 USB 埠的其他裝置已知會有某種干擾在 USB 模式下的調試。

當 WiFi 或 USB 模式無法運作時，您可以輕鬆地嘗試另一種模式。 在 Visual Studio for Mac 中，開啟喜好設定，移至 [喜好設定]/[偵錯工具/iPhone 偵錯工具] 頁面，然後切換 [透過 WiFi 而不是透過 USB 來偵測 iOS 裝置] 核取方塊。 如果兩者都無法運作，您可以在詳細資訊模式中看到裝置主控台的失敗詳細資訊 (透過將 "-v-v" 新增至專案 [選項]) 中的其他 mtouch 引數即可啟用。

## <a name="error-134-mtouch-failed-with-the-following-message"></a>錯誤134： mtouch 失敗，並出現下列訊息：

如果您想要1.4 在 nolink 上使用-來建立的版本，可能會引發此錯誤。 您可以在 monodevelop 專案設定中指定額外的引數，以解決這個錯誤。

新增引數

 `-nosymbolstrip`

而且應該解決問題。

## <a name="distribution-identity-isnt-shown-in-visual-studio-for-mac-project-signing-options"></a>Visual Studio for Mac 專案簽署選項中未顯示發佈身分識別

Visual Studio for Mac 2.2 有 bug，導致它無法偵測包含逗點的發佈憑證。 更新為 Visual Studio for Mac 2.2.1。

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>上傳期間發生「AFCFileRefWrite 傳回：1」錯誤

將應用程式上傳至您的裝置時，您可能會收到「傳回的 AFCFileRefWrite：1」錯誤。 如果您有長度為零的檔案，就會發生這個錯誤。

## <a name="error-mtouch-failed-with-no-output"></a>錯誤「mtouch 失敗，沒有輸出」

當儲存方案或專案的專案名稱或目錄包含空格時，目前的 Xamarin. iOS 和 Visual Studio for Mac 版本會失敗。
修正方法：

- 確定您的專案及其儲存的目錄不包含任何空格。
- 在專案的 [主要設定] 中，確定專案名稱不包含任何空格。

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>錯誤「您上傳的二進位檔無效。 SDK 的發行前版本 Beta 版是用來建立應用程式」

此錯誤通常是因為在2.0.0 發行前于 iPad 開發中啟動的專案所造成。 您的資訊中可能有一些金鑰，如下所示 plist：

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

您應移除此金鑰組，因為 Visual Studio for Mac 會自動為您處理它。

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>「使用 SDK 的發行前版本 Beta 版來建立應用程式」錯誤

由 Ed Anuff 所貢獻的 () 

請遵循下列步驟：

- 將 [iPhone Build] 選項中的 SDK 版本變更為3.2。 iTunes connect 會在上傳時拒絕它，因為它看到使用小於3.2 的 SDK 版本建立的 iPad 相容應用程式
- 建立專案的自訂資訊 plist，並在其中明確地將 MinimumOSVersion 設定為3.0。 這會覆寫由 Xamarin 所設定的 MinimumOSVersion 3.2 值。 如果您沒有這樣做，應用程式將不會在 iPhone 上執行。
- 重建、壓縮及上傳至 iTunes connect。

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>未處理的例外狀況： system.exception：找不到選取器 someSelector：于 {type}

此例外狀況是由下列其中一項所造成：

1. 您已提供目標 C 執行時間的選取器，而不將對應的 [Export] 屬性套用至方法
1. 您已啟用完整連結，且未將 [Preserve] 屬性套用至 [Export] ed 方法。
1. 您已將 [Export] 屬性套用至繼承型別中的私用方法。

## <a name="mainwindowxibdesignercs-file-isnt-updated"></a>MainWindow.xib.designer.cs 檔案未更新

Xamarin Studio 2.4 中發生錯誤，導致無法在新的專案中，將 MainWindow xib 檔案與 MainWindow. xib. 設計工具檔案分組。 這表示它不會更新該特定檔案的設計工具程式碼。

Visual Studio for Mac 的版本已修正此問題，請使用內建的更新程式升級至可用的最新版本。

您可以藉由移除 (不刪除) xib 和其設計工具檔案，然後將其新增回去來修正現有的專案。 檔案將會正確地分組在一起。

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView 或 UIActionSheet 會在建立後消失

如果您有一些像這樣的程式碼：

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

"引導" 物件在函式中會以暫存變數的形式存在。 一旦函式終止，物件就有資格進行垃圾收集。
最後會在通過中進行垃圾收集。

若要修正這個問題，您必須在方法以外的地方保留 "引導" 的參考，而該方法會存留于您的方法之外。

## <a name="project-always-runs-in-the-ipad-simulator"></a>Project 一律在 iPad 模擬器中執行

IPhone SDK 4.0 安裝程式會安裝2個 Sdk。 3.2 SDK，用於建立僅限 iPad 的應用程式。 和 4.0 SDK，用於建立 iPhone 和通用應用程式。 它也會安裝3.2 模擬器，它只會模擬 iPad，以及模擬 iPhone 或 iPhone 4 的4.0 模擬器。 所有舊版 Sdk 和模擬器都已移除。

Visual Studio for Mac iPhone 專案組建選項包含將用來建立應用程式之 SDK 版本的設定。 您可以在 [專案選項] 中找到這項設定 **->組建->IPhone Build**。

Visual Studio for Mac 中的新專案會使用最早安裝的 SDK 作為其預設 SDK 設定。 如果指定的 SDK 不存在，Visual Studio for Mac 將會使用最接近的可用 SDK 來建立您的應用程式。 這樣做的目的是為了讓專案不需要最新的 SDK。 不過，這目前會導致使用 3.2 SDK-這會導致使用 iPad 模擬器。

若要使用 4.0 SDK 來修正此問題，請移至 **專案選項->組建->IPhone Build**> 並使用下拉式方塊將 SDK 值變更為 "4.0"。 您必須針對每個設定和平臺組合進行這項變更，並使用面板頂端的下拉式清單來存取此組合。

SDK 版本不應與「最低 OS 版本」設定混淆。
此值不需要符合 SDK 版本值-它會影響您的應用程式將安裝的最小 OS 版本（可以比 SDK 還舊的版本），只要您只使用舊版作業系統中的 Api，或使用執行時間作業系統版本檢查來保護較新功能的使用。 將它設定為您用來測試應用程式的最舊 OS 版本。

**專案 >iPhone** 模擬器的 [目標]> 功能表可用來挑選執行或對專案進行偵錯工具時預設使用的模擬器。 此外，您可以使用 [ **執行 >與**>] 功能表來挑選要執行的特定模擬器。

## <a name="ibtool-returns-error-133"></a>ibtool 傳回錯誤133

此錯誤表示您已安裝 XCode 4。 在 XCode 4 中，已移除 ibtool。 您無法再使用獨立工具來編輯 XIB 檔。

如果您想要使用 ibtool，請安裝可從 Apple 網站取得的 XCode 系列3。

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-interface-builder"></a>「無法建立 mime 類型的顯示系結： application/vnd.openxmlformats-officedocument.spreadsheetml.sheet. apple-interface-builder」

如果您嘗試從非 iPhone 專案建立 iPhone UI，就會發生此錯誤。 請確定您是從 iPhone/iPad 解決方案開始，無法只將 iPhone UI 元素新增至非 iPhone/iPad 專案。

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>在 iOS 模擬器內部執行時啟動損毀

如果您收到執行時間損毀 (在模擬器內部 SIGSEGV) ，以及如下所示的堆疊追蹤：

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```

...然後，您可能會在您的模擬器應用程式目錄中有一個 (或多個) 過時的元件。 這類元件可能存在，因為 Apple iOS 模擬器會新增和更新檔案，但永遠不會刪除它們。 最簡單的解決方案是選取 [重設及內容及設定 ...]從模擬器功能表。

> [!WARNING]
> 這會從模擬器中移除所有檔案、應用程式和資料。   下次當您執行應用程式時，Visual Studio for Mac 會將它部署至模擬器，而且沒有任何舊的過時元件會造成損毀。

## <a name="simulator-hangs-during-application-installation"></a>模擬器在應用程式安裝期間停止回應

當應用程式名稱在其名稱中包含 '. ' (點) 時，就會發生此錯誤。
在 CFBundleExecutable 中，不允許在可執行檔名稱中使用 '. ' (點) ，即使它在許多其他情況下都能使用 (例如) 的裝置。

「此值不應該包含任何副檔名的名稱。」

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clickingxib-files"></a>錯誤： double-clicking. xib 檔案時，不支援自訂屬性類型0x43

當環境變數設定不正確時，嘗試開啟 xib 檔案，就會導致此訊息。 Visual Studio for Mac 和 Xamarin 一般使用方式不會發生此錯誤。 從/Applications 重新開啟 Visual Studio for Mac 應該可修正問題。

嘗試更新軟體並出現此錯誤訊息時，電子郵件 *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>在模擬器上執行的應用程式，但裝置上的失敗

此問題可能會以數種形式出現，而且不一定會產生一致的錯誤。 如果應用程式包含 xib，請檢查以確定 xib 的 [ **組建] 動作** 設定為 [ **InterfaceDefinition**]。 這是 xib 的預設組建動作。

若要檢查組建動作，請以滑鼠右鍵按一下 xib 檔案，然後選擇 [ **建立動作**]。

## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>NotSupportedException：沒有任何資料可用於編碼437

在您的 Xamarin iOS 應用程式中包含協力廠商程式庫時，您可能會在嘗試編譯及執行應用程式時，收到「NotSupportedException：沒有資料可供編碼437」的錯誤。 例如，程式庫（例如 `Ionic.Zip.ZipFile` ）可能會在作業期間擲回此例外狀況。

您可以開啟 Xamarin 專案的選項，前往 **ios 組建**  >  **國際化** 並檢查 **West** 國際化，以解決此例外狀況。

## <a name="could-not-launch-xamarinlauncher-could-not-find-the-executable-mlaunchexe"></a>無法啟動 Xamarin。啟動器找不到可執行檔 ' mlaunch.exe '

在某些情況下，防毒軟體可能會錯誤地將 Xamarin. iOS SDK 標示為惡意程式碼，並移除必要的檔案，並損毀 SDK。 這會導致錯誤，例如「無法啟動 Xamarin。啟動器找不到可執行檔 ' mlaunch.exe '」。

如果您受到影響，請從防毒軟體掃描程式中排除 mlaunch.exe，以防止重複執行。 如需詳細資訊，請參閱 [如何在 symantec 的 Symantec Endpoint Protection Manager 中建立應用程式例外](https://knowledge.broadcom.com/external/article/180778/how-to-create-an-application-exception-i.html) 狀況，以及 [從諾頓的自動保護、聲納和下載智慧掃描中排除檔案和資料夾](https://support.norton.com/sp/en/uk/home/current/solutions/v3672136) 。 此外，請考慮將錯誤正面回報給 [Symantec](https://symsubmit.symantec.com) 或 [諾頓](https://submit.norton.com/?type=FP)。

> [!IMPORTANT]
> 若為諾頓使用者，則小組成員也會回報您可能需要停用閒置掃描，如下所述： [Windows](https://community.norton.com/en/comment/5179683#comment-5179683)、 [Mac](https://github.com/xamarin/xamarin-macios/issues/8736#issuecomment-642874505)。

新增 mlaunch.exe 的排除之後，將需要重新安裝才能還原遺失的檔案。 只要切換更新程式中的通道，即可重新安裝：

- **Visual Studio** 功能表 > **檢查是否有更新**。
- 在下拉式清單中選取不同的更新通道，然後按下 [ **切換通道** ] 按鈕。
- 等候下載更新。
- 切換回原始頻道並安裝更新。

如果這些指示無法解決您的問題，請在下列 GitHub 問題中新增批註： [8736](https://github.com/xamarin/xamarin-macios/issues/8736)。
