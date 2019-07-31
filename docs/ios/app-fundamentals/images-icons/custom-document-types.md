---
title: Xamarin 中的自訂檔圖示
description: 本文涵蓋的內容包括和管理 Xamarin iOS 應用程式中的影像資產, 以做為自訂檔案類型圖示。
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/23/2017
ms.openlocfilehash: 1125b0c420d6dbd23c5539a2183d531bc0d6089f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642998"
---
# <a name="custom-document-icons-in-xamarinios"></a>Xamarin 中的自訂檔圖示

_本文涵蓋的內容包括和管理 Xamarin iOS 應用程式中的影像資產, 以做為自訂檔案類型圖示。_

如果 Xamarin iOS 應用程式支援載入特定檔案類型, 開發人員可以提供在遇到該檔案類型時, 系統將使用的圖示, 例如, 當使用者在*郵件應用程式*中保存附件時, 如下所示:

 [![](custom-document-types-images/17.png "檔案類型圖示的範例")](custom-document-types-images/17.png#lightbox)

開發人員可以藉由在應用程式的`CFBundleTypeName` `Info.plist`中包含字串和`LSItemContentTypes`陣列的字典專案, 為應用程式開啟的檔案格式加入檔案類型資訊。 檔案類型的圖示會移入`CFBundleTypeIconFiles`陣列中。 如果未提供檔圖示, iOS 會從應用程式圖示衍生一個。
圖示可提供數種大小, 針對各種裝置解析度進行優化。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要在 Visual Studio for Mac 中指派這些值, 請使用`Info.plist`編輯器上 [ **Advanced** ] 索引標籤底下的 [**檔案類型**] 區段, 以加入檔案類型, 並將影像圖示指派給它。 例如, 以下是顯示 PDF 支援註冊的螢幕擷取畫面:

 [![](custom-document-types-images/18.png "[Plist] 編輯器上 [Advanced] 索引標籤下的 [檔案類型] 區段")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中指派這些值, 請使用上 [ **Advanced** `Info.plist`] 索引標籤底下的 [**檔案類型**] 區段:

 ![](custom-document-types-images/doc01w.png "開啟 [Advanced] 索引標籤底下的 [檔案類型] 區段")

按一下 [**新增檔案類型**] 按鈕, 並填寫必要的欄位:

![](custom-document-types-images/doc02w.png "[加入檔案類型] 表單")

-----


如需檔案類型的詳細資訊, 請參閱 Apple 的[統一類型識別碼參考](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html)和[IOS 的檔互動程式設計主題](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html)。


## <a name="related-links"></a>相關連結

- [使用影像 (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和映射建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
