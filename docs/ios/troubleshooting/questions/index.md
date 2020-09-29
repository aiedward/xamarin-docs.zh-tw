---
title: Xamarin iOS 常見問題
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 5e258f350256945c7794ee67f814d30d09894c9a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432178"
---
# <a name="ios-frequently-asked-questions"></a>iOS 常見問題

## <a name="general-questions"></a>一般問題

### <a name="can-i-use-a-mac-vm-with-xamarin"></a>[我可以使用 Mac VM 搭配 Xamarin 嗎？](mac-vm.md)
是，但僅適用于 Mac 硬體。

### <a name="how-can-i-downgrade-xcode"></a>[我該如何降級 Xcode？](./previous-xcode.md)
本指南提供存取舊版 Xcode 和最新版本的連結。

### <a name="where-can-i-set-my-ios-sdk-locations"></a>[在哪裡可以設定 iOS SDK 的位置？](ios-sdk.md)
對於大部分的使用者，這些會自動設定為適當的位置。 本指南列出預設 SDK 位置，以及如何在需要時加以變更。

### <a name="how-can-i-reenable-developer-options-after-updating-ios"></a>[如何在更新 iOS 之後重新啟用開發人員選項？](update-developer-options.md)
IOS bug 可能會導致開發人員選項在更新 iOS 版本之後消失，並在切換至 iOS 8.x 時觀察到。 本指南說明如何重新啟用這些選項。

### <a name="user-location-not-working-in-ios-8"></a>[使用者位置在 iOS 8 中無法運作](ios8-user-location.md)
本指南將告訴您如何編輯資訊 plist，以在 iOS 8 中啟用使用者位置。

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>[在哪裡可以找到用來將 iOS 當機記錄檔符號化的 .dSYM 檔案？](symbolicate-ios-crash.md)
本指南說明 symbolicating iOS 損毀記錄檔以協助診斷損毀的基本步驟。 它也會連結到其他資源，以取得更高階的符號化技術 & 有關解讀 iOS 損毀記錄的資訊。

### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>[如何在 Xamarin Studio 中，為 iOS 專案設定 Mono 執行階段環境變數？](xs-mono-runtime.md)
如果您需要設定 Mono 的任何執行時間環境變數，您可以在 [專案選項] 中設定這些變數， **> 執行 > 一般** ] 頁面。

## <a name="publishing-questions"></a>發行問題

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>[提交至 App Store 時發生錯誤：「不正確套件組合-未允許內嵌于 bitcode 中的選項，提交中偵測到」](invalid-bundle-bitcode.md)

您必須使用 Xcode 9 來提交 _需要_ bitcode 的應用程式，例如 WatchOS 和 tvOS 應用程式。

### <a name="can-i-change-the-output-path-of-the-ipa-file"></a>[我可以變更 IPA 檔案的輸出路徑嗎？](ipa-output-path.md)
從 Xamarin 週期7，您可以使用自訂的 MSBuild 目標來達成這個目的。

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>[如何將 IPA 輸出檔案複製到 TFS 投遞資料夾？](ipa-tfs.md)
是，本指南將說明如何。

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>[在 Visual Studio 中建立檔案之後，是否可以在 IPA 檔案中新增或移除檔案？](modify-ipa.md)
是的，這是可行的，但通常會要求您在進行變更之後重新簽署套件組合 `.app` 。 請注意， `.ipa` 在正常使用時不需要修改檔案。 本文僅供參考之用。

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>[是否可以從 Visual Studio 建立 .xcarchive 封存？](create-xcarchive.md)
從 Xamarin 4 開始，現在可以藉 `.xcarchive` 由將屬性設定為，從 Windows 建立 `ArchiveOnBuild` `true` 。

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>[為什麼我的應用程式提交失敗，並出現：「不允許的路徑 ( "Itunesmetadata.plist. plist" ) 找到于 ...」？](itunesmetadata-disallowed-paths.md)
此錯誤是 Apple 的 App Store 驗證流程變更的結果。 此特定錯誤與您已安裝的特定 Xamarin 版本無關，因此降級將_不__會有説明_。 本指南將詳細說明如何修正問題的相關資訊。

