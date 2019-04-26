---
title: 疑難排解秘訣適用於 Xamarin.iOS
description: 本文件提供適用於 Xamarin.iOS 應用程式開發期間疑難排解祕訣。 它會描述特定的錯誤訊息，以及其他潛在的問題。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B50FE9BD-9E01-AE88-B178-10061E3986DA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2018
ms.openlocfilehash: 1a98cf854ffdd1d4904981f85fd8e33ad486743c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417370"
---
# <a name="troubleshooting-tips-for-xamarinios"></a>疑難排解秘訣適用於 Xamarin.iOS 

## <a name="xamarinios-cannot-resolve-systemvaluetuple"></a>Xamarin.iOS 無法解析 System.ValueTuple

這個錯誤是因為使用 Visual Studio 不相容。

- **Visual Studio 2017 Update 1** （版本 15.1 或更舊版本） 才相容**System.ValueTuple NuGet 4.3.0** （或更舊版本）。

- **Visual Studio 2017 Update 2** （版本 15.2 或更新版本） 才相容**System.ValueTuple NuGet 4.3.1**或更新版本。

請選擇正確的 System.ValueTuple NuGet 與您的 Visual Studio 2017 安裝相對應。


## <a name="receiving-error-retrieving-update-information-error-message"></a>收到 '擷取更新資訊時發生錯誤' 錯誤訊息

嘗試更新的軟體及此錯誤訊息出現時，請嘗試在 IDE 中重新啟動您的 IDE 和登出，然後傳回在您的帳戶。

## <a name="how-do-i-create-outlets-or-actions-with-interface-builder"></a>如何使用 Interface Builder 建立輸出或動作？

使用適用於 Mac 和 Visual studio 的 Xamarin 設計工具，適用於 iOS，Visual Studio 中引進，Xamarin.iOS 開發人員可以現在利用建立分鏡腳本和.xibs 透過 UI。 請參閱[Hello，iOS](~/ios/get-started/hello-ios/index.md)輔助線，如需有關使用設計工具。

您也可以指 Apple[插座](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html)並[動作](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingAction.html)輔助線，如需有關使用輸出和動作中 IB。

## <a name="systemtextencodinggetencoding-throws-notsupportedexception"></a>System.Text.Encoding.GetEncoding 會擲回 NotSupportedException

您可能不會加入預設的編碼方式使用。 請檢查[國際化](~/ios/app-fundamentals/localization/index.md)頁面以了解如何加入更多的編碼方式的支援。

## <a name="systemmissingmethodexception-anything-else"></a>System.MissingMethodException （任何其他項目）

成員可能已由連結器中，移除，因此沒有在執行階段組件中。  有數個解決方案：

