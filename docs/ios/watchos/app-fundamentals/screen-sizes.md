---
title: 運用 watchOS 螢幕大小，在 Xamarin 中使用
description: 本文件說明如何使用各種 watchOS 螢幕大小。 它討論 watchOS 介面設計工具，watchOS 模擬器，以及影像資源。
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: b2f4cc71c1993e51ed55b51edd7c50d393e60873
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61412805"
---
# <a name="working-with-watchos-screen-sizes-in-xamarin"></a>運用 watchOS 螢幕大小，在 Xamarin 中使用

Apple Watch 有兩個螢幕大小：

- **38mm**
  - 第 136 x 170 邏輯像素 （272 x 340 實體像素為單位）

- **42mm**
  - 156 x 195 邏輯像素 （312 x 390 實體像素）。

您應該考慮到設計和測試您的應用程式時的螢幕大小。

## <a name="watchos-interface-designer"></a>watchOS 介面設計工具

根據預設，Visual Studio for Mac 設計工具會顯示監看在介面控制器**Any 的 Apple Watch**。

![](screen-sizes-images/screen-any-sml.png "設計工具顯示監看式介面控制站，在任何 Apple Watch")

您可以使用 [大小] 功能表來編輯及預覽您的分鏡腳本，在這兩個可用的螢幕大小：**38 公釐**或是**42 mm**:

![](screen-sizes-images/screen-menu-sml.png "選擇38mm或42mm的尺寸")

較大的螢幕大小有時候會呈現會是較小螢幕上截斷/隱藏的內容。
請務必在這兩種大小上進行測試。


### <a name="interface-design"></a>介面設計

您的應用程式應該將不論大小為何，在螢幕上顯示相同的內容，並應該展開或收縮適當的項目。 在 Visual Studio for Mac 的設計工具中，在屬性偵測器中，您應該使用**相對於容器**或是**大小，以符合內容**而非固定的大小。

![](screen-sizes-images/sizeattributepanel-sml.png "使用容器的相對或大小，以符合內容，而非固定的大小")

因為 [監看式] 畫面會圍繞著黑色的邊框，不建議提供您的介面周圍的填補。 可讓 rest 對螢幕的邊緣，並讓形成自然的框線，應用程式周圍邊框的項目。


## <a name="watchos-simulator"></a>watchOS 模擬器

當在模擬器上測試輕鬆地切換使用兩個螢幕大小**硬體 > 裝置**功能表。

![](screen-sizes-images/simulator.png "模擬器可以使用硬體裝置功能表的兩個螢幕大小之間切換")


## <a name="image-resources"></a>影像資源

如果單一資產不會尋找擅長使用不同的大小，您應該使用多個影像資產。 影像資產目錄允許個別點陣圖，指定每種大小：

![](screen-sizes-images/images-xcassets.png "影像資產目錄編輯器")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

或者，使用程式碼來判斷螢幕大小，並完全載入不同的映像：

```csharp
bool large = WKInterfaceDevice.CurrentDevice.ScreenBounds.Size.Width > 136.0;
// Load image depending on screen size
using (var image = UIImage.FromBundle (large ? "42mm-Walkway" : "38mm-Walkway"))
{
   myImage.SetImage (image);

}
```

深入了解使用[影像控制項](~/ios/watchos/user-interface/image.md)。



## <a name="related-links"></a>相關連結

- [watchOS 3 簡介](~/ios/watchos/platform/introduction-to-watchos3/index.md)
