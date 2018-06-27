---
title: Xamarin.iOS 的 iCloud 功能
description: 為應用程式新增功能通常需要額外的佈建設定。 本指南說明 iCloud 功能所需的設定。
ms.prod: xamarin
ms.assetid: 3CBAC982-D8DE-48DD-97CD-32B551D9DB85
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: a1d2197a4cc7c7bf8fc9b69463a4679389e45ee8
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785137"
---
# <a name="icloud-capabilities-in-xamarinios"></a>Xamarin.iOS 的 iCloud 功能

_為應用程式新增功能通常需要額外的佈建設定。本指南說明 iCloud 功能所需的設定。_

iCloud 提供一個簡便的方式，可供 iOS 使用者儲存其內容及在裝置之間共用該內容。 有四種方法可供開發人員使用 iCloud 來為其使用者提供儲存方式：「機碼/值」儲存體、UIDocument 儲存體、CoreData，以及直接使用 CloudKit 來為個人檔案和目錄提供儲存體。 如需有關這些方法的詳細資訊，請參閱 [iCloud 簡介](~/ios/data-cloud/introduction-to-icloud.md)指南。

由於「容器」因素，因此為應用程式新增 iCloud 功能比新增其他「應用程式服務」略為困難。 在 iCloud 中會使用容器來儲存應用程式的資訊，並可隔離單一 iCloud 帳戶中包含的所有資訊 – 就像使用者 iOS 裝置上的沙箱一樣。 如需有關容器的詳細資訊，請參閱 [CloudKit 簡介](~/ios/data-cloud/intro-to-cloudkit.md)指南。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)協助開發人員適當地處理歐盟一般資料保護規定 (GDPR)。

<a name="icloud-developer-center" />

## <a name="developer-center"></a>開發人員中心

透過開發人員中心佈建新的應用程式時，需要進行兩個步驟：

1.  建立容器。
2.  建立具有 iCloud 功能的 App ID (應用程式識別碼)，然後在其中新增容器。
3. 建立包含此 App ID (應用程式識別碼) 的佈建設定檔

下列步驟將引導您完成這些步驟：

1.  瀏覽至 [Apple Developer Center](https://developer.apple.com/account/) \(Apple 開發人員中心\)，然後移至 [Certificates, Identifier, and Profiles] \(憑證、識別碼及設定檔\) 區段： 
    
     ![Apple Developer Center (Apple 開發人員中心) 主要頁面](icloud-capabilities-images/image22.png)

2.  在 [Identifiers] \(識別碼\) 底下，選取 [iCloud Containers] \(iCloud 容器\)，然後選取 [+] 來建立新的容器：  
    
    ![iCloud Container (iCloud 容器) 畫面](icloud-capabilities-images/image23.png)

3.  輸入 iCloud 容器的 [Description] \(描述\) 和唯一 [Identifier] \(識別碼\)： 
    
    ![iCloud 容器註冊畫面](icloud-capabilities-images/image24.png)

4.  按 [Continue] \(繼續\)，確定資訊正確，然後按 [註冊] 以建立 iCloud Container (iCloud 容器)：  
    
    ![iCloud 容器註冊畫面](icloud-capabilities-images/image25.png)

若要建立新的 App ID (應用程式識別碼) 並為其新增容器，請執行下列動作：

1.  在 [Developer Center](https://developer.apple.com/account/) \(開發人員中心\) 中，按一下 [Identifiers] \(識別碼\) 底下的 [App IDs] \(應用程式識別碼\)： 
    
    ![在 Developer Center (開發人員中心) 中選取識別碼](icloud-capabilities-images/image26.png)

2.  選取 [+] 按鈕來新增 App ID (應用程式識別碼)： 
    
    ![[Add new App ID] \(新增應用程式識別碼\) 按鈕](icloud-capabilities-images/image27.png)

3.  輸入 App ID (應用程式識別碼) 的 [Name] \(名稱\)，並為它提供 [Explicit App ID] \(明確的應用程式識別碼\)：
    
    ![輸入新 App ID (應用程式識別碼) 詳細資料](icloud-capabilities-images/image28.png)

4.  在 [App Services] \(應用程式服務\) 底下，選取 [iCloud]，然後選擇 [Include CloudKit support] \(包含 CloudKit 支援\)：
    
    ![選取 iCloud 應用程式服務](icloud-capabilities-images/image29.png)

5.  選取 [Continue] \(繼續\)，然後選取 [Register] \(註冊\)。 請注意，在確認畫面上，iCloud 的 [Configurable] \(可設定\) 會顯示成已選取 (帶有黃色符號)：   
    
    ![確認畫面](icloud-capabilities-images/image30.png)

6.  返回 App ID (應用程式識別碼) 清單，然後選取您剛才建立的應用程式識別碼： 
    
    ![選取 App ID (應用程式識別碼) 畫面](icloud-capabilities-images/image31.png)

7.  向下捲動到這個已展開區段的底部，然後按一下 [Edit] \(編輯\)：
    
    ![編輯 App ID (應用程式識別碼)](icloud-capabilities-images/image32.png)

8.  將清單向下捲動到 iCloud，然後按一下 [Edit] \(編輯\) 按鈕：  
    
    ![編輯 iCloud App ID (應用程式識別碼)](icloud-capabilities-images/image33.png)

9.  選取要與此 App ID (應用程式識別碼) 搭配使用的 Container (容器)：  
    
    ![選取容器畫面](icloud-capabilities-images/image34.png)

10. 確認 Container (容器) 指派項目，然後按 [Assign] \(指派\)。
 
此 App ID (應用程式識別碼) 現在已可用來產生 (或重新產生) 新的佈建設定檔，如[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南所述。 

如需有關使用 iCloud 的詳細資訊，請參閱下列指南：

*   [iCloud 簡介](~/ios/data-cloud/introduction-to-icloud.md)
*   [CloudKit 簡介](~/ios/data-cloud/intro-to-cloudkit.md)
*   [文件選擇器簡介](~/ios/platform/document-picker.md)

## <a name="next-steps"></a>後續步驟
 
以下清單說明可能需要進行的額外步驟：

* 在您的應用程式中使用架構命名空間。
* 為您的應用程式新增必要的權利。 如需有關所需權利及如何新增這些權利的詳細資訊，請參閱[使用權利](~/ios/deploy-test/provisioning/entitlements.md)指南。
* 在應用程式的 [iOS 套件組合簽署] 中，確定 [自訂權利] 已設定為 **Entitlements.plist**。 這「不是」「偵錯」和「iOS 模擬器」組建的預設設定。

如果您在應用程式服務方面遇到問題，請參閱主要指南的[疑難排解](~/ios/deploy-test/provisioning/capabilities/index.md)一節。