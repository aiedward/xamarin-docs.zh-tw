---
title: Xamarin.Mac 應用程式的佈建設定檔
description: 本指南逐步引導您建立發行 Xamarin.Mac 應用程式所需的必要佈建設定檔。
ms.prod: xamarin
ms.assetid: bdff6c32-f7e3-4a97-a093-dbda48be8227
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: de87e650f68e5a327564c0a4216bee2b3e04fe2f
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697601"
---
# <a name="provisioning-profiles-for-xamarinmac-apps"></a>Xamarin.Mac 應用程式的佈建設定檔

佈建設定檔可讓開發人員將數項macOS (之前稱為 Mac OS X) 特有功能 (例如 iCloud 與推播通知) 併入其 Xamarin.Mac 應用程式。 開發人員必須為他們所開發會使用這些功能的每個應用程式建立、下載並安裝 Mac 佈建設定檔。

[![Apple 佈建入口網站](profiles-images/certif13.png)](profiles-images/certif13.png#lightbox)

## <a name="development-provisioning-profile"></a>開發佈建設定檔

開發佈建設定檔可讓以 Mac App Store 為目標的應用程式在設定檔中設定的特定電腦上進行測試。 這與使用像是 iCloud 與推播通知等 macOS 功能時特別相關。

> [!NOTE]
> 開發人員必須已經建立「Mac 開發憑證」，才能建立「開發佈建設定檔」。 完成此螢幕擷取畫面所示的詳細資料，以產生可用於建立組建的 **開發佈建設定檔**。 [Certificate (憑證)] 方塊中的選取項目必須是可用的有效 Mac 開發憑證，且至少註冊一個系統以供進行測試。

執行下列動作：

1. 選取要建立的佈建設定檔類型，然後按一下 [Continue (繼續)] 按鈕：

    [![選取設定檔類型](profiles-images/certif14.png)](profiles-images/certif14.png#lightbox)
2. 選取要用於建立設定檔的應用程式識別碼，然後按一下 [Continue (繼續)] 按鈕：

    [![選取應用程式識別碼](profiles-images/certif15.png)](profiles-images/certif15.png#lightbox)
3. 選取用於簽署設定檔的開發人員識別碼，然後按一下 [Continue (繼續)]：

    [![選取開發人員識別碼](profiles-images/certif16.png)](profiles-images/certif16.png#lightbox)
4. 選取可使用此設定檔的電腦，然後按一下 [Continue (繼續)]：

    [![選取允許的電腦](profiles-images/certif17.png)](profiles-images/certif17.png#lightbox)
5. 現在，輸入 **設定檔名稱**，然後按一下 [Generate(產生)] 按鈕：

    [![螢幕擷取畫面顯示在布建視窗中輸入設定檔名稱。](profiles-images/certif18.png)](profiles-images/certif18.png#lightbox)
6. 按一下 [Download (下載)] 按鈕以下載新的設定檔：

    [![螢幕擷取畫面：顯示設定檔的 [下載] 按鈕。](profiles-images/certif19.png)](profiles-images/certif19.png#lightbox)
7. 開發佈建設定檔會安裝至 Mac [系統偏好設定] 應用程式的設定檔偏好設定窗格：

    [![螢幕擷取畫面，顯示 [安裝驗證] 對話方塊。](profiles-images/certif20.png)](profiles-images/certif20.png#lightbox)
8. 設定檔偏好設定窗格會顯示所有安裝的設定檔：

    [![顯示所有安裝的設定檔](profiles-images/image47.png)](profiles-images/image47.png#lightbox)
9. 該設定檔也會顯示在[Developer Certificate Utility (開發人員憑證公用程式)] 中，以防萬一您需要再次下載：

    [![開發人員憑證公用程式](profiles-images/image48.png)](profiles-images/image48.png#lightbox)

您必須為每一個新應用程式或要新增以進行測試的新電腦建立新的開發佈建設定檔。

## <a name="production-provisioning-profile"></a>生產佈建設定檔

若要建置可提交至 Mac App Store 的套件，您需要生產佈建設定檔。

執行下列動作：

1. 選取要建立的設定檔類型，然後按一下 [Continue (繼續)] 按鈕：

    [![選取設定檔類型](profiles-images/certif21.png)](profiles-images/certif21.png#lightbox)
2. 選取要用於建立設定檔的應用程式識別碼，然後按一下 [Continue (繼續)] 按鈕：

    [![選取應用程式識別碼](profiles-images/certif15.png)](profiles-images/certif15.png#lightbox)
3. 選取要簽署設定檔的公司識別碼，然後按一下 [Continue (繼續)] 按鈕：

    [![選取公司識別碼](profiles-images/certif23.png)](profiles-images/certif23.png#lightbox)
4. 輸入 **設定檔名稱**，然後按一下 [Generate(產生)] 按鈕：

    [![產生設定檔](profiles-images/certif24.png)](profiles-images/certif24.png#lightbox)
5. 按一下 [Download (下載)]，以取得佈建設定檔檔案 (副檔名為 `.provisionprofile`)：

    [![下載設定檔](profiles-images/certif25.png)](profiles-images/certif25.png#lightbox)
6. 將檔案拖放到 **Xcode Organizer** 或按兩下以安裝。 接著，設定檔會顯示在 Xcode Organizer 中：

    [![安裝設定檔](profiles-images/image51.png)](profiles-images/image51.png#lightbox)
7. 佈建設定檔也會顯示在清單中：

    [![顯示安裝的設定檔](profiles-images/certif26.png)](profiles-images/certif26.png#lightbox)

如果開發人員變更應用程式識別碼使用的功能 (例如， 啟用 iCloud 或推播通知)，他們應該為該應用程式識別碼重新建立佈建設定檔。

## <a name="related-links"></a>相關連結

- [安裝](~//mac/get-started/installation.md)
- [Hello，Mac 範例](~//mac/get-started/hello-mac.md)
- [在 Mac App Store 上散發應用程式](https://developer.apple.com/devcenter/mac/checklist/) \(英文\)
- [工具指南：對應用程式進行程式碼簽署](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html) \(英文\)
- [開發人員識別碼和 GateKeeper](https://developer.apple.com/developer-id/) \(英文\)
