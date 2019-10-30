---
title: 適用於 iOS 的 fastlane - sigh
description: 本文件描述 fastlane 的 sigh 命令，它用來建立、更新及修復所有 Xamarin.iOS 的組建組態佈建設定檔。
ms.prod: xamarin
ms.assetid: CD17276F-2C8C-4A46-A54C-DD532EBD5720
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 9506d80ab10ce70938f95371a52bf28bda33d42d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028501"
---
# <a name="fastlane-for-ios-sigh"></a>適用於 iOS 的 fastlane - sigh

> [!IMPORTANT]
> fastlane 建議使用 [`match`](~/ios/deploy-test/provisioning/fastlane/match.md) 來產生和維護您的佈建設定檔。 請只在您想要擁有完整控制權，且具備充分程式碼簽署知識的情況下，才直接使用 sigh。

## <a name="overview"></a>總覽

傳統上，是由開發小組的每個成員透過 Xcode 或在 Apple Developer Portal (Apple 開發人員入口網站) 上執行裝置佈建。 它是由數個步驟所組成：

- 要求開發憑證
- 將裝置新增至入口網站
- 建立應用程式識別碼
- 建立佈建設定檔
- 下載設定檔和憑證

這其中每個步驟都包含需要處理的變數，這些變數取決於您所開發的應用程式類型。 如需有關設定裝置以進行開發的所需步驟 (不論是手動還是透過 Xcode) 詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。

本指南介紹如何使用 fastlane 工具來替代使用 Xcode，並提供下列各項說明：

- [什麼是 sigh？](#whatissigh)
- [建立應用程式識別碼](#appid)
- [新增裝置](#newdevices)
- [使用 sigh](#using)
- [其他選項](#options)

> [!NOTE]
> 如果有與您應用程式套件組合識別碼相符的現有「應用程式識別碼」，且開發人員入口網站中有您的裝置，您就可以忽略[建立應用程式識別碼](#appid)和[新增裝置](#newdevices)的相關步驟。 在此情況下，請直接前往[使用 sigh](#using) 來開始進行操作。

## <a name="installation"></a>安裝

如需有關安裝 fastlane 的詳細資訊，請參閱 [fastlane 簡介](~/ios/deploy-test/provisioning/fastlane/index.md#Installation)指南。

<a name="whatissigh" />

## <a name="what-is-sigh"></a>什麼是 sigh

sigh 提供一個終端機介面，可讓您為下列所有組態建立和更新佈建設定檔：開發、App Store 散發、Ad Hoc 散發及企業散發。 此外，它也提供一個簡單的方式來下載和修復佈建設定檔。

<a name="appid" />

## <a name="creating-an-app-id"></a>建立應用程式識別碼

您可以使用下列命令來建立「應用程式識別碼」：

```
fastlane produce -u your@appleid.com -a com.company.appname --skip_itc
```

其中 `com.company.appname` 是您應用程式的套件組合識別碼，您可以在 Xamarin.iOS 應用程式的 Info.plist 檔案中找到此識別碼，如下所示：

[![](sigh-images/fastlane-image5.png "The Info.plist file of the Xamarin.iOS application")](sigh-images/fastlane-image5.png#lightbox)

唯一「應用程式識別碼」必須是反向 DNS 樣式字串。 建立此識別碼之後，請記下它，因為稍後在本指南中使用 sigh 時，將會需要用到它。

如果需要在 [iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) 上建立應用程式，請從上述命令中移除 `--skip_itc` 旗標。

<a name="newdevices" />

## <a name="adding-new-devices"></a>新增裝置

若要從命令列將單一裝置新增至開發人員入口網站，請輸入下列命令：

```bash
fastlane run register_device name:"Adam iPhone" udid:"abcdeg1234567"
```

若要新增多部裝置，請使用 `register_devices` 命令：

```bash
    register_devices(
        devices: {
            "iPhone 6" => "1234567890123456789012345678901234567890",
            "iPad Air 2" => "abcdefghijklmnopqrstvuwxyzabcdefghijklmn"
         }
    )
```

<a name="using" />

## <a name="using-sigh"></a>使用 sigh

若要開始使用 sigh 公用程式，請在終端機中輸入下列命令：

```bash
fastlane sigh
```

根據預設，這會建立一個 [App Store 散發](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)佈建設定檔。 若要設定您的裝置以進行開發，請傳遞 `--development` 旗標：

```bash
fastlane sigh --development
```

當 fastlane 提示您時，輸入您的 Apple ID。 如果這是您第一次使用 fastlane，可能也會要求您輸入密碼。 如果不是，則會從 Keychain 提取密碼環境變數。

如果您的 Apple ID 與多個小組連線，此處將會顯示這些小組。 請選取與您想要使用之小組對應的號碼：

[![](sigh-images/fastlane-image2.png "Select the team that you wish to use")](sigh-images/fastlane-image2.png#lightbox)

您也可以透過下列方式將「小組識別碼」傳遞給 CLI：

```bash
fastlane sigh -l 2TU993NY9J
```

請輸入您應用程式的[應用程式識別碼](#appid)。 請記住，此識別碼應該與您應用程式 Info.plist 中的「套件組合識別碼」相符。

所有與您帳戶連接的裝置都會新增至佈建設定檔。

fastlane 將會接著為您建立、下載及安裝佈建設定檔。

如果您瀏覽 Developer Center (開發人員中心)，就可以檢視新建立的佈建設定檔，如下所示：

[![](sigh-images/fastlane-image10.png "View the newly created provisioning profile")](sigh-images/fastlane-image10.png#lightbox)

sigh 預設會將佈建設定檔儲存在目前的資料夾中。 若要變更輸出目錄，請編輯 `output_path`，或是執行下列動作：

```bash
fastlane sigh -o "~/Library/MobileDevice/Provisioning Profiles"
```

<a name="options" />

## <a name="sigh-additional-options"></a>sigh 其他選項

下列選項可用來在使用 sigh 時提供額外的支援：

- 若要下載所有佈建設定檔，請使用：

    ```bash
    fastlane sigh download_all
    ```

- 若要為您的佈建設定檔使用特定簽署身分識別：

    ```bash
    fastlane sigh -c "Amy cert"
    ```
    
    其中 `Amy cert` 為程式碼簽署身分識別名稱。

## <a name="related-links"></a>相關連結

- [fastlane - sigh](https://github.com/fastlane/fastlane/tree/master/sigh#readme)
