---
title: Xamarin 中的自訂檔圖示
description: 本文涵蓋的內容包括和管理 Xamarin iOS 應用程式中的影像資產，以做為自訂檔案類型圖示。
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/23/2017
ms.openlocfilehash: ac8ee96d6183f9a62233d217c75b03da15605bd2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004227"
---
# <a name="custom-document-icons-in-xamarinios"></a>Xamarin 中的自訂檔圖示

_本文涵蓋的內容包括和管理 Xamarin iOS 應用程式中的影像資產，以做為自訂檔案類型圖示。_

如果 Xamarin iOS 應用程式支援載入特定檔案類型，開發人員可以提供在遇到該檔案類型時，系統將使用的圖示，例如，當使用者在*郵件應用程式*中保存附件時，如下所示：

 [![](custom-document-types-images/17.png "An example of document type icons")](custom-document-types-images/17.png#lightbox)

開發人員可以藉由在應用程式的 `Info.plist`中包含 `CFBundleTypeName` 字串和 `LSItemContentTypes` 陣列的字典專案，來新增應用程式能夠開啟之檔案格式的檔案類型資訊。 檔案類型的圖示會放在 `CFBundleTypeIconFiles` 陣列中。 如果未提供檔圖示，iOS 會從應用程式圖示衍生一個。
圖示可提供數種大小，針對各種裝置解析度進行優化。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要在 Visual Studio for Mac 中指派這些值，請使用 [`Info.plist` 編輯器] 上 [ **Advanced** ] 索引標籤下的 [**檔案類型**] 區段，以新增檔案類型，並為其指派影像圖示。 例如，以下是顯示 PDF 支援註冊的螢幕擷取畫面：

 [![](custom-document-types-images/18.png "The Document Types section under the Advanced tab on the `Info.plist` editor")](custom-document-types-images/18.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中指派這些值，請使用 `Info.plist`上 [ **Advanced** ] 索引標籤下的 [**檔案類型**] 區段：

 ![](custom-document-types-images/doc01w.png "Open the Document Types section under the Advanced tab")

按一下 [**新增檔案類型**] 按鈕，並填寫必要的欄位：

![](custom-document-types-images/doc02w.png "The Add Document Type form")

-----

如需檔案類型的詳細資訊，請參閱 Apple 的[統一類型識別碼參考](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html)和[IOS 的檔互動程式設計主題](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html)。

## <a name="related-links"></a>相關連結

- [使用影像（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和映射建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
