---
title: Xamarin 中的自訂檔圖示
description: 本文涵蓋的內容包括和管理要做為自訂檔案類型圖示的 Xamarin iOS 應用程式中的影像資產。
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/23/2017
ms.openlocfilehash: 3a74084db461271ca7fd440ab2c9779f949b30ff
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436843"
---
# <a name="custom-document-icons-in-xamarinios"></a>Xamarin 中的自訂檔圖示

_本文涵蓋的內容包括和管理要做為自訂檔案類型圖示的 Xamarin iOS 應用程式中的影像資產。_

如果 Xamarin 應用程式支援載入特定檔案類型，則開發人員可以提供系統在遇到該檔案類型時將使用的圖示，例如，當使用者在 *郵件應用程式* 中保存附件時，如以下所示：

 [![檔案類型圖示的範例](custom-document-types-images/17.png)](custom-document-types-images/17.png#lightbox)

開發人員可以藉由在 `CFBundleTypeName` 應用程式中包含字串和陣列的字典專案，來新增檔案格式的檔案類型資訊 `LSItemContentTypes` `Info.plist` 。 檔案類型的圖示會放入陣列中 `CFBundleTypeIconFiles` 。 如果未提供檔圖示，iOS 會從應用程式圖示衍生一個。
您可以針對各種裝置解析度，提供數種大小的圖示。 

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

若要在 Visual Studio for Mac 中指派這些值，請使用編輯器上 [ **Advanced** ] 索引標籤下的 [**檔案類型**] 區段， `Info.plist` 以新增檔案類型並將影像圖示指派給它。 例如，以下是顯示 PDF 支援註冊的螢幕擷取畫面：

 [![[Plist] 編輯器上 [Advanced] 索引標籤底下的 [檔案類型] 區段](custom-document-types-images/18.png)](custom-document-types-images/18.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

若要在 Visual Studio 中指派這些值，請使用 [ **Advanced** ] 索引標籤底下的 [**檔案類型**] 區段 `Info.plist` ：

 ![開啟 [Advanced] 索引標籤底下的 [檔案類型] 區段](custom-document-types-images/doc01w.png)

按一下 [ **新增檔案類型** ] 按鈕，並填寫必要的欄位：

![新增檔案類型表單](custom-document-types-images/doc02w.png)

-----

如需檔案類型的詳細資訊，請參閱 Apple 的 [統一類型識別碼參考](https://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) 和 [適用于 IOS 的檔互動程式設計主題](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html)。

## <a name="related-links"></a>相關連結

- [使用影像 (範例) ](/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和映射建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)