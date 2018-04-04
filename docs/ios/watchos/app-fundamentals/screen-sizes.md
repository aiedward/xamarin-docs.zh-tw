---
title: 使用的螢幕大小
ms.prod: xamarin
ms.assetid: 840DF939-2F59-4ABA-87D8-92AAC8A92BC4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 600b3de6cbf31bd4c3221eb1bf81eda7b4678c09
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-screen-sizes"></a>使用的螢幕大小

Apple Watch 有兩個螢幕大小：

- **38mm**
  - 136 x 170 邏輯像素 （272 x 340 實體像素為單位）

- **42mm**
  - 156 x 195 邏輯像素 （312 x 390 實體像素為單位）。

您應該考量設計和測試您的應用程式時採取的螢幕大小。

## <a name="watchos-interface-designer"></a>watchOS 介面設計工具

根據預設，Visual Studio for Mac 設計工具會顯示觀賞介面控制站在**Any Apple Watch**。

![](screen-sizes-images/screen-any-sml.png "設計工具顯示監看式介面控制站，在任何 Apple Watch")

使用大小功能表來編輯和預覽您在其中一個可用的螢幕大小的分鏡腳本： **38 公釐**或**42 公釐**:

![](screen-sizes-images/screen-menu-sml.png "選取 38 mm42 公釐大小")

較大的螢幕大小有時會轉譯為就是較小螢幕上截斷/隱藏的內容。
請務必先測試兩個大小。


### <a name="interface-design"></a>介面設計

您的應用程式應該將不論大小為何，在螢幕上顯示相同的內容，並應展開或收縮適當的項目。 在 Visual Studio for Mac 的設計工具中，在屬性偵測器中，您應該使用**相對於容器**或**大小，以使內容**而非固定大小。

![](screen-sizes-images/sizeattributepanel-sml.png "使用容器的相對或大小，以容納內容，而非固定的大小")

[監看式] 畫面會以黑色的邊框住，因為不建議提供您的介面周圍的填補。 可讓項目將對螢幕的邊緣，並讓邊框形成自然框線應用程式。


## <a name="watchos-simulator"></a>watchOS 模擬器

當在模擬器上測試輕鬆地切換使用兩個螢幕大小**硬體 > 裝置**功能表。

![](screen-sizes-images/simulator.png "模擬器可以使用硬體裝置功能表的兩個螢幕大小之間切換")


## <a name="image-resources"></a>影像資源

如果單一資產看起來並無不同大小的理想，您應該使用多個影像資產。 指定每個大小都允許不同的點陣圖影像資產目錄：

![](screen-sizes-images/images-xcassets.png "影像資產目錄編輯器")

```csharp
// specify the asset name, the correct size will automatically be loaded
staticImage.SetImage(UIImage.FromBundle("Walkway"));
```

或者，使用程式碼至判斷螢幕大小，並完全載入不同的影像：

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
