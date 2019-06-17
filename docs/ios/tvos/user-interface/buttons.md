---
title: 使用 tvOS 按鈕，在 Xamarin 中
description: 本文件說明如何使用 xamarin 建置的 tvOS 應用程式中的按鈕。 它討論如何使用按鈕和 分鏡腳本，程式碼中，它會檢查如何設定按鈕樣式。
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/07/2017
ms.openlocfilehash: 6d8fc1daaced24dccead78c4f9d0e5d0959b3755
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61198978"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>使用 tvOS 按鈕，在 Xamarin 中

使用的執行個體`UIButton`tvOS 視窗中建立一個可焦點化、 可選取按鈕的類別。 當使用者選取按鈕時，它會傳送動作訊息至目標物件允許您 Xamarin.tvOS 應用程式回應使用者的輸入。

[![](buttons-images/buttons01.png "範例按鈕")](buttons-images/buttons01.png#lightbox)

如需有關使用焦點，然後瀏覽使用 Siri 遠端的詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)並[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。

<a name="About-Buttons" />

## <a name="about-buttons"></a>關於按鈕

在 tvOS 按鈕用於應用程式特定的動作，而且可能包含標題、 圖示或兩者。 當使用者瀏覽應用程式的使用者介面使用[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)，焦點會轉移至指定的按鈕，讓變更文字和背景色彩。 陰影也會套用至加入 3D 效果，讓它以高於其餘的使用者介面顯示的按鈕。

[![](buttons-images/buttons01.png "範例按鈕")](buttons-images/buttons01.png#lightbox)

Apple 已處理按鈕的下列建議：

- **使用標題或圖示的**同時在這兩個圖示和標題可以包含在按鈕空間有限，因此請盡量不結合這兩。
- **清楚標示破壞性按鈕**-如果按鈕會執行破壞性動作 （例如刪除檔案），清楚地將它標示這樣使用文字及/或圖示。 破壞性動作應該一律會出具[警示](~/ios/tvos/user-interface/alerts.md)詢問使用者是否要限制的動作。
- **不使用 [上一步] 按鈕**-Siri 遠端上的 [功能表] 按鈕來返回上一個畫面。 此規則的一個例外狀況是在應用程式內購買或破壞性動作的地方**取消**按鈕應該會顯示。

如需有關使用焦點和瀏覽的詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)文件。

<a name="Button-Icons" />

### <a name="button-icons"></a>按鈕圖示

Apple 建議，您會使用簡單、 高度可辨識的映像按鈕圖示。 過於複雜的圖示難以遙控器在沙發上輕鬆辨識在電視螢幕上，因此請盡量使用的最簡單的表示法可能能夠意會就好。 可能的話，使用標準、 已知圖示 （例如搜尋的放大鏡） 的映像。

<a name="Button-Titles" />

### <a name="button-titles"></a>按鈕的標題

建立您的按鈕標題時，Apple 會有下列建議：

- **顯示以下圖示按鈕描述性文字**-如有可能，下方的圖示，將清除的、 描述性文字只按鈕，以進一步取得跨按鈕的用途。
- **用於標題中的動詞或動詞片語**-清楚將採取的動作時，請放置使用者按一下按鈕。
- **使用標題樣式的大寫**-的文章、 nor 或介係詞除外 (四個字母或更少)，每個單字的按鈕的標題應該為大寫。
- **使用簡短的來點標題**-使用的最短的可能用語來說明按鈕的動作。

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>按鈕和分鏡腳本

Xamarin.tvOS 應用程式中的按鈕所使用的最簡單方式是將它們新增至使用 Xamarin 的設計工具，適用於 iOS 的應用程式的 UI。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)


