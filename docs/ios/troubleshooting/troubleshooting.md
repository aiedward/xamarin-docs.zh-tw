---
title: 適用于 Xamarin 的疑難排解秘訣
description: 本檔提供在開發 Xamarin iOS 應用程式期間用於疑難排解的各種秘訣。 它會描述特定的錯誤訊息，以及其他潛在的問題。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/22/2018
ms.openlocfilehash: 716999002cf90b50b90f4924adc11555cc43717f
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915388"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>適用于 Xamarin 的疑難排解秘訣

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin. iOS 無法解析 System.valuetuple

此錯誤是因為與 Visual Studio 不相容所造成。

- **Visual Studio 2017 Update 1** （15.1 版或更舊版本）只與**system.valuetuple NuGet 4.3.0** （或更舊版本）相容。

- **Visual Studio 2017 Update 2** （15.2 版或更新版本）僅與**system.valuetuple NuGet 4.3.1**或更新版本相容。

請選擇與您的 Visual Studio 2017 安裝對應的正確 System.valuetuple NuGet。

## <a name="receiving-error-retrieving-update-information-error-message"></a>收到「抓取更新資訊時發生錯誤」錯誤訊息

當嘗試更新軟體並出現此錯誤訊息時，請嘗試重新開機您的 IDE，並登出，然後回到 IDE 中的帳戶。

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>如何? 使用 Interface Builder 建立輸出或動作？

隨著 Visual Studio for Mac 和 Visual Studio 中的 Xamarin Designer for iOS 引進，Xamarin。 iOS 開發人員現在可以利用透過分鏡腳本和 xib 來建立 UI。 如需使用設計工具的詳細資訊，請參閱[Hello，iOS](~/ios/get-started/hello-ios/index.md)指南。

您也可以參閱 Apple 的[輸出](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html)和[動作](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html)指南，以取得在 IB 中使用輸出和動作的詳細資訊。

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>Encoding.getencoding 擲回 NotSupportedException

您可能使用預設未新增的編碼方式。 請查看 [[國際化](~/ios/app-fundamentals/localization/index.md)] 頁面，以瞭解如何新增更多編碼的支援。

## <a name="systemmissingmethodexception-anything-else"></a>MissingMethodException （其他任何專案）

連結器可能已移除該成員，因此在執行時間並不存在於元件中。  有幾個解決辦法：

- 將[`[Preserve]`](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute)屬性加入至成員。  這會讓連結器無法移除它。
- 叫用[**mtouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29)時，請使用 **-nolink**或 **-linksdkonly**選項：
  - **-nolink**會停用所有連結。
  - **-linksdkonly**只會連結 xamarin ios 提供的元件（例如**xamarin**），同時保留使用者建立元件（即您的應用程式專案）中的所有類型。

請注意，元件是連結的，因此產生的可執行檔較小;因此，停用連結可能會導致比預期更大的可執行檔。

## <a name="you-are-getting-a-modelnotimplementedexception"></a>您正在取得 ModelNotImplementedException

如果您收到這個例外狀況，這表示您正在呼叫 base。在覆寫模型的類別上的方法（）。 您不需要在模型的類別中呼叫基底方法（這些是以 [Model] 屬性標記的類別）。

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>此類別不符合金鑰 XXXX 的索引鍵值編碼

如果您在載入筆尖檔案時收到此錯誤，表示在您的 managed 類別上找不到值 XXXX。 這表示您遺失的宣告如下：

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

上述定義會針對您新增至 `NAME_OF_YOUR_XIB_FILE.designer.xib.cs` 檔案中 Visual Studio for Mac 的任何 XIB 檔案 Visual Studio for Mac 自動產生。

此外，包含上述程式碼的類型必須是[NSObject](xref:Foundation.NSObject)的子類別。  如果包含類型是在命名空間內，它也應該有一個[[Register]](xref:Foundation.RegisterAttribute)屬性，它會提供不含命名空間的類型名稱（因為 Interface Builder 不支援類型中的命名空間）：

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>Interface Builder 檔案中有未知的類別 XXXX

如果您在介面產生器檔案中定義類別，但未在程式C#代碼中提供它的實際執行，則會產生此錯誤。

