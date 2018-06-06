---
title: Xamarin.iOS 的疑難排解秘訣
description: 本文件提供各種適合 Xamarin.iOS 應用程式開發期間疑難排解的秘訣。 它會描述特定的錯誤訊息，以及其他潛在問題。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/22/0201
ms.openlocfilehash: 26fe2fb848fb81940bc01a34c69b1b28897005dc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789349"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>Xamarin.iOS 的疑難排解秘訣 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin.iOS 無法解析 System.ValueTuple

這個錯誤是因為 Visual Studio 中的不相容。

- **Visual Studio 2017 Update 1** （15.1 或較舊版本） 才相容**System.ValueTuple NuGet 4.3.0** （或舊版）。

- **Visual Studio 2017 Update 2** （版本 15.2 或更新版本） 是只與相容**System.ValueTuple NuGet 4.3.1**或更新版本。

請選擇您的 Visual Studio 2017 安裝對應的正確 System.ValueTuple NuGet。


## <a name="receiving-error-retrieving-update-information-error-message"></a>收到 '擷取更新資訊時發生錯誤' 錯誤訊息

嘗試更新的軟體及此錯誤訊息出現時，請嘗試重新啟動您 IDE 和登出，然後回到在您的帳戶，在 IDE 中。

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>如何使用介面產生器建立插座或動作？

適用於 Mac 和 Visual studio 的 Xamarin 設計工具，適用於 Visual Studio 中的 iOS 引入，Xamarin.iOS 開發人員可以立即利用建立分鏡腳本和.xibs 透過 UI。 請參閱[Hello，iOS](~/ios/get-started/hello-ios/index.md)輔助線，如需有關使用設計工具。

您也可以指 Apple[插座](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html)和[動作](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html)IB 在使用插座和動作的詳細資訊的指南。

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding 擲回 NotSupportedException

您可能不會加入預設的編碼方式使用。 請檢查[國際化](~/ios/app-fundamentals/localization/index.md)頁面，即可了解如何將更多的編碼方式。

## <a name="systemmissingmethodexception-anything-else"></a>System.MissingMethodException （其他）

成員可能已移除的連結器，並因此不存在於執行階段組件。  有數個解決方案：

-  新增[[保留]](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute)屬性的成員。  如此可防止連結器將它移除。
-  叫用時[mtouch](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29) ，使用 **-nolink**或 **-linksdkonly**選項。 -    **-nolink**停用所有連結。
-    **-linksdkonly**將只 Xamarin.iOS 提供組件連結，例如*monotouch.dll*或 xamarin.ios.dll。

請注意，組件連結，以便產生的可執行檔是較小。因此，停用連結可能會導致比理想的較大型可執行檔。

## <a name="you-are-getting-a-modelnotimplementedexception"></a>您要取得 ModelNotImplementedException

如果您要取得這個例外狀況表示您要呼叫基底。類別會覆寫模型上的方法 （)。 您不需要呼叫類別中的基底方法的模型 （這些是與 [模型] 屬性標示的類別）。

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>這個類別不是索引鍵值編碼標準 XXXX 索引鍵

如果載入 NIB 檔案，這表示您的 managed 類別找不到 XXXX 的值時，您會收到這個錯誤。 這表示您遺漏的宣告如下：

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

在上述定義自動產生的 Visual Studio for Mac 加入至 Visual Studio for Mac 中的任何 XIB 檔案`NAME_OF_YOUR_XIB_FILE.designer.xib.cs`檔案。

此外，包含上述的程式碼的類型必須是子類別[NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)。  如果包含的型別是在命名空間中，它也應該有[[註冊]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/)屬性提供型別沒有命名空間名稱 （如介面產生器不支援在類型中的命名空間）：

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>未知的類別介面產生器檔案中的 XXXX

如果您定義介面產生器檔案中的類別，但您未提供為其 C# 程式碼中的實際實作，會產生這個錯誤。

您需要加入一些程式碼如下：

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System.MissingMethodException： 沒有建構函式找到 Foo.Bar::ctor(System.IntPtr)

這個程式碼嘗試具現化您從介面產生器檔案參考類別的執行個體時，會在執行階段產生這個錯誤。 這表示您忘了將會做為參數的單一 IntPtr 建構函式。

IntPtr 控制代碼與建構函式用來繫結其 unmanaged 的表示法與受管理的物件。

