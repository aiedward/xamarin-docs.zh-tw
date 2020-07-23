---
title: 使用 Xamarin 來顯示影像
description: 本檔說明如何在 Xamarin 中顯示影像。 其中涵蓋以程式設計方式或透過 iOS 設計工具將影像新增至應用程式。
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 32f145d791a40bd90e848f1124ce150cbd9c7575
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937750"
---
# <a name="displaying-images-with-xamarinios"></a>使用 Xamarin 來顯示影像

將影像新增至您的應用程式需要兩個步驟：首先，將影像新增至您的專案;然後，新增控制項和程式碼，以將它們顯示在螢幕上。 請參閱[使用影像](~/ios/app-fundamentals/images-icons/index.md)一文，以取得在 Xamarin 中更詳細的影像處理涵蓋範圍。

## <a name="adding-images-to-your-app"></a>將影像新增至您的應用程式

影像可以加入至 Visual Studio for Mac 解決方案中的任何資料夾，如果**組建動作**設定為 [**內容**]，則該檔案將會包含在您的應用程式中，並可顯示。

Visual Studio for Mac 也支援稱為**資源**的特殊目錄，也可以包含影像檔案。 [資源] 資料夾中的檔案應該將 [**組建] 動作**設定為 [ **BundleResource**]。

這個螢幕擷取畫面顯示以滑鼠右鍵按一下檔案時所顯示的**組建動作**選項：

 [![組建動作功能表](image-images/image30a.png)](image-images/image30a.png#lightbox)

Visual Studio for Mac 通常會自動選擇正確的**組建動作**，但您應該留意這些設定，特別是當您在專案中移動檔案時。

### <a name="adding-an-image-file"></a>新增影像檔案

若要將影像檔案新增至專案，請先以滑鼠右鍵按一下專案，然後選擇 [**加入**檔案]。

 [![新增檔案 .。。下拉式功能表](image-images/image31a.png)](image-images/image31a.png#lightbox)

選取您想要包含在 [標準檔案] 對話方塊中的影像（或影像）。 影像的預設組建動作將會是**BundleResource** –除非您有特定原因，否則請勿覆寫此值。

 [![[新增檔案] 對話方塊](image-images/image32a.png)](image-images/image32a.png#lightbox)

映射將會新增至您的專案，並可在程式碼中載入和顯示。 這個螢幕擷取畫面顯示新增至 iOS 應用程式專案的映射：

 [![專案中的影像](image-images/image33a.png)](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>什麼是資原始目錄？

放在**Resources**目錄中的檔案會以不同于一般檔案的方式處理- **resources**資料夾的內容會複製到應用程式的根目錄，並可在您的程式碼中加以參考。 這可能很有用，原因如下：

- 儲存在應用程式屬性中設定的映射，例如預設啟動映射和應用程式圖示。
- 將其他映射和檔案與程式碼分開儲存，使其更容易管理（當複製資原始目錄內容時，會保留子目錄）。

**Resources**目錄在程式庫專案中特別有用，因為程式碼可以假設這些影像會複製到取用應用程式的根目錄，讓您更輕鬆地撰寫需要影像、音效、影片、XML 或其他檔案的共用程式碼程式庫。

**Resources**目錄必須命名為，而且所有檔案都應該將組建動作設定為**BundleResource**。

## <a name="displaying-the-image"></a>顯示影像

在 iOS 設計工具中，使用**影像視圖**來顯示影像或動畫系列的影像。 [工具箱] 中的**影像視圖**圖示如下所示：

 [![工具箱中的 ImageView](image-images/image35a.png)](image-images/image35.png#lightbox)

將 [**影像] 視圖**從 [**工具箱**] 拖曳至 View 控制器。 然後，在 [**影像視圖] > [影像**] 下，下拉式清單會提供專案中所有可用的影像檔案清單。 選取其中任何一項，將其新增至您的影像視圖。

 [![工具箱中的 ImageView](image-images/image36a.png)](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>以程式設計方式顯示影像

因為**SF Monkey.jpg**位於**Resources**目錄的根目錄中，所以它會在執行時間的應用程式套件組合根目錄中提供。 若要在影像視圖控制項中顯示此影像，請使用下列程式碼：

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

如果我們已將影像放在 **/Resources/Pics/SF Monkey.jpg**，則程式碼會在路徑中包含 [**圖片**] 資料夾：

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

資源檔參考永遠不需要包含**Resources**資料夾。

## <a name="related-links"></a>相關連結

- [控制項（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
