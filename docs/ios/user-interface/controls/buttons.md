---
title: 按鈕
description: UIButton 類別用於表示按鈕 iOS 螢幕中的各種不同的樣式。 本節將介紹 iOS 中的按鈕所使用的不同選項。
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: c2c33103c005a5ed567b1c4703846f887d824ac4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="buttons"></a>按鈕

_UIButton 類別用於表示按鈕 iOS 螢幕中的各種不同的樣式。本節將介紹 iOS 中的按鈕所使用的不同選項。_

`UIButton`類別表示在 iOS 中的按鈕控制項。 

可以在編輯按鈕內容`Properties Pad`iOS 設計工具：


![](buttons-images/properties.png "IOS 設計工具屬性頁預留空間")

## <a name="creating-a-button"></a>建立按鈕

UIButton 可以透過只需幾行程式碼中建立。

首先，具現化新的按鈕，然後指定您需要的按鈕的型別：

```csharp
UIButton myButton = new UIButton(UIButtonType.System);
```

UIButtonType 應該指定為下列其中一項：

- **系統**-這是 iOS 所使用的標準按鈕類型，而且您最常使用的類型。
- **DetailDisclosure** -顯示 「 關閉 」 的按鈕類型可用來隱藏或顯示詳細的資訊。
- **InfoDark** -在深色詳細的資訊是圓形中有顯示"i"的按鈕。
- **InfoLight** -光線的詳細資訊是圓形中有顯示"i"的按鈕。
- **AddContact** -新增連絡人 按鈕以顯示  按鈕。
- **自訂**-可讓您自訂按鈕的數個特性。

按鈕類型的相關資訊位於[按鈕類型](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/create_different_types_of_buttons/)配方。

接著，可以定義螢幕大小和按鈕的位置。 範例：

```csharp
myButton.Frame = new CGRect (25, 25, 300, 150);
```

若要變更按鈕的文字，請使用`SetTitle`按鈕，要求您設定的文字字串的屬性和`UIControlStyle`。 例如: 

```csharp
myButton.SetTitle("Hello, World!", UIControlState.Normal);
```

設定每個狀態不同的屬性可讓您將多個資訊傳達給使用者 （例如 請的文字色彩的停用狀態的灰色）。 您可以使用 iOS 設計工具中，每個狀態之間切換，或以程式設計方式執行。 如需有關設定按鈕文字和狀態的詳細資訊，請參閱[設定按鈕文字](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/set_button_text/)配方。

## <a name="dealing-with-user-interactions"></a>處理使用者互動


按鈕不是非常有用的除非它們執行某個動作時按下 ！ 

在 iOS 上按鈕的事件會幾乎觸控事件，以讓它使用互動與螢幕上的按鈕。 列出所有可能的 ui 控制事件的清單[這裡](https://developer.apple.com/documentation/uikit/uicontrolevents)，但在 iOS 上最常使用的事件是`TouchUpInside`。 然後，您可以建立以做到一旦按下按鈕的事件處理常式：


```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

### <a name="adding-events-in-the-ios-designer"></a>加入 iOS 設計工具中的事件
 
使用屬性輸入板中的 [事件] 索引標籤，將事件加入至控制項。

選取事件，並輸入新的事件處理常式或從清單中選取其中一個的名稱。 如此一來，將檢視控制器類別中建立新的部分方法。

![事件索引標籤](buttons-images/image1.png)

## <a name="styling-a-button"></a>設定按鈕樣式

UIButtons 不同於大部分 UIKit 控制，因為它們具有狀態，因此您無法只變更標題，您必須變更每個`UIControlState`。 設定標題色彩和陰影色彩的方式是以類似的方式：

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

此外，您可以使用屬性化的文字做為按鈕的標題。 例如: 

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>自訂的按鈕類型


當您將`Custom`按鈕類型，該物件具有任何預設呈現方式。 您可以藉由設定不同狀態的影像設定按鈕的外觀。 例如，下列程式碼示範如何將不同的映像`Normal`，`Highlighted`和`Selected`狀態：


```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```


根據是否使用者是否接觸 按鈕，它將會轉譯為下列映像的其中一個 (`Normal`，`Highlighted`和`Selected`狀態分別):


![](buttons-images/image22.png "UIButton 狀態正常")
![](buttons-images/image23.png "UIButton 狀態反白顯示")
![](buttons-images/image24.png "選取 UIButton 狀態")

如需使用自訂按鈕的詳細資訊，請參閱[按鈕使用的映像](https://developer.xamarin.com/recipes/ios/standard_controls/buttons/use_an_image_for_a_button/)。


## <a name="related-links"></a>相關連結

- [UIButton 活頁簿](https://developer.xamarin.com/workbooks/ios/user-interface/UIbutton/uibutton.workbook)
