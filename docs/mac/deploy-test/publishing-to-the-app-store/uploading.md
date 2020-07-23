---
title: 上傳至 Mac App Store
description: 本文件描述如何使用 iTunes Connect 將 Xamarin.Mac 應用程式上傳至 Mac App Store。 並討論 iTunes Connect 完成程序所需的資訊。
ms.prod: xamarin
ms.assetid: 30cd0e47-1b2e-47ef-93f6-4bed20b15c03
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 57591a6e9fcaa3c8271ab27756160ee1c46a4fa3
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86935924"
---
# <a name="upload-to-mac-app-store"></a>上傳至 Mac App Store

_本指南將逐步解說如何上傳 Xamarin.Mac 應用程式以發行至 Mac App Store。_

透過 [iTunes Connect](https://itunesconnect.apple.com/) 將應用程式提交給 Mac App Store 核准。 您也會需要來自 App Store 的 [**Transporter**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) 工具。

1. 選擇要建立的 **macOS 應用程式**：

    [![iTunes Connect](uploading-images/image65.png)](uploading-images/image65.png#lightbox)

2. 輸入應用程式的名稱與其他詳細資料。 開發人員只能從先前建立的現有 [Bundle ID] \(套件組合識別碼\)**** 中選擇：

    [![選取組合識別碼](uploading-images/image66.png)](uploading-images/image66.png#lightbox)

3. 選取上市日期與價格。 不論開發人員選取的上市日期為何，應用程式只有在通過核准後才可供銷售。 如果開發人員想要更充分掌控實際的上市日期，可將此值設定為很久以後的將來：

    [![設定上市日期與價格](uploading-images/image67.png)](uploading-images/image67.png#lightbox)

4. 輸入應用程式的資訊，包括其所屬的 App Store 類別：

    [![輸入應用程式資訊](uploading-images/image68.png)](uploading-images/image68.png#lightbox)

    選取適用的分級：

    [![設定應用程式評等](uploading-images/image69.png)](uploading-images/image69.png#lightbox)

    描述、關鍵字及連絡 URL：

    [![編輯描述、關鍵字及連絡 URL](uploading-images/image70.png)](uploading-images/image70.png#lightbox)

    提供給 App Store 檢閱者的連絡資訊和建議：

    [![編輯供 App Store 檢閱者參考的連絡資訊和建議](uploading-images/image71.png)](uploading-images/image71.png#lightbox)

    最後，螢幕擷取畫面：

    [![新增必要的螢幕擷取畫面](uploading-images/image72.png)](uploading-images/image72.png#lightbox)

    螢幕擷取畫面的格式應為 JPG、TIF 或 PNG，大小為 1280x800、1440x900、2880x1800 或 2560x1600 像素。 按 [Save] \(儲存\)**** 以完成。

5. 應用程式資訊隨即顯示以供檢閱。 按一下 [View Details] \(檢視詳細資料\)**** 來變更狀態：

    [![檢視應用程式詳細資料](uploading-images/image73.png)](uploading-images/image73.png#lightbox)

6. 在詳細資料檢視中，按一下 [Ready to Upload Binary] \(上傳二進位檔準備就緒\)，以提交應用程式套件檔案：

    [![選取準備好上傳的二進位檔](uploading-images/image74.png)](uploading-images/image74.png#lightbox)

7. 回答加密問題：

    [![回答加密問題](uploading-images/image75.png)](uploading-images/image75.png#lightbox)

8. 網站準備就緒可接受應用程式套件檔案時會通知您：

    [![接受通知](uploading-images/image76.png)](uploading-images/image76.png#lightbox)

9. 啟動 **Transporter** 並使用您的 Apple ID 登入，然後選擇 [新增應用程式]****：

    [![Application Loader 介面](uploading-images/transporter01-sml.png)](uploading-images/transporter01.png#lightbox)

    遵循指示將應用程式套件上傳至 iTunes Connect。

    > [!NOTE]
    > [**Transporter**](https://apps.apple.com/us/app/transporter/id1450874784?mt=12) 取代 Xcode 10 和較舊版本曾使用的 **Application Loader** 工具。
    > Xcode 11 或更新版本中已不再提供 Application Loader。

應用程式通過核准後，即可從 Mac App Store 下載或購買。

## <a name="related-links"></a>相關連結

- [安裝](~//mac/get-started/installation.md)
- [Hello，Mac 範例](~/mac/get-started/hello-mac.md)
- [在 Mac App Store 上散發應用程式](https://developer.apple.com/devcenter/mac/checklist/) \(英文\)
- [工具指南：對應用程式進行程式碼簽署](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html) \(英文\)
- [開發人員識別碼和 GateKeeper](https://developer.apple.com/developer-id/) \(英文\)
