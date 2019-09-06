---
title: 使用 Xamarin 中的 tvOS 按鈕
description: 本檔說明如何在以 Xamarin 建立的 tvOS 應用程式中使用按鈕。 它討論如何在程式碼和分鏡腳本中使用按鈕，並檢查如何將按鈕樣式。
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/07/2017
ms.openlocfilehash: aa1dd0b503dd59a89f526fef53d7d7756a3883de
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291114"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>使用 Xamarin 中的 tvOS 按鈕

使用`UIButton`類別的實例，在 tvOS 視窗中建立可設定焦點的可選取按鈕。 當使用者選取按鈕時，它會將動作訊息傳送至目標物件，讓您的 tvOS 應用程式回應使用者的輸入。

[![](buttons-images/buttons01.png "範例按鈕")](buttons-images/buttons01.png#lightbox)

如需有關使用焦點並使用 Siri 遠端流覽的詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)和[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)檔。

<a name="About-Buttons" />

## <a name="about-buttons"></a>關於按鈕

在 tvOS 中，按鈕會用於應用程式特定的動作，而且可能包含標題、圖示或兩者。 當使用者使用[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)導覽應用程式的使用者介面時，焦點會轉移至指定的按鈕，使其變更文字和背景色彩。 陰影也會套用至加入3D 效果的按鈕，使其外觀高於使用者介面的其餘部分。

[![](buttons-images/buttons01.png "範例按鈕")](buttons-images/buttons01.png#lightbox)

Apple 具有下列使用按鈕的建議：

- **使用標題或圖示**-當圖示和標題都可以包含在按鈕中時，空間會受到限制，因此請儘量不要結合兩者。
- **清楚標示破壞性按鈕**-如果按鈕執行破壞性動作（例如刪除檔案），請使用文字和/或圖示將其清楚標示為。 破壞性動作應該一律會出現[警示](~/ios/tvos/user-interface/alerts.md)，要求使用者限制動作。
- **不要使用 [上一頁] 按鈕**-[Siri] 遠端上的 [功能表] 按鈕會用來回到上一個畫面。 這項規則的唯一例外是針對應用程式內購買或應顯示 [**取消**] 按鈕的破壞性動作。

如需有關使用焦點和導覽的詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)檔。

<a name="Button-Icons" />

### <a name="button-icons"></a>按鈕圖示

Apple 建議您針對按鈕圖示使用簡單、可高度辨識的影像。 過於複雜的圖示很難在沙發上的房間內辨識電視畫面，因此請儘量使用最簡單的標記法來取得整個概念。 可能的話，請使用適用于圖示的標準、清楚的影像（例如搜尋的放大鏡）。

<a name="Button-Titles" />

### <a name="button-titles"></a>按鈕標題

當您建立按鈕的標題時，Apple 具有下列建議：

- **顯示下方圖示按鈕的描述性文字**-可能的話，請將明確的描述性文字放在下方圖示，以進一步取得按鈕的用途。
- **使用 [標題] 的動詞或動詞片語**-清楚地指出當使用者按一下按鈕時，將會發生的動作。
- **使用標題樣式的大小寫**-除了文章、連接詞或介係詞（四個字母或更少）的每個字組外，每個按鈕的標題都應該為大寫。
- **使用簡短**的點對點標題-使用最短的可能用語來描述按鈕的動作。

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>按鈕和分鏡腳本

在 tvOS 應用程式中使用按鈕最簡單的方式，就是使用 Xamarin Designer for iOS 將它們新增至應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


