---
title: "發行至 App Store"
description: "本指南逐步說明使用 Visual Studio for Mac 部署 Xamarin.Mac 應用程式。 文中說明如何設定 Mac 開發人員帳戶、逐步說明建立用於程式碼簽署之憑證的程序，以及示範如何使用它們以建置可直接散發或透過 Mac App Store 散發的 Mac 應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: D26C5E54-EAD2-5487-264D-4263AEA1EBF2
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 86514dbd6b40e9d59ea9a598395cf6f0a3f4889a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="publishing-to-the-app-store"></a>發佈至 App Store

_本指南逐步說明使用 Visual Studio for Mac 部署 Xamarin.Mac 應用程式。文中說明如何設定 Mac 開發人員帳戶、逐步說明建立用於程式碼簽署之憑證的程序，以及示範如何使用它們以建置可直接散發或透過 Mac App Store 散發的 Mac 應用程式。_

## <a name="overview"></a>總覽

Xamarin.Mac 應用程式可以兩種方式散發：

- **開發人員識別碼**：使用開發人員識別碼簽署的應用程式可在 Mac App Store 外部散發，但是由閘道管理員辨識和允許安裝。
- **Mac App Store**：應用程式必須有安裝程式套件，而且應用程式與安裝程式必須都經過簽署，才能提交給 Mac App Store。

本文說明如何使用 Visual Studio for Mac 和 Xcode 來設定 Apple 開發人員帳戶，以及為每個部署類型設定 Xamarin.Mac 專案。


## <a name="mac-developer-program"></a>Mac 開發人員計劃

當您加入 [Mac 開發人員計劃](https://developer.apple.com/devcenter/mac/) \(英文\) 時，開發人員可選擇要以 [Individual] \(個人\) 或 [Company] \(公司\) 的身分加入，如下列螢幕擷取畫面所示：

[![Apple 開發人員入口網站](images/image1.png "Apple 開發人員入口網站")](images/image1-large.png#lightbox)

請依據您的情況選擇正確的註冊類型。

> [!NOTE]
> **注意**：此處所做的選擇會影響設定開發人員帳戶時一些畫面的顯示方式。 本文中的說明與螢幕擷取畫面都是從 [Individual] \(個人\) 開發人員帳戶的觀點完成。 以 [Company] \(公司\) 的身分進行時，有些選項僅供**小組系統管理員**使用。


### <a name="certificates-and-identifiersmacdeploy-testpublishing-to-the-app-storecertificates-identifiersmd"></a>[憑證和識別碼](~/mac/deploy-test/publishing-to-the-app-store/certificates-identifiers.md)

本指南逐步引導您建立發行 Xamarin.Mac 應用程式所需的必要憑證與識別碼。


### <a name="create-provisioning-profilemacdeploy-testpublishing-to-the-app-storeprofilesmd"></a>[建立佈建設定檔](~/mac/deploy-test/publishing-to-the-app-store/profiles.md)

本指南逐步引導您建立發行 Xamarin.Mac 應用程式所需的必要佈建設定檔。


### <a name="mac-app-configurationmacdeploy-testpublishing-to-the-app-storeapp-configurationmd"></a>[Mac 應用程式組態](~/mac/deploy-test/publishing-to-the-app-store/app-configuration.md)

本指南逐步說明設定 Xamarin.Mac 應用程式以供發行。


### <a name="sign-with-developer-idmacdeploy-testpublishing-to-the-app-storesigningmd"></a>[使用開發人員識別碼簽署](~/mac/deploy-test/publishing-to-the-app-store/signing.md)

本指南逐步說明使用開發人員識別碼簽署 Xamarin.Mac 應用程式以供發行。


### <a name="bundle-for-mac-app-storemacdeploy-testpublishing-to-the-app-storebundlingmd"></a>[Mac App Store 的套件組合](~/mac/deploy-test/publishing-to-the-app-store/bundling.md)

本指南逐步說明統合 Xamarin.Mac 應用程式以發行至 Mac App Store。


### <a name="upload-to-mac-app-storemacdeploy-testpublishing-to-the-app-storeuploadingmd"></a>[上傳至 Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/uploading.md)

本指南逐步說明上傳 Xamarin.Mac 應用程式以發行至 Mac App Store。


## <a name="related-links"></a>相關連結

- [安裝](/visualstudio/mac/installation/)
- [Hello, Mac 範例](~/mac/get-started/hello-mac.md)
- [開發人員識別碼和閘道管理員](https://developer.apple.com/resources/developer-id/) \(英文\)
