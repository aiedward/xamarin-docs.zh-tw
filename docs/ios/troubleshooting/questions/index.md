---
title: 常見問題集
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: f8264c48b3b4679d45d5603b5637df0016cd3d17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30780230"
---
# <a name="frequently-asked-questions"></a>常見問題集

## <a name="general-questions"></a>一般問題

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[我可以使用 Mac VM 搭配 Xamarin 嗎？](mac-vm.md)
是的但只在 Mac 硬體上。

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[我該如何降級 Xcode？](downgrade-xcode.md)
本指南提供連結來存取舊版 Xcode 以及最新版本。

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[我在哪裡可以設定 iOS SDK 的位置？](ios-sdk.md)
對大多數使用者兩者會設為適當的位置自動。 本指南列出預設 SDK 位置以及如何視需要加以變更。

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[如何重新開發人員選項啟用之後更新 iOS](update-developer-options.md)
IOS bug 可能會導致開發人員選項，更新 iOS 版本後消失，這已經被注意到時切換至 iOS 8.x。 本指南說明如何可以重新啟用的選項。

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[使用者位置無法在 iOS 8 中運作](ios8-user-location.md)
本指南將告訴您如何編輯 info.plist，以啟用 iOS 8 中的使用者位置。

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[我在哪裡可以找到要將 iOS 當機記錄檔符號化的 .dSYM 檔案？](symbolicate-ios-crash.md)
本指南說明 symbolicating iOS 當機記錄檔，以協助診斷損毀的基本步驟。 它也會更進階的 symbolication 技術及解譯 iOS 當機記錄檔資訊的其他資源連結。


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[我該如何針對 Xamarin Studio 中的 iOS 專案設定 Mono 執行階段環境變數？](xs-mono-runtime.md)
如果您需要為單聲道設定任何執行階段環境變數，您可以設定**專案選項 > 執行 > 一般**頁面。

## <a name="publishing-questions"></a>發行的問題

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[提交至應用程式存放區時發生錯誤:"提交中，未偵測到無效的套件組合的選項不可以內嵌在 bitcode"](invalid-bundle-bitcode.md)

送出應用程式的_需要_bitcode，例如 watchOS 與 tvOS 應用程式，必須以 Xcode 9。

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[可以變更以及 IPA 檔案的輸出路徑嗎？](ipa-output-path.md)
為準，Xamarin 循環 7，您可以使用自訂的 MSBuild 目標來達成此目的。

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[如何將 IPA 輸出檔案複製到 TFS 置放資料夾](ipa-tfs.md)
是，本指南說明如何。

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[我可以將檔案加入或移除 IPA 檔案中的檔案，在 Visual Studio 中建置它之後？](modify-ipa.md)
是的是可行的但是它通常會需要重新簽署`.app`進行變更後的組合。 請注意，修改`.ipa`檔案不需要在正常使用。 這篇文章純粹供資訊之用途。

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[是可以從 Visual Studio 建立.xcarchive 封存？](create-xcarchive.md)
為準，Xamarin 4 時，現在可以建立`.xcarchive`從藉由設定 Windows`ArchiveOnBuild`屬性`true`。

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[為什麼我的應用程式提交因為「找到不允許的路徑 ( "iTunesMetadata.plist" ) ...」而失敗？](itunesmetadata-disallowed-paths.md)
這個錯誤是變更 Apple App Store 驗證程序的結果。 此特定錯誤_不_相關的特定版本的已安裝的 Xamarin，因此降級將_不_幫助。 如何修正此問題詳細資訊此指南連結。


## <a name="diagnosing-specific-error-messages"></a>診斷特定的錯誤訊息

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[含 RegisterServicePort 的 iOS 設計工具錯誤](error-registerserviceport.md)
錯誤與`RegisterServicePort`和類似的錯誤訊息，上面通常是間諜軟體/惡意程式碼的電腦上的問題。 本指南詳述，確認診斷且需移除間諜軟體/惡意程式碼的資訊。

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[為什麼我的 iOS 組建因為「Keychain 中找不到任何有效的 iPhone 程式碼簽署金鑰」而失敗？](no-codesigning-keys.md)
當程式碼簽署的有效認證尋找有問題的專案時，就會發生這個錯誤訊息但找不到它們。 程式碼簽署做是為了測試及部署實體 iOS 裝置。與臨機操作 （& s） 應用程式儲存組建。

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[為什麼我的 iOS 9 應用程式因為「System.Exception: 無法封送處理 Objective-C 物件」而失敗？](exception-marshal-obj-c.md)
IOS 9 中的應用程式開發介面變更要求為基礎的 API，以立即呼叫 unmanaged 程式碼，需要時使用的回呼建構函式。

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[執行階段錯誤：找不到組件 mscorlib.dll 或無法載入](error-mscorlib-not-found.md)
就會發生此問題時*隱藏*`.monotouch-32`和`.monotouch-64`資料夾是遺漏`.xcarchive`簽署 / IPA 建立、 執行階段錯誤。

## <a name="deprecated"></a>已被取代

> [!IMPORTANT]
> 以下文章適用於 Xamarin 的最新版的已解決的問題。 但是，若發生此問題，軟體的最新版本，請檔案[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本資訊和完整建置記錄檔輸出。



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[IPA 檔案為 0 個位元組](ipa-zero-bytes.md)
在舊版中可能會導致 IPA 檔案是 0 個位元組的 Windows 上的 Xamarin 的發生一些已知的問題。

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[IBTool 錯誤：無法完成作業。](error-ibtool.md)
Apple[固定](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html)這`ibtool`Xcode 6.1.1，因此升級到 Xcode 6.1.1 或更高版本中的 bug 時的最簡單的修正。

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[錯誤 MT1009：無法複製組件](error-mt1009.md)
這會影響執行 Xamarin.iOS 7.2.6 的使用者。 此問題是因為 Xamarin.iOS 會隨不同的使用者帳戶時，需要更高權限的檔案權限然後開發人員的主要帳戶。

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[已傳回 System.Exception AMDeviceNotificationSubscribe ...](exception-amddevicenotificationsubscribe.md)
當您第一次啟動 Visual Studio for Mac，或在錯誤對話方塊中會出現此訊息`mtbserver.log`檔案。 請注意，這不常發生的問題。 Visual Studio 無法連接到 Mac 組建主機，如果有較有可能會出現在其他錯誤`mtbserver.log`檔案。

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
這個錯誤可能會出現在`Mac Server Log`Visual Studio 中。