若要修正此問題，請將下列程式碼加入類別 Foo.Bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>Type {Foo} 不包含的定義`GetNativeField' and no extension method `GetNativeField' 的型別無法找到 {Foo}

如果您收到此錯誤在設計工具產生的檔案 (*。 xib.designer.cs)，表示其中一個兩件事：

 **1） 遺漏部分類別或基底類別**

在設計工具產生的部分類別必須有對應的部分類別使用者程式碼中的某些子類別會繼承`NSObject`，通常`UIViewController`。 確定您有這種類別提供錯誤的類型。

 **2） 預設命名空間變更**

設計工具的檔案是使用您的專案預設命名空間設定所產生的。 如果您變更這些設定，或重新命名專案，產生的部分類別可能不再是使用者程式碼與其相同的命名空間中。

專案選項 對話方塊中，可以找到命名空間設定。 預設命名空間中找到**一般]-> [主要設定**> 一節。 如果是空白，預設會使用您的專案名稱。 中可以找到更多進階的命名空間設定**原始碼.NET Naming Policies]-> [** > 一節。

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>動作的警告: 'Foo' 從未使用過的私用方法。 (CS0169)

動作的介面產生器檔案相連的 widget 所反映在執行階段，以便預期這個警告。

您可以使用 [#pragma 警告停用 0169年]"#pragma 警告啟用 0169 」 解決您的動作如果您想要隱藏這個警告只針對這些方法，或如果您想要停用整個專案 （不要加入 0169 「 略過警告 」 中的欄位編譯器選項建議使用）。

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch 失敗並出現下列訊息： 無法開啟組件 ' / path/to/yourproject.exe'

如果您看到此錯誤訊息，一般的問題是您的專案的絕對路徑包含空格。 這會修正 Xamarin.iOS，未來版本中，但將專案移動至不含空格的資料夾，可以暫時解決此問題。

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>Sqlite3 版本太舊-請至少升級至 v3.5.0 ！

會發生這種情況是當您執行下列動作：

1.  使用 Mono.Data.Sqlite
1.  使用 Mac OS X Leopard (10.5)
1.  執行中模擬器的應用程式。


問題在於 Mono 收取 OS X `libsqlite3.dylib`，不 iPhoneSimulator 的`libsqlite3.dylib`檔案。 您的應用程式*將*裝置，但不是您模擬器上的工作。

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>將部署到裝置失敗，並 System.Exception: AMDeviceInstallApplication 傳回 3892346901

此錯誤表示的程式碼簽署憑證/套件組合識別碼的組態不符合安裝在您的裝置上佈建設定檔。  確認您有適當的憑證選取專案選項]-> [iPhone 套件組合簽署，而且專案選項中指定正確的套件組合識別碼]-> [iPhone 應用程式

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>程式碼完成功能無法使用 Visual Studio for Mac

請確定您用於 Mac 和 Xamarin.iOS 使用最新版本的 Visual Studio

如果問題仍然存在，請[提報 bug](http://monodevelop.com/Developers#Reporting_Bugs)、 附加 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **AndroidTools-{TIMESTAMP}.log**，和**元件-{TIMESTAMP}.log**記錄檔。

如果所有解決方案均失敗，您可以嘗試移除程式碼完成快取，以便重新產生時：

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

請小心正確地輸入這個命令，或您可能會不小心移除重要檔案。

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>當您複製文字時，visual Studio for Mac 損毀

熱門 Mac 公用程式 QuickSilver、 Google 工具列和啟動列具有 Mac 的記憶體損毀 Visual Studio 的剪貼簿功能。 在他們的選項，您可以列出 Visual Studio for Mac 做為處理序，它們不應該干擾。

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio for Mac 抱怨有關所需的 Mono 2.4

如果 Mac 的更新新的更新，因為 Visual Studio，而且當您嘗試將它啟動時再次它抱怨有關 Mono 2.4 未出現，您只需要為[Mono 2.4 安裝升級](http://www.go-mono.com/mono-downloads/download.html)。  

單聲道 2.4.2.3_6 修正防止 Visual Studio for Mac 可靠地執行有時無回應的 Visual Studio for Mac 在啟動或禁止在產生程式碼完成資料庫的某些重要問題。

一旦您安裝新的單聲道，如預期般，將會啟動 Visual Studio for Mac。

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>在判斷提示.../../../../mono/metadata/generic-sharing.c:704，'oti' 不符合條件

如果您收到的堆疊追蹤如下：

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

這表示您要連結至您的專案以捲動方塊程式碼編譯靜態程式庫。 從 iPhone SDK 版本 3.1 （或更高版本，在撰寫本文時） 開始連結捲動方塊程式碼 （靜態程式庫） 與非捲動方塊程式碼 (Xamarin.iOS) 時，Apple 引進了 bug 及其連結器中的。您必須使用靜態程式庫要解決這個問題的非 Thumb 版本連結。

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System.ExecutionEngineException： 嘗試 JIT 編譯方法 （管理--受管理的包裝函式） Foo[]:System.Collections.Generic.ICollection'1.get_Count （）

[] 後置詞表示，您或類別庫會上呼叫方法的泛型集合，例如 ienumerable<>、 ICollection <> 或 IList <> 透過陣列。 因應措施，您可以明確地強制 AOT 編譯器包含這類方法呼叫的方法，並藉由確定觸發例外狀況的呼叫之前執行此程式碼。 在此情況下，您可以撰寫：

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

這會強制 AOT 編譯器包含 get_Count 方法。

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio for Mac 來源編輯器是很慢

有時 Visual Studio for Mac 原始檔編輯器變得很慢，似乎幾秒鐘的時間之間輸入字元的停止回應。

此問題是非常罕見而且非常難以重現-它通常無法重現同一部電腦上之後重新啟動 Visual Studio for mac。 基於這個理由，我們歡迎它如果您無法執行數個偵錯步驟執行之前重新啟動 Visual Studio for Mac，並將結果傳送給我們。

1.  請嘗試關閉編輯器索引標籤，並將它重新開啟。 花費少量編輯或四處移動插入號，直到再次發生速度變慢？
1.  停用 「 資料交換同步處理 」 使用 「 Quartz 偵錯 」 開發人員工具 (您可以找到使用精選)，並檢查是否為 normal 還原來源編輯器的效能。
1.  仍然停用的資料交換同步，請嘗試重複步驟 (1)。
1.  如果編輯器擱置超過幾秒鐘，嘗試執行"killall-結束 [Visual Studio for Mac]"在終止時，它會停止。 可能會難以時間 kill 命令發生時停止回應編輯器 中，但務必要這樣做，因為此命令會強制 Mono MD 記錄中，我們可以使用探索的執行緒都在 XS 停止時將什麼狀態寫入所有執行緒的堆疊追蹤。



請將 XS 記錄檔，附加 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **AndroidTools-{TIMESTAMP}.log**，和**元件-{TIMESTAMP}.log**(在較舊版本的 XS/MonoDevelop，只需傳送 **~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**)。

 **注意： 上述問題已修正在最終 XS 2.2**

## <a name="compiled-application-is-very-large"></a>編譯的應用程式是非常大

若要支援偵錯，偵錯組建中包含額外的程式碼。 在發行模式中所建置的專案是大小的分數。

為準，Xamarin.iOS 1.3 偵錯組建包含偵錯支援單聲道 （每一個架構的每個類別中的方法） 的每一個單一元件。  

與 Xamarin.iOS 1.4 中，我們會介紹更精細的方法偵錯，預設行為是只提供偵錯檢測您的程式碼和程式庫，並不這樣做的所有[Mono 組件](~/cross-platform/internals/available-assemblies.md)（這仍會是可行的但您必須選擇加入以偵錯這些組件）。

## <a name="installation-hangs"></a>安裝重複停止回應

如果您有 iPhone 模擬器正在執行的停止回應 Mono 和 Xamarin.iOS 安裝程式。 這個問題不限於單聲道或 Xamarin.iOS，這是在嘗試安裝軟體 MacOS 雪 Leopard 上，如果 iPhone 模擬器在安裝期間執行的任何軟體的一致性的問題。

請確定您結束 iPhone 模擬器，然後重試安裝。

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>類型 0 之 trampolines 不足

如果您收到此訊息時執行的裝置，您可以建立多個類型 （輸入特定） 的 0 trampolines 藉由修改專案選項"iPhone 組建 」 一節。  您想要新增裝置的額外引數建置目標：

 `-aot "ntrampolines=2048"`

Trampolines 的預設數目為 1024年。  請嘗試增加這個數字，直到您擁有足夠的應用程式。

## <a name="ran-out-of-trampolines-of-type-1"></a>已用完 trampolines 類型 1

若要大量使用遞迴泛型，您可能會在裝置上收到這個訊息。  您可以建立多個類型 1 trampolines （型別 RGCTX） 修改專案選項"iPhone 組建 」 一節。  您想要新增裝置的額外引數建置目標：

 `-aot "nrgctx-trampolines=2048"`

Trampolines 的預設數目為 1024年。  請嘗試增加這個數字，直到您有足夠供您使用泛型。

## <a name="ran-out-of-trampolines-of-type-2"></a>已用完 trampolines 類型 2

若要大量使用介面，您可能會在裝置上收到這個訊息。
您可以建立多個類型 2 trampolines （型別 IMT Thunk），藉由修改專案選項"iPhone 組建 」 一節。  您想要新增裝置的額外引數建置目標：

 `-aot "nimt-trampolines=512"`

IMT Thunk trampolines 的預設數目為 128。  請嘗試增加這個數字，直到您有足夠供您使用的介面。

## <a name="debugger-is-unable-to-connect-with-the-device"></a>偵錯工具會無法連線到裝置

當您啟動偵錯裝置設定時，您會看到顯示對話方塊，指出它嘗試連接到應用程式偵錯工具。 有數個偵錯工具可能無法連線到應用程式的理由，根據的模式您用來連接 （USB 或 WiFi）。

 **如果裝置和偵錯工具主機位於不同的網路上**，防火牆或私人網路可能會導致應用程式無法連線到 WiFi 模式中的偵錯工具主機。

 **Visual Studio for Mac 可能無法查詢主機的正確 IP**。 在 WiFi 模式 Visual Studio for Mac 應用程式提供的所有 Ip，它可以找到的主機，而且應用程式會嘗試全部以查看是否它可以使用它們其中任何一個來連接至 Visual Studio for mac。

 **另一個裝置連線到主機上的 USB 連接埠。** 在少數情況下，其他裝置連線到 USB 主機上的連接埠也會以某種方式會干擾 USB 模式中的偵錯。

如果 WiFi 或 USB 模式下無法運作，您可以輕鬆地嘗試另： 在 Visual Studio for Mac，開啟 [喜好設定，移至 iPhone/喜好設定/偵錯工具偵錯工具] 頁面上，然後切換 」 進行偵錯 iOS 裝置透過 WiFi 而不是透過 USB 」 的核取方塊。   如果兩者皆非的運作方式，您可以看到裝置主控台的詳細資訊模式中失敗的詳細資訊 (這會啟用新增"-v-v-v 」 專案的選項中的其他 mtouch 引數)。

## <a name="error-134-mtouch-failed-with-the-following-message"></a>錯誤 134: mtouch 失敗並出現下列訊息：

如果您嘗試使用-nolink 建置 Xamarin.iOS 1.4 樣式的版本上，無法產生此錯誤。 您可以在您的 monodevelop 專案組態中指定多餘的引數，以解決這個錯誤。

加入引數

 `-nosymbolstrip`

您應該可以解決問題。

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>發佈身分識別不會顯示在 Visual Studio for Mac 專案簽署選項

Visual Studio for Mac 2.2 有的 bug 會導致它無法偵測包含逗號的發佈憑證。 請更新至 Visual Studio for Mac 2.2.1。

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>錯誤"傳回 AFCFileRefWrite: 1 」 上傳期間

將應用程式上傳至您的裝置時，您可能會收到錯誤 「 傳回 AFCFileRefWrite: 1"。 如果您擁有長度為零的檔案，也可能會發生。

## <a name="error-mtouch-failed-with-no-output"></a>錯誤"失敗，沒有輸出 mtouch"

最新版的 Xamarin.iOS 和 Visual Studio for Mac 失敗時的專案名稱或儲存方案或專案的目錄包含空格。
若要修正此問題：


-  請確定您的專案或儲存位置的目錄不包含空格。
-  在您的專案 「 主要設定 「 請確定專案名稱不包含任何空格。

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>錯誤 「 您上傳二進位檔無效。 發行前版本的 beta 版本的 SDK 已用來建置應用程式 」

這個錯誤通常造成 Xamarin.iOS 2.0.0 發行之前啟動 iPad 開發中的專案，您可能需要某些索引鍵在像是程式 Info.plist 中：

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

應移除此金鑰組，因為 Visual Studio for Mac 它為您自動處理。

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>錯誤 」 用來建置應用程式 SDK 的發行前測試版 」

（Ed Anuff 所提供）

請依照下列步驟：

-  3.2 或 iTunes iphone 組建的 SDK 版本所連接的變更將會拒絕它上傳因為它會查看 iPad 相容應用程式使用小於 3.2 的 SDK 版本來建置
-  建立專案的自訂 Info.plist，並明確設定為 3.0 中的 MinimumOSVersion。   這會覆寫設定 Xamarin.iOS MinimumOSVersion 3.2 值。   如果不這麼做，應用程式將無法在 iPhone 上執行。
-  重建、 zip 和上的傳至 iTunes 連線。

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>未處理的例外狀況： System.Exception： 找不到選取器 someSelector: {type} 上

導致這個例外狀況是由其中一個三件事：


1.  您已提供至 Objective C 執行階段選取器沒有相對應的 [匯出] 屬性套用至方法
1.  您已啟用完整的連結，而且 [保留] 屬性不會套用到 [匯出] ed 方法。
1.  您已將 [匯出] 屬性套用至私用方法與繼承類型中。


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>未更新 MainWindow.xib.designer.cs 檔案

Xamarin Studio 2.4 造成它無法群組 MainWindow.xib 檔案與新的專案中 MainWindow.xib.designer 檔案時發生錯誤。 這表示它不會更新該特定檔案的設計工具程式碼。

在 Visual Studio 版本中修正此問題適用於 Mac 所提供的內建的更新程式，因此請確定您使用較新版本。

您可以修正現有的專案，藉由移除 （未刪除） xib 和其設計工具的檔案，然後將它重新加回。 這應該正確地重新群組檔案。

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>建立之後的 UIAlertView 或 UIActionSheet 消失

如果您有一些類似的程式碼：

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

「 actionSheet"物件存在函式中的暫存變數和函式結束時，物件就可以進行記憶體回收，讓它最終會進行記憶體回收。

若要修正這個問題，您需要保留"actionSheet 」 您的方法，某處將時間超過您的方法的參考。

## <a name="project-always-runs-in-the-ipad-simulator"></a>專案一律會在 iPad 模擬器

IPhone SDK 4.0 安裝程式會安裝 2 Sdk-3.2 SDK，以建置僅限 iPad 應用程式和 4.0 SDK，用於 bulding iPhone 及通用應用程式。 它也會安裝 3.2 的模擬器，模擬只 iPad 和 4.0 的模擬器，以模擬 iPhone 或 iPhone 4。 會移除所有舊版的 Sdk 和模擬器。

Visual Studio for Mac iPhone 專案建置選項包括將使用的 SDK 版本的設定來建立您的應用程式。 中可以找到此設定**專案選項]-> [建置]-> [iPhone 組建**。

適用於 Mac 的 Visual Studio 中的新專案做為其預設 SDK 設定中，使用最舊的已安裝的 SDK，如果指定的 SDK 不存在，Visual Studio for Mac 將會使用它來建置您的應用程式可以找到最接近。 這項作業完成，讓專案不一定會 requre 最新的 SDK。 不過，這目前會導致 3.2 SDK 正在使用-結果會在 iPad 模擬器正在使用中。

若要使用 4.0 SDK 修正此問題，請前往**專案選項]-> [建置]-> [iPhone 組建**> 並將 SDK 值變更為"4.0"使用下拉式方塊。 您必須針對每個組態和和平台組合，使用下拉式清單頂端的 [面板] 中存取此動作。

不應以 「 最低 OS 版本 」 設定產生混淆的 SDK 版本。
此值並沒有相符的 SDK 版本值-它會影響最小版本的作業系統，將會安裝您的應用程式可以晚於 SDK，只要您使用只存在於較舊的作業系統，或防止使用較新的功能使用執行階段 OS 版本非常耗時的 Apiks。 您應該將它設定為測試您的應用程式的最舊的作業系統版本。

也請注意，**專案]-> [iPhone 模擬器目標**> 功能表可以用來選擇執行/偵錯專案時，依預設會使用模擬器。 此外，**執行-> 執行與**> 功能表可以用來取得用來執行特定模擬器

## <a name="ibtool-returns-error-133"></a>ibtool 會傳回錯誤 133

這表示您已經安裝 XCode 4。   在 XCode 4 工具 ibtool 已移除，就不再能夠編輯使用獨立工具 XIB 檔案。

如果您想要使用介面產生器時，安裝[XCode 系列 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792)、 可從 Apple 網站上取得。

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>「 無法建立為 mime 類型的顯示繫結： application/vnd.apple-<wbr/>介面產生器 」

如果您嘗試從非 iPhone 專案建立 iPhone UI，就會發生此錯誤。 請確定您開始使用 iPhone/iPad 方案，就不可能將 iPhone UI 項目加入至非 iPhone/iPad 專案。

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>在 iOS 模擬器內部執行時啟動會損毀。

如果您收到執行階段當機 (SIGSEGV) 內看起來像這樣的堆疊追蹤以及模擬器：

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
...then 程式模擬器的應用程式目錄中可能有一個 （或以上） 過時的組件。 由於 Apple iOS 模擬器新增和更新的檔案，但永遠不會刪除它們，可能存在這類組件。 如果發生這種情況然後最簡單的解決方案是從 [模擬器] 功能表中選取 [重設和內容和設定]。   

**警告：** 這將會移除所有的檔案、 應用程式和資料從模擬器。   下一次執行應用程式，適用於 Mac 的 Visual Studio 會將它部署至模擬器，而且會有任何舊、 過時的組件造成損毀。

## <a name="simulator-hangs-during-application-installation"></a>在應用程式安裝期間停止回應之模擬器

這種情形時應用程式名稱包含 '。 '（點） 名稱中。
即使它可以在許多 （例如裝置） 的情況下的運作方式是禁止 CFBundleExecutable-的可執行檔名稱。

 *"值不應該包含任何延伸模組名稱 」。- [http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](http://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>錯誤:"自訂屬性類型 0x43 不支援 」 時連按兩下.xib 檔案

這被因為嘗試開啟.xib 檔案時環境變數設定不正確。 這應該不會發生與標準的 Visual Studio 使用的 Mac/Xamarin.iOS，並重新開啟 Visual Studio for Mac 從 /Applications 應該可以解決問題。

嘗試更新時的軟體及此錯誤訊息隨即出現，請電子郵件 *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>應用程式在模擬器上執行，但在裝置上失敗

此問題可能會在數種格式，而且永遠不會產生一致的錯誤。 如果應用程式包含.xib，請先確定**建置動作**.xib 會設定為**InterfaceDefinition**。 這是.xibs 的預設建置動作。

若要檢查的建置動作，.xib 檔案上按一下滑鼠右鍵，然後選擇**建置動作**。


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>沒有資料也可用來編碼 437 System.NotSupportedException:

當包含 Xamarin.iOS 應用程式中的第 3 個合作對象文件庫，您可能會發生錯誤，在表單中"System.NotSupportedException： 沒有資料可用於編碼 437 」 嘗試編譯並執行應用程式時。 例如，程式庫，例如`Ionic.Zip.ZipFile`，可能會擲回這個例外狀況，在作業期間。

這可以藉由開啟 Xamarin.iOS 專案中，移至的選項來解決**iOS 建置** > **國際化**和檢查**西**國際化。



<a name="Can't_upgrade_to_Indie/Business_from_Trial_Account" />


## <a name="cant-upgrade-to-indiebusiness-from-trial-account"></a>無法從試用帳戶升級為 Indie/Business

如果您最近購買 Xamarin.iOS，先前已啟動 Xamarin.iOS 試用版，您可能需要完成下列步驟，以取得這項授權變更收取的 Visual Studio for Mac 或 Visual Studio。

-  關閉 Visual Studio for Mac/Visual Studio
-  從 Mac 上的 ~/Library/MonoTouch 或 %PROGRAMDATA%\MonoTouch\License\ 移除所有檔案，適用於 Windows
-  重新開啟 Visual Studio for Mac/Visual Studio，並建置 Xamarin.iOS 專案


## <a name="receiving-activation-incomplete-error-message"></a>收到 '啟用不完整' 錯誤訊息

當使用 Xamarin.iOS for Visual Studio 時，可能會發生此問題。 若要解決此問題，請從傳送記錄檔的下列位置[ contact@xamarin.com ](mailto:contact@xamarin.com)。

-  記錄位置： %LocalAppData%/Xamarin/Logs
