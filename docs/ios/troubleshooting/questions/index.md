---
title: Xamarin iOS 常見問題
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 101e27d3ebb26295418c63c1be703976091e50d6
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/09/2019
ms.locfileid: "68869348"
---
# <a name="ios-frequently-asked-questions"></a>iOS 常見問題

## <a name="general-questions"></a>一般問題

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[我可以使用 Mac VM 搭配 Xamarin 嗎？](mac-vm.md)
是, 但僅限於 Mac 硬體。

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[我該如何降級 Xcode？](downgrade-xcode.md)
本指南提供存取舊版 Xcode 以及最新版本的連結。

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[我在哪裡可以設定 iOS SDK 的位置？](ios-sdk.md)
對於大部分的使用者, 這些都會自動設定為適當的位置。 本指南會列出預設的 SDK 位置, 以及如何在需要時加以變更。

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[如何在更新 iOS 之後重新啟用開發人員選項？](update-developer-options.md)
IOS 錯誤可能會導致開發人員選項在更新 iOS 版本後消失, 這在切換到 iOS 8.x 時已被觀察到。 本指南說明如何可以重新啟用這些選項。

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[使用者位置無法在 iOS 8 中運作](ios8-user-location.md)
本指南會告訴您如何編輯 plist, 以在 iOS 8 中啟用使用者位置。

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[我在哪裡可以找到要將 iOS 當機記錄檔符號化的 .dSYM 檔案？](symbolicate-ios-crash.md)
本指南說明 symbolicating iOS 損毀記錄檔以協助診斷當機的基本步驟。 它也會連結至其他資源, 以取得更先進的符號化技術, & 解釋 iOS 損毀記錄的資訊。


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[我該如何針對 Xamarin Studio 中的 iOS 專案設定 Mono 執行階段環境變數？](xs-mono-runtime.md)
如果您需要為 Mono 設定任何執行時間環境變數, 可以在 專案選項 中設定, **> 執行 > 一般** 頁面。

## <a name="publishing-questions"></a>發行問題

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[提交至 App Store 時發生錯誤:在提交期間, 偵測到不允許內嵌在 bitcode 中的套件組合-無效選項](invalid-bundle-bitcode.md)

提交_需要_bitcode 的應用程式 (例如 WatchOS 和 tvOS 應用程式), 必須使用 Xcode 9 來完成。

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[我可以變更 .IPA 檔案的輸出路徑嗎？](ipa-output-path.md)
從 Xamarin 迴圈 7, 您可以使用自訂的 MSBuild 目標來達到這個目的。

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[如何將 .IPA 輸出檔案複製到 TFS 投遞資料夾？](ipa-tfs.md)
是, 本指南將說明如何進行。

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[在 Visual Studio 中建立檔案之後, 我可以在 .IPA 檔案中新增檔案或從中移除檔案嗎？](modify-ipa.md)
沒錯, 這是可行的, 但通常會要求您在進行變更之後`.app`重新簽署配套。 請注意, 一般`.ipa`使用時不需要修改檔案。 本文僅供資訊參考之用。

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[可以從 Visual Studio 建立建立 .xcarchive 封存嗎？](create-xcarchive.md)
`.xcarchive`從 Xamarin 4 開始, 現在可以藉由`ArchiveOnBuild`將屬性設定為`true`, 從 Windows 建立。

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[為什麼我的應用程式提交失敗，而且出現：[有不允許的路徑 ( "iTunesMetadata.plist" ) 出現在...]？](itunesmetadata-disallowed-paths.md)
此錯誤是 Apple 的 App Store 驗證程式發生變更的結果。 此特定錯誤與您安裝的特定 Xamarin 版本無關, 因此降級將_不_會有説明。 本指南會連結至如何修正問題的詳細資訊。


## <a name="diagnosing-specific-error-messages"></a>診斷特定錯誤訊息

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[含 RegisterServicePort 的 iOS 設計工具錯誤](error-registerserviceport.md)
與`RegisterServicePort`上述錯誤訊息類似的錯誤, 通常是電腦上的間諜軟體/惡意程式碼問題。 本指南詳細說明如何確認診斷和移除間諜軟體/惡意程式碼的資訊。

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[為什麼我的 iOS 組建因為「Keychain 中找不到任何有效的 iPhone 程式碼簽署金鑰」而失敗？](no-codesigning-keys.md)
當有問題的專案正在尋找有效的程式碼簽署認證, 但找不到它們時, 就會出現這個錯誤訊息。 在實體 iOS 裝置上進行測試和部署時, 需要進行程式碼簽署;和臨機操作 & App store 組建。

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[有什麼我的 iOS 9 應用程式失敗，而且出現：System.Exception:無法封送 Objective-C 物件？](exception-marshal-obj-c.md)
IOS 9 中的 API 變更要求在呼叫非受控碼時使用回呼的函式, 因為基礎 API 現在會預期它。

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[執行階段錯誤：找不到或無法載入組件 mscorlib.dll](error-mscorlib-not-found.md)
當`.xcarchive`用於簽署/.ipa 建立的`.monotouch-64` *隱藏* `.monotouch-32`和資料夾遺失, 觸發執行階段錯誤時, 就會發生此問題。

### <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocationerror-encode-offset-scattered-relocationmd"></a>[編譯錯誤:無法在產生的散佈位置中編碼位移 X](error-encode-offset-scattered-relocation.md)
當最後一個二進位檔對原生工具鏈而言太大時, 建立32位架構 (例如 ARMv7) 時, 就會發生此問題。

## <a name="deprecated"></a>不再

> [!IMPORTANT]
> 下列文章適用于最新 Xamarin 版本中已解決的問題。 不過, 如果軟體的最新版本發生問題, 請使用完整版本設定資訊和完整組建記錄檔輸出來提出[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[IPA 檔案為 0 個位元組](ipa-zero-bytes.md)
舊版 Xamarin 中有一些已知問題, 可能導致 Windows 上的 .IPA 檔案為0個位元組。

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[IBTool 錯誤：作業無法完成。](error-ibtool.md)
Apple [已修正](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html)`ibtool` Xcode 6.1.1 中的這個錯誤, 因此升級至 Xcode 6.1.1 或更新版本是最簡單的修正程式。

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[錯誤 MT1009：無法複製組件](error-mt1009.md)
這會影響執行 Xamarin 7.2.6 的使用者。 此問題的原因是, 當使用不同的使用者帳戶安裝 Xamarin 時, 需要較高許可權的檔案許可權, 然後是開發人員的主要帳戶。

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[已傳回 System.Exception AMDeviceNotificationSubscribe ...](exception-amddevicenotificationsubscribe.md)
當您第一次啟動 Visual Studio for Mac 或檔案中的`mtbserver.log`時, 此訊息可能會出現在錯誤對話方塊中。 請注意, 這是不常見的問題。 如果 Visual Studio 連線到 Mac 組建主機時發生問題, 檔案中`mtbserver.log`可能會出現其他錯誤。

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
此錯誤可能會出現在`Mac Server Log` Visual Studio 中。
