---
title: 在 Xamarin 中使用 tvOS 分割的控制項
description: 本檔說明如何在以 Xamarin 建立的應用程式中使用 tvOS 分段的控制項。 它會討論區段圖示和文字、事件、修改控制項的外觀等等。
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 47530b27fb754bf2efd50615fef9dfc558d9c7d2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436192"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>在 Xamarin 中使用 tvOS 分割的控制項

分割的控制項會提供一組線性元素，其中每個專案都可以包含一個圖示或文字，並且用來提供一組相關的選項給使用者。

[![範例區段控制項](segmented-controls-images/segment01.png)](segmented-controls-images/segment01.png#lightbox)

Apple 有下列建議來處理分段的控制項：

- 在其他可[設定焦點的專案](~/ios/tvos/app-fundamentals/navigation-focus.md)與分段的控制項**之間提供足夠的空間**時，請務必小心。 當系統處於焦點狀態時，會選取個別的區段 (不會在) ，而且當使用者實際想要選取目前區段上的另一個可設定焦點專案時，使用者可能會不小心變更區段。
- **將分割視圖用於內容篩選** -分割的控制項不會為內容篩選提供絕佳的選擇，因為分割視圖的設計是為了方便在內容和篩選之間流覽。
- **最多七個區段上限** -您應嘗試將最大區段數目維持在 8 (8) ，因為這比較容易從沙發的房間內剖析，並且更容易流覽。
- **使用一致的區段內容大小** -所有區段的寬度都相同，而且可能的話，您應該嘗試將每個區段中的內容保持相同的大小。 這不僅讓區段控制項更有視覺效果，還可讓您更容易閱讀。
- **避免混合圖示和文字** -每個個別區段可以包含圖示或文字，但不能同時包含兩者。 雖然可以將圖示和文字混合在相同的分割控制項中，但應該避免這種情況。

<a name="About-Segment-Icons"></a>

## <a name="about-segment-icons"></a>關於區段圖示

Apple 建議您針對區段圖示使用簡單、可辨識的影像，例如搜尋的放大鏡。 過於複雜的圖示很難在房間內的電視螢幕上辨識，因此最好將圖示限制為簡單的標記法。

您無法在指定的區段上混合文字和圖示，而且應該避免在單一分割的控制項中混合使用圖示和文字。 它應該是所有圖示或所有文字。

<a name="Summary"></a>

## <a name="segment-text"></a>區段文字

Apple 針對使用區段文字提出下列建議：

- **使用簡短且有意義的名詞** -區段標題應該清楚地指出當選取指定的區段時，使用者應該預期的內容類型。 例如：音樂或影片。
- **使用標題-大小寫大小** 寫-區段標題的每個字都應為大寫，但不超過四 (4) 個字元的文章、結合和介係詞。
- **使用簡短的焦點標題** -將標題、簡短和焦點放在選取區段時預期的內容類型。

同樣地，您無法在指定的區段上混合文字和圖示，而且應該避免在單一分割的控制項中混合使用圖示和文字。

<a name="Segment-Controls-and-Storyboards"></a>

## <a name="segment-controls-and-storyboards"></a>區段控制項和分鏡腳本

在 tvOS 應用程式中使用區段控制項最簡單的方式，就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 [ **Solution Pad**中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 將 **區段控制項** 從 [ **工具箱** ] 拖放到視圖上： 

    [![區段控制項](segmented-controls-images/segment02.png)](segmented-controls-images/segment02.png#lightbox)
1. 在**屬性板**的 [ **Widget]** 索引標籤中，您可以調整區段控制項（例如其**樣式**和**狀態**）的數個屬性： 

    [![[Widget] 索引標籤](segmented-controls-images/segment03.png)](segmented-controls-images/segment03.png#lightbox)
1. 使用 [ **區段** ] 欄位來控制控制器中的區段數目。
1. 從 [ **區段] 下拉式清單** 中選取指定的區段，以調整其個別屬性（例如 **標題** 或 **影像** ），以及控制在顯示控制項時，是否 **啟用** 或 **選取** 指定的區段。
1. 最後，將 **名稱** 指派給控制項，讓您可以在 c # 程式碼中回應它們。 例如： 

    [![指派名稱](segmented-controls-images/segment04.png)](segmented-controls-images/segment04.png#lightbox)
1. 儲存變更。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 將 **區段控制項** 從 [ **工具箱** ] 拖放到視圖上： 

    [![區段控制項](segmented-controls-images/segment02-vs.png)](segmented-controls-images/segment02-vs.png#lightbox)
1. 在**屬性瀏覽器**的 [ **Widget]** 索引標籤中，您可以調整區段控制項（例如其**樣式**和**狀態**）的數個屬性： 

    [![[Widget] 索引標籤](segmented-controls-images/segment03-vs.png)](segmented-controls-images/segment03-vs.png#lightbox)
1. 使用 [ **區段** ] 欄位來控制控制器中的區段數目。
1. 從 [ **區段] 下拉式清單** 中選取指定的區段，以調整其個別屬性（例如 **標題** 或 **影像** ），以及控制在顯示控制項時，是否 **啟用** 或 **選取** 指定的區段。
1. 最後，將 **名稱** 指派給控制項，讓您可以在 c # 程式碼中回應它們。 例如： 

    [![指派名稱](segmented-controls-images/segment04-vs.png)](segmented-controls-images/segment04-vs.png#lightbox)
1. 儲存變更。

-----

如需使用分鏡腳本的詳細資訊，請參閱我們的 [Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Segmented-Controls"></a>

## <a name="working-with-segmented-controls"></a>使用分段的控制項

如上所述，分割的控制項會提供一組線性元素，其中每個專案都可以包含一個圖示或文字，並且用來提供一組與使用者相關的選項。

有幾種不同的方式可讓您在 tvOS 應用程式中使用分割的控制項。

<a name="Exposed-as-Outlets-and-Actions"></a>

## <a name="exposed-as-names-and-events"></a>公開為名稱和事件

如果您在介面設計工具中建立了區段控制項，並將其公開為命名控制項和事件，您可以使用下列程式碼來回應區段變更：

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take action based on the number of players selected
    switch(PlayerCount.SelectedSegment) {
    case 0:
        // Do something if the segment is selected
        ...
        break;
    case 1:
        // Do something if the segment is selected
        ...
        break;
    case 2:
        // Do something if the segment is selected
        ...
        break;
    }
}
```

在上述範例的案例中，區段控制項會公開為 `PlayerCount` 名稱和 `PlayerCountChanged` 事件動作。 如需有關使用動作和輸出的詳細資訊，請參閱[Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)的「[使用輸出和動作撰寫程式碼](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)」一節。

`SelectedSegment`屬性會取得或設定目前選取的區段，以零 (0) 為基礎的索引。 因此，如果您有五個 (5 個) 區段，則第一個區段的索引為零 (0) 和最後一個索引為四 (4) 。

<a name="Modifying-Segments"></a>

## <a name="modifying-segments"></a>修改區段

您隨時都可以修改分段控制項的數目和內容。 使用下列程式碼來插入新的圖示區段：

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

第二個參數會定義使用零 (0) 基礎的索引來插入區段的位置。 如果最後一個參數是 `true` ，則會將插入動畫。

若要移除指定的區段，請使用下列各項：

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

以下是移除所有區段的下列內容：

```csharp
SegmentedControl.RemoveAllSegments();
```

同樣地，如果最後一個參數是，將會以 `true` 動畫顯示移除。 您 `NumberOfSegments` 可以使用屬性來傳回目前的區段數目。

若要取得指定區段的 **標題** 或 **圖示** ，請使用下列程式：

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

若要變更 **標題** 或 **圖示**，請使用下列專案：

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

若要查看是否已 **啟用**指定的區段，請使用下列程式：

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

若要 **啟用/停** 用指定的區段，請使用下列各項：

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance"></a>

## <a name="modifying-the-segmented-controls-appearance"></a>修改分段控制項的外觀

您可以使用下列程式碼，將指定區段的背景變更為影像：

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

其中 `UIControlState` 指定您要設定影像的控制項狀態，如下所示：

- 正常
- 強調顯示
- 已停用
- 已選取
- 已取得焦點

並 `UIBarMetrics` 指定要使用的計量：

- 預設
- 精簡
- DefaultPrompt
- CompactPrompt

此外，您可以使用下列內容來設定區段之間的分隔線：

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

其中第一個 `UIControlState` 指定分割區左邊的區段狀態，而第二個指定 `UIControlState` 右邊區段的狀態。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了如何設計和使用 tvOS 應用程式內的分段控制項。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)