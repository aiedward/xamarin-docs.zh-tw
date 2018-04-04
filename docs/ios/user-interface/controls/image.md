---
title: 顯示影像
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 406cfe813cbb58111769203f3b6c3fb0c2edad3c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="displaying-images"></a>顯示影像

將影像加入至您的應用程式需要兩個步驟： 首先，將影像加入至您的專案。然後，加入控制項和它們在螢幕上顯示的程式碼。 請參閱[處理映像](~/ios/app-fundamentals/images-icons/index.md)文件如需詳細的映像處理 Xamarin.iOS 涵蓋範圍。

## <a name="adding-images-to-your-app"></a>將影像加入至您的應用程式

映像可以新增至您的 Visual Studio for Mac 方案的任何資料夾，而且當**建置動作**設**內容**檔案將會包含在您的應用程式，並且可以顯示。

Visual Studio for Mac 也支援特殊的目錄稱為也可以包含映像檔案的資源。 [資源] 資料夾中的檔案應該具有**建置動作**設**BundleResource**。

這個螢幕擷取畫面顯示**建置動作**檔案時，會出現的選項是右鍵：

 [![](image-images/image30a.png "建置動作 功能表")](image-images/image30a.png#lightbox)

Visual Studio for Mac 通常會選擇正確**建置動作**自動，但是您應該留意這些設定，特別是當您在專案中移動檔案。

### <a name="adding-an-image-file"></a>加入影像檔

若要將影像檔加入至您的專案，第一次滑鼠右鍵按一下專案，然後選擇**加入檔案...**

 [![](image-images/image31a.png "新增檔案 功能表")](image-images/image31a.png#lightbox)

選取映像 （或影像） 您要包含在標準檔案對話方塊中。 預設的建置動作將會是映像**BundleResource** – 不要覆寫此值，除非您有特定原因。

 [![](image-images/image32a.png "加入檔案對話方塊")](image-images/image32a.png#lightbox)

映像將電腦新增至您的專案，且可載入並顯示在程式碼中。 這個螢幕擷取畫面顯示新增至 iOS 應用程式專案的影像：

 [![](image-images/image33a.png "在專案中的映像")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>什麼是資源目錄？

[資源] 目錄中的檔案將以不同方式處理從一般檔案 – [資源] 資料夾的內容會複製到應用程式根目錄，而且可以從該處，在您的程式碼中參考。 這會很有用的原因很多：

-  儲存應用程式的內容，例如預設啟動映像和應用程式圖示中設定的映像。
-  儲存其他影像和程式碼分開的檔案，因此它們更容易管理 （資源目錄的內容會複製時，會保留子目錄）。


資源目錄是在程式庫專案中，特別有用，因為程式碼就可以假設這些映像將會複製到使用的應用程式，讓它更容易撰寫需要影像、 音效、 視訊、 XML 或其他的共用程式碼程式庫的根檔案。



資源目錄必須如此命名，而所有的檔案應該有建置動作設定為**BundleResource**

## <a name="displaying-the-image"></a>顯示影像

若要顯示映像使用設計工具，影像檢視應該做為容器，並可以顯示的單一映像或映像的動畫。 **影像檢視**從 [工具箱] 的圖示如下所示：

 [![](image-images/image35a.png "ImageView 工具箱中")](image-images/image35.png#lightbox)

拖曳**影像檢視**從**Toobox**到檢視的控制器。 然後在 * * 影像檢視 > 映像 * * 下拉式清單將提供您的專案中的所有可用的映像檔案的清單。 選取要在其中新增至您的映像檢視任何。

 [![](image-images/image36a.png "ImageView 工具箱中")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>以程式設計方式顯示的影像

因為 blocks.jpg 位於 [資源] 目錄的根目錄中，無法在執行階段應用程式套件組合的根目錄中。 若要顯示此映像中 ImageView 控制項會使用下列程式碼：

```csharp
imageview1.Image = UIImage.FromBundle ("SF Monkey.png");
```

如果我們必須放在映像`/Resources/Pics/blocks.jpg`則在程式碼會在路徑中包含 Pics 資料夾：

```csharp
imageview1.Image = UIImage.FromBundle ("Pics/SF Monkey.png");
```

資源檔參考永遠不需要加入`Resources`資料夾。


## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/Controls/)
