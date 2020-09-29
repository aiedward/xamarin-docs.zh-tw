---
title: 在 Xamarin 中使用 tvOS 按鈕
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用按鈕。 它會討論如何在程式碼和分鏡腳本中使用按鈕，並檢查如何為按鈕建立樣式。
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/07/2017
ms.openlocfilehash: 07d48a38751680f5e7227682d0639b77b3a3627b
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436155"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>在 Xamarin 中使用 tvOS 按鈕

您可以使用類別的實例 `UIButton` ，在 tvOS 視窗中建立可設定焦點、可選取的按鈕。 當使用者選取按鈕時，它會將動作訊息傳送至目標物件，讓您的 tvOS 應用程式回應使用者的輸入。

[![範例按鈕](buttons-images/buttons01.png)](buttons-images/buttons01.png#lightbox)

如需有關使用焦點並與 Siri 遠端流覽的詳細資訊，請參閱我們使用 [導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md) ，以及 [Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md) 檔。

<a name="About-Buttons"></a>

## <a name="about-buttons"></a>關於按鈕

在 tvOS 中，按鈕可用於應用程式特定的動作，而且可能包含標題、圖示或兩者。 當使用者使用 [Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)流覽應用程式消費者介面時，焦點會移至指定的按鈕，使其變更文字和背景色彩。 陰影也會套用至按鈕，以新增3D 效果，使其出現在消費者介面其餘部分的上方。

[![範例按鈕](buttons-images/buttons01.png)](buttons-images/buttons01.png#lightbox)

Apple 針對使用按鈕有下列建議：

- **使用標題或圖示** -雖然圖示和標題都可包含在按鈕中，但空間有限，因此請不要將兩者合併。
- **清楚標示破壞性按鈕** -如果按鈕執行破壞性動作 (例如) 刪除檔案，請使用文字及/或圖示清楚地標示。 破壞性動作應該一律提出 [警示](~/ios/tvos/user-interface/alerts.md) ，要求使用者限制該動作。
- **不要使用 [上一頁] 按鈕** -Siri 遠端上的功能表按鈕會用來回到上一個畫面。 這項規則的唯一例外狀況是針對應用程式內購買或破壞性動作，其中應該會顯示 [ **取消** ] 按鈕。

如需使用焦點和導覽的詳細資訊，請參閱我們使用 [導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md) 檔。

<a name="Button-Icons"></a>

### <a name="button-icons"></a>按鈕圖示

Apple 建議您針對按鈕圖示使用簡單、可高度辨識的影像。 過於複雜的圖示很難在一台沙發上的電視螢幕上辨識，因此請盡可能使用最簡單的標記法來取得想法。 可能的話，請使用標準、已知的圖示影像 (例如搜尋) 的放大鏡。

<a name="Button-Titles"></a>

### <a name="button-titles"></a>按鈕標題

當您為按鈕建立標題時，Apple 有下列建議：

- **顯示下列圖示按鈕的描述性文字** -可能的話，請將清楚的描述性文字放在下方圖示，以進一步取得按鈕的用途。
- **針對標題使用動詞或動詞片語** -清楚陳述當使用者按一下按鈕時將會發生的動作。
- **使用標題樣式的大小寫** -除了文章、結合或介係詞 (四個字母或較少的) 之外，按鈕標題的每個字都應為大寫。
- **使用簡短** 的點對點標題-使用最短的措辭來描述按鈕的動作。

<a name="Buttons-and-Storyboards"></a>

## <a name="buttons-and-storyboards"></a>按鈕和分鏡腳本

在 tvOS 應用程式中使用按鈕最簡單的方式，就是使用 Xamarin Designer for iOS 將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 從連結**庫**拖曳**按鈕**，並將它放在視圖上： 

    [![A 按鍵](buttons-images/storyboard01.png)](buttons-images/storyboard01.png#lightbox)
1. 在 **屬性瀏覽器**中，您可以調整按鈕的數個屬性，例如其 **標題** 和 **文字色彩**： 

    [![按鈕屬性](buttons-images/storyboard02.png)](buttons-images/storyboard02.png#lightbox)
1. 接下來，切換至 [**事件]** 索引標籤，然後從**按鈕**連接**事件**並加以呼叫 `ButtonPressed` ： 

    [![[事件] 索引標籤](buttons-images/storyboard03.png)](buttons-images/storyboard03.png#lightbox)
1. 您將會自動切換到您 `ViewController.cs` 可以使用 **向上** 鍵和 **向下** 鍵在程式碼中放置新動作的視圖： 

    [![在程式碼中放置新的動作](buttons-images/storyboard04.png)](buttons-images/storyboard04.png#lightbox)
1. 按下 **enter** 鍵以選取位置： 

    [![程式碼編輯器](buttons-images/storyboard05.png)](buttons-images/storyboard05.png#lightbox)
1. 將變更儲存至所有檔案。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ，然後開啟它進行編輯。
1. 從連結**庫**拖曳**按鈕**，並將它放在視圖上： 

    [![A 按鍵](buttons-images/storyboard01vs.png)](buttons-images/storyboard01vs.png#lightbox)
1. 在 **屬性瀏覽器**中，您可以調整按鈕的數個屬性，例如其 **標題** 和 **文字色彩**： 

    [![屬性瀏覽器](buttons-images/storyboard02vs.png)](buttons-images/storyboard02vs.png#lightbox)
1. 接下來，切換至 [**事件]** 索引標籤，然後從**按鈕**連接**事件**並加以呼叫 `ButtonPressed` ： 

    [![[事件] 索引標籤](buttons-images/storyboard03vs.png)](buttons-images/storyboard03vs.png#lightbox)
1. 將變更儲存至所有檔案。

編輯您的視圖控制器 (範例 `ViewController.cs`) 檔案，並新增下列程式碼來處理選取的按鈕：

```

using System;
using UIKit;

namespace tvRemote
{
    public partial class ViewController : UIViewController
    {
        ...

        partial void ButtonPressed (UIButton sender) {
            // Handle click here
            ...
        }
    }
}

```

-----

只要按鈕的 `Enabled` 屬性為 `true` ，而且不是由另一個控制項或視圖所涵蓋，則可以使用 Siri 遠端來使其成為焦點專案。 如果使用者選取按鈕並按一下觸控介面，則會 `ButtonPressed` 執行上面所定義的動作。

> [!IMPORTANT]
> 雖然 `TouchUpInside` `UIButton` 您可以在建立 **事件處理常式**時，將動作指派給 iOS 設計工具中的，但永遠不會呼叫它，因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 建立 tvOS 使用者介面元素的**動作**時，您應該一律使用預設**動作類型**。

如需使用分鏡腳本的詳細資訊，請參閱我們的 [Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Buttons-and-Code"></a>

## <a name="buttons-and-code"></a>按鈕和程式碼

（選擇性）您 `UIButton` 可以在 c # 程式碼中建立，並將其新增至 tvOS 應用程式的視圖。 例如：

```csharp
var button = new UIButton(UIButtonType.System);
button.Frame = new CGRect (25, 25, 300, 150);
button.SetTitle ("Hello", UIControlState.Normal);
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
View.AddSubview (button);
```

當您在程式碼中建立新的時 `UIButton` ，您可以將其指定 `UIButtonType` 為下列其中一項：

- **系統** -這是 tvOS 所顯示的標準按鈕類型，而且是您最常使用的類型。
- **DetailDisclosure** -提供用來隱藏或顯示詳細資訊的 [關閉] 按鈕類型。
- **InfoDark** -深色的詳細資訊按鈕在圓形中顯示「i」。
- **InfoLight** -輕量詳細資訊按鈕在圓形中顯示「i」。
- **AddContact** -將按鈕顯示為 [新增連絡人] 按鈕。
- **自訂** -可讓您自訂按鈕的數個特性。

接下來，您要定義按鈕的螢幕大小和位置。 範例：

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

然後，設定按鈕的標題。 `UIButtons` 不同于大部分的 `UIKit` 控制項，因為它們具有狀態，所以您必須針對指定的變更標題 `UIControlState` 。 例如：

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

接下來，使用 `AllEvents` 事件來查看使用者按下按鈕的時間。 範例：

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

最後，您會將按鈕新增至視圖以顯示它：

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> 雖然可以將動作（例如）指派給 `TouchUpInside` `UIButton` ，但永遠不會呼叫它，因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 您應該一律使用 **AllEvents** 或 **PrimaryActionTriggered**之類的事件。

<a name="Styling-a-Button"></a>

## <a name="styling-a-button"></a>設定按鈕樣式

tvOS 提供數個屬性 `UIButton` ，可用來提供其標題，並以背景色彩和影像等專案為其建立樣式。

<a name="Button-Titles"></a>

### <a name="button-titles"></a>按鈕標題

如上所述，與大部分的 `UIButtons` 控制項不同的是， `UIKit` 它們的狀態是，您不能只是變更標題，您必須針對指定的變更 `UIControlState` 。 例如：

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

您可以使用方法來設定按鈕的標題色彩 `SetTitleColor` 。 例如：

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

您可以使用來調整標題的陰影 `SetTitleShadowColor` 。 例如：

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

使用下列程式碼反白顯示按鈕時，您可以設定標題陰影從 *陰* 文變成 *浮凸* 變更：

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

此外，您也可以使用屬性化的文字做為按鈕的標題。 例如：

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>按鈕影像

`UIButton`可以有附加的影像，而且可以使用影像作為其背景。

若要為指定的設定按鈕的背景影像 `UIControlState` ，請使用下列程式碼：

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

將屬性設為可在反 `AdjustsImageWhenHiglighted` `true` 白顯示時，將影像繪製得較淺 (這是預設) 。 將 `AdjustsImageWhenDisabled` 屬性設為，以在 `true` 停用按鈕時將影像繪製成較暗的 (，這是預設) 。

若要設定顯示在按鈕上的影像，請使用下列程式碼：

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

您 `TintColor` 可以使用屬性來設定同時套用至標題和按鈕影像的色彩色調。 針對類型的按鈕 `Custom` ，此屬性不會有任何作用，您必須自行執行此 `TintColor` 行為。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了如何設計和使用 tvOS 應用程式內的按鈕。 它會示範如何使用 iOS 設計工具中的按鈕，以及如何在 c # 程式碼中建立按鈕。 最後，它會示範如何修改按鈕的標題，以及變更其樣式和外觀。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)