---
title: "使用按鈕"
description: "本文涵蓋設計和使用 Xamarin.tvOS 應用程式內的按鈕。"
ms.topic: article
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/07/2017
ms.openlocfilehash: 5da834e7e77fb8ab080c124bd09653bc840be3b0
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-buttons"></a>使用按鈕

_本文涵蓋設計和使用 Xamarin.tvOS 應用程式內的按鈕。_


使用的執行個體`UIButton`tvOS 視窗中建立可設定焦點，可選取按鈕的類別。 當使用者選取按鈕時，會傳送動作訊息至目標物件允許您 Xamarin.tvOS 應用程式的回應使用者的輸入。

[![](buttons-images/buttons01.png "範例按鈕")](buttons-images/buttons01.png#lightbox)

如需有關使用具有焦點，以及使用 Siri 遠端巡覽的詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)和[Siri 遠端和藍芽控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。

<a name="About-Buttons" />

## <a name="about-buttons"></a>關於按鈕

中 tvOS，按鈕會用於應用程式的特定動作，而且可能包含標題、 圖示或兩者。 當使用者瀏覽應用程式的使用者介面使用[Siri 遠端](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)，焦點會轉移至指定的按鈕，讓您變更文字和背景色彩。 陰影也會套用至加入 3D 效果，讓它顯示上端其餘的使用者介面上方的按鈕。

[![](buttons-images/buttons01.png "範例按鈕")](buttons-images/buttons01.png#lightbox)

Apple 已處理按鈕的下列建議：

- **使用標題或的圖示**-時這兩個圖示和標題可以包含在按鈕空間有限，因此盡量不要結合兩者。
- **清楚地標記破壞性按鈕**-如果破壞性 按鈕會執行動作 （例如刪除檔案），清楚地將它標示為使用文字及/或圖示。 破壞性動作應該一律會出具[警示](~/ios/tvos/user-interface/alerts.md)詢問使用者，將限制動作。
- **不使用上一步按鈕**-Siri 遙控器上的功能表按鈕用來傳回上一個螢幕。 此規則的一個例外狀況是在應用程式內購買或破壞性動作的地方**取消**應顯示按鈕。

如需使用焦點時，瀏覽的詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)文件。

<a name="Button-Icons" />

### <a name="button-icons"></a>按鈕圖示

Apple 建議您使用簡單且高度可辨識的影像按鈕圖示。 任何過度複雜圖示難以遙控器在沙發上輕鬆辨識在電視螢幕上，所以請嘗試使用的最簡單的表示法可能取得概念。 您應該盡可能使用標準，也知道映像 （例如搜尋放大鏡） 的圖示。

<a name="Button-Titles" />

### <a name="button-titles"></a>按鈕標題

建立標題為按鈕時，Apple 有下列建議：

- **顯示以下圖示按鈕描述性文字**-圖示下方將清除的、 描述性文字，可能只按鈕，以進一步 get 跨按鈕的用途。
- **用於標題中的動詞或動詞片語**-清楚將採取的動作時，放入使用者按一下按鈕。
- **使用標題樣式的大小寫**-除了文件、 nor 或介系詞 (四個字母或更少)，每個字的按鈕的標題應該第一個字母大寫。
- **使用簡短，以點標題**-使用最短的可能用語描述按鈕的動作。

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>按鈕和分鏡腳本

使用按鈕在 Xamarin.tvOS 應用程式中的最簡單方式是將它們新增到使用 Xamarin 設計工具適用於 iOS 的應用程式的 UI。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)


