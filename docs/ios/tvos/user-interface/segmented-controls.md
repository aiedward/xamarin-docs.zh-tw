---
title: 在 Xamarin 中使用 tvOS 分段的控制項
description: 本檔說明如何在以 Xamarin 建立的應用程式中使用 tvOS 分割的控制項。 它討論區段圖示和文字、事件、修改控制項的外觀等等。
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 622960f73c96209c1a67a08a4f0abe27656331bf
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200246"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>在 Xamarin 中使用 tvOS 分段的控制項

分段的控制項會提供一組線性專案, 每個元素都可以包含一個圖示或文字, 並用來提供一組相關的選項給使用者。

[![](segmented-controls-images/segment01.png "範例區段控制項")](segmented-controls-images/segment01.png#lightbox)

Apple 具有下列使用分段控制項的建議:

- 請**提供足夠的空間**, 以在其他可[設定焦點的專案](~/ios/tvos/app-fundamentals/navigation-focus.md)與分段的控制項之間提供足夠的空間。 當個別區段為焦點時 (而不是按一下時), 而且當使用者想要在目前的區段上選取另一個可設定的專案時, 可能會不小心變更區段。
- **使用內容篩選的分割視圖**-分割視圖對於內容篩選而言, 不是理想的選擇, 因為已針對在內容和篩選之間輕鬆導覽而設計的分割區。
- **限制為最多七個區段**-您應該嘗試將最大區段數目保持在八 (8) 以下, 因為這比較容易從沙發上的房間進行剖析, 並更容易流覽。
- **使用一致的區段內容大小**-所有區段都具有相同的寬度, 而且可能的話, 您應該嘗試將每個區段中的內容保持相同大小。 這不僅讓區段控制項變得更美觀, 更容易閱讀。
- **避免混用圖示和文字**-每個個別區段都可以包含圖示或文字, 但不能同時包含兩者。 雖然您可以在相同的分割控制項中混用圖示和文字, 但應該避免這種情況。

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>關於區段圖示

Apple 建議您使用簡單、可辨識的影像來進行區段圖示, 例如用於搜尋的放大鏡。 過於複雜的圖示很難在房間的電視畫面上辨識, 因此最好將您的圖示限制為簡單的標記法。

您不能在指定的區段上混合文字和圖示, 而且應該避免在單一分割的控制項中混用圖示和文字。 它應該是所有圖示或所有文字。

<a name="Summary" />

## <a name="segment-text"></a>區段文字

Apple 會針對使用區段文字提供下列建議:

- **使用簡短且有意義的名詞**-區段標題應該清楚地陳述使用者選取指定的區段時應預期的內容類型。 例如：音樂或影片。
- **使用標題-大小寫大寫**-區段標題的每個單字都應該大寫, 但不超過四 (4) 個字元的文章、連接詞和介係詞。
- **使用簡短且專注的標題**-保留標題、簡短, 並將焦點放在選取區段時預期的內容類型。

同樣地, 您不能在指定的區段上混合文字和圖示, 而且應該避免在單一分割的控制項中混用圖示和文字。

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>區段控制項和分鏡腳本

在 tvOS 應用程式中使用區段控制項最簡單的方式, 就是使用 iOS 設計工具將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**中, 按兩下`Main.storyboard`檔案, 然後開啟檔案進行編輯。
1. 從 [**工具箱**] 拖曳**區段控制項**並放在視圖上: 

    [![](segmented-controls-images/segment02.png "區段控制項")](segmented-controls-images/segment02.png#lightbox)
1. 在**屬性 Pad**的 [ **Widget]** 索引標籤中, 您可以調整區段控制項的數個屬性, 例如其**樣式**和**狀態**: 

    [![](segmented-controls-images/segment03.png "[Widget] 索引標籤")](segmented-controls-images/segment03.png#lightbox)
1. 使用 [**區段**] 欄位可控制控制器中的區段數目。
1. 從 [**區段] 下拉式清單**中選取指定的區段, 以調整其個別屬性 (例如**標題**或**影像**), 以及控制是否要在顯示控制項時**啟用**或**選取**特定區段。
1. 最後, 將**名稱**指派給控制項, 以便您可以在程式碼中C#對其進行回應。 例如： 

    [![](segmented-controls-images/segment04.png "指派名稱")](segmented-controls-images/segment04.png#lightbox)
1. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**中, 按兩下`Main.storyboard`檔案, 然後開啟檔案進行編輯。
1. 從 [**工具箱**] 拖曳**區段控制項**並放在視圖上: 

    [![](segmented-controls-images/segment02-vs.png "區段控制項")](segmented-controls-images/segment02-vs.png#lightbox)
1. 在 [**屬性瀏覽器**] 的 [ **Widget]** 索引標籤中, 您可以調整區段控制項的數個屬性, 例如其**樣式**和**狀態**: 

    [![](segmented-controls-images/segment03-vs.png "[Widget] 索引標籤")](segmented-controls-images/segment03-vs.png#lightbox)
1. 使用 [**區段**] 欄位可控制控制器中的區段數目。
1. 從 [**區段] 下拉式清單**中選取指定的區段, 以調整其個別屬性 (例如**標題**或**影像**), 以及控制是否要在顯示控制項時**啟用**或**選取**特定區段。
1. 最後, 將**名稱**指派給控制項, 以便您可以在程式碼中C#對其進行回應。 例如： 

    [![](segmented-controls-images/segment04-vs.png "指派名稱")](segmented-controls-images/segment04-vs.png#lightbox)
1. 儲存您的變更。

-----

如需使用分鏡腳本的詳細資訊, 請參閱我們的[Hello, tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>使用分段的控制項

如上所述, s 分段的控制項提供一組線性元素, 每個專案都可以包含一個圖示或文字, 並用來提供一組相關的選項給使用者。

有幾種不同的方式可讓您在 tvOS 應用程式中使用分割的控制項。

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>以名稱和事件的形式公開

如果您在介面設計工具中建立區段控制項, 並將它公開為命名控制項和事件, 您可以使用下列程式碼來回應區段的變更:

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

在上述範例的案例中, 區段控制項已公開為`PlayerCount`名稱`PlayerCountChanged`和事件動作。 如需使用動作和輸出的詳細資訊, 請參閱我們的[Hello, tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)的[以輸出和動作撰寫程式碼](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)一節。

`SelectedSegment`屬性會取得或設定目前選取的區段, 做為以零 (0) 為基底的索引。 因此, 如果您有五個 (5) 區段, 第一個區段的索引為零 (0), 最後一個索引為四 (4)。

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>修改區段

您隨時都可以修改分割控制項的數目和內容。 使用下列程式碼來插入新的圖示區段:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

第二個參數會定義使用以零 (0) 為基底的索引來插入區段的位置。 如果最後一個參數是`true` , 則會以動畫顯示插入。

若要移除指定的區段, 請使用下列各項:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

以下是移除所有區段的下列各項:

```csharp
SegmentedControl.RemoveAllSegments();
```

同樣地, 如果最後一個參數`true`是, 則移除動作會以動畫顯示。 `NumberOfSegments`使用屬性來傳回目前的區段數目。

若要取得指定區段的**標題**或**圖示**, 請使用下列專案:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

若要變更**標題**或**圖示**, 請使用下列專案:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

若要查看是否已**啟用**指定的區段, 請使用下列各項:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

若要**啟用/停**用指定的區段, 請使用下列各項:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>修改分段控制項的外觀

您可以使用下列程式碼, 將指定區段的背景變更為影像:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

其中`UIControlState`指定您要將影像設定為的控制項狀態:

- 一般
- 重點
- 已停用
- 已選取
- 已取得焦點

和`UIBarMetrics`會指定要用來做為的計量:

- 預設
- 小巧
- DefaultPrompt
- CompactPrompt

此外, 您可以使用來設定區段之間的分隔線:

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

其中, 第`UIControlState`一個指定分割區左邊的區段狀態, 第二個`UIControlState`指定區段在右邊的狀態。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋在 tvOS 應用程式中設計和使用分段控制項。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
