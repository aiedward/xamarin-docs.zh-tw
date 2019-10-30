---
title: 在 Xamarin 中的按鈕
description: UIButton 類別是用來代表 iOS 畫面中各種不同的按鈕樣式。 本指南介紹在 iOS 中使用按鈕的不同選項。
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2018
ms.openlocfilehash: a8dfd267fe9f5f838927fc216d53c2475398ed16
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022119"
---
# <a name="buttons-in-xamarinios"></a>在 Xamarin 中的按鈕

在 iOS 中，`UIButton` 類別代表按鈕控制項。

您可以用程式設計的方式或使用 iOS 設計工具的**Properties Pad**來修改按鈕的屬性：

![IOS 設計工具的 Properties Pad](buttons-images/properties.png "IOS 設計工具的 Properties Pad")

## <a name="creating-a-button-programmatically"></a>以程式設計方式建立按鈕

只能使用幾行程式碼來建立 `UIButton`。

- 具現化按鈕並指定其類型：

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  按鈕的類型是由 `UIButtonType`所指定：

  - `UIButtonType.System`-一般用途按鈕
  - `UIButtonType.DetailDisclosure`-表示詳細資訊的可用性，通常是關於資料表中的特定專案
  - `UIButtonType.InfoDark`-指出設定資訊的可用性;深色色彩
  - `UIButtonType.InfoLight`-指出設定資訊的可用性;淺彩色
  - `UIButtonType..AddContact`-表示可以新增連絡人
  - `UIButtonType.Custom` 可自訂按鈕

  如需不同按鈕類型的詳細資訊，請參閱：
  
  - 本檔的[自訂按鈕類型](#custom-button-types)一節
  - [按鈕類型](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons)配方
  - Apple 的[IOS 人力介面指導方針](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/)。

- 定義按鈕的大小和位置：

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- 設定按鈕的文字。 使用 `SetTitle` 方法，這需要文字和 `UIControlState` 值：

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  如需如何設定按鈕和其文字樣式的詳細資訊，請參閱：

  - 本檔的設定[按鈕樣式](#styling-a-button)一節
  - [[設定] 按鈕文字](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text)配方。

## <a name="handling-a-button-tap"></a>處理按鈕點按鍵

若要回應按鈕點按動作，請提供按鈕 `TouchUpInside` 事件的處理常式：

```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` 不是唯一可用的按鈕事件。 `UIButton` 是 `UIControl`的子類別，其定義了[許多不同的事件](xref:UIKit.UIControlEvent)。

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>使用 iOS 設計工具來指定按鈕事件處理常式

使用**Properties Pad**的 [**事件**] 索引標籤，指定按鈕各種事件的事件處理常式。

針對適當的事件，請輸入新事件處理常式的名稱，或從清單中選取一個。 這麼做會在按鈕的視圖控制器的程式碼中建立事件處理常式。

![Properties Pad 的 [事件] 索引標籤](buttons-images/image1.png "Properties Pad 的 [事件] 索引標籤")

## <a name="styling-a-button"></a>設定按鈕的樣式

`UIButton` 控制項可以存在於數個不同的狀態中，每個都是由 `UIControlState` 值所指定– `Normal`、`Disabled`、`Focused`、`Highlighted`等等。每個狀態可以提供獨特的樣式，並以程式設計方式或使用 iOS 設計工具指定。

> [!NOTE]
> 如需所有 `UIControlState` 值的完整清單，請查看[`UIKit.UIControlState enumeration`](xref:UIKit.UIControlState)
> 附帶.

例如，若要設定 `UIControlState.Normal`的標題色彩和陰影色彩：

```csharp
button.SetTitleColor(UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

下列程式碼會將按鈕標題設定為 `UIControlState.Normal` 和 `UIControlState.Highlighted`的屬性化（樣式）字串：

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>自訂按鈕類型

具有 `Custom` `UIButtonType` 的按鈕沒有預設樣式。 不過，您可以設定按鈕的外觀，方法是為其不同的狀態設定影像：

```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

根據使用者是否觸及按鈕而定，它會轉譯為下列其中一個影像（分別是`UIControlState.Normal`、`UIControlState.Highlighted` 和 `UIControlState.Selected` 狀態）：

![UIControlState。 Normal](buttons-images/image22.png "UIControlState. Normal")
![UIControlState。已反白顯示](buttons-images/image23.png "UIControlState。反白顯示")
![UIControlState。已選取](buttons-images/image24.png "UIControlState。已選取")

如需使用自訂按鈕的詳細資訊，請參閱[使用影像](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button)做為按鈕配方。