您需要新增一些程式碼，如下所示：

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```

## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>MissingMethodException：找不到 Foo. Bar：： ctor （system.string）的任何函數

當程式碼嘗試將您從 Interface Builder 檔案參考的類別實例具現化時，會在執行時間產生此錯誤。 這表示您忘了新增使用單一 IntPtr 做為參數的函式。

使用 IntPtr 控制碼的函式是用來系結受管理物件及其非受控表示。

若要修正此問題，請將下列程式程式碼新增至類別 Foo. Bar：

```csharp
public Bar (IntPtr handle) : base (handle) { }
```

## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>類型 {Foo} 不包含 `GetNativeField` 的定義，而且找不到任何類型 {Foo} 的擴充方法 `GetNativeField`

如果您在設計工具產生的檔案（*. xib.designer.cs）中收到此錯誤，則表示這兩個專案的其中一項：

 **1）遺漏部分類別或基類**

設計工具產生的部分類別必須在使用者程式碼中有對應的部分類別，而這些類別是繼承自 `NSObject`的某個子類別，通常是 `UIViewController`。 請確定您有提供錯誤之類型的類別。

 **2）已變更預設命名空間**

設計工具檔案是使用您專案的預設命名空間設定產生的。 如果您變更了這些設定，或重新命名了專案，則產生的部分類別可能不再與其使用者程式碼對應的命名空間相同。

命名空間設定可以在 [專案選項] 對話方塊中找到。 預設的命名空間位於**一般 > 的 [主要設定**] 區段中。 如果它是空白的，您的專案名稱會當做預設值使用。 您可以在**原始程式碼-> .Net 命名原則**一節中找到更多的 advanced namespace 設定。

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>動作的警告：絕對不會使用私用方法 ' Foo '。 CS0169

介面產生器檔案的動作會在執行時間依反映連接到 widget，因此應該會出現這項警告。

如果您想要針對這些方法隱藏此警告，您可以使用「#pragma 警告停用0169」」 #pragma 警告啟用0169」，或者如果您想要針對整個專案停用此警告，請將0169新增至 [忽略警告] 欄位（不建議）。

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch 失敗，並出現下列訊息：無法開啟元件 '/path/to/yourproject.exe '

如果您看到此錯誤訊息，通常問題是專案的絕對路徑包含一個空格。 這在未來的 Xamarin 版本中將會修正，但是您可以將專案移至不含空格的資料夾，以解決此問題。

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>您的 db.sqlite3 版本已過時，請至少升級至 v 3.5.0！

當您執行下列所有動作時，就會發生這種情況：

1. 使用 Mono. Sqlite
1. 使用 Mac OS X Leopard （10.5）
1. 在模擬器中執行您的應用程式。

問題在於 Mono 會挑選 OS X `libsqlite3.dylib`，而不是 Iphonesimulator; 的 `libsqlite3.dylib` 檔案。 您的應用程式*會*在裝置上工作，而不是您的模擬器。

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>使用系統部署至裝置失敗。例外狀況： AMDeviceInstallApplication 傳回3892346901

此錯誤表示憑證/套件組合識別碼的程式碼簽署設定與裝置上安裝的布建設定檔不相符。  確認您已在 [專案選項-> iPhone 配套簽署] 中選取適當的憑證，並在 [專案選項-> iPhone 應用程式] 中指定正確的配套識別碼

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>程式碼完成無法在 Visual Studio for Mac 中運作

請確定您使用的是最新版本的 Visual Studio for Mac 和 Xamarin. iOS

如果問題仍然存在，請提出[錯誤](https://monodevelop.com/Developers#Reporting_Bugs)、附加 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**、 **AndroidTools-{timestamp} .LOG**和**元件-{timestamp} .log**記錄檔。

如果其他所有動作都失敗，您可以嘗試移除程式碼完成快取，使其重新產生：

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

請小心輸入此命令，或者您可能會不小心移除重要的檔案。

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>當您複製文字時 Visual Studio for Mac 損毀

[常用的 Mac 公用程式 QuickSilver]、[Google 工具列] 和 [啟動列] 的剪貼簿功能已損毀 Visual Studio for Mac 的記憶體 在其選項中，您可以將 Visual Studio for Mac 列為不應干擾的進程。

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>需要有關 Mono 2.4 的 Visual Studio for Mac 抱怨

如果您因為最近的更新而更新 Visual Studio for Mac，而且您嘗試再次啟動它時，抱怨有關 Mono 2.4 不存在，您只需要[升級 mono 2.4 安裝](http://www.go-mono.com/mono-downloads/download.html)即可。  

Mono 2.4.2.3 _6 會修正一些導致 Visual Studio for Mac 無法可靠執行的重要問題，有時會在啟動時停止回應 Visual Studio for Mac 或防止產生程式碼完成資料庫。

當您安裝新的 Mono 之後，Visual Studio for Mac 會如預期般啟動。

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>判斷提示位於。/../../../mono/metadata/generic-sharing.c：704，條件 ' oti ' 不符合

如果您收到下列堆疊追蹤：

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

這表示您要將以 thumb 程式碼編譯的靜態程式庫連結至您的專案。 自 iPhone SDK 3.1 版（或在撰寫本文時更高版本）開始，Apple 在連結非 Thumb 程式碼（Xamarin. iOS）與 Thumb 程式碼（您的靜態程式庫）時，在連結器中引進了 bug。您必須使用靜態程式庫的非 Thumb 版本來進行連結，以減輕這個問題。

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1get_count-"></a>ExecutionEngineException：正在嘗試 JIT 編譯方法（包裝函式 managed） Foo []： system.string ' 1. get_Count （）

[] 尾碼表示您或類別庫正透過泛型集合在陣列上呼叫方法，例如 IEnumerable < >、ICollection < > 或 IList < >。 因應措施是，您可以明確地強制 AOT 編譯器藉由呼叫方法來包含這類方法，並確保在觸發例外狀況的呼叫之前執行此程式碼。 在此情況下，您可以撰寫：

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

這會強制 AOT 編譯器包含 get_Count 方法。

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio for Mac 原始檔編輯器的速度非常慢

有時候 Visual Studio for Mac 來源編輯器會變得非常緩慢，似乎在輸入字元之間有幾秒鐘的時間停止回應。

這個問題非常罕見，而且非常難以重現-在重新開機 Visual Studio for Mac 之後，通常無法在同一部電腦上重現此問題。 基於這個理由，如果您在重新開機 Visual Studio for Mac 之前可以執行數個偵錯工具，我們會非常感謝，並將結果傳送給我們。

1. 請嘗試關閉 [編輯器] 索引標籤，然後重新開啟它。 是否需要稍微編輯或移動插入號，直到速度變慢的情況？
1. 使用「Quartz Debug」開發人員工具（您可以使用焦點來尋找）來停用「橫樑同步」，並檢查來源編輯器效能是否還原為正常。
1. 嘗試重複步驟（1），並將 [橫樑同步] 保持為停用。
1. 如果編輯器停止回應超過幾秒鐘，請嘗試在終端機中執行 "killall-QUIT [Visual Studio for Mac]"。 當編輯器停止回應時，可能會很難以結束 kill 命令，但這是必要的動作，因為命令會強制 Mono 將所有線程的堆疊追蹤寫入 MD 記錄檔中，我們可以使用這些內容來探索當 XS 停止回應時執行緒所在的狀態。

請附加 XS 記錄檔 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **ANDROIDTOOLS-{timestamp} .log**和**元件-{timestamp} .log** （在較舊版本的 XS/MonoDevelop 中，只傳送 **~/Library/Logs/MonoDevelop-（3.0 | 2.8 | 2.6）/MonoDevelop.log**）。

> [!NOTE]
> 上述問題已在 XS 2.2 最後的版本中修正 * *

## <a name="compiled-application-is-very-large"></a>編譯的應用程式非常大

為了支援偵錯工具，debug build 包含額外的程式碼。 [發行] 模式內建的專案是大小的一小部分。

從 Xamarin. iOS 1.3，debug 組建包含 Mono 之每個單一元件（架構的每個類別）的偵錯工具支援。  

使用 Xamarin. iOS 1.4，我們將為您介紹更精細的偵錯工具方法，預設值是只提供程式碼和程式庫的偵錯工具檢測，而不是對所有[Mono 元件](~/cross-platform/internals/available-assemblies.md)執行此動作（這仍然可行，但您必須選擇使用這些元件來進行偵錯工具）。

## <a name="installation-hangs"></a>安裝停止回應

如果您有執行 iPhone 模擬器，則 Mono 和 Xamarin 安裝程式都會停止回應。 這個問題不限於 Mono 或 Xamarin，這在安裝時，如果 iPhone 模擬器正在執行，則任何嘗試在 MacOS 雪 Leopard 上安裝軟體的軟體都是一致的問題。

請確定您已結束 iPhone 模擬器，然後再試一次安裝。

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>已用盡 trampolines 類型0

如果您在執行裝置時收到此訊息，您可以藉由修改專案選項「iPhone 組建」一節來建立更多類型 0 trampolines （類型特定）。  您想要為裝置組建目標新增額外的引數：

 `-aot "ntrampolines=2048"`

預設的 trampolines 數目為1024。 請嘗試增加此數位，直到您的應用程式夠好。

## <a name="ran-out-of-trampolines-of-type-1"></a>已用完類型1的 trampolines

如果您大量使用遞迴泛型，您可能會在裝置上收到此訊息。  您可以藉由修改專案選項 [iPhone Build] 區段，建立更多類型 1 trampolines （類型 RGCTX）。  您想要為裝置組建目標新增額外的引數：

 `-aot "nrgctx-trampolines=2048"`

預設的 trampolines 數目為1024。 請嘗試增加此數位，直到您已足夠使用泛型。

## <a name="ran-out-of-trampolines-of-type-2"></a>已用盡 trampolines 類型2

如果您使用大量的介面，您可能會在裝置上收到此訊息。
您可以藉由修改專案選項 [iPhone Build] 區段，來建立更多類型 2 trampolines （類型 IMT Thunk）。  您想要為裝置組建目標新增額外的引數：

 `-aot "nimt-trampolines=512"`

預設的 IMT Thunk trampolines 數目是128。 請嘗試增加此數位，直到您已足夠使用介面。

## <a name="debugger-is-unable-to-connect-with-the-device"></a>偵錯工具無法與裝置連接

當您開始對裝置設定進行檢查時，您會看到偵錯工具顯示一個對話方塊，指出它正嘗試連接到應用程式。 根據您用來連線的模式（USB 或 WiFi），偵錯工具可能無法連接到應用程式的原因有好幾個。

 **如果裝置和偵錯工具主機位於不同的網路上**，防火牆或私人網路可能會導致應用程式無法以 WiFi 模式連接到偵錯工具主機。

 **Visual Studio for Mac 可能無法查詢主機的正確 IP**。 在 WiFi 模式中 Visual Studio for Mac 會為應用程式提供它可以尋找主機的所有 Ip，而應用程式會嘗試全部，以查看是否可以使用其中任何一個來連線到 Visual Studio for Mac。

 **另一個裝置已連接到主機上的 USB 埠。** 在少數情況下，連接到主機上 USB 埠的其他裝置已知會在 USB 模式中干擾調試。

如果 WiFi 或 USB 模式無法運作，您可以輕鬆地嘗試另一個：在 Visual Studio for Mac 中，開啟喜好設定，移至 [喜好設定/偵錯工具/iPhone 偵錯工具] 頁面，然後切換 [透過 WiFi 而非透過 USB 來偵測 iOS 裝置] 核取方塊。   如果沒有作用，您可以在 [詳細資訊] 模式中查看裝置主控台失敗的詳細資訊（藉由將 "-v-v" 新增至專案選項中的其他 mtouch 引數來啟用）。

## <a name="error-134-mtouch-failed-with-the-following-message"></a>錯誤134： mtouch 失敗，並出現下列訊息：

如果您嘗試使用-nolink 在1.4 版的版本上建立，則可能會引發此錯誤。 您可以藉由在您的 monodevelop 專案設定中指定額外的引數，來解決這個錯誤。

新增引數

 `-nosymbolstrip`

而且應該解決此問題。

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>Visual Studio for Mac 專案簽署選項中未顯示發佈身分識別

Visual Studio for Mac 2.2 有一個 bug，導致它不會偵測到包含逗號的散發憑證。 請更新為 Visual Studio for Mac 2.2.1。

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>上傳期間發生錯誤「AFCFileRefWrite 傳回：1」

將應用程式上傳至您的裝置時，您可能會收到「AFCFileRefWrite 傳回：1」錯誤。 如果您有長度為零的檔案，就會發生這種情況。

## <a name="error-mtouch-failed-with-no-output"></a>「Mtouch 失敗，沒有輸出」錯誤

當儲存方案或專案的專案名稱或目錄包含空格時，目前的 Xamarin. iOS 和 Visual Studio for Mac 版本會失敗。
若要修正此問題：

- 請確定您的專案或其儲存所在的目錄都不包含空格。
- 在您的專案「主要設定」中，請確定專案名稱不包含任何空格。

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>錯誤「您上傳的二進位檔無效。 SDK 的發行前版本 Beta 版用來建立應用程式」

此錯誤通常是在2.0.0 發行之前于 iPad 開發中啟動的專案所造成，您的資訊中可能有一些金鑰。 plist，如下所示：

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

應該移除此金鑰組，因為 Visual Studio for Mac 會自動為您處理。

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>「用來建立應用程式的 SDK 發行前版本 Beta 版」錯誤

（由 Ed Anuff 貢獻）

請遵循下列步驟：

- 將 iPhone Build 中的 SDK 版本變更為3.2 或 iTunes connect 會在上傳時拒絕它，因為它會看到使用低於3.2 的 SDK 版本所建立的 iPad 相容應用程式
- 建立專案的自訂 plist，並在其中將 MinimumOSVersion 明確設定為3.0。   這會覆寫 Xamarin 所設定的 MinimumOSVersion 3.2 值。   如果您未這麼做，應用程式將無法在 iPhone 上執行。
- 重建、壓縮並上傳至 iTunes connect。

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>未處理的例外狀況： System.object：找不到選取器 someSelector：在 {type} 上

這個例外狀況是由下列三個專案的其中一項所造成：

1. 您已為目標-C 執行時間提供選取器，但未將對應的 [Export] 屬性套用至方法
1. 您已啟用完整連結，但未將 [Preserve] 屬性套用至 [Export] ed 方法。
1. 您已將 [Export] 屬性套用至繼承類型中的私用方法。

## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>未更新 MainWindow.xib.designer.cs 檔案

Xamarin Studio 2.4 中有一個 bug，導致它不會在新專案中將 Mainwindow.xaml xib 檔案與 Mainwindow.xaml 檔案群組在一起。 也就是說，它不會更新該特定檔案的設計工具程式碼。

此問題已在內建更新程式中提供的 Visual Studio for Mac 版本中修正，因此請確定您使用的是較新的版本。

您可以藉由移除（而不是刪除） xib 及其設計工具檔案，然後將其新增回去來修正現有的專案。 這應該會正確地重新群組檔案。

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView 或 UIActionSheet 在建立後消失

如果您有像這樣的程式碼：

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

"上動作表" 物件在函式中是以暫存變數的形式存在，一旦函式終止，物件就符合垃圾收集的資格，因此最終會進行垃圾收集。

若要修正這個問題，您需要在方法以外的地方保留 "上動作表" 的參考，而這會在您的方法外。

## <a name="project-always-runs-in-the-ipad-simulator"></a>專案一律在 iPad 模擬器中執行

IPhone SDK 4.0 安裝程式會安裝2個 Sdk-3.2 SDK，用於建立僅限 iPad 的應用程式，以及 4.0 SDK，用於建立 iPhone 和通用應用程式。 它也會安裝3.2 模擬器，此模擬器只會模擬一個 iPad，以及一個模擬 iPhone 或 iPhone 4 的4.0 模擬器。 所有舊版的 Sdk 和模擬器都會被移除。

Visual Studio for Mac iPhone 專案組建選項包含將用來建立應用程式之 SDK 版本的設定。 這項設定可以在 [**專案選項->] [組建-> IPhone 組建**] 中找到。

Visual Studio for Mac 中的新專案會使用最舊的已安裝 SDK 做為其預設 SDK 設定，而且如果指定的 SDK 不存在，Visual Studio for Mac 會使用最接近的功能來建立您的應用程式。 這麼做是為了讓專案不一定需要最新的 SDK。 不過，這目前會導致使用 3.2 SDK-這會導致使用 iPad 模擬器。

若要使用 4.0 SDK 修正此問題，請移至 **專案選項-> > IPhone 組建** >，然後使用下拉式方塊將 SDK 值變更為 "4.0"。 您必須針對每個設定和平臺組合執行此動作，使用面板頂端的下拉式清單來存取。

SDK 版本不應與「最低 OS 版本」設定混淆。
此值不需要符合 SDK 版本值-它會影響您的應用程式將安裝的 OS 最低版本（可以比 SDK 舊），只要您只使用舊版 OS 中存在的 Api，或使用執行時間作業系統版本簽來保護較新功能的使用ks. 您應該將它設定為您在其中測試應用程式的最舊 OS 版本。

另請注意，**專案 > iPhone 模擬器目標**> 功能表可用來挑選在執行/偵錯工具時預設使用的模擬器。 此外，使用 > 功能表執行 **>** 執行，可以選擇要執行的特定模擬器

## <a name="ibtool-returns-error-133"></a>ibtool 傳回錯誤133

這表示您已安裝 XCode 4。   在 XCode 4 中，已移除工具 ibtool，因此無法再使用獨立工具來編輯您的 XIB 檔案。

如果您想要使用 Interface Builder，請安裝可從 Apple 網站取得的 XCode 系列3。

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-interface-builder"></a>「無法建立 mime 類型的顯示系結： application/application. apple-interface-builder」

如果您嘗試從非 iPhone 專案建立 iPhone UI，就會發生此錯誤。 請確定您是從 iPhone/iPad 解決方案開始，不可能只是將 iPhone UI 元素新增至非 iPhone/iPad 專案。

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>在 iOS 模擬器內執行時啟動損毀

如果您在模擬器內取得執行時間損毀（SIGSEGV）以及堆疊追蹤，如下所示：

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```