- 新增[ `[Preserve]` ](http://www.go-mono.com/docs/index.aspx?link=T:MonoTouch.Foundation.PreserveAttribute)屬性的成員。  如此可防止連結器將它移除。
- 當叫用[ **mtouch**](http://www.go-mono.com/docs/index.aspx?link=man:mtouch%281%29)，使用 **-nolink**或是 **-linksdkonly**選項：
  - **-nolink**會停用所有連結。
  - **-linksdkonly**只會將這類連結提供 Xamarin.iOS 組件**xamarin.ios.dll**，同時保留使用者建立的組件中的所有類型 (亦即。 您的應用程式專案)。

請注意，組件連結，以便產生的可執行檔是較小;因此，停用連結可能會導致比理想的較大可執行檔。

## <a name="you-are-getting-a-modelnotimplementedexception"></a>您會收到 ModelNotImplementedException

如果您收到這個例外狀況這表示您要呼叫基底。類別會覆寫模型上的方法 （)。 您不需要呼叫類別中的基底方法 （這些是 [模型] 屬性會標示的類別） 的模型。

## <a name="this-class-is-not-key-value-coding-compliant-for-the-key-xxxx"></a>這個類別不是索引鍵值編碼標準索引鍵 XXXX

如果載入 NIB 檔案，這表示您受管理的類別找不到 XXXX 的值時，您會收到這個錯誤。 這表示您沒有這類宣告：

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

上述的定義由自動產生 Visual Studio for Mac 的 XIB 檔案加入至 Visual Studio for Mac 中`NAME_OF_YOUR_XIB_FILE.designer.xib.cs`檔案。

此外，其中包含上述的程式碼的型別必須的子類別[NSObject](xref:Foundation.NSObject)。  如果包含的型別命名空間內，它也應該[[註冊]](xref:Foundation.RegisterAttribute)屬性以提供型別名稱沒有命名空間 （如介面產生器不支援在類型中的命名空間）：

```csharp
namespace Samples.GLPaint {

    // The [Register] attribute overrides the type name registered
    // with the Objective-C runtime, in this case removing the namespace.
    [Register ("AppDelegate")]
    public class AppDelegate {/* ... */}
}
```

## <a name="unknown-class-xxxx-in-interface-builder-file"></a>未知的類別介面產生器檔案中的 XXXX

如果您在介面產生器檔案中定義的類別，但您未提供實際的實作中，會產生這個錯誤您C#程式碼。

您需要新增一些程式碼，就像這樣：

```csharp
public partial class MyImageView : UIView {
   public MyImageView (IntPtr handle) : base (handle {}
}
```
## <a name="systemmissingmethodexception-no-constructor-found-for-foobarctorsystemintptr"></a>System.MissingMethodException:找到 Foo.Bar::ctor(System.IntPtr) 沒有建構函式

當程式碼嘗試將您從您的介面產生器檔案參考的類別的執行個體具現化時，會在執行階段產生這個錯誤。 這表示您忘記新增單一的 IntPtr 做為參數的建構函式。

建構函式的 IntPtr 控制代碼用來繫結具有其未受管理的表示法的 managed 的物件。

若要修正此問題，請將下列程式碼行加入類別 Foo.Bar:

```csharp
public Bar (IntPtr handle) : base (handle) { }
```
## <a name="type-foo--does-not-contain-a-definition-for-getnativefield-and-no-extension-method-getnativefield-of-type-foo-could-be-found"></a>類型 {Foo} 不包含的定義`GetNativeField' and no extension method `GetNativeField' 的型別找不到 {Foo}

如果您收到這個錯誤在設計工具產生的檔案 (*。 xib.designer.cs)，它表示下列其中一種：

 **1） 遺漏部分類別或基底類別**

在設計工具所產生的部分類別必須有對應的部分類別使用者程式碼中的一些子類別會繼承`NSObject`，通常`UIViewController`。 請確定您有這種類型，提供錯誤的類別。

 **2） 預設命名空間變更**

設計工具檔案會產生使用您的專案預設命名空間設定。 如果您變更這些設定，或重新命名專案，產生的部分類別可能不再是使用者程式碼與其相同的命名空間。

命名空間設定位於 [專案選項] 對話方塊。 預設命名空間中找到**一般]-> [主要設定**一節。 如果空白，預設會使用您的專案名稱。 更多進階命名空間設定可在**原始碼.NET 命名原則]-> [** 一節。

## <a name="warning-for-actions-the-private-method-foo-is-never-used-cs0169"></a>動作的警告：絕不會使用私用方法 'Foo'。 (CS0169)

介面產生器檔案的動作會連線到 widget 反映在執行階段，因此預期會有此警告。

您可以使用 [#pragma 警告停用 0169年]"#pragma 警告啟用 0169 」 解決您的動作如果您想要隱藏這個警告只針對這些方法，或新增 0169 編譯器選項中的 「 略過警告 」 欄位，如果您想要將它整個專案 （請不要停用建議使用）。

## <a name="mtouch-failed-with-the-following-message-cannot-open-assembly-pathtoyourprojectexe"></a>mtouch 失敗並出現下列訊息：無法開啟組件 ' / path/to/yourproject.exe'

如果您看到此錯誤訊息，通常問題是您專案的絕對路徑包含空格。 這將會修正的 Xamarin.iOS，未來版本中，但您可以暫時解決此問題，將專案移至不含空格的資料夾。

## <a name="your-sqlite3-version-is-old---please-upgrade-to-at-least-v350"></a>Sqlite3 版本太舊-請至少升級至 v3.5.0 ！

會發生這種情況是當您執行下列各項：

1.  使用 Mono.Data.Sqlite
1.  使用 Mac OS X Lepard (10.5)
1.  執行中模擬器的應用程式。


問題在於 Mono 可挑出 OS X `libsqlite3.dylib`，不 iPhoneSimulator 的`libsqlite3.dylib`檔案。 您的應用程式*將*公司裝置，而不只是您的模擬器。

## <a name="deploy-to-device-fails-with-systemexception-amdeviceinstallapplication-returned-3892346901"></a>將部署至裝置失敗，System.Exception:AMDeviceInstallApplication returned 3892346901

此錯誤表示您的憑證/套件組合識別碼的程式碼簽署設定不符合您裝置上安裝佈建設定檔。  確認您有適當的憑證在 專案選項中選取 iPhone 套件組合簽署-> 專案選項中指定正確的套件組合識別碼-> iPhone 應用程式

## <a name="code-completion-is-not-working-in-visual-studio-for-mac"></a>程式碼完成功能無法使用 Visual Studio for Mac

確定您使用最新版本的 Visual Studio for Mac 和 Xamarin.iOS

如果問題仍然存在，請[提報 bug](http://monodevelop.com/Developers#Reporting_Bugs)、 附加 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **AndroidTools-{TIMESTAMP}.log**，並**元件-{TIMESTAMP}.log**記錄檔。

如果所有解決方案均失敗，您可以嘗試移除程式碼完成快取，以便重新產生時：

 `[rm -r ~/.config/XamarinStudio-{VERSION}/CodeCompletionData]`

請小心正確輸入下列命令，或您可能不小心移除重要檔案。

## <a name="visual-studio-for-mac-crashes-when-you-copy-text"></a>當您複製的文字時，visual Studio for Mac 損毀

熱門 Mac 公用程式 QuickSilver、 Google 工具列和啟動列有 Visual Studio for Mac 的記憶體會損毀的剪貼簿功能。 在他們的選項，您可以列出 Visual Studio for Mac 作為它們應該不會干擾處理序。

## <a name="visual-studio-for-mac-complains-about-mono-24-required"></a>Visual Studio for Mac 會反映所需的 Mono 2.4

如果 Visual Studio for Mac 更新最近的更新，因為當您嘗試啟動它一次它會反映 Mono 2.4 未出現，您只需要就[升級 Mono 2.4 安裝](http://www.go-mono.com/mono-downloads/download.html)。  

Mono 2.4.2.3_6 修正導致 Visual Studio for Mac 中，無法可靠地執行有時無回應的 Visual Studio for Mac 中啟動時，或防止程式碼完成資料庫所產生的一些重要問題。

一旦您安裝新的 Mono 時，Visual Studio for Mac 會開始如預期般運作。

## <a name="assertion-at-monometadatageneric-sharingc704-condition-oti-not-met"></a>在判斷提示.../../../../mono/metadata/generic-sharing.c:704，條件不符合 ' oti'

如果您收到下列的堆疊追蹤：

```csharp
 - Assertion at ../../../../mono/metadata/generic-sharing.c:704, condition `oti' not met
Stacktrace:
    at System.Collections.Generic.List`1<object>..cctor () <0xffffffff>
    at System.Collections.Generic.List`1<object>..cctor () <0x0001c>
    at (wrapper runtime-invoke) object.runtime_invoke_dynamic (intptr,intptr,intptr,intptr) <0xffffffff>`
```

這表示您要連結到您的專案編譯捲動方塊的程式碼的靜態程式庫。 IPhone SDK 版本 3.1 （或更高版本，在撰寫本文時） Apple 已引進其連結器中的 bug 連結非捲動方塊的程式碼 (Xamarin.iOS) 與捲動方塊 （靜態程式庫） 時。您必須使用靜態程式庫來解決這個問題的非 Thumb 版本連結。

## <a name="systemexecutionengineexception-attempting-to-jit-compile-method-wrapper-managed-to-managed-foosystemcollectionsgenericicollection1getcount-"></a>System.ExecutionEngineException:嘗試將 JIT 編譯方法 （managed 至 managed 包裝函式） Foo[]:System.Collections.Generic.ICollection'1.get_Count （）

[] 後置詞表示，您或類別庫會呼叫方法的泛型集合，例如 ienumerable<>、 icollection<> 或 IList <> 透過在陣列上。 因應措施，您可以明確地強制 AOT 編譯器包含這類方法呼叫的方法，並確定觸發例外狀況的呼叫之前執行此程式碼。 在此情況下，您可以撰寫：

```csharp
Foo [] array = null;
int count = ((ICollection<Foo>) array).Count;
```

這會強制 AOT 編譯器包含 get_Count 方法。

## <a name="visual-studio-for-mac-source-editor-is-extremely-slow"></a>Visual Studio for Mac 原始檔編輯器是非常緩慢

有時候 Visual Studio for Mac 原始檔編輯器變得很慢，似乎停止回應數秒間輸入字元。

這個問題就非常罕見，很難重現-它通常無法重現同一部電腦上之後重新啟動 Visual Studio for mac。 基於這個理由，非常歡迎它如果您可以執行數個偵錯的步驟，才能重新啟動 Visual Studio for Mac，並將結果傳送給我們。

1.  請嘗試關閉編輯器 索引標籤，然後重新開啟它。 花費少量編輯或四處移動插入號，直到再次發生速度變慢？
1.  停用 「 資料交換同步 」 使用 「 Quartz 偵錯 」 開發人員工具 (您可以找到使用焦點)，並檢查原始檔編輯器效能是否還原為標準。
1.  使用資料交換同步仍然停用，請嘗試重複步驟 (1)。
1.  如果編輯器停止超過幾秒鐘的時間，請試著執行"killall-結束 [Visual Studio for Mac] 」 在終端機，而它已停止。 它可能難以 kill 命令發生時，編輯器已停止，但務必要這樣做，因為此命令會強制 Mono MD 記錄中，我們可以用來探索哪些執行緒都在 XS 已停止時的狀態寫入所有執行緒的堆疊追蹤的時間。



請附加 XS 記錄檔 **~/Library/Logs/XamarinStudio-{VERSION}/Ide-{TIMESTAMP}.log**， **AndroidTools-{TIMESTAMP}.log**，和**元件-{TIMESTAMP}.log**(在舊版的 X/MonoDevelop 中，只傳送 **~/Library/Logs/MonoDevelop-(3.0|2.8|2.6)/MonoDevelop.log**)。

 **注意：上述的問題已修正在 XS 2.2 最終**

## <a name="compiled-application-is-very-large"></a>編譯的應用程式是非常大

若要支援偵錯，偵錯組建會包含額外的程式碼。 以發行模式建置的專案是大小的一小部分。

自 Xamarin.iOS 1.3 偵錯組建會包含偵錯支援 Mono （每個架構的每個類別中的方法） 的每個單一元件。  

與 Xamarin.iOS 1.4 中，我們將介紹用於偵錯的更細緻的方法，預設行為是只提供偵錯檢測您的程式碼和您的程式庫，並不這麼做的所有[Mono 組件](~/cross-platform/internals/available-assemblies.md)（這仍會是可行的但您將必須 opt-in 以偵錯這些組件）。

## <a name="installation-hangs"></a>安裝停止回應

如果您有執行 iPhone 模擬器的 Mono 和 Xamarin.iOS 的安裝程式停止回應。 此問題並不限於 Mono 或 Xamarin.iOS，這是一致性的問題嘗試安裝軟體在 MacOS Snow Leopard 如果 iPhone 模擬器在安裝期間執行的任何軟體。

請確定您結束 iPhone 模擬器，然後重試安裝。

<a name="trampolines" />

## <a name="ran-out-of-trampolines-of-type-0"></a>已用完 trampolines 型別的 0

如果您執行裝置時收到此訊息，您可以建立多個型別 （類型特定） 的 0 trampolines 藉由修改您的專案選項"iPhone 組建 」 一節。  您想要新增裝置的額外引數建置目標：

 `-aot "ntrampolines=2048"`

Trampolines 的預設數目為 1024年。  請嘗試增加這個數字，直到擁有足夠的應用程式。

## <a name="ran-out-of-trampolines-of-type-1"></a>已用完 trampolines 類型 1

若要大量使用遞迴泛型的您可能會在裝置上收到此訊息。  您可以建立多個類型 1 trampolines （型別 RGCTX），藉由修改您的專案選項"iPhone 組建 」 一節。  您想要新增裝置的額外引數建置目標：

 `-aot "nrgctx-trampolines=2048"`

Trampolines 的預設數目為 1024年。  請嘗試增加這個數字，直到您有足夠供您使用泛型。

## <a name="ran-out-of-trampolines-of-type-2"></a>已用完 trampolines 類型 2

若要大量使用介面，您可能會在裝置上收到此訊息。
您可以建立多個類型 2 trampolines （型別 IMT Thunk），藉由修改您的專案選項"iPhone 組建 」 一節。  您想要新增裝置的額外引數建置目標：

 `-aot "nimt-trampolines=512"`

IMT Thunk trampolines 的預設數目為 128。  請嘗試增加這個數字，直到您有足夠供您使用的介面。

## <a name="debugger-is-unable-to-connect-with-the-device"></a>偵錯工具是無法與裝置連線

當您啟動偵錯的裝置組態時，您會看到顯示對話方塊，指出它嘗試連線到應用程式的偵錯工具。 有幾個原因，可能無法連接到應用程式進行偵錯工具，根據的模式您用來連接 （USB 或 WiFi）。

 **如果裝置和偵錯工具主機位於不同網路**，防火牆或私人網路可能會導致應用程式無法連線到 WiFi 模式中偵錯工具主機。

 **Visual Studio for Mac 無法查詢主機的正確 IP**。 在 WiFi 模式 Visual Studio for Mac 可讓應用程式的所有 Ip，它可以找到的主機，而且應用程式會嘗試全部要看看是否它可以使用其中任何連接到 Visual Studio for mac。

 **另一個裝置會連接到主機上的 USB 連接埠。** 在少數情況下，其他裝置連線到 USB 主機上的連接埠也會以某種方式會影響 USB 模式中的偵錯。

如果 USB 或 WiFi 模式無法運作，您可以輕鬆地嘗試其他： 在 Visual Studio for Mac 中，開啟 喜好設定，請移至 喜好設定/偵錯工具/iPhone 偵錯工具 頁面中，然後切換 」 iOS 裝置使用 wifi 偵錯而不是透過 USB 」 的核取方塊。   如果兩者皆非的運作方式，您可以看到裝置主控台，以詳細資訊模式中失敗的詳細資訊 (加上啟用 「-v-v-v 」 專案的選項中的其他 mtouch 引數)。

## <a name="error-134-mtouch-failed-with-the-following-message"></a>錯誤 134: mtouch 失敗並出現下列訊息：

如果您嘗試使用-nolink 建置 Xamarin.iOS 1.4 樣式的版本上，可能會產生此錯誤。 您可以指定額外的引數，monodevelop 專案組態中，以解決這個錯誤。

新增引數

 `-nosymbolstrip`

應該會解決問題。

## <a name="distribution-identity-is-not-shown-in-visual-studio-for-mac-project-signing-options"></a>發佈身分識別不會顯示在 Visual Studio for Mac 專案簽署選項

Visual Studio for Mac 2.2 有問題，而不是用來偵測包含逗號的散發憑證。 請更新至 Visual Studio for Mac 2.2.1。

## <a name="error-afcfilerefwrite-returned-1-during-upload"></a>錯誤"AFCFileRefWrite 傳回：1-在上傳期間

將應用程式上傳至您的裝置時，您可能會收到錯誤 「 AFCFileRefWrite 傳回：1". 如果您擁有長度為零的檔案，會發生這項目。

## <a name="error-mtouch-failed-with-no-output"></a>錯誤"失敗，沒有輸出的 mtouch"

最新版的 Xamarin.iOS 和 Visual Studio for Mac 失敗時的專案名稱或儲存的方案或專案的目錄包含空格。
修正方法：


-  請確定您的專案或儲存位置的目錄不包含空格。
-  在 Main 設定 「 請確定您的專案中專案名稱不包含任何空格。

## <a name="error-the-binary-you-uploaded-was-invalid-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-application"></a>錯誤 「 您已上傳二進位檔無效。 發行前版本的 beta 版本的 SDK 來建置應用程式 」

此錯誤的原因通常是使用 Xamarin.iOS 2.0.0 發行之前，已開始在 iPad 開發的專案，您可能有某些金鑰等 Info.plist 中：

```xml
<key>UIDeviceFamily</key>
       <array>
               <string>1</string>
       </array>
```

應移除此金鑰組，因為 Visual Studio for Mac 它為您自動處理。

## <a name="error-a-pre-release-beta-version-of-the-sdk-was-used-to-build-the-app"></a>錯誤 「 發行前版本的 beta 版本的 SDK 來建置應用程式 」

（由 Ed Anuff 發表）

請依照下列步驟：

-  在 iPhone 組建 3.2 或 iTunes 中的 SDK 版本連接的變更將會拒絕它上傳因為它所查看 iPad 相容應用程式使用小於 3.2 的 SDK 版本建置的內容
-  建立自訂的 Info.plist 專案，並明確設定為 3.0 中的 MinimumOSVersion。   這會覆寫設定 Xamarin.iOS 的 MinimumOSVersion 3.2 值。   如果不這麼做，應用程式將無法在 iPhone 上執行。
-  重建、 郵遞區號和上的傳至 iTunes connect。

## <a name="unhandled-exception-systemexception-failed-to-find-selector-someselector-on-type"></a>未處理的例外狀況：System.Exception:找不到選取器 someSelector： 在 {type}

這個例外狀況是由三個事項之一造成：


1.  您所提供的選取器以 OBJECTIVE-C 執行階段，而不將對應的 [匯出] 屬性套用至方法
1.  您已啟用完整的連結，而且不會 [Preserve] 屬性套用至 [匯出] ed 方法。
1.  [匯出] 屬性套用至繼承的型別中的私用方法。


## <a name="mainwindowxibdesignercs-file-is-not-updated"></a>MainWindow.xib.designer.cs 檔案不會更新

在 Xamarin Studio 2.4 造成它無法群組 MainWindow.xib 檔案與新的專案中 MainWindow.xib.designer 檔案時發生錯誤。 這表示它不會更新該特定檔案所需的設計工具程式碼。

此問題已修正版本的 Visual Studio for Mac 所提供的內建的更新程式，因此請確定您使用較新版本。

您可以藉由移除 （不會刪除） 來修正現有的專案 xib 和其設計工具的檔案，然後將它加回。 這應該正確地重新群組檔案。

## <a name="uialertview-or-uiactionsheet-vanish-after-being-created"></a>UIAlertView 或 UIActionSheet 消失之後所建立

如果您有一些程式碼，就像這樣：

```csharp
var actionSheet = new UIActionSheet ("My ActionSheet", null, null, "OK", null){
   Style = UIActionSheetStyle.Default
};

actionSheet.Clicked += delegate (sender, args){
    Console.WriteLine ("Clicked on item {0}", args.ButtonIndex);
};
```

「 actionSheet 「 物件存留的函式中的暫存變數和函式會終止，因為物件適合進行記憶體回收，因此它最終會進行記憶體回收。

若要修正此問題，您需要保留"actionSheet 」 意即您的方法，某處，將時間超過您的方法的參考。

## <a name="project-always-runs-in-the-ipad-simulator"></a>專案一律會在執行 iPad 模擬器

IPhone SDK 4.0 安裝程式會安裝 2 Sdk-3.2 的 SDK，建置僅限 iPad 應用程式和 4.0 的 SDK，用於建立 iPhone 及通用應用程式。 它也會安裝 3.2 模擬器會模擬只 iPad 和 4.0 的模擬器來模擬 iPhone 或 iPhone 4。 會移除所有舊版的 Sdk 和模擬器。

Visual Studio for Mac iPhone 專案組建選項中建置您的應用程式，包括將使用的 SDK 版本的設定。 此設定可在**專案選項]-> [組建]-> [iPhone 組建**。

在 Visual Studio for Mac 的新專案使用最舊的已安裝的 SDK，為其預設的 SDK 設定，以及如果不存在指定的 SDK，Visual Studio for Mac 將會使用它來建置您的應用程式可以找到最接近。 這項作業完成，因此專案就不一定需要最新的 SDK。 不過，這目前會導致 3.2 的 SDK 正在使用-這會導致 iPad 模擬器所使用。

若要使用 4.0 的 SDK 來修正此問題，請前往**專案選項]-> [組建]-> [iPhone 組建**> 並將 SDK 值變更為"4.0"使用下拉式方塊。 您必須針對每個組態與平台組合，使用下拉式清單頂端的 [面板] 中存取此動作。

SDK 版本不應使用 「 最小 OS 版本 」 設定相混淆。
此值並沒有相符的 SDK 版本值-它會影響最小版本的作業系統，安裝您的應用程式，將只要您使用只存在於較舊的作業系統，或保護使用較新的功能使用執行階段作業系統版本檢查的 Api 可以晚於 SDK 中，ks。 您應該將它設定為 最舊的 OS 版本，您可以在其測試您的應用程式。

另請注意**專案]-> [iPhone 模擬器目標**> 功能表可用來選擇執行/偵錯專案時，會將預設使用的模擬器。 此外，**回合]-> [執行與**> 功能表可用來挑選用來執行特定的模擬器

## <a name="ibtool-returns-error-133"></a>ibtool 會傳回錯誤 133

這表示您已安裝的 XCode 4。   在 XCode 4 工具 ibtool 已移除，就不再能夠編輯使用獨立工具 XIB 檔案。

如果您想要使用 Interface Builder，安裝[XCode 系列 3](http://connect.apple.com/cgi-bin/WebObjects/MemberSite.woa/wa/getSoftware?bundleID=20792)、 可從 Apple 網站。

## <a name="cant-create-display-binding-for-mime-type-applicationvndapple-wbrinterface-builder"></a>「 無法建立 mime 類型的顯示繫結： application/vnd.apple-<wbr/>介面產生器 」

如果您嘗試從非 iPhone 專案建立 iPhone UI，就會發生此錯誤。 請確定您使用 iPhone/iPad 解決方案啟動時，不可能只要將 iPhone UI 項目新增至非 iPhone/iPad 專案。

## <a name="startup-crash-when-executing-inside-the-ios-simulator"></a>IOS 模擬器內執行時啟動會損毀。

如果您收到執行階段當機 (SIGSEGV) 內看起來像這樣的堆疊追蹤以及模擬器：

```csharp
  at (wrapper managed-to-native) System.Reflection.Assembly.GetTypes (System.Reflection.Assembly,bool)
  at MonoTouch.ObjCRuntime.Runtime.RegisterAssembly (System.Reflection.Assembly)
  at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr)
```
....then 模擬器應用程式目錄中可能有一個 （或以上） 過時的組件。 這類組件可能會存在，由於 Apple iOS 模擬器新增和更新的檔案，但永遠不會刪除它們。 如果發生這種情況則最簡單的解決方案是從 [模擬器] 功能表中選取 [重設和內容和設定]。   

**警告：** 這將會移除所有的檔案、 應用程式和資料從模擬器。   下次您執行您的應用程式，Visual Studio for Mac 會將它部署到模擬器，而且會有任何舊的、 過時組件上，可導致損毀。

## <a name="simulator-hangs-during-application-installation"></a>模擬器停止回應之應用程式安裝期間

可能發生這種應用程式名稱包含 '。 '（點） 的名稱。
即使它可以在許多 （如裝置） 的情況下的運作方式是禁止 CFBundleExecutable-的可執行檔名稱。

 *"值不應包含任何延伸模組名稱 」。- [https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf](https://developer.apple.com/library/mac/documentation/General/Reference/InfoPlistKeyReference/InfoPlistKeyReference.pdf)

## <a name="error-custom-attribute-type-0x43-is-not-supported-when-double-clicking-xib-files"></a>錯誤：「 不支援自訂屬性型別 0x43 」 時按兩下.xib 檔案

這被因為嘗試開啟.xib 檔案時未正確設定環境變數。 這應該不會與 Visual studio 的一般使用方式，針對 Mac/Xamarin.iOS，並重新開啟 Visual Studio for Mac 從 /Applications 應可修正此問題。

在嘗試更新的軟體及此錯誤訊息出現時，請電子郵件 *support@xamarin.com*

## <a name="application-runs-on-simulator-but-fails-on-device"></a>應用程式會在模擬器上執行，但無法在裝置上

此問題可以數種形式資訊清單，並不一定會產生一致的錯誤。 如果應用程式包含.xib，請檢查以確定**建置動作**.xib 會設定為**InterfaceDefinition**。 這是.xibs 的預設建置動作。

若要檢查的建置動作，以滑鼠右鍵按一下.xib 檔案，然後選擇**建置動作**。


## <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System.NotSupportedException:沒有資料也可用來編碼 437

在 Xamarin.iOS 應用程式中包含第 3 個廠商程式庫，您可能會發生錯誤，在表單中 「 System.NotSupportedException:沒有資料可供編碼 437 」 嘗試編譯及執行應用程式時。 比方說，程式庫，例如`Ionic.Zip.ZipFile`，可能會擲回這個例外狀況，在作業期間。

解決此問題的 Xamarin.iOS 專案中，移至的選項 %installationdirectory **iOS 組建** > **國際化**並檢查**西部**國際化。
