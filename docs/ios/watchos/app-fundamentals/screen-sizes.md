---
title: 在 Xamarin 中使用 watchOS 螢幕大小
description: 本檔說明如何使用各種 watchOS 螢幕大小。 它討論 watchOS 介面設計工具、watchOS 模擬器和影像資源。
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 18720ee396952cfe1feaaa8de35a425f60575eae
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930113"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>在 Xamarin 中使用 watchOS 螢幕大小

Apple Watch 有兩種螢幕大小：

- **38mm**
  - 136 x 170 邏輯圖元（272 x 340 實體圖元）

- **42mm**
  - 156 x 195 邏輯圖元（312 x 390 實體圖元）。

設計和測試應用程式時，您應該將螢幕大小納入考慮。

## <a name="watchos-interface-designer"></a>watchOS 介面設計工具

根據預設，Visual Studio for Mac 設計工具會顯示**任何 Apple Watch**的監看式介面控制器。

![設計工具會顯示任何 Apple Watch 的監看式介面控制器](screen-sizes-images/screen-any-sml.png)

使用 [大小] 功能表來編輯和預覽您的分鏡腳本，請在其中一個可用的螢幕大小： **38mm**或**42mm**：

![選取38mm 或42mm 大小](screen-sizes-images/screen-menu-sml.png)

較大的螢幕大小有時候會轉譯在較小螢幕上截斷/隱藏的內容。
請務必測試這兩種大小。

### <a name="interface-design"></a>介面設計

無論大小為何，您的應用程式都應該在畫面上顯示相同的內容，而且應該適當地展開或合約元素。 在 [Visual Studio for Mac 設計工具] 的 [屬性偵測器] 中，您應該使用 [**相對於容器**或大小]，將喜好設定中的**內容調整**為固定大小。

![使用相對於容器或大小，將喜好設定中的內容調整為固定大小](screen-sizes-images/sizeattributepanel-sml.png)

由於監看式畫面是以黑色擋板括住，因此不建議在您的介面周圍提供填補。 讓元素與畫面邊緣 rest，並讓擋板在應用程式周圍形成自然框線。

## <a name="watchos-simulator"></a>watchOS 模擬器

在模擬器上測試時，您可以使用 [**硬體 > 裝置**] 功能表，輕鬆地在兩個螢幕大小之間切換。

![模擬器可以使用硬體裝置功能表在兩個螢幕大小之間切換](screen-sizes-images/simulator.png)

## <a name="image-resources"></a>影像資源

如果單一資產看起來不會有不同的大小，您應該使用多個映射資產。 映射資產目錄允許為每個大小指定個別的點陣圖：

![影像資產目錄編輯器](screen-sizes-images/images-xcassets.png)

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

或者，使用程式碼來判斷螢幕大小，並將不同的影像全部載入：

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

閱讀更多有關使用[影像控制項](~/ios/watchos/user-interface/image.md)的資訊。

## <a name="related-links"></a>相關連結

- [watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
