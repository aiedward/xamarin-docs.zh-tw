---
title: 使用 tvOS 在 Xamarin 中的分段控制項
description: 本文件說明如何使用 tvOS 分段的控制項中使用 Xamarin 建置的應用程式。 它討論區段圖示和文字、 事件、 修改控制項的外觀等等。
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 98a770d05014e0498b805ed9ffa0c84314efc765
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107106"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>使用 tvOS 在 Xamarin 中的分段控制項

分段控制項提供一組線性項目，其中每個圖示或文字，可以包含與用來向使用者提供一組相關的選擇。

[![](segmented-controls-images/segment01.png "區段控制項範例")](segmented-controls-images/segment01.png#lightbox)

Apple 會有下列建議使用分段控制項：

- **提供足夠的空間**-照護應該提供足夠的空間之間其他的 take[可焦點化項目](~/ios/tvos/app-fundamentals/navigation-focus.md)和分段控制項。 就設為焦點 （未按一下） 時，使用者也可以在他們其實想要選取另一個可焦點化項目。 目前的區段時不小心變更區段時，會變成選取個別的區段。
- **用於分割檢視的內容篩選**-分段控制項不要篩選分割檢視所設計的內容和篩選條件之間輕鬆巡覽的內容很好的選擇。
- **七個區段的最大限制**-您應該嘗試保持八個以下的區段數目上限 (8) 做為這是您更輕鬆地從剖析跨房間的沙發上且更輕鬆地瀏覽。
- **使用一致的區段內容大小**-所有區段都具有相同的寬度和可能的話，您應該嘗試將內容保留在各區段的大小相同。 這讓區段控制項更賞心悅目，不僅可讓您更容易閱讀且一目了然。
- **避免混用的圖示和文字**-每個個別的區段可能包含圖示或文字，但非兩者。 雖然您可以混合使用圖示和相同的分段控制項中的文字，這應該避免。

<a name="About-Segment-Icons" />

## <a name="about-segment-icons"></a>關於區段圖示

Apple 建議使用簡單、 可辨識的映像區段的圖示，例如搜尋的放大鏡。 過於複雜的圖示難以辨識在電視螢幕上跨房間，因此最好是限制您的圖示，以簡單的表示法。

文字和圖示的指定區段上，不能混用，您應該避免混用圖示和單一的分段控制項中的文字。 它應該是所有的圖示] 或 [所有文字。

<a name="Summary" />

## <a name="segment-text"></a>區段的文字

Apple 會進行處理的文字區段的下列建議：

- **使用簡短，有意義的名詞**-區段標題應該清楚的選取指定的區段時，使用者應預期的內容類型。 例如： 音樂或視訊。
- **使用字首大寫的大小寫**-區段標題的每個單字應該為大寫文章、 nor 和介係詞的字元少於四 （4） 除外。
- **使用短，已取得焦點的標題**-保持簡短，並著重於在選取的區段時所預期的內容類型項目。

同樣地，您不能混用文字和圖示上指定的區段和您應該避免混用圖示和單一的分段控制項中的文字。

<a name="Segment-Controls-and-Storyboards" />

## <a name="segment-controls-and-storyboards"></a>區段的控制項和分鏡腳本

Xamarin.tvOS 應用程式中使用區段控制項的最簡單方式是將它們新增至使用 iOS 設計工具的應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在  **Solution Pad**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**區段控制**從**工具箱**並將它放在檢視上： 

    [![](segmented-controls-images/segment02.png "區段控制")](segmented-controls-images/segment02.png#lightbox)
1. 在 [**小工具] 索引標籤**的**屬性輸入板**，您可以調整的區段控制數個屬性，例如其**樣式**和**狀態**: 

    [![](segmented-controls-images/segment03.png "小工具 索引標籤")](segmented-controls-images/segment03.png#lightbox)
1. 使用**區段**欄位來控制控制器中的區段數目。
1. 選取指定的區段，從**區段下拉式清單**來調整其個別的屬性，例如**標題**或**映像**並且控制，如果指定的區段**已啟用**或是**選取**時顯示的控制項。
1. 最後，將指派**名稱**控制項，讓您可以回應其C#程式碼。 例如:  

    [![](segmented-controls-images/segment04.png "指派的名稱")](segmented-controls-images/segment04.png#lightbox)
1. 儲存您的變更。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. 在 **方案總管**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**區段控制**從**工具箱**並將它放在檢視上： 

    [![](segmented-controls-images/segment02-vs.png "區段控制")](segmented-controls-images/segment02-vs.png#lightbox)
1. 在 [**小工具] 索引標籤**的**屬性總管**，您可以調整的區段控制數個屬性，例如其**樣式**並**狀態**: 

    [![](segmented-controls-images/segment03-vs.png "小工具 索引標籤")](segmented-controls-images/segment03-vs.png#lightbox)
1. 使用**區段**欄位來控制控制器中的區段數目。
1. 選取指定的區段，從**區段下拉式清單**來調整其個別的屬性，例如**標題**或**映像**並且控制，如果指定的區段**已啟用**或是**選取**時顯示的控制項。
1. 最後，將指派**名稱**控制項，讓您可以回應其C#程式碼。 例如:  

    [![](segmented-controls-images/segment04-vs.png "指派的名稱")](segmented-controls-images/segment04-vs.png#lightbox)
1. 儲存您的變更。
    
-----

如需有關使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。 

<a name="Working-with-Segmented-Controls" />

## <a name="working-with-segmented-controls"></a>使用分段控制項

如上所述，s 分段控制項提供一組線性項目，其中每個圖示或文字，可以包含，並用來提供給使用者的一組相關的選擇。

有數個不同的方式，您可以使用分段控制項 Xamarin.tvOS 應用程式中。

<a name="Exposed-as-Outlets-and-Actions" />

## <a name="exposed-as-names-and-events"></a>公開為名稱和事件

如果您在介面設計工具中建立區段控制，並公開為具名的控制項和事件您可以使用下列程式碼以回應區段的變化：

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

在上述範例中，區段控制已公開為`PlayerCount`名稱和`PlayerCountChanged`事件動作。 如需有關使用動作與出口的詳細資訊，請參閱[撰寫程式碼輸出和動作](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)一節我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。

`SelectedSegment`屬性會取得或設定基礎為零 (0) 目前選取的區段索引。 因此如果您有五 （5） 區段，第一個區段的索引為零 (0)，而最後的索引四 （4)。

<a name="Modifying-Segments" />

## <a name="modifying-segments"></a>修改區段

在任何時候，您可以修改數目與分段控制項的內容。 您可以使用下列程式碼插入新的圖示區段：

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

第二個參數可讓您定義會區段插入使用零 (0) 起始的索引。 如果最後一個參數是`true`插入項目建立動畫。

若要移除指定的區段，使用下列方法：

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

或下列項目以移除所有區段：

```csharp
SegmentedControl.RemoveAllSegments();
```

同樣地，如果最後一個參數是`true`，移除項目建立動畫。 使用`NumberOfSegments`屬性來傳回目前的區段數目。

若要取得**標題**或是**圖示**為指定的區段中，使用下列項目：

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

以及變更**標題**或是**圖示**，使用下列命令：

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

若要查看指定的區段是否**已啟用**，使用下列命令：

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

以及**啟用/停用**指定區段，使用下列命令：

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance" />

## <a name="modifying-the-segmented-controls-appearance"></a>修改分割的控制項的外觀

若要變更指定區段的背景影像，您可以使用下列程式碼：

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

其中`UIControlState`指定您要設定為影像之控制項的狀態：

- 一般
- 反白顯示
- 已停用
- 已選取
- 已取得焦點

和`UIBarMetrics`指定要做的計量：

- 預設
- 壓縮
- DefaultPrompt
- CompactPrompt

此外，您可以設定使用的區段之間的分隔線：

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

其中第一個`UIControlState`指定左邊的分割線與第二個區段的狀態`UIControlState`指定右邊區段的狀態。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計與 Xamarin.tvOS 應用程式內使用分段的控制項。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
