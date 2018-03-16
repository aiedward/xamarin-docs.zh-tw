---
title: "適用於 iOS 的 fastlane - match"
ms.topic: article
ms.prod: xamarin
ms.assetid: C4A2A67E-0643-4CED-B1A9-79D65054F3CA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: d92f820e22277148b4de3ff87e3fdaca0f573f52
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="fastlane-for-ios---match"></a>適用於 iOS 的 fastlane - match

傳統上，是由開發小組的每個成員透過 Xcode 或在 Apple Developer Portal (Apple 開發人員入口網站) 上執行裝置佈建。 它是由數個步驟所組成：

- 要求開發憑證
- 將裝置新增至入口網站
- 建立應用程式識別碼
- 建立佈建設定檔
- 下載設定檔和憑證

如需有關設定裝置以進行開發的所需步驟 (不論是手動還是透過 Xcode) 詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。

本指南介紹如何使用 fastlane 工具來替代使用 Xcode。

## <a name="installation"></a>安裝

如需有關安裝 fastlane 的詳細資訊，請參閱 [fastlane 簡介](~/ios/deploy-test/provisioning/fastlane/index.md#Installation)指南。

<a name="whatismatch" />

## <a name="what-is-match"></a>什麼是 match？

match 負責建立和維護程式碼簽署憑證與佈建設定檔，並可讓 iOS 開發小組在所有開發人員之間共用一個程式碼簽署身分識別。

將應用程式部署到 App Store、執行 Beta 版測試或在裝置上安裝應用程式時，每個開發小組成員都有自己的簽署身分識別。 這會導致身分識別及設定檔發生衝突，而必須手動建立、輪替及管理設定檔和「應用程式識別碼」。

match 則是會為您建立和維護所有憑證與設定檔，並將其儲存在私人 Git 存放庫。 這可讓小組中的所有開發人員存取及使用這些認證。 這也進而為您的憑證提供額外的安全性：除了將憑證儲存在私人 Git 存放庫中之外，也會以[複雜密碼](#passphrase)加密這些憑證。 將程式碼簽署成品儲存在存放庫可讓小組代理人和系統管理員視需要更新及輪替憑證，這意謂著可花費較少的時間散發新憑證給每個開發人員。

> [!IMPORTANT]
> match 目前不支援 In-House Enterprise (企業內部) 設定檔。

<a name="initializing" />

## <a name="initializing-your-project-with-match"></a>使用 match 將專案初始化

如果您是小組系統管理員，請透過 github.com 或 bitbucket.com 建立私人 Git 存放庫，並確保將所有小組成員都新增至存放庫中作為參與者。

使用您的終端機將目錄變更至專案目錄，然後執行：

    fastlane match init

出現提示時，輸入 Git 存放庫的 URL：

 [![](match-images/fastlane-image7.png "輸入 Git 存放庫的 URL")](match-images/fastlane-image7.png#lightbox)

若要尋找及複製此 URL，請在 github.com 上按一下 [Clone or Download] \(複製或下載\) 按鈕，如下所示：

[![](match-images/fastlane-image6.png "github.com 上 [Clone or Download] \(複製或下載\) 按鈕底下的 URL")](match-images/fastlane-image6.png#lightbox)

將專案初始化會建立一個 matchfile – 這是一個文字檔，您可以編輯此檔案以將環境變數傳遞給 match 工具。 matchfile 範例的說明如下：

[![](match-images/fastlane-image8.png "matchfile 範例")](match-images/fastlane-image8.png#lightbox)

<a name="running" />

## <a name="running-match"></a>執行 match

> [!NOTE]
> fastlane 建議您在第一次執行 match 之前，應該考慮使用 [match nuke 命令](#using)來清除現有的設定檔和憑證。

視您所需的環境而定，您可以使用下列任何命令來建立憑證和佈建設定檔，並將其儲存在新的 Git 存放庫中：

    fastlane match appstore

    fastlane match adhoc

    fastlane match development

除了建立新憑證和設定檔之外，使用這任何一個命令也會將下列項目新增 (或如果已經存在，則會更新) 至您的 Git 存放庫：

- 憑證資料夾
- 設定檔資料夾
- 含有基本指示的讀我檔案
- match 版本

[![](match-images/fastlane-image9.png "Git 存放庫中的專案結構")](match-images/fastlane-image9.png#lightbox)

佈建設定檔安裝在 `~/Library/MobileDevice/Provisioning Profiles`中。 憑證和私密金鑰會直接安裝在您的 Keychain 中。

<a name="using" />

### <a name="using-the-nuke-command"></a>使用 `nuke` 命令

如果您有凌亂的憑證，您可以使用下列命令以 `nuke` 撤銷每個環境的憑證和設定檔：

    fastlane match nuke

撤銷特定環境的所有憑證和佈建設定檔：

    fastlane match nuke development

 或

    fastlane match nuke distribution

fastlane 會先確認將移除的檔案，然後才會將其刪除。

<a name="passphrase" />

### <a name="passphrase"></a>複雜密碼

第一次執行 `match` 時，系統會要求您為 Git 存放庫設定複雜密碼。 請務必記住此密碼，因為當您在不同的電腦上執行 match 時，將會需要此密碼。 這提供一層額外的安全性：將會使用 OpenSSL 來加密每個檔案。 後續在新電腦上每次執行 `match` 時都會提示輸入此複雜密碼 – 初次輸入之後，此複雜密碼將會新增至本機 Keychain。

若要使用環境變數來設定複雜密碼以將設定檔解密，請使用 `MATCH_PASSWORD`。

<a name="options" />

## <a name="additional-options"></a>其他選項

下列選項可用來在使用 match 時提供額外的支援：

- 使用 `-–help` 旗標可顯示所有可用命令的清單：

        fastlane match cert --help

- 使用 `-–verbose` 旗標可提高輸出的詳細程度：

        fastlane match --development --verbose

- 使用 `--force_for_new_devices` 旗標可強制更新佈建設定檔 (如果開發人員入口網站上的裝置計數已變更的話)

        fastlane match development --force_for_new_devices

## <a name="related-links"></a>相關連結

- [fastlane - match](https://github.com/fastlane/fastlane/blob/master/match/README.md)
