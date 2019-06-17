---
title: Xamarin.iOS 常見問題集
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 8b90b76cad5277fe76fc476a0bcd6f600e91b256
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421821"
---
# <a name="ios-frequently-asked-questions"></a>iOS 常見問題集

## <a name="general-questions"></a>一般問題

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[我可以使用 Mac VM 搭配 Xamarin 嗎？](mac-vm.md)
是的但只能在 Mac 硬體上。

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[我該如何降級 Xcode？](downgrade-xcode.md)
本指南提供存取舊版 Xcode 以及最新版本的連結。

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[我在哪裡可以設定 iOS SDK 的位置？](ios-sdk.md)
對於大部分使用者這些會以適當的位置自動設定。 本指南列出的預設 SDK 位置，以及如何視需要變更它們。

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[如何重新開發人員選項啟用更新 iOS 之後](update-developer-options.md)
IOS bug 可能會導致更新 iOS 版本之後消失的開發人員選項，這已發現切換至 iOS 時 8.x。 本指南說明如何可以重新啟用的選項。

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[使用者位置無法在 iOS 8 中運作](ios8-user-location.md)
本指南將告訴您如何編輯 info.plist，若要在 iOS 8 中啟用使用者的位置。

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[我在哪裡可以找到要將 iOS 當機記錄檔符號化的 .dSYM 檔案？](symbolicate-ios-crash.md)
本指南說明 symbolicating iOS 當機記錄檔，以協助診斷當機的基本步驟。 它也會連結至更進階的符號化解析技術與解譯 iOS 當機記錄檔資訊的其他資源。


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[我該如何針對 Xamarin Studio 中的 iOS 專案設定 Mono 執行階段環境變數？](xs-mono-runtime.md)
如果您需要設定任何執行階段環境變數的 Mono，他們可以設定**專案選項 > 執行 > 一般**頁面。

## <a name="publishing-questions"></a>發佈問題

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[提交至 App Store 時的錯誤：「 在送出作業中，會偵測到無效的套件組合-不允許內嵌在 bitcode 中的選項 」](invalid-bundle-bitcode.md)

提交應用程式，_需要_bitcode，例如 watchOS 和 tvOS 應用程式，必須透過 Xcode 9。

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[我可以變更輸出路徑的 IPA 檔案嗎？](ipa-output-path.md)
截至 Xamarin 週期 7，您可以使用自訂的 MSBuild 目標來達到此目的。

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[如何將 IPA 輸出檔案複製到 TFS 放置資料夾？](ipa-tfs.md)
是，本指南說明如何。

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[我可以將檔案加入或移除的 IPA 檔案中的檔案之後在 Visual Studio 中建立, 嗎？](modify-ipa.md)
是的但它通常需要重新簽署`.app`進行變更之後的套件組合。 請注意，修改`.ipa`檔案不需要在正常使用。 這篇文章只供僅供參考之用。

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[是否可以從 Visual Studio 建立.xcarchive 封存？](create-xcarchive.md)
截至 Xamarin 4 是，現在可以建立`.xcarchive`從 Windows 藉由設定`ArchiveOnBuild`屬性設`true`。

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[為什麼我的應用程式提交失敗，而且出現：[有不允許的路徑 ( "iTunesMetadata.plist" ) 出現在...]？](itunesmetadata-disallowed-paths.md)
此錯誤是 Apple 的 App Store 驗證程序中的變更結果。 這個特定的錯誤_未_相關的 Xamarin，您已安裝特定版本，因此降級會_不_幫助。 本指南連結至有關如何修正此問題的詳細資訊的說明。


## <a name="diagnosing-specific-error-messages"></a>診斷特定的錯誤訊息

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[含 RegisterServicePort 的 iOS 設計工具錯誤](error-registerserviceport.md)
錯誤`RegisterServicePort`和這類類似的錯誤訊息，上面通常是間諜軟體/惡意程式碼的電腦上的問題。 本指南詳細說明如何確認診斷及有關移除間諜軟體/惡意程式碼的資訊。

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[為什麼我的 iOS 組建因為「Keychain 中找不到任何有效的 iPhone 程式碼簽署金鑰」而失敗？](no-codesigning-keys.md)
此錯誤訊息時發生問題專案需要有效的程式碼簽署憑證，但找不到它們。 程式碼簽署，才能測試和實體 iOS 裝置上的部署以及臨機操作及應用程式儲存組建。

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[有什麼我的 iOS 9 應用程式失敗，而且出現：System.Exception:無法封送 Objective-C 物件？](exception-marshal-obj-c.md)
在 iOS 9 的 API 變更需要回呼的建構函式，呼叫 unmanaged 程式碼，為基礎的 API 現在預期它時使用。

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[執行階段錯誤：找不到或無法載入組件 mscorlib.dll](error-mscorlib-not-found.md)
就會發生此問題時*隱藏*`.monotouch-32`並`.monotouch-64`資料夾中遺漏的`.xcarchive`來簽署 / IPA 建立、 執行階段錯誤。

## <a name="deprecated"></a>已被取代

> [!IMPORTANT]
> 以下文章適用於 Xamarin 的最新版本中已解決的問題。 不過，如果最新版本的軟體，就會發生問題，請指導，申請[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本控制資訊和完整建置記錄檔輸出。



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[IPA 檔案為 0 個位元組](ipa-zero-bytes.md)
在舊版的 Xamarin，可能會導致要 0 個位元組的 Windows 上的 IPA 檔案發生一些已知的問題。

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[IBTool 錯誤：作業無法完成。](error-ibtool.md)
Apple[修正](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html)這`ibtool`Xcode 6.1.1，因此升級到 Xcode 6.1.1 或更高版本中的 bug 是最簡單的修正程式。

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[錯誤 MT1009：無法複製組件](error-mt1009.md)
這會影響執行 Xamarin.iOS 7.2.6 的使用者。 此問題是因為 Xamarin.iOS 安裝使用不同的使用者帳戶時，需要較高的權限的檔案權限則開發人員的主要帳戶。

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[已傳回 System.Exception AMDeviceNotificationSubscribe ...](exception-amddevicenotificationsubscribe.md)
當您第一次啟動 Visual Studio for Mac，或在 [錯誤] 對話方塊中會出現此訊息`mtbserver.log`檔案。 請注意，這是常見的問題。 如果 Visual Studio 無法連接到 Mac 組建主機，還有更有可能會出現在其他錯誤`mtbserver.log`檔案。

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
此錯誤可能會出現在`Mac Server Log`Visual Studio 中。
