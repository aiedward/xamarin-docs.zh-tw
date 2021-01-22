---
title: Xamarin.Mac 中的憑證和識別碼
description: 本指南將逐步引導您建立發佈 Xamarin.Mac 應用程式所需的必要憑證和識別碼。
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2019
ms.openlocfilehash: ae62a695670f50c5385b9279c6d6de79b6d59fb7
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697562"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Xamarin.Mac 中的憑證和識別碼

_本指南將逐步引導您建立發佈 Xamarin.Mac 應用程式所需的必要憑證和識別碼。_

## <a name="setup"></a>安裝程式

前往 [Apple Developer Member Center](https://developer.apple.com) \(英文\) 以設定 Mac 進行開發。 按一下 [帳戶] 連結並登入。 主功能表如下所示：

> [!div class="mx-imgBorder"]
> [![Apple Developer Member Center](certificates-identifiers-images/devcenter01.png)](certificates-identifiers-images/devcenter01-large.png#lightbox)

按一下 [Certificates, Identifiers & Profiles] \(憑證、識別碼及設定檔\) 按鈕 (或 **憑證** 標題附近的加號按鈕)：

> [!div class="mx-imgBorder"]
> [![選取憑證、識別碼 & 設定檔](certificates-identifiers-images/devcenter02.png)](certificates-identifiers-images/devcenter02-large.png#lightbox)

選取憑證類型，然後按一下 [繼續]：

> [!div class="mx-imgBorder"]
> [![選取 [憑證] 連結](certificates-identifiers-images/devcenter03.png)](certificates-identifiers-images/devcenter03-large.png#lightbox)

您可視需要在此下載 **中繼憑證** (Worldwide Developer Relations Certificate Authority 和 Developer ID Certificate Authority) (頁面底部的最後一個項目)。 不過，這些應會由 Xcode 為開發人員自動設定。

本節其餘部分將逐步解說與 Mac 開發人員相關的各節：

- **註冊 Mac 應用程式識別碼**：開發人員所撰寫的每個應用程式都必須依照這些步驟進行。
- **註冊 macOS 系統**：這只有在新增要測試的電腦時才需要使用。
- **建立憑證**：設定憑證時和稍後予以更新時，都需要進行一次。
- **建立佈建設定檔**：開發人員撰寫的每個新應用程式都必須依照這些步驟進行。

## <a name="register-mac-app-id"></a>註冊 Mac 應用程式識別碼

您必須為每個應用程式註冊一個應用程式識別碼。 請遵循下列步驟建立項目：

1. 按 "+" (加號) 或 **註冊應用程式識別碼**：

    > [!div class="mx-imgBorder"]
    > [![螢幕擷取畫面顯示憑證、識別碼和設定檔中的應用程式 I Ds 開始使用。](certificates-identifiers-images/appid01.png)](certificates-identifiers-images/appid01-large.png#lightbox)

1. 選擇 **應用程式識別碼**

    > [!div class="mx-imgBorder"]
    > [![螢幕擷取畫面：顯示註冊新的識別碼選項。](certificates-identifiers-images/appid02.png)](certificates-identifiers-images/appid02-large.png#lightbox)

1. 輸入 **描述**，然後選取應用程式需要的任何 **應用程式服務**：a. 平台應為 **macOS** a. 選擇 [描述] (僅在此入口網站中使用) a. 輸入 **組合識別碼**，其應符合您的 **Info.plist** a. 選取應用程式需要的功能

    > [!div class="mx-imgBorder"]
    > [![輸入描述與應用程式服務](certificates-identifiers-images/appid03.png)](certificates-identifiers-images/appid03-large.png#lightbox)

    按 [繼續] 以檢閱您的選取項目。

1. 如果資訊正確無誤，請按一下 [註冊] 完成設定：

    > [!div class="mx-imgBorder"]
    > [![檢查輸入的資料](certificates-identifiers-images/appid04.png)](certificates-identifiers-images/appid04-large.png#lightbox)

1. 請確認資訊，然後按一下 [Submit] \(提交\)按鈕：

    > [!div class="mx-imgBorder"]
    > ![驗證資訊](certificates-identifiers-images/appid05.png)

有些 [App Services] \(應用程式服務\) 可能需要進一步設定 (例如 iCloud)。 如果是這種情況，請選取剛才建立的新應用程式識別碼，然後按一下 [Edit] \(編輯\) 按鈕：

> [!div class="mx-imgBorder"]
> [![編輯新的應用程式識別碼](certificates-identifiers-images/appid06.png)](certificates-identifiers-images/appid06-large.png#lightbox)

若要設定 iCloud 服務，請按一下 [編輯] 按鈕：

> [!div class="mx-imgBorder"]
> [![設定 iCloud 服務](certificates-identifiers-images/appid07.png)](certificates-identifiers-images/appid07-large.png#lightbox)

## <a name="register-macos-devices"></a>註冊 macOS 裝置

開發人員必須先註冊其 Mac 電腦，才能建立用於測試的佈建設定檔。 測試時最多可註冊 100 部電腦。

1. 在 Mac Developer Center 中，選取 [Devices] \(裝置\) 區段的 [All] \(全部\)，然後按一下 **+** 按鈕：

    > [!div class="mx-imgBorder"]
    > [![加入新電腦](certificates-identifiers-images/device01.png)](certificates-identifiers-images/device01-large.png#lightbox)

1. 輸入要新增電腦的 [Name] \(名稱\) 與 [UUID]，然後按一下 [Continue] \(繼續\) 按鈕。 檢閱資訊，然後按一下 [Register] \(註冊\) 按鈕：

    > [!div class="mx-imgBorder"]
    > [![螢幕擷取畫面顯示 [註冊新裝置] 頁面，您可以在其中輸入名稱和 U U I D。](certificates-identifiers-images/device02.png)](certificates-identifiers-images/device02-large.png#lightbox)

1. 檢閱與確認輸入的資料：

    > [!div class="mx-imgBorder"]
    > [![螢幕擷取畫面顯示 [註冊新裝置] 頁面，您可以在其中確認名稱和 U U I D。](certificates-identifiers-images/device03.png)](certificates-identifiers-images/device03-large.png#lightbox)

## <a name="create-certificates"></a>建立憑證

使用 [Certificates] \(憑證\) 區段建立幾種不同類型的憑證，用於簽署 Mac 應用程式：

> [!div class="mx-imgBorder"]
> [![建立新憑證](certificates-identifiers-images/devcenter04.png)](certificates-identifiers-images/devcenter04-large.png#lightbox)

與 macOS 開發相關的憑證有五大類型：

- **Mac Development**：開發一般應用程式時為選用項目，但如果開發人員打算使用 iCloud 或推播通知等功能時，則為必要項目。 開發人員必須要有開發憑證，才能建立佈建設定檔以存取上述功能。
- **Mac App Distribution**：開發人員其應用程式和安裝程式都需要有各自的憑證。
- **Mac Installer Distribution**：開發人員其應用程式和安裝程式都需要有各自的憑證。
- **Developer ID Installer**：在 Mac App Store 外部散發安裝程式時所需的憑證。
- **Developer ID Application**：在 Mac App Store 外部散發應用程式時所需的憑證。

下列各節會提供建立上述部分憑證類型的範例。

### <a name="mac-development-certificate"></a>Mac 開發憑證

如前所述，只有在使用 iCloud 或推播通知等 macOS 功能時，才必須使用 Mac Development 憑證。

請執行下列步驟來建立新的開發憑證：

1. 選取 [Mac Development] \(Mac 開發\) 選項按鈕，然後按一下 [Continue] \(繼續\)：

    > [!div class="mx-imgBorder"]
    > [![新增開發憑證](certificates-identifiers-images/certif02.png)](certificates-identifiers-images/certif02-large.png#lightbox)

1. 上傳 _憑證簽署要求_。 憑證要求檔案 (副檔名為 `.certSigningRequest`) 將會儲存在 Mac 本機上。 按一下 [選擇檔案] 以選取憑證要求，然後按 [繼續]。

    > [!div class="mx-imgBorder"]
    > [![上傳憑證要求檔案](certificates-identifiers-images/certif03.png)](certificates-identifiers-images/certif03-large.png#lightbox)

    遵循 [深入了解 >](https://help.apple.com/developer-account/#/devbfa00fef7) 連結以取得如何使用 **Keychain 存取** 來建立憑證要求檔案的指示。

1. 按 [下載] 以取得憑證檔案，然後按兩下檔案加以安裝：

    > [!div class="mx-imgBorder"]
    > [![下載憑證檔案](certificates-identifiers-images/certif04.png)](certificates-identifiers-images/certif04-large.png#lightbox)

如先前所述，除非開發人員實作像是 iCloud 與推播通知等 macOS 功能，否則不一定需要開發人員憑證。 建立測試 Mac App Store 應用程式所需的 [Development Provisioning Profile] \(開發佈建設定檔\) 時也需要此憑證。

### <a name="mac-app-store-certificates"></a>Mac App Store 憑證

若要在 App Store 發行應用程式，您需有兩項憑證：

- **Mac App Distribution** 憑證，用於簽署應用程式；以及
- **Mac Installer Distribution** 憑證，用於簽署安裝程式。

> [!TIP]
> 請謹慎命名這些金鑰的憑證要求：請使用含有 `Application` 與 `Installer` 文字的描述性名稱，方便稍後辨別。

首先，請建立安裝程式憑證：

1. 選取 [Mac Installer Distribution] 作為憑證類型，然後按一下 [繼續]**Continue** 按鈕：

    > [!div class="mx-imgBorder"]
    > [![建立 App Store 憑證](certificates-identifiers-images/certif05.png)](certificates-identifiers-images/certif05-large.png#lightbox)

1. 下一個頁面說明如何使用 [鑰匙圈存取] 產生憑證要求檔案。 請依照下列指示執行：

    > [!div class="mx-imgBorder"]
    > [![上傳憑證要求](certificates-identifiers-images/certif06.png)](certificates-identifiers-images/certif06-large.png#lightbox)

    遵循 [深入了解 >](https://help.apple.com/developer-account/#/devbfa00fef7) 連結以取得如何使用 **Keychain 存取** 來建立憑證要求檔案的指示。 請記得選擇可反映憑證 _類型_ (應用程式或安裝程式) 的憑證名稱。

1. 按一下 [Download] \(下載\) 以取得憑證，然後按兩下以安裝在 [鑰匙圈] 中：

    > [!div class="mx-imgBorder"]
    > [![下載 App Store 憑證](certificates-identifiers-images/certif07.png)](certificates-identifiers-images/certif07-large.png#lightbox)

**針對 Mac App Distribution 憑證，請遵循相同步驟。**

![Mac App Distribution 憑證](certificates-identifiers-images/certif08.png)

### <a name="developer-id-certificates"></a>開發人員識別碼憑證

若要自行發行 Xamarin.Mac 應用程式 (不是透過 Apple App Store 發行)，您需有兩項憑證：

- **Developer ID Installer** 憑證，用於簽署應用程式；以及
- **Developer ID Application** 憑證，用於簽署安裝程式。

> [!TIP]
> 請謹慎命名這些金鑰的憑證要求：請使用含有 `Application` 與 `Installer` 文字的描述性名稱，方便稍後辨別。

建立、下載及安裝憑證之後，這些憑證就會出現在 **Keychain 存取** 中：

[Keychain 存取憑證清單](certificates-identifiers-images/certif09.png)

## <a name="related-links"></a>相關連結

- [安裝](/visualstudio/mac/installation/)
- [Hello，Mac 範例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上散發應用程式](https://developer.apple.com/devcenter/mac/checklist/) \(英文\)
- [開發人員識別碼和 GateKeeper](https://developer.apple.com/developer-id/) \(英文\)