## <a name="diagnosing-specific-error-messages"></a>診斷特定錯誤訊息

### <a name="ios-designer-error-with-registerserviceport"></a>[含 RegisterServicePort 的 iOS 設計工具錯誤](error-registerserviceport.md)
與 `RegisterServicePort` 上述類似的錯誤訊息的錯誤通常是電腦上的間諜軟體/惡意程式碼的問題。 本指南詳細說明如何確認診斷和移除間諜軟體/惡意程式碼的資訊。

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>[為什麼我的 iOS 組建失敗，並出現：在 keychain 中找不到有效的 iPhone 程式碼簽署金鑰？](no-codesigning-keys.md)
當有問題的專案正在尋找有效的程式碼簽署認證，但是找不到它們時，就會出現此錯誤訊息。 需要程式碼簽署才能在實體 iOS 裝置上進行測試和部署;以及臨機操作 & App store 組建。

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>[為什麼我的 iOS 9 應用程式失敗，並出現： system.exception：無法封送處理目標 C 物件？](exception-marshal-obj-c.md)
IOS 9 中的 API 變更需要在呼叫非受控程式碼時使用回呼函式，因為基礎 API 現在會預期它。

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loaded"></a>[執行階段錯誤：找不到或無法載入元件 mscorlib.dll](error-mscorlib-not-found.md)
當*hidden* `.monotouch-32` 用於簽署/IPA 建立的隱藏和資料夾遺失時，就會發生此問題 `.monotouch-64` `.xcarchive` ，並觸發執行階段錯誤。

### <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocation"></a>[編譯錯誤：無法在產生的散佈重新配置中編碼位移 X](error-encode-offset-scattered-relocation.md)
當最終二進位檔對原生工具鏈而言太大時，當建立32位架構（例如 ARMv7）時，就會發生這個問題。

## <a name="deprecated"></a>已被取代

> [!IMPORTANT]
> 下列文章適用于 Xamarin 最新版本中已解決的問題。 但是，如果軟體的最新版本發生問題，請使用完整版本設定資訊和完整組建記錄檔輸出提出 [新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

### <a name="ipa-file-is-0-bytes"></a>[IPA 檔案為 0 個位元組](ipa-zero-bytes.md)
舊版 Xamarin 有一些已知問題，可能會導致 Windows 上的 IPA 檔案為0個位元組。

### <a name="ibtool-error-the-operation-couldnt-be-completed"></a>[IBTool 錯誤：無法完成操作。](error-ibtool.md)
Apple 修正 `ibtool` 了 Xcode 6.1.1 中的此錯誤，因此升級至 Xcode 6.1.1 或更高版本是最簡單的修正程式。

### <a name="error-mt1009-could-not-copy-the-assembly"></a>[錯誤 MT1009：無法複製元件](error-mt1009.md)
這會影響執行 Xamarin 7.2.6 的使用者。 此問題的原因是，當使用不同的使用者帳戶安裝 Xamarin 時，需要較高許可權的檔案許可權，就是開發人員的主帳戶。

### <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>[傳回了 System.Exception AMDeviceNotificationSubscribe ...](exception-amddevicenotificationsubscribe.md)
當您第一次啟動 Visual Studio for Mac 或在檔案中時，此訊息可能會出現在 [錯誤] 對話方塊中 `mtbserver.log` 。 請注意，這是不常見的問題。 如果 Visual Studio 在連線到 Mac 組建主機時發生問題，檔案中可能會出現其他錯誤 `mtbserver.log` 。

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>[找不到 MDocArchiveToMsxDocConverter.exe rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
此錯誤可能會出現在 `Mac Server Log` Visual Studio 中。