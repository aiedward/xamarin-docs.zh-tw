---
title: 在 Xamarin 中使用 tvOS 堆疊視圖
description: 本檔說明如何在以 Xamarin 建立的應用程式中使用 tvOS 堆疊流覽。 它提供堆疊視圖的高階總覽，並討論自動設定、定位和調整堆疊視圖、常見用途、與分鏡腳本的整合等等。
ms.prod: xamarin
ms.assetid: 00B07F85-F30B-4DD4-8664-A61D0A1CDB0E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 1d6f2b6cbc18317562396308bf353c83a8471c2c
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602888"
---
# <a name="working-with-tvos-stacked-views-in-xamarin"></a>在 Xamarin 中使用 tvOS 堆疊視圖
> [!WARNING]
> IOS 設計工具在 Visual Studio 2019 16.8 版和 Visual Studio 2019 for Mac 版本8.8 中已淘汰，並已在 Visual Studio 2019 版本16.9 和 Visual Studio for Mac 版本8.9 中移除。
> 建立 iOS 使用者介面的建議方式是直接在執行 Xcode 介面產生器的 Mac 上。 如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](~/ios/user-interface/storyboards/index.md)。 

Stack View 控制項 (`UIStackView`) 利用自動設定和大小類別的功能，以水準或垂直方式管理子檢視堆疊，其動態地回應內容變更和 APPLE TV 裝置的螢幕大小。

所有附加至堆疊視圖的子檢視配置都是由其根據開發人員定義的屬性（例如軸、分佈、對齊和間距）進行管理：

