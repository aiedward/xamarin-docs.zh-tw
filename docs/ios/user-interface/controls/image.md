---
title: 顯示利用 Xamarin.iOS 的影像
description: 本文件說明如何在 Xamarin.iOS 中顯示影像。 以程式設計方式或透過 「 iOS 設計工具，它就會涵蓋新增至應用程式的映像。
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: bb57dbdb87555fb0b3ab8941024e5a84e4723099
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112633"
---
# <a name="displaying-images-with-xamarinios"></a>顯示利用 Xamarin.iOS 的影像

將映像新增至您的應用程式需要兩個步驟： 首先，將影像加入至您的專案;然後，新增控制項以及它們在螢幕上顯示的程式碼。 請參閱[處理映像](~/ios/app-fundamentals/images-icons/index.md)一文，以更深入探討在 Xamarin.iOS 中處理的映像。

## <a name="adding-images-to-your-app"></a>將映像新增至您的應用程式

映像可以新增至您的 Visual Studio for Mac 解決方案中任何資料夾，才**建置動作**設為**內容**則檔案將會包含在您的應用程式，並可顯示。

Visual Studio for Mac 也支援特殊的目錄稱為**資源**，也可以包含映像檔案。 [資源] 資料夾中的檔案應該具有**建置動作**設為**BundleResource**。

此螢幕擷取畫面顯示**建置動作**出現的檔案時的選項是以滑鼠右鍵按一下：

 [![](image-images/image30a.png "建置動作 功能表")](image-images/image30a.png#lightbox)

Visual Studio for Mac 通常會選擇正確**建置動作**自動，但您應該留意這些設定，特別是當您在您的專案中移動檔案。

### <a name="adding-an-image-file"></a>新增影像檔

若要將影像檔加入至您的專案，先以滑鼠右鍵按一下專案，然後選擇**新增檔案...**

 [![](image-images/image31a.png "新增檔案 功能表")](image-images/image31a.png#lightbox)

選取的映像 （或映像） 您想要包含在標準檔案對話方塊中。 預設建置動作，都是映像**BundleResource** – 除非您有特定的理由不覆寫此值。

 [![](image-images/image32a.png "新增檔案 對話方塊")](image-images/image32a.png#lightbox)

映像會加入至您的專案，並可供載入並顯示在程式碼中。 此螢幕擷取畫面顯示映像新增至 iOS 應用程式專案：

 [![](image-images/image33a.png "在專案中的映像")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>什麼是資源目錄？

檔案位於**資源**從一般檔案 – 的內容會以不同方式處理目錄**資源**資料夾複製到應用程式的根目錄，而且可以從該處，在參考您的程式碼。 這適合用於有許多原因：

-  儲存應用程式的內容，例如預設啟動映像和應用程式圖示中設定的映像。
-  儲存其他映像和檔案與程式碼分開，因此它們更易於管理 （資源目錄內容會複製時，會保留子目錄）。


**資源**目錄是在程式庫專案中，特別有用，因為程式碼可以假設這些映像將會複製到取用應用程式，讓您更輕鬆地共用的寫入要求的程式碼程式庫的根目錄影像、 音效、 視訊、 XML 或其他檔案。

**資源**目錄必須如此命名，和所有檔案都應該都有建置動作設為**BundleResource**。

## <a name="displaying-the-image"></a>顯示映像

在 iOS 設計工具中，使用**映像檢視**顯示映像或映像的動畫的序列。 **映像檢視**從 [工具箱] 的圖示如下所示：

 [![](image-images/image35a.png "在 [工具箱] 中的 ImageView")](image-images/image35.png#lightbox)

拖曳**映像檢視**從**工具箱**到檢視控制器。 然後，在**映像檢視 > 映像**下拉式清單會提供您的專案中的所有可用映像檔案的清單。 選取任何要在其中將它新增至您的映像檢視。

 [![](image-images/image36a.png "在 [工具箱] 中的 ImageView")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>以程式設計的方式顯示的影像

因為**SF Monkey.jpg**位於根目錄中的**資源**它可在執行階段應用程式套件組合的根目錄中的目錄。 若要在影像檢視控制項中顯示此映像，請使用下列程式碼：

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

如果我們必須放在映像**SF/資源/Pics Monkey.jpg**，然後將程式碼會包含**Pics**資料夾路徑中：

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

資源檔參考永遠不需要包含**資源**資料夾。

## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/Controls/)
