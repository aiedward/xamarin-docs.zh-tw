---
title: "使用分割的控制項"
description: "本文涵蓋設計和 Xamarin.tvOS 應用程式內使用分割的控制項。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 6561ff70997af05ed4df6b7bfe0ba6345fb44d9d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-segmented-controls"></a>使用分割的控制項

_本文涵蓋設計和 Xamarin.tvOS 應用程式內使用分割的控制項。_


分割的控制項提供一組線性項目，其中每個可以包含的圖示或文字，並用來向使用者提供的一組相關的選擇。

[![](segmented-controls-images/segment01.png "區段控制項範例")](segmented-controls-images/segment01.png#lightbox)

Apple 已使用不同客層之控制項的下列建議：

- **提供足夠的空間**-照護則是需要提供其他的之間有很大的空間[可設定焦點的項目](~/ios/tvos/app-fundamentals/navigation-focus.md)和分割的控制項。 在焦點 （不點擊） 時，且時，使用者也可以在實際要選取另一個可設定焦點的項目，在目前的區段時不小心變更區段，會選取個別的區段。
- **使用分割檢視的內容篩選**-分割控制項不讓內容篩選內容和篩選之間輕鬆瀏覽分割檢視所設計的很好的選擇。
- **七個區段的最大限制**-您應該試著保留八個以下的區段數目上限 (8) 做為這是要對其剖析遙控器輕鬆在沙發上，且更容易瀏覽更容易。
- **使用一致的區段內容大小**-所有區段都有相同的寬度，可能的話，您應該嘗試將內容保留在各區段的大小相同。 這讓區段控制項更賞心悅目，不僅會讓您更容易讀取一眼。
- **避免混用圖示和文字**-圖示或文字，但不可同時，可以包含每個個別的區段。 雖然可以混合使用圖示和相同的分割控制項中的文字，這應該避免。

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>關於區段圖示

Apple 建議區段圖示，例如搜尋放大鏡使用簡單且可辨識的映像。 任何過度複雜的圖示是難以辨識在電視螢幕上遙控器輕鬆，因此最好是限制您簡單的表示法的圖示。

文字和圖示上指定的區段不能混用，您應該避免混用圖示和文字方塊控制項中的單一分割。 它應該是所有的圖示] 或 [所有文字。

<a name="Summary" />

## <a name="segment-text"></a>區段的文字

Apple 會進行處理的文字區段的下列建議：

- **使用短，有意義的名詞**-區段標題應該清楚的選取指定的區段時，使用者應預期的內容類型。 例如： 音樂或視訊。
- **使用字首大寫的大小寫**-每個字組的區段標題必須大寫除了文件、 nor 和介系詞的少於 4 個字元。
- **使用短，已取得焦點的標題**-保持簡短，並著重於選取的區段時所預期的內容類型的項目。

同樣地，文字和圖示上指定的區段不能混用，您應該避免混用圖示和文字方塊控制項中的單一分割。

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>區段控制項和分鏡腳本

Xamarin.tvOS 應用程式中使用的區段控制項的最簡單方式是將它們新增到應用程式的 UI 使用 iOS 設計工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**方案板**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**區段控制**從**工具箱**並將它放在檢視上： 

    [![](segmented-controls-images/segment02.png "區段控制項")](segmented-controls-images/segment02.png#lightbox)
1. 在**Widget 索引標籤**的**屬性板**，您可以調整的區段控制數個屬性，例如其**樣式**和**狀態**: 

    [![](segmented-controls-images/segment03.png "[小工具] 索引標籤")](segmented-controls-images/segment03.png#lightbox)
1. 使用**區段**欄位來控制控制器中的區段數目。
1. 選取指定的區段，從**區段的下拉式清單中**調整其個別屬性，例如**標題**或**映像**和控制，如果指定的區段**啟用**或**選取**顯示控制項時。
1. 最後，指派**名稱**控制項，讓您可以在 C# 程式碼回應給他們。 例如:  

    [![](segmented-controls-images/segment04.png "指派的名稱")](segmented-controls-images/segment04.png#lightbox)
1. 儲存您的變更。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**區段控制**從**工具箱**並將它放在檢視上： 

    [![](segmented-controls-images/segment02-vs.png "區段控制項")](segmented-controls-images/segment02-vs.png#lightbox)
1. 在**Widget 索引標籤**的**屬性總管**，您可以調整的區段控制數個屬性，例如其**樣式**和**狀態**: 

    [![](segmented-controls-images/segment03-vs.png "[小工具] 索引標籤")](segmented-controls-images/segment03-vs.png#lightbox)
1. 使用**區段**欄位來控制控制器中的區段數目。
1. 選取指定的區段，從**區段的下拉式清單中**調整其個別屬性，例如**標題**或**映像**和控制，如果指定的區段**啟用**或**選取**顯示控制項時。
1. 最後，指派**名稱**控制項，讓您可以在 C# 程式碼回應給他們。 例如:  

    [![](segmented-controls-images/segment04-vs.png "指派的名稱")](segmented-controls-images/segment04-vs.png#lightbox)
1. 儲存您的變更。
    
-----

如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>使用分割的控制項

如上所述，s 分割控制項提供一組線性項目，其中每個可以包含的圖示或文字，並用來向使用者提供的一組相關的選擇。

有數個不同的方式，您可以使用分割控制項 Xamarin.tvOS 應用程式中。

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>公開為名稱和事件

如果您建立區段控制項介面的設計工具中，公開為具名的控制項和事件您可以使用下列程式碼以回應不斷區段變更：

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

在上述範例中，區段控制已公開為`PlayerCount`名稱和`PlayerCountChanged`事件動作。 如需使用動作和出口的詳細資訊，請參閱[撰寫的程式碼使用插座和動作](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)區段我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。

`SelectedSegment`屬性會取得或設定目前所選取的區段零 (0) 為基礎的索引。 如果您有五 （5） 區段，第一個區段必須為零 (0) 和最後一個索引的索引，因此四 （4)。

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>修改區段

您可以在任何時間修改數目和不同客層之控制項的內容。 使用下列程式碼插入新的圖示區段：

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

第二個參數會定義將區段插入使用零 (0) 起始的索引。 如果最後一個參數是`true`插入將會顯示動畫。

若要移除指定的區段，使用下列方法：

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

或若要移除所有區段下列：

```csharp
SegmentedControl.RemoveAllSegments();
```

同樣地，如果最後一個參數是`true`，移除將會顯示動畫。 使用`NumberOfSegments`屬性來傳回目前的區段數目。

若要取得**標題**或**圖示**指定區段中，使用下列命令：

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

並將變更**標題**或**圖示**，使用下列命令：

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

若要查看是否指定的區段**啟用**，使用下列命令：

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

和**啟用/停用**指定區段，使用下列命令：

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>修改不同客層之的控制項的外觀

若要變更指定區段的背景影像，您可以使用下列程式碼：

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

其中`UIControlState`指定您要設定的影像做為控制項的狀態：

- 一般
- 反白顯示
- 已停用
- 已選取
- 已取得焦點

和`UIBarMetrics`指定要做為度量：

- 預設
- 壓縮
- DefaultPrompt
- CompactPrompt

此外，您可以設定使用的區段之間的分隔線：

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

在第一個`UIControlState`指定左邊的分割線與第二個區段的狀態`UIControlState`指定右側區段的狀態。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和 Xamarin.tvOS 應用程式內使用分割的控制。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
