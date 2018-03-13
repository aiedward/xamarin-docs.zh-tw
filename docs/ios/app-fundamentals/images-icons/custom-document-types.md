---
title: "自訂文件圖示"
description: "本文涵蓋包括和管理 Xamarin.iOS 應用程式中要做為自訂文件類型圖示的影像資產。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/23/2017
ms.openlocfilehash: e14cfb8d3c09d17bdee4b60786f434ff94ef31dc
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="custom-document-icons"></a>自訂文件圖示

_本文涵蓋包括和管理 Xamarin.iOS 應用程式中要做為自訂文件類型圖示的影像資產。_

如果 Xamarin.iOS 應用程式支援載入特定的文件類型，開發人員可以提供遇到該文件類型，例如當使用者按住中的附加檔案時，系統會使用的圖示*郵件應用程式*為如下所示：

 [![](custom-document-types-images/17.png "舉例來說，文件類型圖示")](custom-document-types-images/17.png#lightbox)

開發人員可以加入文件類型資訊的應用程式的檔案格式是藉由包含字典的項目能夠開啟`CFBundleTypeName`字串和`LSItemContentTypes`的應用程式中的陣列`Info.plist`。 文件類型的圖示將會進入`CFBundleTypeIconFiles`陣列。 如果未提供文件圖示，iOS 會衍生一項來自應用程式圖示。
圖示可以提供數個的大小，針對各種不同的裝置解析度最佳化。 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要指派適用於 Mac 的 Visual Studio 中的這些值，請使用**文件類型**區段**進階**索引標籤上`Info.plist`編輯器加入文件類型，並將影像圖示指派給它。 例如，以下是螢幕擷取畫面顯示 PDF 支援的註冊：

 [![](custom-document-types-images/18.png "'Info.plist' 編輯器上的 [進階] 索引標籤下的 [文件類型] 區段")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要指定這些值在 Visual Studio 中的，使用**文件類型**區段**進階**索引標籤上`Info.plist`:

 ![](custom-document-types-images/doc01w.png "開啟文件類型區段的 [進階] 索引標籤")

按一下**加入文件類型**按鈕，並填寫必要的欄位：

![](custom-document-types-images/doc02w.png "新增文件類型表單")

-----


如需文件類型的詳細資訊，請參閱 Apple[統一的型別識別項參考](http://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html)和[文件互動程式設計主題適用於 iOS](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html)。


## <a name="related-links"></a>相關連結

- [使用映像 （範例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和映像建立指導方針](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
