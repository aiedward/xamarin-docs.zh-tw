---
title: 使用 watchOS 在 Xamarin 中的版面配置
description: 本文件說明如何建立使用 Xamarin watchOS 版面配置。 它討論介面控制器、 群組、 分隔符號，以及內容控制項。
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a714b8bd1984f22a138d09912a0a7fe1a51e857c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646812"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>使用 watchOS 在 Xamarin 中的版面配置

設計版面配置的 Apple Watch[螢幕大小](~/ios/watchos/app-fundamentals/screen-sizes.md)提供獨特的挑戰。

## <a name="design-tips"></a>設計秘訣

重點是： 可讀取且可用於大型手指小型監看式畫面，讓您的使用者介面。 不屬於設計相關的設陷**iOS 模擬器**（會出現很大的） 和**滑鼠指標**（這適用於小型觸控目標） ！

- 使用黑色背景-它會建立較大螢幕的視覺效果與手錶的黑色的邊框。

- 填補住您的畫面版面配置-邊框形成自然的視覺化與邊框距離。

- 專注於可讀性。 以確保可讀取文字請謹慎使用字型和色彩。 若要取得自動動態型別支援使用內建的文字樣式。

![](layout-images/type.png "動態類型支援的範例")

- 焦點放在觸控目標大小。 具有文字標籤的按鈕/tappable 資料表資料列應跨越整個螢幕。 Apple 會說 「 永遠不會將放在超過三個項目-並存 」，是否您使用圖示和非文字標籤。

- 使用[`Menu`控制](~/ios/watchos/user-interface/menu.md)保留您的應用程式設計清楚且簡潔的較少使用的公開功能。


## <a name="implementation"></a>實作

監看式套件包含下列控制項來協助您建置吸引人的監看式應用程式版面配置：

### <a name="interface-controller"></a>介面控制器

`WKInterfaceController`是基底類別，所有您場景。

介面控制器的設計介面的行為類似垂直**群組**： 您可以將其他控制項拖曳至介面控制器以及它們將會在其他的自動配置外的：

![](layout-images/controller-scene.png "控制項不會自動配置外會在彼此上方")

您可以設定**位置**並**大小**上每個控制項，來控制其外觀的屬性：

![](layout-images/positionsize-attributes.png "在每個控制項上設定的位置和大小屬性")

當大小設定為**相對於容器**比例的值及位移的調整，您可以提供。 此螢幕擷取畫面顯示一個按鈕，已設定為使用 80%的監看式螢幕的寬度 (**0.8**):

![](layout-images/button-attributes.png "提供比例的值及位移的調整")


### <a name="group"></a>群組

`WKInterfaceGroup` 是可設定為堆疊的簡單版面配置容器控制水平或垂直。 它包含根據預設，每個控制項之間的間距，但您可以修改的間距 （和內凹） 中**屬性**偵測器。

![](layout-images/group-attributes.png "修改的間距和內凹屬性偵測器中")

群組可以自行調整大小並相對於控制項，並可以巢狀群組，以建立複雜的版面配置。

![](layout-images/group-scene.png "群組可以是巢狀來建立複雜的版面配置")


### <a name="separator"></a>Separator

分隔符號控制項被要協助提供您在配置中的視覺化導引。 使用分隔符號 （或背景色彩或影像） 來協助使用者了解在螢幕上相關的內容。

![](layout-images/separator-scene.png "分隔符號用法的範例")

請注意，使用已設定不使用完整的螢幕寬度的藍色與綠色分隔符號**Fixed**或**相對於容器**大小。

### <a name="content-controls"></a>內容控制項

任何配置就不算完整`Label`， `Image`， `Button`， `Switch`， `Slider`， `Map`，以及[其他控制項](~/ios/watchos/user-interface/index.md)。
這些可以放置在您使用的版面配置**群組**或每個控制項上的位置和大小 設定。



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的版面配置參考](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Apple 的色彩 & 印刷樣式參考](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