1. 在 **方案總管**中，按兩下`Main.storyboard`檔案，然後開啟檔案進行編輯。
1. 拖曳 **按鈕** 從 **程式庫** 並將它放在檢視上： 

    [![](buttons-images/storyboard01.png "按鈕")](buttons-images/storyboard01.png#lightbox)
1. 在 [**屬性] 瀏覽器**中，您可以調整按鈕的數個屬性，例如其**標題**和**文字色彩**： 

    [![](buttons-images/storyboard02.png "按鈕屬性")](buttons-images/storyboard02.png#lightbox)
1. 接下來，切換至 [**事件]** 索引標籤，然後從**按鈕**連接**事件**並呼叫`ButtonPressed`它： 

    [![](buttons-images/storyboard03.png "[事件] 索引標籤")](buttons-images/storyboard03.png#lightbox)
1. 您將會自動切換至 [ `ViewController.cs`觀看]，讓您可以使用**向上**和**向下**鍵，在程式碼中放置新動作： 

    [![](buttons-images/storyboard04.png "在程式碼中放置新動作")](buttons-images/storyboard04.png#lightbox)
1. 按下**enter**鍵以選取位置： 

    [![](buttons-images/storyboard05.png "程式碼編輯器")](buttons-images/storyboard05.png#lightbox)
1. 將變更儲存至所有檔案。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**中，按兩下`Main.storyboard`檔案，然後開啟檔案進行編輯。
1. 拖曳 **按鈕** 從 **程式庫** 並將它放在檢視上： 

    [![](buttons-images/storyboard01vs.png "按鈕")](buttons-images/storyboard01vs.png#lightbox)
1. 在 [**屬性] 瀏覽器**中，您可以調整按鈕的數個屬性，例如其**標題**和**文字色彩**： 

    [![](buttons-images/storyboard02vs.png "屬性瀏覽器")](buttons-images/storyboard02vs.png#lightbox)
1. 接下來，切換至 [**事件]** 索引標籤，然後從**按鈕**連接**事件**並呼叫`ButtonPressed`它： 

    [![](buttons-images/storyboard03vs.png "[事件] 索引標籤")](buttons-images/storyboard03vs.png#lightbox)
1. 將變更儲存至所有檔案。



編輯您的 View Controller （ `ViewController.cs`範例）檔案，並新增下列程式碼來處理所選取的按鈕：


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

只要按鈕的`Enabled`屬性為`true` ，而且它不是由另一個控制項或視圖所涵蓋，就可以使用 Siri 遠端，將它設為焦點專案。 如果使用者選取按鈕並按一下觸控介面，則會執行上述`ButtonPressed`定義的動作。

> [!IMPORTANT]
> 雖然`UIButton`在建立**事件處理常式**時，可以`TouchUpInside`在 iOS 設計工具中將動作（例如）指派給，但絕不會呼叫它，因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 建立 tvOS 使用者介面元素的**動作**時，您應該一律使用預設**動作類型**。




如需使用分鏡腳本的詳細資訊，請參閱我們的[Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>按鈕和程式碼

您`UIButton`可以選擇性地在程式碼C#中建立，並將其新增至 tvOS 應用程式的 view。 例如：

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

當您在程式碼`UIButton`中建立新的時， `UIButtonType`您會將其指定為下列其中一項：

- **System** -這是 tvOS 所呈現的標準按鈕類型，而且是您最常使用的類型。
- **DetailDisclosure** -提供「關閉」類型的按鈕，用來隱藏或顯示詳細資訊。
- **InfoDark** -深色詳細資訊按鈕在圓形中顯示 "i"。
- **InfoLight** -淺色詳細資訊按鈕會在圓形中顯示 "i"。
- **AddContact** -將按鈕顯示為 [加入連絡人] 按鈕。
- [**自訂**]-可讓您自訂按鈕的數個特性。

接下來，您要定義按鈕的螢幕大小和位置。 範例：

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

然後，設定按鈕的標題。 `UIButtons`與大部分`UIKit`的控制項不同，因為它們具有狀態，所以您不能只是變更標題，而必須針對指定`UIControlState`的進行變更。 例如：

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

接下來，使用`AllEvents`事件來查看使用者按一下按鈕的時間。 範例：

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

最後，您可以將按鈕新增至視圖以顯示它：

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> 雖然可以將動作（例如`TouchUpInside` `UIButton`）指派給，但永遠不會呼叫它，因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 您應該一律使用**AllEvents**或**PrimaryActionTriggered**之類的事件。




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>設定按鈕的樣式

tvOS 提供的數個屬性`UIButton` ，可用來提供其標題，並使用背景色彩和影像等專案來為其樣式。

<a name="Button-Titles" />

### <a name="button-titles"></a>按鈕標題

如先前所見， `UIButtons`與大部分`UIKit`的控制項不同，因為它們具有狀態，所以您不能只是變更標題，而必須針對指定`UIControlState`的進行變更。 例如：

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

您可以使用`SetTitleColor`方法來設定按鈕的標題色彩。 例如：

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

而且您可以使用`SetTitleShadowColor`來調整標題的陰影。 例如：

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

使用下列程式碼反白顯示按鈕時，您可以將標題陰影設定為從*陰*文變更為*浮凸*：

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

此外，您可以使用屬性化文字作為按鈕的標題。 例如：

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>按鈕影像

`UIButton`可以有附加的影像，並且可以使用影像做為其背景。

若要為指定`UIControlState`的設定按鈕的背景影像，請使用下列程式碼：

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

將屬性設定為`true` ，以在反白顯示按鈕時，將影像繪製為較淡（這是預設值）。 `AdjustsImageWhenHiglighted` 將屬性設定為`true` ，以在停用按鈕時將影像繪製為較暗（同樣地，這是預設值）。 `AdjustsImageWhenDisabled`

若要設定顯示在按鈕上的影像，請使用下列程式碼：

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

您可以使用屬性來設定同時套用至標題和按鈕影像的色彩色調。 `TintColor` 對於`Custom`類型的按鈕，此屬性沒有任何作用，您必須自行`TintColor`執行行為。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋設計和使用 tvOS 應用程式內的按鈕。 它示範如何使用 iOS 設計工具中的按鈕，以及如何在程式碼中C#建立按鈕。 最後，它會示範如何修改按鈕的標題，以及變更其樣式和外觀。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