...接著，您的模擬器應用程式目錄中可能有一個（或多個）過時的元件。 這類元件可能存在，因為 Apple iOS 模擬器會新增和更新檔案，但絕不會刪除檔案。 如果發生這種情況，最簡單的解決方式是選取 [重設和內容和設定 ...]從模擬器功能表。   

> [!WARNING]
> 這會從模擬器移除所有檔案、應用程式和資料。   下次執行應用程式時，Visual Studio for Mac 會將它部署到模擬器，而且不會有任何舊的過時元件來造成損毀。

## <a name="simulator-hangs-during-application-installation"></a>模擬器在應用程式安裝期間停止回應

當應用程式名稱包含 '. ' 時，就可能發生這種情況。其名稱中的（點）。
這會被禁止為 CFBundleExecutable 中的可執行檔名稱，即使它可以在許多其他情況下運作（例如裝置）也一樣。

「值不應在名稱中包含任何延伸模組」。

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>錯誤：按兩下 xib 檔案時，不支援自訂屬性類型0x43

這是因為當環境變數設定不正確時，嘗試開啟 xib 檔案所造成的。 這應該不會在 Visual Studio for Mac/Xamarin 的正常使用方式下發生，而且從/Applications 重新開啟 Visual Studio for Mac 應該會修正問題。

嘗試更新軟體並出現此錯誤訊息時，請寄電子郵件 *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>應用程式在模擬器上執行，但在裝置上失敗

此問題可能會以數種形式出現，而且不一定會產生一致的錯誤。 如果應用程式包含 xib，請檢查並確定 xib 上的 [**建立] 動作**已設定為 [ **InterfaceDefinition**]。 這是 xib 的預設組建動作。

若要檢查組建動作，請以滑鼠右鍵按一下 xib 檔案，然後選擇 [**建立動作**]。

## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>NotSupportedException：沒有可供編碼437使用的資料

在您的 Xamarin iOS 應用程式中包含協力廠商程式庫時，在嘗試編譯和執行應用程式時，您可能會收到「NotSupportedException：沒有資料可供編碼437」形式的錯誤。 例如，程式庫（例如 `Ionic.Zip.ZipFile`）可能會在操作期間擲回此例外狀況。

這可以藉由開啟 Xamarin 專案的選項來解決，前往**Ios Build** > **國際化**並檢查**West**國際化。