[![子視圖版面配置圖表](stacked-views-images/stacked01.png)](stacked-views-images/stacked01.png#lightbox)

`UIStackView`在 tvOS 應用程式中使用時，開發人員可以在 IOS 設計工具的分鏡腳本內定義子檢視，或在 c # 程式碼中新增和移除子檢視。

## <a name="about-stacked-view-controls"></a>關於堆疊視圖控制項

`UIStackView`是設計成非轉譯的容器視圖，因此不會像的其他子類別一樣繪製到畫布上 `UIView` 。 設定屬性（例如 `BackgroundColor` 或覆寫） `DrawRect` 不會有視覺化效果。

有幾個屬性會控制堆疊視圖如何排列其子檢視的集合：

- **軸** –決定堆疊視圖是以 **水準** 或 **垂直** 方式排列子檢視。
- **對齊** –控制子檢視在堆疊視圖內的對齊方式。
- **分佈** –控制子檢視在堆疊視圖內的大小。
- **間距** -控制堆疊視圖中每個子視圖之間的最短空間。
- **基準相對** -如果為 `true` ，則每個子視圖的垂直間距將衍生自其基準。
- **相對的版面配置邊界** ：使子檢視相對於標準版面配置邊界。

通常您會使用堆疊視圖來排列少量的子檢視。 您可以藉由將一或多個堆疊視圖嵌套在彼此之間，來建立更複雜的使用者介面。

您可以藉由將額外的條件約束新增至子檢視 (（例如控制高度或寬度) ），進一步微調 Ui 外觀。 不過，請小心不要將衝突的條件約束加入堆疊視圖本身所引進的條件約束。

<a name="Auto-Layout-and-Size-Classes"></a>

## <a name="auto-layout-and-size-classes"></a>自動版面配置和大小類別

當子類別加入至堆疊視圖時，其配置完全由該堆疊視圖使用自動設定和大小類別來定位和調整排列檢視的大小。

堆疊視圖會將集合中的第一個和最後一個子視圖 _釘_ 選到垂直堆疊視圖的 **上** 邊緣和 **下** 邊緣，或是水準堆疊視圖的 **左邊** 緣和 **右** 邊緣。 如果您將 `LayoutMarginsRelativeArrangement` 屬性設定為 `true` ，則視圖會將子檢視釘選到相關邊界，而不是邊緣。

`IntrinsicContentSize`當您沿著定義的 `Axis` (（) 除外）計運算元檢視大小時，堆疊視圖會使用子視圖的屬性 `FillEqually Distribution` 。 `FillEqually Distribution`會調整所有子檢視的大小，使其大小相同，因此在中填入堆疊視圖 `Axis` 。

除了之外 `Fill Alignment` ，堆疊視圖會使用子視圖的 `IntrinsicContentSize` 屬性來計算視圖的大小，使其與指定的垂直垂直 `Axis` 。 若為 `Fill Alignment` ，則會將所有的子檢視調整大小，使其填滿垂直于指定的堆疊視圖 `Axis` 。

<a name="Positioning-and-Sizing-the-Stack-View"></a>

## <a name="positioning-and-sizing-the-stack-view"></a>定位和調整堆疊視圖的大小

雖然堆疊視圖對於任何子視圖 (的版面配置具有完全控制權，但根據 `Axis` 和) 等屬性 `Distribution` ，您仍然需要使用自動設定和大小類別，將堆疊 view (`UIStackView`) 放置在父視圖內。

一般而言，這表示要釘選堆疊視圖的兩個邊來展開和收縮，進而定義其位置。 如果沒有任何額外的條件約束，堆疊視圖會自動調整大小以符合其所有的子檢視，如下所示：

- 的大小 `Axis` 會是所有子視圖大小加上每個子視圖之間已定義之任何空間的總和。
- 如果 `LayoutMarginsRelativeArrangement` 屬性為 `true` ，堆疊視圖大小也會包含邊界的空間。
- 垂直的大小 `Axis` 會設定為集合中最大的子視圖。

此外，您可以指定堆疊視圖的 **高度** 和 **寬度** 條件約束。 在此情況下，子檢視會配置 (大小) 以填滿由和屬性決定的堆疊視圖所指定的空間 `Distribution` `Alignment` 。

如果 `BaselineRelativeArrangement` 屬性為 `true` ，則會根據第一個或最後一個子視圖的基準配置子檢視，而不是使用 **頂端**、**下** 或 * 中間的 -  **Y** 位置。 這些會在堆疊視圖的內容上計算，如下所示：

- 垂直堆疊視圖會傳回第一個基準的第一個子視圖，最後一個是最後一個。 如果其中一個子檢視本身是堆疊視圖，則會使用其第一個或最後一個基準。
- 水準堆疊視圖會針對第一個和最後一個基準使用其最高的子視圖。 如果最高的視圖也是堆疊視圖，它會使用最高的子視圖作為基準。

> [!IMPORTANT]
> 基準對齊無法在延伸或壓縮的子視圖大小上運作，因為基準將會計算到錯誤的位置。 針對基準對齊，請確定子視圖的 **高度** 符合內建內容視圖的 **高度**。

<a name="Common-Stack-View-Uses"></a>

## <a name="common-stack-view-uses"></a>一般堆疊視圖使用

有數種版面配置類型可搭配 Stack View 控制項運作。 根據 Apple，以下是幾個較常見的用途：

- **定義沿著軸的大小** –藉由將兩個邊緣沿著堆疊視圖 `Axis` 和連續的其中一個邊緣釘選來設定位置，堆疊視圖將沿著軸成長，以符合其子檢視所定義的空間。
- **定義子視圖的位置** –藉由釘選到堆疊視圖的相鄰邊緣至其父視圖，堆疊視圖會在這兩個維度中成長，以符合其包含子檢視。
- **定義堆疊的大小和位置** –藉由將堆疊視圖的四個邊緣釘選到父視圖，堆疊視圖會根據堆疊視圖內定義的空間來排列子檢視。
- **定義垂直軸的大小** –藉由將兩個邊緣垂直釘選到堆疊視圖 `Axis` ，以及沿著軸的其中一個邊緣來設定位置，堆疊視圖將會隨著軸垂直成長，以符合其子檢視所定義的空間。

<a name="Stack-Views-and-Storyboards"></a>

## <a name="stack-views-and-storyboards"></a>堆疊視圖和分鏡腳本

在 tvOS 應用程式中使用堆疊流覽的最簡單方式，就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **Solution Pad** 中，按兩下該檔案， `Main.storyboard` 然後開啟它進行編輯。
1. 設計您要加入至堆疊視圖之個別元素的版面配置：

    [![元素版面配置範例](stacked-views-images/layout01.png)](stacked-views-images/layout01.png#lightbox)
1. 將任何必要的條件約束加入至專案，以確保它們能夠正確調整。 一旦將專案新增至堆疊視圖，此步驟就很重要。
1. 在此案例中，請將所需的複本數目 (四) ：

    [![所需的複本數目](stacked-views-images/layout02.png)](stacked-views-images/layout02.png#lightbox)
1. 從 [**工具箱**] 拖曳 **堆疊視圖**，然後將它放在視圖上：

    [![堆疊視圖](stacked-views-images/layout03.png)](stacked-views-images/layout03.png#lightbox)
1. 選取 [堆疊] 視圖，在 [**屬性] 面板** 的 [ **Widget]** 索引標籤中，選取 [**填滿****對齊**]，針對 **分佈****平均填滿**，然後輸入 `25` **間距**：

    [![[Widget] 索引標籤](stacked-views-images/layout04.png)](stacked-views-images/layout04.png#lightbox)
1. 將堆疊視圖放在您想要的畫面上，並加入條件約束以將它保留在所需的位置。
1. 選取個別的專案，並將它們拖曳到堆疊視圖中：

    [![堆疊視圖中的個別元素](stacked-views-images/layout05.png)](stacked-views-images/layout05.png#lightbox)
1. 系統會調整配置，並根據您在上面設定的屬性，在堆疊視圖中排列元素。
1. 在 [**屬性瀏覽器**] 的 [ **Widget]** 索引標籤中指派 **名稱**，以使用 c # 程式碼中的 UI 控制項。
1. 儲存您的變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 [ **方案瀏覽器**] 中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 設計您要加入至堆疊視圖之個別元素的版面配置：

    [![範例元素版面配置](stacked-views-images/layout01.png)](stacked-views-images/layout01.png#lightbox)
1. 將任何必要的條件約束加入至專案，以確保它們能夠正確調整。 一旦將專案新增至堆疊視圖，此步驟就很重要。
1. 在此案例中，請將所需的複本數目 (四) ：

    [![所需的複本數目](stacked-views-images/layout02.png)](stacked-views-images/layout02.png#lightbox)
1. 從 [**工具箱**] 拖曳 **堆疊視圖**，然後將它放在視圖上：

    [![堆疊視圖](stacked-views-images/layout03-vs.png)](stacked-views-images/layout03-vs.png#lightbox)
1. 選取堆疊視圖，並在 **屬性瀏覽器** 的 [ **Widget]** 索引標籤中，選取 [**對齊**] 的 [**填滿**]，針對 **分佈****平均填滿**，然後輸入 `25` **間距**：

    [![[Widget] 索引標籤](stacked-views-images/layout04-vs.png)](stacked-views-images/layout04-vs.png#lightbox)
1. 將堆疊視圖放在您想要的畫面上，並加入條件約束以將它保留在所需的位置。
1. 選取個別的專案，並將它們拖曳到堆疊視圖中：

    [![堆疊視圖中的個別元素](stacked-views-images/layout05-vs.png)](stacked-views-images/layout05-vs.png#lightbox)
1. 系統會調整配置，並根據您在上面設定的屬性，在堆疊視圖中排列元素。
1. 在 [**屬性瀏覽器**] 的 [ **Widget]** 索引標籤中指派 **名稱**，以使用 c # 程式碼中的 UI 控制項。
1. 儲存您的變更。

-----

> [!IMPORTANT]
> 雖然您可以在 `TouchUpInside` `UIButton` 建立事件處理常式時，將動作指派給 UI 元素，例如 iOS 設計工具中的)  (例如，但永遠不會呼叫它，因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 `Action Type`建立 tvOS 使用者介面元素的動作時，您應該一律使用預設值。

如需使用分鏡腳本的詳細資訊，請參閱我們的 [Hello，TvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

在我們的範例中，我們已針對每個「玩家卡片」公開區段控制項和輸出的輸出口和動作。 在程式碼中，我們會根據目前的區段隱藏和顯示播放機。 例如：

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take Action based on the segment
    switch(PlayerCount.SelectedSegment) {
    case 0:
        Player1.Hidden = false;
        Player2.Hidden = true;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 1:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = true;
        Player4.Hidden = true;
        break;
    case 2:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = true;
        break;
    case 3:
        Player1.Hidden = false;
        Player2.Hidden = false;
        Player3.Hidden = false;
        Player4.Hidden = false;
        break;
    }
}
```

當應用程式執行時，系統會將四個元素平均散發在我們的堆疊視圖中：

[![當應用程式執行時，系統會將四個元素平均分散在我們的堆疊視圖中](stacked-views-images/layout06.png)](stacked-views-images/layout06.png#lightbox)

如果播放程式數目減少，未使用的視圖會隱藏，而堆疊視圖則會調整配置以符合：

[![如果播放程式數目減少，未使用的視圖會隱藏，而堆疊視圖則會調整配置以符合](stacked-views-images/layout07.png)](stacked-views-images/layout07.png#lightbox)

<a name="Populate-a-Stack-View-from-Code"></a>

### <a name="populate-a-stack-view-from-code"></a>從程式碼填入堆疊視圖

除了在 iOS 設計工具中完全定義堆疊視圖的內容和版面配置之外，您還可以從 c # 程式碼動態建立和移除。

採用下列範例，以使用堆疊視圖來處理審核 (1 到 5) 中的「星星」：

```csharp
public int Rating { get; set;} = 0;
...

partial void IncreaseRating (Foundation.NSObject sender) {

    // Maximum of 5 "stars"
    if (++Rating > 5 ) {
        // Abort
        Rating = 5;
        return;
    }

    // Create new rating icon and add it to stack
    var icon = new UIImageView (new UIImage("icon.png"));
    icon.ContentMode = UIViewContentMode.ScaleAspectFit;
    RatingView.AddArrangedSubview(icon);

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });

}

partial void DecreaseRating (Foundation.NSObject sender) {

    // Minimum of zero "stars"
    if (--Rating < 0) {
        // Abort
        Rating =0;
        return;
    }

    // Get the last subview added
    var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];

    // Remove from stack and screen
    RatingView.RemoveArrangedSubview(icon);
    icon.RemoveFromSuperview();

    // Animate stack
    UIView.Animate(0.25, ()=>{
        // Adjust stack view
        RatingView.LayoutIfNeeded();
    });
}
```

讓我們詳細看看這段程式碼的幾個部分。 首先，我們會使用 `if` 語句來檢查不超過五個 "星星" 或小於零。

若要加入新的「星星」，我們會載入其影像，並將其 **內容模式** 設定為 **適當** 調整：

```csharp
var icon = new UIImageView (new UIImage("icon.png"));
icon.ContentMode = UIViewContentMode.ScaleAspectFit;
```

這會讓「星形」圖示在新增至堆疊視圖時變得不失真。

接下來，我們會將新的「星星」圖示新增至堆疊視圖的子檢視集合：

```csharp
RatingView.AddArrangedSubview(icon);
```

您將會注意到，我們已將加入 `UIImageView` 至的 `UIStackView` `ArrangedSubviews` 屬性，而不是加入至 `SubView` 。 您要讓堆疊視圖控制其版面配置的任何視圖都必須加入至 `ArrangedSubviews` 屬性。

若要從堆疊視圖中移除子視圖，請先取得要移除的子視圖：

```csharp
var icon = RatingView.ArrangedSubviews[RatingView.ArrangedSubviews.Length-1];
```

接著，我們需要從 `ArrangedSubviews` 集合和超級視圖中移除它：

```csharp
// Remove from stack and screen
RatingView.RemoveArrangedSubview(icon);
icon.RemoveFromSuperview();
```

從集合中移除子視圖 `ArrangedSubviews` 會將它移出堆疊視圖的控制項，但不會將它從畫面中移除。

<a name="Dynamically-Changing-Content"></a>

## <a name="dynamically-changing-content"></a>動態變更內容

每當加入、移除或隱藏子視圖時，堆疊視圖都會自動調整子檢視的版面配置。 如果堆疊視圖的任何屬性調整 (例如其) ，也會調整版面配置 `Axis` 。

版面配置變更可透過將其放在動畫區塊內進行動畫，例如：

```csharp
// Animate stack
UIView.Animate(0.25, ()=>{
    // Adjust stack view
    RatingView.LayoutIfNeeded();
});
```

許多堆疊視圖的屬性都可以使用分鏡腳本內的大小類別來指定。 這些屬性將會自動以動畫顯示，回應大小或方向變更。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了在 tvOS 應用程式內設計和使用堆疊視圖的說明。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)