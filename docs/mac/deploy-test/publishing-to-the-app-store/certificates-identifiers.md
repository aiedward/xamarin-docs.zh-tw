---
title: "憑證和識別碼"
description: "本指南逐步引導您建立發行 Xamarin.Mac 應用程式所需的必要憑證與識別碼。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a1065fb91a23827c4876654470cda5022aa1d3b8
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="certificates-and-identifiers"></a>憑證和識別碼

_本指南將逐步引導您建立發行 Xamarin.Mac 應用程式所需的必要憑證和識別碼。_

## <a name="certificates-and-identifiers"></a>憑證和識別碼

前往 [Apple Developer Member Center](http://developer.apple.com) \(英文\) 以設定 Mac 進行開發。 主功能表如下所示：

[![Apple Developer Member Center (Apple 開發人員成員中心)](certificates-identifiers-images/devcenter01.png "Apple Developer Member Center (Apple 開發人員成員中心)")](certificates-identifiers-images/devcenter01-large.png#lightbox)

按一下 [Certificates, Identifiers & Profiles] \(憑證、識別碼及設定檔\) 連結：

[![選取 Certificates, Identifiers & Profiles 憑證、識別碼及設定檔](certificates-identifiers-images/devcenter02.png "選取 Certificates, Identifiers & Profiles 憑證、識別碼及設定檔")](certificates-identifiers-images/devcenter02-large.png#lightbox)

接著，按一下 [Mac Apps] \(Mac 應用程式\) 區段下的 [Certificates] \(憑證\) 連結：

[![選取 Certificates 憑證 連結](certificates-identifiers-images/devcenter03.png "選取 Certificates 憑證 連結")](certificates-identifiers-images/devcenter03-large.png#lightbox)

按一下 [All] \(全部\) 連結，然後按一下 [+] 按鈕：

[![全部選取並新增項目](certificates-identifiers-images/certif01.png "全部選取並新增項目")](certificates-identifiers-images/certif01-large.png#lightbox)

請視需要下載此處的 [Intermediate Certificates] \(中繼憑證\) (Worldwide Developer Relations Certificate Authority 與 Developer ID Certificate Authority)。 不過，這些應會由 Xcode 為開發人員自動設定。

本文其餘的四節會逐步說明以完成 Mac 開發人員帳戶設定。

-   **註冊 Mac 應用程式識別碼**：開發人員所撰寫的每個應用程式都必須依照這些步驟進行。
-   **註冊 macOS 系統**：這只有在新增要測試的電腦時才需要使用。
-   **建立憑證**：設定憑證時和稍後予以更新時，都需要進行一次。
-   **建立佈建設定檔**：開發人員撰寫的每個新應用程式都必須依照這些步驟進行。

按一下頁面左上方的 [Overview] \(概觀\) 連結，即可隨時回到此功能表。

### <a name="register-mac-app-id"></a>註冊 Mac 應用程式識別碼

開發人員必須為撰寫的每個應用程式註冊應用程式識別碼。 使用下列步驟建立稱為 “MacWriter” 的基本範例應用程式項目。

1. 輸入 [App ID Description] \(應用程式識別碼描述\)，然後選取應用程式需要的任何 [App Services] \(應用程式服務\)： 

    [![輸入描述與應用程式服務](certificates-identifiers-images/devcenter04.png "輸入描述與應用程式服務")](certificates-identifiers-images/devcenter04-large.png#lightbox)
2. 輸入應用程式的 [Bundle ID] \(套件組合識別碼\)，然後按一下 [Continue] \(繼續\) 按鈕： 

    [![輸入套件組合識別碼](certificates-identifiers-images/devcenter05.png "輸入套件組合識別碼")](certificates-identifiers-images/devcenter05-large.png#lightbox)
3. 請確認資訊，然後按一下 [Submit] \(提交\)按鈕： 

    [![確認資訊](certificates-identifiers-images/devcenter06.png "確認資訊")](certificates-identifiers-images/devcenter06-large.png#lightbox)

有些 [App Services] \(應用程式服務\) 可能需要進一步設定 (例如 iCloud)。 如果是這種情況，請選取剛才建立的新應用程式識別碼，然後按一下 [Edit] \(編輯\) 按鈕：

[![編輯新的應用程式識別碼](certificates-identifiers-images/devcenter07.png "編輯新的應用程式識別碼")](certificates-identifiers-images/devcenter07-large.png#lightbox)

若要設定 iCloud 服務，請按一下 [Edit] \(編輯\) 按鈕：

[![設定 iCloud 服務](certificates-identifiers-images/devcenter08.png "設定 iCloud 服務")](certificates-identifiers-images/devcenter08-large.png#lightbox)

開發人員可以在這裡設定即將使用的資料庫：

[![設定資料庫](certificates-identifiers-images/devcenter09.png "設定資料庫")](certificates-identifiers-images/devcenter09-large.png#lightbox)

### <a name="register-macos-systems"></a>註冊 macOS 系統

開發人員必須先註冊其 Mac 電腦，才能建立用於測試的佈建設定檔。 他們可以註冊最多 100 部用於測試其 Mac 應用程式的電腦。

在 Mac Developer Center 中，選取 [Devices] \(裝置\) 區段的 [All] \(全部\)，然後按一下 **+** 按鈕：

[![新增電腦](certificates-identifiers-images/devcenter10.png "新增電腦")](certificates-identifiers-images/devcenter10-large.png#lightbox)

輸入要新增電腦的 [Name] \(名稱\) 與 [UUID]，然後按一下 [Continue] \(繼續\) 按鈕。 檢閱資訊，然後按一下 [Register] \(註冊\) 按鈕：

[![輸入新電腦資訊](certificates-identifiers-images/devcenter11.png "輸入新電腦資訊")](certificates-identifiers-images/devcenter11-large.png#lightbox)

### <a name="create-certificates"></a>建立憑證

使用 [Certificates] \(憑證\) 區段建立幾種不同類型的憑證，用於簽署 Mac 應用程式：

[![建立新憑證](certificates-identifiers-images/certif01.png "建立新憑證")](certificates-identifiers-images/certif01-large.png#lightbox)

主要有三種憑證類型：

-   **Mac Development Certificate \(Mac 開發憑證)**：開發一般應用程式時可選擇性使用，但如果開發人員打算使用像是 iCloud 或推播通知時則務必要使用。 開發人員必須要有開發憑證，才能建立佈建設定檔以存取上述功能。
-   **Mac App Store**：開發人員的應用程式和安裝程式都需要各自的憑證。
-   **Developer ID \(開發人員識別碼\)**：如果選擇在 Mac App Store 外部散發，應用程式與安裝程式都需要憑證。

下列各節會提供建立上述各種憑證類型的範例。

#### <a name="mac-development-certificate"></a>Mac 開發憑證

如先前所述，除非使用像是 iCloud 或推播通知等 macOS 功能，才必須使用 Mac 開發憑證。

請執行下列步驟來建立新的開發憑證：

1. 選取 [Mac Development] \(Mac 開發\) 選項按鈕，然後按一下 [Continue] \(繼續\)： 

     [![新增開發憑證](certificates-identifiers-images/certif02.png "新增開發憑證")](certificates-identifiers-images/certif02-large.png#lightbox)
2. 下一個畫面說明如何使用 [鑰匙圈存取] 來建立要上傳的憑證簽署要求： 

    [![鑰匙圈存取上傳畫面](certificates-identifiers-images/certif03.png "鑰匙圈存取上傳畫面")](certificates-identifiers-images/certif03-large.png#lightbox)
3. 選擇有意義的憑證 [Common Name] \(通用名稱\)，以便稍後建立最終憑證時可輕鬆辨識。 請記住儲存檔案的位置，方便在下一個步驟找到檔案： 

     ![匯出憑證](certificates-identifiers-images/image12.png "匯出憑證")
4. 憑證要求檔案 (副檔名為 `.certSigningRequest`) 會儲存在 Mac 本機上。 請記住儲存位置 (預設位置為桌面)，您必須在下一個步驟中選擇該位置： 

     [![上傳憑證檔案](certificates-identifiers-images/image13.png "上傳憑證檔案")](certificates-identifiers-images/image13-large.png#lightbox)
5. 按一下 [Download] \(下載\) 以取得憑證，然後按兩下以安裝在 [鑰匙圈] 中： 

     [![下載開發憑證](certificates-identifiers-images/image15.png "下載開發憑證")](certificates-identifiers-images/image15-large.png#lightbox)
6. 按一下 [Download] \(下載\) 以取得憑證，然後按兩下以安裝在 [鑰匙圈] 中。 [Developer Certificate Utility] \(開發人員憑證公用程式\) 會顯示憑證如下： 

     [![Developer Certificate Utility (開發人員憑證公用程式)](certificates-identifiers-images/image16.png "Developer Certificate Utility (開發人員憑證公用程式)")](certificates-identifiers-images/image16-large.png#lightbox)
7. 它也會在 [鑰匙圈] 中顯示如下： 

     ![[鑰匙圈存取] 中的憑證](certificates-identifiers-images/image17.png "[鑰匙圈存取] 中的憑證")

如先前所述，除非開發人員實作像是 iCloud 與推播通知等 macOS 功能，否則不一定需要開發人員憑證。 建立測試 Mac App Store 應用程式所需的 [Development Provisioning Profile] \(開發佈建設定檔\) 時也需要此憑證。

#### <a name="mac-app-store-certificates"></a>Mac App Store 憑證

若要在 App Store 上發行應用程式，**Mac App Store** 憑證會用來簽署應用程式，而且也必須建立 Mac 安裝程式套件。

1. 選取 [Mac App Store] 作為憑證類型，然後按一下 [Continue] \(繼續\) 按鈕： 

    [![建立 App Store 憑證](certificates-identifiers-images/certif04.png "建立 App Store 憑證")](certificates-identifiers-images/certif04-large.png#lightbox)
2. 選取要建立的憑證類型 (需要每種類型各一才能發行至 App Store)： 

    [![選取憑證類型](certificates-identifiers-images/certif05.png "選取憑證類型")](certificates-identifiers-images/certif05-large.png#lightbox)
3. 下一個頁面說明如何使用 [鑰匙圈存取] 產生憑證要求檔案。 請依照下列指示執行： 

     [![產生鑰匙圈要求](certificates-identifiers-images/certif06.png "產生鑰匙圈要求")](certificates-identifiers-images/certif06-large.png#lightbox)
4. 選擇描述性 [Common Name] \(一般名稱\)：例如在名稱中使用「App Store 應用程式」的文字： 

     ![輸入描述性名稱](certificates-identifiers-images/image20.png "輸入描述性名稱")
5. 憑證要求檔案 (副檔名為 `.certSigningRequest`) 會儲存在 Mac 本機上。 請記住儲存位置 (預設位置為桌面)： 

     [![建立憑證](certificates-identifiers-images/image21.png "建立憑證")](certificates-identifiers-images/image21-large.png#lightbox)
6. 按一下 [Download] \(下載\) 以取得憑證，然後按兩下以安裝在 [鑰匙圈] 中： 

      [![下載 App Store 憑證](certificates-identifiers-images/image23.png "下載 App Store 憑證")](certificates-identifiers-images/image23-large.png#lightbox)
7. 按一下 [Continue] \(繼續\)，然後依照完全相同的步驟下載另一個憑證，這次則是供「安裝程式」使用： 

     [![選取安裝程式](certificates-identifiers-images/image24.png "")](certificates-identifiers-images/image24-large.png#lightbox)
8. 選擇描述性 [Common Name] \(一般名稱\)：例如在名稱中使用「AppStore 安裝程式」的文字： 

     ![設定憑證的名稱](certificates-identifiers-images/image25.png "設定憑證的名稱")
9. 憑證要求檔案 (副檔名為 `.certSigningRequest`) 會儲存在 Mac 本機上。 請記住儲存位置 (預設位置為桌面)： 

     [![上傳憑證](certificates-identifiers-images/image26.png "上傳憑證")](certificates-identifiers-images/image26-large.png#lightbox) 

     [![下載散發憑證](certificates-identifiers-images/image28.png "下載散發憑證")](certificates-identifiers-images/image28-large.png#lightbox)
10. 按一下 [Download] \(下載\) 以取得憑證，然後按兩下以安裝在 [鑰匙圈] 中。 [Developer Certificate Utility] \(開發人員憑證公用程式\) 會顯示憑證如下： 

     [![Developer Certificate Utility (開發人員憑證公用程式)](certificates-identifiers-images/image29.png "Developer Certificate Utility (開發人員憑證公用程式)")](certificates-identifiers-images/image29-large.png#lightbox)
11. 現在可在 [鑰匙圈] 中看到這兩個新憑證： 

     [![鑰匙圈存取 中的憑證](certificates-identifiers-images/image30.png "鑰匙圈存取 中的憑證")](certificates-identifiers-images/image30-large.png#lightbox)

#### <a name="developer-id-certificates"></a>開發人員識別碼憑證

若要自行發行 Xamarin.Mac 應用程式 (不透過 Apple App Store 發行)，開發人員需要有開發人員識別碼憑證，才能簽署應用程式以供發行和安裝。

請執行下列動作：

1. 一開始請按一下 [Certificates] \(憑證\) 區段的 [+] 按鈕，然後選取 [Developer ID] \(開發人員識別碼\) 選項按鈕： 

    [![新增開發人員識別碼](certificates-identifiers-images/certif07.png "新增開發人員識別碼")](certificates-identifiers-images/certif07-large.png#lightbox)
2. 按一下 [Continue] \(繼續\) 按鈕，然後選取要建立的開發人員識別碼類型： 

    [![選取開發人員識別碼類型](certificates-identifiers-images/certif08.png "選取開發人員識別碼類型")](certificates-identifiers-images/certif08-large.png#lightbox)
3. 共需要兩個，一個用以簽署應用程式本身，而另一個則用以簽署應用程式的安裝程式。 請謹慎命名這些金鑰的憑證要求：請使用含有 `Application` 與 `Installer` 文字的描述性名稱，方便稍後辨別。
4. 下一個畫面會提供如何建立憑證，的詳細指示，請按一下 [Continue] \(繼續\) 按鈕： 

    [![如何建立憑證](certificates-identifiers-images/certif09.png "如何建立憑證")](certificates-identifiers-images/certif09-large.png#lightbox)
5. 選擇描述性 [Common Name] \(一般名稱\)：例如在名稱中使用「開發人員識別碼應用程式」的文字： 

     ![輸入憑證的名稱](certificates-identifiers-images/image33.png "輸入憑證的名稱")
6. 憑證要求檔案 (副檔名為 `.certSigningRequest`) 會儲存在 Mac 本機上。 請記住儲存位置 (預設位置為桌面)： 

     [![上傳憑證](certificates-identifiers-images/certif10.png "上傳憑證")](certificates-identifiers-images/certif10-large.png#lightbox) 

     [![下載開發人員識別碼](certificates-identifiers-images/certif11.png "下載開發人員識別碼")](certificates-identifiers-images/certif11-large.png#lightbox)
7. 按一下 [Download] \(下載\) 以取得憑證，然後按兩下以安裝在 [鑰匙圈] 中。
8. 按一下 [Continue] \(繼續\)，然後依照完全相同的步驟下載另一個憑證，這次則是供「安裝程式」使用。
9. 選擇描述性 [Common Name] \(一般名稱\)：例如在名稱中使用「開發人員識別碼安裝程式」的文字： 

     ![輸入一般名稱](certificates-identifiers-images/image38.png "")
10. 憑證要求檔案 (副檔名為 `.certSigningRequest`) 會儲存在 Mac 本機上。 請記住儲存位置 (預設位置為桌面)： 

     [![上傳憑證](certificates-identifiers-images/certif10.png "上傳憑證")](certificates-identifiers-images/certif10-large.png#lightbox)
11. 接著，即可下載該憑證，請先按一下 [Download] \(下載\)，然後再按一下 [Done] \(完成\)： 

     [![下載憑證](certificates-identifiers-images/certif11.png "下載憑證")](certificates-identifiers-images/certif11-large.png#lightbox)
12. 按一下 [Download] \(下載\) 以取得憑證，然後按兩下以安裝在 [鑰匙圈] 中。 [Developer Certificate Utility] \(開發人員憑證公用程式\) 會顯示憑證如下： 

     [![Developer Certificate Utility (開發人員憑證公用程式)](certificates-identifiers-images/certif12.png "Developer Certificate Utility (開發人員憑證公用程式)")](certificates-identifiers-images/certif12-large.png#lightbox)
13. 您可在 [鑰匙圈] 中看到下列項目： 

     [![鑰匙圈存取 中的憑證](certificates-identifiers-images/image43.png "鑰匙圈存取 中的憑證")](certificates-identifiers-images/image43-large.png#lightbox)


## <a name="related-links"></a>相關連結

- [安裝](/visualstudio/mac/installation/)
- [Hello, Mac 範例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上散發應用程式](https://developer.apple.com/devcenter/mac/checklist/) \(英文\)
- [開發人員識別碼和 GateKeeper](https://developer.apple.com/resources/developer-id/) \(英文\)
