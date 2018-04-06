---
title: 電子錢包功能
description: 為應用程式新增功能通常需要額外的佈建設定。 本指南說明「電子錢包」功能所需的設定。
ms.prod: xamarin
ms.assetid: BD9475E6-F586-488C-93D4-8A2A1629B99B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: f41ba2b693b37f39ea49f62e322932537014a317
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="wallet-capabilities"></a>電子錢包功能

_為應用程式新增功能通常需要額外的佈建設定。本指南說明「電子錢包」功能所需的設定。_

「電子錢包」是一個應用程式，可儲存和顯示條碼及其他可讓使用者從其裝置顯示票證、登機證和優待券的內容。 此資訊儲存在「票卡」上。 例如，登機證或單一票證會是單一票卡。 

開發人員可以透過各種方式使用「電子錢包」：

*   若要存取票卡，並不需要建置應用程式。 Passfile (票卡檔) 是一個 ZIP 壓縮封存，其中包含一些 JSON 檔案和選擇性的中繼資料檔案。 若要準備此檔案，必須要有[票卡類型識別碼](~/ios/platform/passkit.md)和[票卡憑證](~/ios/platform/passkit.md)。 然後以 JSON 檔案宣告此資訊。 如需有關佈建 Passfile (票卡檔) 的詳細資訊，請參閱 [PassKit 簡介](~/ios/platform/passkit.md)指南。

*   撰寫「隨附應用程式」來散發票卡。 這些應用程式也具備建立、編輯及更新票卡的功能，然後還能將這些票卡新增至「電子錢包」應用程式。 這類應用程式的其中一個良好範例就是電影院應用程式 – 在使用者透過應用程式購買票證之後，便可直接從應用程式將該票證新增至「電子錢包」。 若要使用隨附應用程式，您的佈建設定檔必須包含具有「電子錢包」功能的 App ID (應用程式識別碼)，這可以透過依照下列步驟來進行設定。 您的應用程式比須也包含必要的權利。

*   「導管應用程式」(Conduit Apps) 係指不會直接操控票卡的應用程式。 除了接收票卡並提供選項給使用者以將票卡新增至「電子錢包」之外，它們與票卡只有最低限度的互動。 這些應用程式不需要任何特殊的佈建或權利，但會使用一些來自「PassKit 架構」的方法。

## <a name="developer-center"></a>開發人員中心

若要建立與「電子錢包」搭配使用的新佈建設定檔，請執行下列動作：

1.  瀏覽至 Apple Developer Portal (Apple 開發人員入口網站) 的 [Certificates, Identifiers, and Profiles](https://developer.apple.com/account/ios/certificate/) \(憑證、識別碼及設定檔\) 區段。
2.  在 [Identifiers] \(識別碼\) 底下，瀏覽至 [App IDs] \(應用程式識別碼\)： 
    
    ![選取 App ID (應用程式識別碼)](wallet-capabilities-images/image17.png)

3.  按一下頁面右上角的 [+] 圖示。
4.  為新 App ID (應用程式識別碼) 提供 [Name] \(名稱\) 和 [Bundle ID] \(套件組合識別碼\) 來註冊它。 (請注意，這個套件組合識別碼必須與您專案中的套件組合識別碼相符)：
   
    ![新增 App ID (應用程式識別碼) 詳細資料](wallet-capabilities-images/image18.png)

5.  從服務清單中選取 [Wallet] \(電子錢包\) 應用程式服務：
    
    ![選取服務畫面](wallet-capabilities-images/image19.png)

6.  按 [Continue] \(繼續\)，然後按 [Register] \(註冊\) 來建立 App ID (應用程式識別碼)。

如有需要，可以編輯現有的 App ID (應用程式識別碼) 來新增「電子錢包」功能。

此 App ID (應用程式識別碼) 現在已可用來產生 (或重新產生) 新的佈建設定檔，如[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南所述：

![使用新建立的 App ID (應用程式識別碼) 來建立佈建設定檔](wallet-capabilities-images/image20.png)


如需有關使用「電子錢包」的詳細資訊，請參閱下列指南：

*   [PassKit 簡介](~/ios/platform/passkit.md)
 
## <a name="next-steps"></a>後續步驟
 
以下清單說明可能需要進行的額外步驟：

* 在您的應用程式中使用架構命名空間。
* 為您的應用程式新增必要的權利。 如需有關所需權利及如何新增這些權利的詳細資訊，請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。
* 在應用程式的 [iOS 套件組合簽署] 中，確定 [自訂權利] 已設定為 **Entitlements.plist**。 這「不是」「偵錯」和「iOS 模擬器」組建的預設設定。

如果您在應用程式服務方面遇到問題，請參閱主要指南的[疑難排解](~/ios/deploy-test/provisioning/capabilities/index.md)一節。