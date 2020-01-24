---
title: Xamarin.Mac 中的憑證和識別碼
description: 本指南會逐步解說如何建立發行 Xamarin. Mac 應用程式所需的必要憑證和識別碼。
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2019
ms.openlocfilehash: 2ea3516c1fb89c8c9b9cc3694d7c95ccd87e9d41
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489475"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Xamarin.Mac 中的憑證和識別碼

_本指南將逐步引導您建立發行 Xamarin.Mac 應用程式所需的必要憑證和識別碼。_

## <a name="setup"></a>安裝程式

前往 [Apple Developer Member Center](https://developer.apple.com) \(英文\) 以設定 Mac 進行開發。 按一下**帳戶**連結並登入。 主功能表如下所示：

> [!div class="mx-imgBorder"]
> [![Apple 開發人員成員中心](certificates-identifiers-images/devcenter01.png)](certificates-identifiers-images/devcenter01-large.png#lightbox)

按一下 [**憑證、識別碼 & 設定檔**] 按鈕（或 [**憑證**] 標題附近的加號按鈕）：

> [!div class="mx-imgBorder"]
> [![選取憑證、識別碼 & 設定檔](certificates-identifiers-images/devcenter02.png)](certificates-identifiers-images/devcenter02-large.png#lightbox)

選取憑證類型，然後按一下 [**繼續**]：

> [!div class="mx-imgBorder"]
> [![選取憑證連結](certificates-identifiers-images/devcenter03.png)](certificates-identifiers-images/devcenter03-large.png#lightbox)

在這裡，您可以視需要下載**中繼憑證**（全球開發人員關係憑證授權單位單位和開發人員識別碼憑證授權單位單位）（最後一個專案位於頁面底部）。 不過，這些應會由 Xcode 為開發人員自動設定。

本節的其餘部分將逐步解說與 Mac 開發人員相關的各節：

- **註冊 Mac 應用程式識別碼**：開發人員所撰寫的每個應用程式都必須依照這些步驟進行。
- **註冊 macOS 系統**：這只有在新增要測試的電腦時才需要使用。
- **建立憑證**–只有在設定憑證時才需要一次，而在更新時則為。
- **建立佈建設定檔**：開發人員撰寫的每個新應用程式都必須依照這些步驟進行。

## <a name="register-mac-app-id"></a>註冊 Mac 應用程式識別碼

您必須註冊每個應用程式的應用程式識別碼。 請依照下列步驟建立專案：

1. 按 "+" （加號）或**註冊應用程式識別碼**：

    > [!div class="mx-imgBorder"]
    > [![開始使用應用程式識別碼](certificates-identifiers-images/appid01.png)](certificates-identifiers-images/appid01-large.png#lightbox)

1. 選擇**應用程式識別碼**

    > [!div class="mx-imgBorder"]
    > [![開始使用應用程式識別碼](certificates-identifiers-images/appid02.png)](certificates-identifiers-images/appid02-large.png#lightbox)

1. 輸入 [**描述**]，然後選取應用程式將需要的任何**應用程式服務**： a。 平臺應為**macOS** 。 選擇 [**描述**] （僅在此入口網站中使用） a。 輸入套件組合**識別碼**，其應符合您的**資訊。 plist** a。 選取您的應用程式所需的功能

    > [!div class="mx-imgBorder"]
    > [![輸入描述和應用程式服務](certificates-identifiers-images/appid03.png)](certificates-identifiers-images/appid03-large.png#lightbox)

    按 [**繼續**] 以查看您的選擇。

1. 如果資訊正確無誤，請按一下 [**註冊**] 以完成設定：

    > [!div class="mx-imgBorder"]
    > [![檢查輸入的資料](certificates-identifiers-images/appid04.png)](certificates-identifiers-images/appid04-large.png#lightbox)

1. 請確認資訊，然後按一下 [Submit] \(提交\)按鈕：

    > [!div class="mx-imgBorder"]
    > ![驗證資訊](certificates-identifiers-images/appid05.png)

有些 [App Services] \(應用程式服務\) 可能需要進一步設定 (例如 iCloud)。 如果是這種情況，請選取剛才建立的新應用程式識別碼，然後按一下 [Edit] \(編輯\) 按鈕：

> [!div class="mx-imgBorder"]
> [![編輯新的應用程式識別碼](certificates-identifiers-images/appid06.png)](certificates-identifiers-images/appid06-large.png#lightbox)

若要設定 iCloud 服務，例如，請按一下 [**編輯**] 按鈕：

> [!div class="mx-imgBorder"]
> [![設定 iCloud 服務](certificates-identifiers-images/appid07.png)](certificates-identifiers-images/appid07-large.png#lightbox)

## <a name="register-macos-devices"></a>註冊 macOS 裝置

開發人員必須先註冊其 Mac 電腦，才能建立用於測試的佈建設定檔。 最多可以註冊100部電腦進行測試。

1. 在 Mac Developer Center 中，選取 [Devices] \(裝置\) 區段的 [All] \(全部\)，然後按一下 **+** 按鈕：

    > [!div class="mx-imgBorder"]
    > [![新增電腦](certificates-identifiers-images/device01.png)](certificates-identifiers-images/device01-large.png#lightbox)

1. 輸入要新增電腦的 [Name] \(名稱\) 與 [UUID]，然後按一下 [Continue] \(繼續\) 按鈕。 檢閱資訊，然後按一下 [Register] \(註冊\) 按鈕：

    > [!div class="mx-imgBorder"]
    > [輸入新電腦資訊 ![](certificates-identifiers-images/device02.png)](certificates-identifiers-images/device02-large.png#lightbox)

1. 檢查並確認輸入的資料：

    > [!div class="mx-imgBorder"]
    > [輸入新電腦資訊 ![](certificates-identifiers-images/device03.png)](certificates-identifiers-images/device03-large.png#lightbox)

## <a name="create-certificates"></a>建立憑證

使用 [Certificates] \(憑證\) 區段建立幾種不同類型的憑證，用於簽署 Mac 應用程式：

> [!div class="mx-imgBorder"]
> [![建立新的憑證](certificates-identifiers-images/devcenter04.png)](certificates-identifiers-images/devcenter04-large.png#lightbox)

有五種與 macOS 開發相關的主要憑證類型：

- **Mac 開發**–適用于一般應用程式開發的選擇性，但如果開發人員打算使用 iCloud 或推播通知等功能，則為必要。 開發人員必須要有開發憑證，才能建立佈建設定檔以存取上述功能。
- **Mac 應用程式散發**–開發人員需要應用程式的憑證，以及安裝程式的另一個憑證。
- **Mac 安裝程式散發**–開發人員需要應用程式的憑證和安裝程式的另一個憑證。
- **開發人員識別碼安裝**程式-要在 Mac App Store 外部散發的安裝程式憑證。
- **開發人員識別碼應用程式**-要在 Mac app Store 外部散發之應用程式的憑證。

下列各節將提供建立其中一些憑證類型的範例。

### <a name="mac-development-certificate"></a>Mac 開發憑證

如先前所述，除非使用 iCloud 或推播通知等 macOS 功能，否則不需要 Mac 開發憑證。

請執行下列步驟來建立新的開發憑證：

1. 選取 [Mac Development] \(Mac 開發\) 選項按鈕，然後按一下 [Continue] \(繼續\)：

    > [!div class="mx-imgBorder"]
    > [![新增開發憑證](certificates-identifiers-images/certif02.png)](certificates-identifiers-images/certif02-large.png#lightbox)

1. 上傳_憑證簽署要求_。 憑證要求檔案（延伸 `.certSigningRequest`）將會儲存在 Mac 本機上。 按一下 **[選擇**檔案] 以選取憑證要求，然後按 [**繼續**]。

    > [!div class="mx-imgBorder"]
    > [![上傳憑證要求檔案](certificates-identifiers-images/certif03.png)](certificates-identifiers-images/certif03-large.png#lightbox)

    如需有關如何使用**Keychain 存取**來建立憑證要求檔案的指示，請遵循[深入瞭解 >](https://help.apple.com/developer-account/#/devbfa00fef7)連結。

1. 按下 [**下載**] 以取得憑證檔案，然後按兩下以安裝：

    > [!div class="mx-imgBorder"]
    > [![下載憑證檔案](certificates-identifiers-images/certif04.png)](certificates-identifiers-images/certif04-large.png#lightbox)

如先前所述，除非開發人員實作像是 iCloud 與推播通知等 macOS 功能，否則不一定需要開發人員憑證。 建立測試 Mac App Store 應用程式所需的 [Development Provisioning Profile] \(開發佈建設定檔\) 時也需要此憑證。

### <a name="mac-app-store-certificates"></a>Mac App Store 憑證

若要在 App Store 上發行應用程式，您將需要兩個憑證：

- 將用來簽署應用程式的**Mac 應用程式散發**憑證;和 
- **Mac 安裝程式散發**憑證，用來簽署安裝程式。

> [!TIP]
> 請謹慎命名這些金鑰的憑證要求：請使用含有 `Application` 與 `Installer` 文字的描述性名稱，方便稍後辨別。

首先，建立安裝程式憑證：

1. 選取 [ **Mac 安裝程式散發**] 做為憑證類型，然後按一下 [**繼續**] 按鈕：

    > [!div class="mx-imgBorder"]
    > [建立 App Store 憑證 ![](certificates-identifiers-images/certif05.png)](certificates-identifiers-images/certif05-large.png#lightbox)

1. 下一個頁面說明如何使用 [鑰匙圈存取] 產生憑證要求檔案。 請依照下列指示執行：

    > [!div class="mx-imgBorder"]
    > [![上傳憑證要求](certificates-identifiers-images/certif06.png)](certificates-identifiers-images/certif06-large.png#lightbox)

    如需有關如何使用**Keychain 存取**來建立憑證要求檔案的指示，請遵循[深入瞭解 >](https://help.apple.com/developer-account/#/devbfa00fef7)連結。 請記得選擇反映憑證_類型_的憑證名稱（應用程式或安裝程式）。

1. 按一下 [Download] \(下載\) 以取得憑證，然後按兩下以安裝在 [鑰匙圈] 中：

    > [!div class="mx-imgBorder"]
    > [![下載 App Store 憑證](certificates-identifiers-images/certif07.png)](certificates-identifiers-images/certif07-large.png#lightbox)

**遵循 Mac 應用程式散發憑證的相同步驟。**

![Mac 應用程式散發憑證](certificates-identifiers-images/certif08.png)

### <a name="developer-id-certificates"></a>開發人員識別碼憑證

若要自行發行 Xamarin. Mac 應用程式（不是透過 Apple App Store 發行），您將需要兩個憑證：

- 將用來簽署應用程式的**開發人員識別碼安裝程式**憑證;和 
- **開發人員識別碼應用程式**憑證，用來簽署安裝程式。

> [!TIP]
> 請謹慎命名這些金鑰的憑證要求：請使用含有 `Application` 與 `Installer` 文字的描述性名稱，方便稍後辨別。

建立、下載及安裝憑證之後，它們就會顯示在 [ **Keychain 存取**] 中：

[Keychain 存取憑證清單](certificates-identifiers-images/certif09.png)

## <a name="related-links"></a>相關連結

- [安裝](/visualstudio/mac/installation/)
- [Hello, Mac 範例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上散發應用程式](https://developer.apple.com/devcenter/mac/checklist/) \(英文\)
- [開發人員識別碼和 GateKeeper](https://developer.apple.com/resources/developer-id/) \(英文\)
