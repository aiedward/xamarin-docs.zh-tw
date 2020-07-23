---
title: 在 Xamarin 中使用 watchOS 版面配置
description: 本檔說明如何使用 Xamarin 建立 watchOS 版面配置。 其中討論介面控制器、群組、分隔符號和內容控制項。
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 345c05a439423474644ac64ef86f9adc580ab0b1
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937718"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>在 Xamarin 中使用 watchOS 版面配置

設計 Apple Watch[螢幕大小](~/ios/watchos/app-fundamentals/screen-sizes.md)的版面配置帶來了獨特的挑戰。

## <a name="design-tips"></a>設計秘訣

重點在於：讓您的使用者介面在小型的監看式畫面上可供讀取和使用，並具有大手指。 不屬於**iOS**模擬器的設計的陷阱（看起來很大）和**滑鼠指標**（適用于小型觸控目標）！

- 使用黑色背景-它會使用監看式的黑色擋板，建立較大螢幕的夢想。

- 不要填補您的螢幕版面配置-擋板會形成自然的視覺填補。

- 專注于可讀性。 謹慎使用字型大小和色彩，以確保文字可供讀取。 使用內建的文字樣式來取得自動動態類型支援。

![動態類型支援的範例](layout-images/type.png)

- 專注于觸控目標大小。 具有文字標籤的按鈕/tappable 資料表資料列應該橫跨整個畫面。 Apple 說「不要將三個以上的專案並存」，而且如果您使用圖示而不是文字標籤。

- 使用[ `Menu` 控制項](~/ios/watchos/user-interface/menu.md)來公開較不常使用的功能，讓您的應用程式設計更清楚且簡潔。

## <a name="implementation"></a>實作

Watch 套件包含下列控制項，可協助您建立引人注目的監看式應用程式佈建：

### <a name="interface-controller"></a>介面控制器

`WKInterfaceController`是您所有場景的基本類別。

介面控制器的設計介面的行為就像垂直**群組**：您可以將其他控制項拖曳到介面控制器上，它們會自動設定在另一個上方：

![控制項會自動設定給另一個上方的](layout-images/controller-scene.png)

您可以在每個控制項上設定 [**位置**] 和 [**大小**] 屬性，以控制其外觀：

![設定每個控制項的位置和大小屬性](layout-images/positionsize-attributes.png)

當 [大小] 設定為 [**相對於容器**] 時，您可以提供比例值和位移調整。 這個螢幕擷取畫面顯示已設定為使用 [監看式] 畫面寬度（**0.8**）80% 的按鈕：

![提供比例值和位移調整](layout-images/button-attributes.png)

### <a name="group"></a>群組

`WKInterfaceGroup`是簡單的版面配置容器，可設定為垂直或水準堆疊控制項。 根據預設，它會包含每個控制項之間的間距，但是您可以在**屬性**偵測器中修改間距（和內凹）。

![修改屬性偵測器中的間距和內凹](layout-images/group-attributes.png)

群組本身可以相對於其周圍的控制項調整大小和位置，而且可以嵌套群組來建立複雜的配置。

![群組可以進行嵌套以建立複雜的版面配置](layout-images/group-scene.png)

### <a name="separator"></a>Separator

分隔符號控制項的目的是要協助您在版面配置中提供視覺指引。 使用分隔符號（或背景色彩或影像），協助使用者瞭解哪些內容與您的螢幕相關。

![分隔符號使用方式的範例](layout-images/separator-scene.png)

請注意，未使用畫面完整寬度的藍色和綠色分隔符號已設定為**固定**或**相對於容器**大小。

### <a name="content-controls"></a>內容控制項

沒有 `Label` 、 `Image` 、、、、 `Button` `Switch` `Slider` `Map` 和[其他控制項](~/ios/watchos/user-interface/index.md)，就不會完成任何版面配置。
您可以使用**群組**或每個控制項上的位置和大小設定，在您的版面配置中放置這些值。

## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的版面配置參考](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Apple 的色彩 & 印刷樣式參考](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
