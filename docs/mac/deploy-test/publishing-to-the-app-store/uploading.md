---
title: 上傳至 Mac App Store
description: 本文件描述如何使用 iTunes Connect 將 Xamarin.Mac 應用程式上傳至 Mac App Store。 並討論 iTunes Connect 完成程序所需的資訊。
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: fede666071aad8702eda17a94a06c7de49c7397e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029480"
---
# <a name="upload-to-mac-app-store"></a>上傳至 Mac App Store

_本指南將逐步解說如何上傳 Xamarin.Mac 應用程式以發行至 Mac App Store。_

透過 [iTunes Connect](https://itunesconnect.apple.com/) 將應用程式提交給 Mac App Store 核准。

1. 選擇要建立的 **macOS 應用程式**： 

    [![](uploading-images/image65.png "iTunes Connect")](uploading-images/image65.png#lightbox)

2. 輸入應用程式的名稱與其他詳細資料。 開發人員只能從先前建立的現有 [Bundle ID] \(套件組合識別碼\) 中選擇： 

    [![](uploading-images/image66.png "Selecting the bundle ID")](uploading-images/image66.png#lightbox)

3. 選取上市日期與價格。 不論開發人員選取的上市日期為何，應用程式只有在通過核准後才可供銷售。 如果開發人員想要更充分掌控實際的上市日期，可將此值設定為很久以後的將來： 

    [![](uploading-images/image67.png "Setting the available date and price")](uploading-images/image67.png#lightbox)

4. 輸入應用程式的資訊，包括其所屬的 App Store 類別： 

    [![](uploading-images/image68.png "Entering the app information")](uploading-images/image68.png#lightbox) 

    選取適用的分級： 

    [![](uploading-images/image69.png "Setting the app ratings")](uploading-images/image69.png#lightbox) 

    描述、關鍵字及連絡 URL： 

    [![](uploading-images/image70.png "Editing the Description, keywords and contact URLs")](uploading-images/image70.png#lightbox) 

    提供給 App Store 檢閱者的連絡資訊和建議： 

    [![](uploading-images/image71.png "Editing the contact information and advice for the App Store reviewers")](uploading-images/image71.png#lightbox) 

    最後，螢幕擷取畫面： 

    [![](uploading-images/image72.png "Adding the required screenshots")](uploading-images/image72.png#lightbox) 

    螢幕擷取畫面的格式應為 JPG、TIF 或 PNG，大小為 1280x800、1440x900、2880x1800 或 2560x1600 像素。 按 [Save] \(儲存\) 以完成。

5. 應用程式資訊隨即顯示以供檢閱。 按一下 [View Details] \(檢視詳細資料\) 來變更狀態： 

    [![](uploading-images/image73.png "Viewing the app details")](uploading-images/image73.png#lightbox)

6. 在詳細資料檢視中，按一下 [Ready to Upload Binary] \(上傳二進位檔準備就緒\)，以提交應用程式套件檔案： 

    [![](uploading-images/image74.png "Selecting Ready to Upload Binary")](uploading-images/image74.png#lightbox)

7. 回答加密問題： 

    [![](uploading-images/image75.png "Answering the cryptography question")](uploading-images/image75.png#lightbox)

8. 網站準備就緒可接受應用程式套件檔案時會通知您： 

    [![](uploading-images/image76.png "The acceptance notification")](uploading-images/image76.png#lightbox)

9. 啟動 Application Loader，請務必使用 Apple ID 來登入。
選擇 [Deliver Your App] \(傳遞應用程式\) 以繼續進行： 

    [![](uploading-images/image77.png "The Application Loader interface")](uploading-images/image77.png#lightbox)

10. 從處於 [Ready to Upload Binary] \(上傳二進位檔準備就緒\) 狀態的應用程式清單中選取，然後按一下 [Next] \(下一步\)： 

    [![](uploading-images/image78.png "Selecting the app to load")](uploading-images/image78.png#lightbox)

11. 檢閱應用程式中繼資料，然後按一下 [Choose...] \(選擇...\) 以尋找套件檔案： 

    [![](uploading-images/image79.png "Reviewing the app metadata")](uploading-images/image79.png#lightbox)

12. 尋找在 Visual Studio for Mac 中使用 App Store 建置組態所建置的套件檔案： 

    [![](uploading-images/image80.png "Selecting the file to upload")](uploading-images/image80.png#lightbox)

13. 按 [Send] \(傳送\)： 

    [![](uploading-images/image81.png "Sending the app")](uploading-images/image81.png#lightbox)

14. 此套件會經過驗證並回報任何錯誤。 請修正這些錯誤，然後重新上傳。 上傳成功完成後，會自動提交應用程式以供 App Store 團隊檢閱： 

    [![](uploading-images/image82.png "An example of upload errors")](uploading-images/image82.png#lightbox)

應用程式通過核准後，即可從 Mac App Store 下載或購買。

## <a name="related-links"></a>相關連結

- [安裝](~//mac/get-started/installation.md)
- [Hello, Mac 範例](~//mac/get-started/hello-mac.md)
- [在 Mac App Store 上散發應用程式](https://developer.apple.com/devcenter/mac/checklist/) \(英文\)
- [工具指南：對應用程式進行程式碼簽署](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html) \(英文\)
- [開發人員識別碼和閘道管理員](https://developer.apple.com/resources/developer-id/) \(英文\)