1. 在 **方案總管**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳 **按鈕** 從 **程式庫** 並將它放在檢視上： 

    [![](buttons-images/storyboard01.png "按鈕")](buttons-images/storyboard01.png#lightbox)
1. 在 **屬性總管**，您可以調整按鈕的數個屬性，例如其**標題**並**文字色彩**: 

    [![](buttons-images/storyboard02.png "按鈕屬性")](buttons-images/storyboard02.png#lightbox)
1. 接下來，切換至**事件] 索引標籤**和 [連接**事件**從**按鈕**並呼叫它`ButtonPressed`: 

    [![](buttons-images/storyboard03.png "[事件] 索引標籤")](buttons-images/storyboard03.png#lightbox)
1. 您將會自動切換到`ViewController.cs`您可以將新的動作放在程式碼中使用的檢視**向上**並**向下**方向鍵： 

    [![](buttons-images/storyboard04.png "在程式碼中放置新的動作")](buttons-images/storyboard04.png#lightbox)
1. 按下**Enter**選取位置： 

    [![](buttons-images/storyboard05.png "程式碼編輯器")](buttons-images/storyboard05.png#lightbox)
1. 儲存所有檔案的變更。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 **方案總管**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳 **按鈕** 從 **程式庫** 並將它放在檢視上： 

    [![](buttons-images/storyboard01vs.png "按鈕")](buttons-images/storyboard01vs.png#lightbox)
1. 在 **屬性總管**，您可以調整按鈕的數個屬性，例如其**標題**並**文字色彩**: 

    [![](buttons-images/storyboard02vs.png "屬性總管")](buttons-images/storyboard02vs.png#lightbox)
1. 接下來，切換至**事件] 索引標籤**和 [連接**事件**從**按鈕**並呼叫它`ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "[事件] 索引標籤")](buttons-images/storyboard03vs.png#lightbox)
1. 儲存所有檔案的變更。



編輯您的檢視控制器 (範例`ViewController.cs`) 檔案，並新增下列程式碼來處理所選取的按鈕：


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

只要一個按鈕`Enabled`屬性是`true`並不受到另一個控制項或檢視，可使用 Siri 遠端單元的焦點項目。 如果使用者選取按鈕，然後按一下 觸控介面時，`ButtonPressed`想執行上述定義的動作。

> [!IMPORTANT]
> 雖然您可以指派動作，例如`TouchUpInside`要`UIButton`在 iOS 設計工具建立時**事件處理常式**，它將永遠不可叫用因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 您應該一律使用預設值**動作類型**建立時**動作**tvOS 使用者介面項目。




如需有關使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>按鈕和程式碼

（選擇性）`UIButton`可以在中建立C#程式碼，並加入 tvOS 應用程式的檢視。 例如: 

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

當您建立新`UIButton`在程式碼中，您可以指定其`UIButtonType`為下列其中之一：

- **系統**-這是標準的 tvOS 所呈現的按鈕類型，而且您最常使用的型別。
- **DetailDisclosure** -會顯示 [開啟向下] 按鈕用來隱藏或顯示詳細的資訊類型。
- **InfoDark** -暗色調的詳細資訊 按鈕顯示在圓形中的"i"。
- **InfoLight** -光線的詳細資訊 按鈕顯示在圓形中的"i"。
- **AddContact** -顯示為 [新增連絡人] 按鈕的按鈕。
- **自訂**-可讓您自訂按鈕的數個特性。

接下來，您定義螢幕大小和位置 按鈕。 範例：

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

然後，設定按鈕的標題。 `UIButtons` 不同於大部分`UIKit`控制項，因為它們具有狀態，因此您不能只是將標題變更，您必須將它用於變更指定`UIControlState`。 例如: 

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

接下來，使用`AllEvents`事件以了解當使用者按一下按鈕。 範例：

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

最後，您將按鈕加入要顯示它的檢視：

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> 雖然您可以指派動作，例如`TouchUpInside`至`UIButton`，它將永遠不可叫用因為 Apple TV 沒有觸控式螢幕或支援觸控事件。 例如，您應該一律使用事件**AllEvents**或是**PrimaryActionTriggered**。




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>設定按鈕的樣式

tvOS 提供的數個屬性`UIButton`，可用來提供其標題，並與背景色彩和影像等項目設定樣式。

<a name="Button-Titles" />

### <a name="button-titles"></a>按鈕的標題

如我們所見，上述`UIButtons`不同於大部分`UIKit`控制項，因為它們具有狀態，因此您不能只是將標題變更，您必須將它用於變更指定`UIControlState`。 例如: 

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

您可以設定按鈕使用的標題色彩`SetTitleColor`方法。 例如: 

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

您可以調整標題的陰影使用`SetTitleShadowColor`。 例如: 

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

您可以設定從變更標題陰影*Engraved*要*浮雕*按鈕反白顯示，使用下列程式碼時：

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

此外，您可以使用屬性化的文字，做為按鈕的標題。 例如: 

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>按鈕的影像

A`UIButton`能連接到它的映像，而且可以使用映像做為其背景。

若要設定按鈕的背景影像指定`UIControlState`，使用下列程式碼：

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

設定`AdjustsImageWhenHiglighted`屬性設`true`按鈕會反白顯示時，繪製較淺的映像 （這是預設值）。 設定`AdjustsImageWhenDisabled`屬性設`true`繪製較暗的映像，當按鈕已停用 （同樣地，這是預設值）。

若要設定顯示在按鈕上的映像，請使用下列程式碼：

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

使用`TintColor`屬性來設定會套用至標題和按鈕的影像色彩濃淡。 按鈕的`Custom`型別，這個屬性沒有任何作用，您必須實作`TintColor`行為自己。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和使用 Xamarin.tvOS 應用程式內的按鈕。 它示範如何使用 iOS 設計工具中的按鈕以及如何建立按鈕，在C#程式碼。 最後，它會示範如何修改按鈕的標題，並變更其樣式和外觀。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
