---
title: 使用 Xamarin 顯示影像
description: 本檔說明如何在 Xamarin 中顯示影像。 它涵蓋以程式設計方式或透過 iOS 設計工具將影像新增至應用程式。
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 900a4bf3407b4a3d37f6102ff4b10292edf4bd25
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434046"
---
# <a name="displaying-images-with-xamarinios"></a>使用 Xamarin 顯示影像

將影像新增至您的應用程式需要兩個步驟：首先，將影像新增至您的專案;然後，新增控制項和程式碼，以將它們顯示在畫面上。 請參閱「使用 [影像](~/ios/app-fundamentals/images-icons/index.md) 」一文，以取得更詳細的在 Xamarin 中處理影像的涵蓋範圍。

## <a name="adding-images-to-your-app"></a>將影像新增至您的應用程式

您可以將影像新增至 Visual Studio for Mac 解決方案中的任何資料夾，如果 **組建動作** 設定為 [ **內容** ]，則該檔案將會包含在您的應用程式中，而且可以顯示。

Visual Studio for Mac 也支援稱為 **資源** 的特殊目錄，也可以包含影像檔案。 [資源] 資料夾中的檔案應該將 [ **組建] 動作** 設為 [ **套件套件**]。

這個螢幕擷取畫面顯示以滑鼠右鍵按一下檔案時所顯示的 **組建動作** 選項：

 [![組建動作功能表](image-images/image30a.png)](image-images/image30a.png#lightbox)

Visual Studio for Mac 通常會自動選擇正確的 **組建動作** ，但您應該注意這些設定，特別是當您在專案中四處移動檔案時。

### <a name="adding-an-image-file"></a>新增影像檔案

若要將影像檔加入至專案，請先以滑鼠右鍵按一下專案，然後選擇 [**加入**檔案]。

 [![新增檔案 .。。功能表](image-images/image31a.png)](image-images/image31a.png#lightbox)

選取您要包含在標準檔案對話方塊中的映射 (或影像) 。 影像的預設組建動作將會是 **套件套件** –除非您有特定原因，否則請不要覆寫此值。

 [![[新增檔案] 對話方塊](image-images/image32a.png)](image-images/image32a.png#lightbox)

影像將會加入至您的專案，並可在程式碼中載入和顯示。 此螢幕擷取畫面顯示已新增至 iOS 應用程式專案的影像：

 [![專案中的影像](image-images/image33a.png)](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>什麼是資原始目錄？

放在 **resources** 目錄中的檔案會以不同于一般檔案的方式處理， **資源** 資料夾的內容會複製到應用程式的根目錄，並可在您的程式碼中參考。 這可能有許多原因：

- 儲存在應用程式屬性中設定的影像，例如預設的啟動映射和應用程式圖示。
- 將其他映射和檔案與程式碼分開儲存，讓您更容易管理 (子目錄會在資原始目錄內容複寫) 時保留。

**Resources**目錄在程式庫專案中特別有用，因為程式碼可以假設這些映射將複製到取用應用程式的根目錄中，讓您更輕鬆地撰寫需要影像、音效、影片、XML 或其他檔案的共用程式碼程式庫。

**Resources**目錄必須命名為，而且所有檔案的組建動作都必須設為**套件套件**。

## <a name="displaying-the-image"></a>顯示影像

在 iOS 設計工具中，使用 **影像視圖** 來顯示影像或動畫系列的影像。 [工具箱] 中的 **影像視圖** 圖示如下所示：

 [![工具箱中的 >imageview](image-images/image35a.png)](image-images/image35.png#lightbox)

將 [ **影像視圖** ] 從 [ **工具箱** ] 拖曳到視圖控制器上。 然後，在 [ **影像視圖] > 影像** 下，下拉式清單將提供您專案中所有可用影像檔案的清單。 選取其中任何一項，將它新增至您的影像視圖。

 [![工具箱中的 >imageview](image-images/image36a.png)](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>以程式設計方式顯示影像

由於 **SF Monkey.jpg** 位於 **Resources** 目錄的根目錄中，因此會在應用程式套件組合的根目錄中，于執行時間提供。 若要在影像視圖控制項中顯示此影像，請使用下列程式碼：

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

如果我們已將影像放在 **/Resources/Pics/SF Monkey.jpg**中，則程式碼會在路徑中包含 [ **圖片** ] 資料夾：

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

資源檔參考永遠不需要包含 **Resources** 資料夾。

## <a name="related-links"></a>相關連結

- [控制項 (範例) ](/samples/xamarin/ios-samples/controls)