1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**按鈕**從**文件庫**並將它放在檢視上： 

    [![](buttons-images/storyboard01.png "按鈕")](buttons-images/storyboard01.png#lightbox)
1. 在**屬性總管**，您可以調整按鈕的數個屬性，例如其**標題**和**文字色彩**: 

    [![](buttons-images/storyboard02.png "按鈕屬性")](buttons-images/storyboard02.png#lightbox)
1. 接下來，切換至**事件 索引標籤**和連接**事件**從**按鈕**並呼叫它`ButtonPressed`: 

    [![](buttons-images/storyboard03.png "[事件] 索引標籤")](buttons-images/storyboard03.png#lightbox)
1. 您將會自動切換到`ViewController.cs`檢視您可以將新的動作放在程式碼中使用**向上**和**向**方向鍵： 

    [![](buttons-images/storyboard04.png "在程式碼中放置新的動作")](buttons-images/storyboard04.png#lightbox)
1. 按**Enter**選取的位置： 

    [![](buttons-images/storyboard05.png "程式碼編輯器")](buttons-images/storyboard05.png#lightbox)
1. 儲存所有檔案的變更。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案，並開啟它進行編輯。
1. 拖曳**按鈕**從**文件庫**並將它放在檢視上： 

    [![](buttons-images/storyboard01vs.png "按鈕")](buttons-images/storyboard01vs.png#lightbox)
1. 在**屬性總管**，您可以調整按鈕的數個屬性，例如其**標題**和**文字色彩**: 

    [![](buttons-images/storyboard02vs.png "屬性總管")](buttons-images/storyboard02vs.png#lightbox)
1. 接下來，切換至**事件 索引標籤**和連接**事件**從**按鈕**並呼叫它`ButtonPressed`: 

    [![](buttons-images/storyboard03vs.png "[事件] 索引標籤")](buttons-images/storyboard03vs.png#lightbox)
1. 儲存所有檔案的變更。



編輯檢視控制器 (範例`ViewController.cs`) 檔案，然後加入下列程式碼來處理選取的按鈕：


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

只要按鈕`Enabled`屬性是`true`而且它未涵蓋其他控制項或檢視，可以設為使用 Siri 遠端的焦點在項目。 如果使用者選取的按鈕，按一下 [觸控] 介面`ButtonPressed`上述定義的動作就會執行。

> [!IMPORTANT]
> 雖然您可以指派動作，例如`TouchUpInside`至`UIButton`iOS 建立時的設計工具中**事件處理常式**，它會永遠不會呼叫因為 Apple TV 沒有觸控螢幕或支援觸控事件。 您應該一律使用預設**動作類型**建立時**動作**tvOS 使用者介面項目。




如需使用分鏡腳本的詳細資訊，請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>按鈕和程式碼

（選擇性）`UIButton`可以在 C# 程式碼中建立並加入 tvOS 應用程式的檢視。 例如: 

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

當您建立新`UIButton`在程式碼中，您可以指定其`UIButtonType`做為下列其中一項：

- **系統**-這是標準的 tvOS 所呈現的按鈕類型，而且您最常使用的類型。
- **DetailDisclosure** -顯示 「 關閉 」 的按鈕類型可用來隱藏或顯示詳細的資訊。
- **InfoDark** -在深色詳細的資訊是圓形中有顯示"i"的按鈕。
- **InfoLight** -光線的詳細資訊是圓形中有顯示"i"的按鈕。
- **AddContact** -新增連絡人 按鈕以顯示  按鈕。
- **自訂**-可讓您自訂按鈕的數個特性。

接著，您可以定義螢幕大小和按鈕的位置。 範例：

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

然後，設定按鈕的標題。 `UIButtons` 不同於大部分`UIKit`控制項，因為它們具有狀態，因此您不能只變更標題，您必須變更它的給定`UIControlState`。 例如: 

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

接下來，使用`AllEvents`事件，以查看使用者已按一下按鈕時。 範例：

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

最後，您將按鈕加入到檢視來顯示它：

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> 雖然您可以指派動作，例如`TouchUpInside`至`UIButton`，它會永遠不會呼叫因為 Apple TV 沒有觸控螢幕或支援觸控事件。 例如，您應該一律使用事件**AllEvents**或**PrimaryActionTriggered**。




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>設定按鈕樣式

tvOS 提供數個屬性`UIButton`，可用來提供其標題並設定樣式的背景色彩和影像等。

<a name="Button-Titles" />

### <a name="button-titles"></a>按鈕標題

如我們所見上方、`UIButtons`不同於大部分`UIKit`控制項，因為它們具有狀態，因此您不能只變更標題，您必須變更它的給定`UIControlState`。 例如: 

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

您可以設定標題色彩按鈕使用`SetTitleColor`方法。 例如: 

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

您可以調整項目的陰影使用`SetTitleShadowColor`。 例如: 

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

您可以設定從變更標題陰影*Engraved*至*浮雕*按鈕會反白顯示時使用下列程式碼：

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

此外，您可以使用屬性化的文字做為按鈕的標題。 例如: 

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>按鈕影像

A`UIButton`可能會連接到它的映像，而且可以將影像當做它的背景。

若要設定的按鈕的背景影像給定`UIControlState`，使用下列程式碼：

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

設定`AdjustsImageWhenHiglighted`屬性`true`按鈕會反白顯示時，繪製較淡的映像 （這是預設值）。 設定`AdjustsImageWhenDisabled`屬性`true`繪製深影像按鈕已停用時 （同樣地，這是預設值）。

若要設定顯示在按鈕上的映像，請使用下列程式碼：

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

使用`TintColor`屬性來設定套用至標題和按鈕的影像色彩濃淡。 按鈕的`Custom`型別，這個屬性沒有任何作用，您必須實作`TintColor`行為自己。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋設計和使用 Xamarin.tvOS 應用程式內的按鈕。 它示範如何使用 iOS 設計工具中的按鈕以及如何在 C# 程式碼中建立按鈕。 最後，它會示範如何修改的按鈕標題，並變更其樣式和外觀。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
