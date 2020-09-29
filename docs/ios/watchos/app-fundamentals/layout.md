---
title: 在 Xamarin 中使用 watchOS 版面配置
description: 本檔說明如何使用 Xamarin 建立 watchOS 版面配置。 它會討論介面控制器、群組、分隔符號和內容控制項。
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 1fdb7a10bd767085ba8758fa2e026cc36c93639a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436914"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>在 Xamarin 中使用 watchOS 版面配置

設計 Apple Watch [螢幕大小](~/ios/watchos/app-fundamentals/screen-sizes.md) 的版面配置會帶來獨特的挑戰。

## <a name="design-tips"></a>設計秘訣

重點是：使用大型手指讓您的使用者介面在小型監看式畫面上可供讀取和使用。 請勿進入 **iOS** 模擬器 (的設計，這種情況會顯示非常大的) ，而 **滑鼠指標** (可搭配小型觸控目標) ！

- 使用黑色背景-它會使用觀賞的黑色邊框來建立較大螢幕的假像。

- 請勿填補您的畫面版面配置-邊框形成自然的視覺填補。

- 專注于可讀性。 謹慎使用字型大小和色彩，以確保可讀取文字。 使用內建的文字樣式來取得自動動態類型支援。

![動態型別支援的範例](layout-images/type.png)

- 專注于觸控目標大小。 按鈕/tappable 具有文字標籤的資料表資料列應該橫跨整個畫面。 Apple 表示「絕不會將三個以上的專案並排顯示」，而且如果您使用圖示而非文字標籤，則為。

- 使用[ `Menu` 控制項](~/ios/watchos/user-interface/menu.md)來公開較不常使用的功能，讓您的應用程式設計更清楚且簡潔。

## <a name="implementation"></a>實作

Watch 套件包含下列控制項，可協助您建立引人注目的 watch 應用程式版面配置：

### <a name="interface-controller"></a>介面控制器

`WKInterfaceController`是所有場景的基本類別。

介面控制器的設計介面會像垂直 **群組**一樣：您可以將其他控制項拖曳至介面控制器，並將其自動設定到另一個上方：

![控制項會自動設定到另一個上方](layout-images/controller-scene.png)

您可以設定每個控制項的 **位置** 和 **大小** 屬性來控制其外觀：

![設定每個控制項的位置和大小屬性](layout-images/positionsize-attributes.png)

當大小設定為 **相對於容器** 時，您可以提供比例值和位移調整。 此螢幕擷取畫面顯示已設定為使用 watch 畫面寬度 80% (**0.8**) 的按鈕：

![提供比例值和位移調整](layout-images/button-attributes.png)

### <a name="group"></a>Group

`WKInterfaceGroup` 是一個簡單的版面配置容器，可以設定為垂直或水準堆疊控制項。 根據預設，它會包含每個控制項之間的間距，但您可以修改 **屬性** 偵測器中 (和內建) 的間距。

![修改屬性偵測器中的間距和內凹](layout-images/group-attributes.png)

群組本身可以相對於其周圍的控制項調整大小和位置，而且可以將群組嵌套以建立複雜的版面配置。

![群組可以嵌套以建立複雜的版面配置](layout-images/group-scene.png)

### <a name="separator"></a>Separator

分隔符號控制項的目的是要協助在您的版面配置中提供視覺化的指引。 使用分隔符號 (或背景色彩或影像) ，以協助使用者瞭解哪些內容與您的畫面相關。

![分隔符號使用方式的範例](layout-images/separator-scene.png)

請注意，未使用螢幕全形的藍色和綠色分隔符號已設定為 **固定** 或 **相對於容器** 大小。

### <a name="content-controls"></a>內容控制項

沒有 `Label` 、 `Image` 、、、、 `Button` `Switch` `Slider` `Map` 和 [其他控制項](~/ios/watchos/user-interface/index.md)，也不會完成任何版面配置。
您可以使用 **群組** 或每個控制項上的位置和大小設定，將這些設定放在您的版面配置中。

## <a name="related-links"></a>相關連結

- [WatchKitCatalog (範例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的版面配置參考](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Apple 的色彩 & 印刷樣式參考](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)