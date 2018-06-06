---
title: 使用 watchOS Xamarin 在版面配置
description: 本文件說明如何建立使用 Xamarin watchOS 版面配置。 它討論介面控制站、 群組、 分隔符號和內容控制項。
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 11ff5ec2fc8fe99a780a3d728d3d84af59794cea
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790610"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>使用 watchOS Xamarin 在版面配置

設計版面配置的 Apple Watch[畫面大小](~/ios/watchos/app-fundamentals/screen-sizes.md)唯一的挑戰。

## <a name="design-tips"></a>設計秘訣

重點是： 讓您的使用者介面，可讀取和食指的大型小監看式螢幕上使用。 不屬於設計相關的設陷**iOS 模擬器**（只會出現很大） 和**滑鼠指標**（這可用於小觸控目標） ！

- 使用在黑色背景-它會建立較大螢幕的視覺效果與手錶的黑色的邊框。

- 畫面配置周圍填補-邊框形成自然的視覺化填補。

- 專注於可讀性。 若要確定可讀取的文字審慎地使用字型和色彩。 若要取得自動動態類型支援使用內建文字樣式。

![](layout-images/type.png "動態類型支援的範例")

- 專注於觸控目標大小。 按鈕/tappable 資料表的資料列，具有文字標籤應該跨越整個螢幕。 Apple 說 「 永遠不會將超過三個項目-並存 」，是否您使用圖示和非文字標籤。

- 使用[`Menu`控制項](~/ios/watchos/user-interface/menu.md)較不常使用的公開功能，將您的應用程式的設計清楚且精確。


## <a name="implementation"></a>實作

監看式套件包含下列控制項來協助您建立具吸引力的監看式應用程式配置：

### <a name="interface-controller"></a>介面控制站

`WKInterfaceController`是基底類別，所有您場景。

介面控制站的設計介面的行為類似垂直**群組**： 您可以將其他控制項拖曳到介面控制站，並且也可以在其他的自動配置外一個：

![](layout-images/controller-scene.png "控制項是在其他的自動配置外一")

您可以設定**位置**和**大小**上每個控制項來控制其外觀屬性：

![](layout-images/positionsize-attributes.png "每個控制項上設定的位置和大小屬性")

當大小設定為**相對於容器**您可以提供比例的值和位移的調整。 這個螢幕擷取畫面顯示已設定為使用監看式螢幕的寬度為 80%的按鈕 (**0.8**):

![](layout-images/button-attributes.png "提供比例的值及位移的調整")


### <a name="group"></a>群組

`WKInterfaceGroup` 是可以設定堆疊的簡單的版面配置容器控制水平或垂直。 它包含依預設，每個控制項之間的間距，但您可以修改的間距和 （內凹） 中**屬性**偵測器。

![](layout-images/group-attributes.png "修改的間距和內凹屬性偵測器中")

群組可以自行可調整大小和位置，控制項的相對位置和群組可以巢狀來建立複雜配置。

![](layout-images/group-scene.png "群組可以是巢狀，以建立複雜的版面配置")


### <a name="separator"></a>Separator

分隔符號控制項被要協助提供您在配置中的視覺化導引。 使用分隔符號 （或背景色彩或影像） 可協助使用者了解在螢幕上相關的內容。

![](layout-images/separator-scene.png "分隔符號用法的範例")

請注意不要使用完整的螢幕寬度的藍色與綠色分隔符號都已設定其中一種**固定**或**相對於容器**大小。

### <a name="content-controls"></a>內容控制項

沒有配置是完整`Label`， `Image`， `Button`， `Switch`， `Slider`， `Map`，和[其他控制項](~/ios/watchos/user-interface/index.md)。
這些可以放置在您使用的配置**群組**或每個控制項的位置和大小設定。



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Apple 的版面配置參考](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Apple 的色彩和印刷樣式參考](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
