---
title: 在 Xamarin.iOS 中的自訂文件圖示
description: 本文涵蓋包括及管理在 Xamarin.iOS 應用程式中要做為自訂文件類型的圖示的影像資產。
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/23/2017
ms.openlocfilehash: 51abd00f9a21b702811bb3897f273deff54f7d01
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110481"
---
# <a name="custom-document-icons-in-xamarinios"></a>在 Xamarin.iOS 中的自訂文件圖示

_本文涵蓋包括及管理在 Xamarin.iOS 應用程式中要做為自訂文件類型的圖示的影像資產。_

Xamarin.iOS 應用程式支援載入特定的文件類型，如果開發人員可以提供遇到該文件類型，例如當使用者按住中的附加檔案時，系統會使用的圖示*郵件應用程式*為如下所示：

 [![](custom-document-types-images/17.png "舉例來說，文件類型圖示")](custom-document-types-images/17.png#lightbox)

開發人員可以新增文件類型資訊的檔案格式的應用程式是開啟能夠藉由包括字典的項目`CFBundleTypeName`字串和`LSItemContentTypes`的應用程式中的陣列`Info.plist`。 文件類型的圖示進入`CFBundleTypeIconFiles`陣列。 如果未提供文件圖示，iOS 會衍生從應用程式圖示的其中一個。
可以提供數種大小，適用於各種不同的裝置解析度的圖示。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要指派這些值，在 Visual Studio for Mac，請使用**文件類型**區段底下**進階**索引標籤上`Info.plist`編輯器新增文件類型，並將映像圖示指派給它。 例如，以下是螢幕擷取畫面顯示註冊 PDF 支援：

 [![](custom-document-types-images/18.png "'Info.plist' 編輯器的 [進階] 索引標籤底下的 [文件類型] 區段")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要將指派 Visual Studio 中的這些值，請使用**文件類型**區段底下**進階**索引標籤上`Info.plist`:

 ![](custom-document-types-images/doc01w.png "開啟 [進階] 索引標籤底下的 [文件類型] 區段")

按一下 **加入文件類型**按鈕，然後填寫必要欄位：

![](custom-document-types-images/doc02w.png "新增文件類型的表單")

-----


如需文件類型的詳細資訊，請參閱 Apple[統一的型別識別項參考](http://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html)並[文件互動程式設計主題適用於 iOS](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html)。


## <a name="related-links"></a>相關連結

- [使用映像 （範例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和影像建立指導方針](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
