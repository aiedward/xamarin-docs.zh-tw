---
title: 適用於 iOS 的 fastlane - cert
description: 本文件描述 fastlane，它是一種可自動化許多 iOS 應用程式佈建程序部分的工具：要求憑證、在 Apple 開發人員入口網站中新增裝置、建立應用程式識別碼等等。
ms.prod: xamarin
ms.assetid: 900FA6FF-F3C9-4D35-993E-B0D88E6B1883
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 8e98a8e0314d8cb5ace4d17fdc62eb2ec75ca336
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292455"
---
# <a name="fastlane-for-ios--cert"></a>適用於 iOS 的 fastlane - cert

> [!IMPORTANT]
> fastlane 建議使用 [`match`](~/ios/deploy-test/provisioning/fastlane/match.md) 工具來產生和維護憑證。 請只在您想要擁有完整控制權，且具備充分程式碼簽署知識的情況下，才直接使用 `cert`。 請使用此動作來下載最新的程式碼簽署身分識別。

## <a name="overview"></a>總覽

傳統上，是由開發小組的每個成員透過 Xcode 或在 Apple Developer Portal (Apple 開發人員入口網站) 上執行裝置佈建。 它是由數個步驟所組成：

- 要求開發憑證
- 將裝置新增至入口網站
- 建立應用程式識別碼
- 建立佈建設定檔
- 下載設定檔和憑證

這其中每個步驟都包含需要處理的變數，這些變數取決於您所開發的應用程式類型。 如需有關設定裝置以進行開發的所需步驟 (不論是手動還是透過 Xcode) 詳細資訊，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)指南。

本指南介紹如何使用 fastlane 工具來替代使用 Xcode，並提供下列各項說明：

- [什麼是 cert？](#whatiscert)
- [使用 cert](#using)
- [其他選項](#options)

## <a name="installation"></a>安裝

如需有關安裝和更新 fastlane 的詳細資訊，請參閱 [fastlane 簡介](~/ios/deploy-test/provisioning/fastlane/index.md#Installation)指南。

<a name="whatiscert" />

## <a name="what-is-cert"></a>什麼是 cert？

cert 提供一個終端機介面，可針對開發和散發環境建立新的程式碼簽署身分識別 (通常稱為開發人員「憑證」)。

<a name="using" />

## <a name="using-cert"></a>使用 cert

若要使用 cert 公用程式，請在終端機 CLI 中輸入下列命令：

```
fastlane cert
```

根據預設，這會建立散發憑證。 若要建立開發憑證，請傳遞 `--development` 旗標：

```
fastlane cert --development
```

cert 會提示您輸入 Apple ID 和密碼，因此請立即輸入此資訊：

[![](cert-images/fastlane-image1.png "cert 會提示您輸入 Apple ID 和密碼")](cert-images/fastlane-image1.png#lightbox)

> [!IMPORTANT]
> 第一次輸入密碼時，密碼會儲存至本機 macOS 鑰匙圈。 或者，您也可以使用「環境變數」來儲存使用者名稱和密碼，或如果您不想要將密碼儲存在鑰匙圈中，則可使用 `export fastlane_DONT_STORE_PASSWORD=1`。 如需有關使用 fastlane 來管理認證的詳細資訊，請參閱 fastlane 的[認證管理員指南](https://github.com/fastlane/fastlane/blob/master/credentials_manager/README.md)(英文\)。

您也可以使用下列命令將 Apple ID 當作引數來傳遞：

```
fastlane cert -u myemailadress@domain.com
```

如果您的 Apple ID 與多個小組連線，此處將會顯示這些小組。 請選取與您想要使用之小組對應的號碼：

[![](cert-images/fastlane-image2.png "選取您想要使用的小組")](cert-images/fastlane-image2.png#lightbox)

您也可以使用下列旗標來傳遞「小組識別碼」：

```
fastlane cert -l 2TU993NY9J
```

fastlane 會檢查您的本機電腦上是否有安裝任何可用的簽署憑證，如果有，就會使用它。

如果沒有任何簽署憑證，cert 將會：

- 建立新的私密金鑰和簽署要求
- 產生、下載及安裝憑證
- 將憑證和私密金鑰匯入至 Keychain

當達到您帳戶允許的簽署身分識別數目上限時，將會發出例外狀況。 如果您想要建立新的簽署身分識別，就必須透過 Developer Center (開發人員中心) 手動撤銷其中一個現有的憑證，然後再試一次。

> [!NOTE]
> 如果現有的簽署身分識別尚未存在於您的鑰匙圈中，fastlane 並無法從 Developer Center (開發人員中心) 下載這些簽署身分識別。 這是因為私密金鑰只會存在於您的電腦上，或存在於匯出版 (*.p12) 的憑證中，而永遠不會存在於開發人員中心內。

<a name="options" />

## <a name="additional-options"></a>其他選項

下列選項可用來在使用 cert 時提供額外的支援：

- 使用 `-–help` 旗標可顯示所有可用命令的清單：

    ```
    fastlane cert --help
    ```

- 使用 `-–verbose` 旗標可提高輸出的詳細程度

    ```
    fastlane cert --development --verbose
    ```

## <a name="related-links"></a>相關連結

- [fastlane – cert](https://github.com/fastlane/fastlane/blob/master/cert/README.md)
