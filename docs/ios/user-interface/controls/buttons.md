---
title: Xamarin 中的按鈕
description: UIButton 類別是用來表示 iOS 畫面中各種不同的按鈕樣式。 本指南介紹在 iOS 中使用按鈕的不同選項。
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2018
ms.openlocfilehash: 2de52400241d45046f58222231b8d865ecf6666d
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602797"
---
# <a name="buttons-in-xamarinios"></a>Xamarin 中的按鈕

在 iOS 中， `UIButton` 類別代表按鈕控制項。

您可以透過程式設計方式或使用 Xcode 的 Interface Builder 來修改按鈕的屬性。

## <a name="creating-a-button-programmatically"></a>以程式設計方式建立按鈕

`UIButton`只要幾行程式碼就能建立。

- 將按鈕具現化，並指定其類型：

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  按鈕的類型是由屬性所指定 `UIButtonType` ：

  - `UIButtonType.System` -一般用途按鈕
  - `UIButtonType.DetailDisclosure` -表示詳細資訊的可用性，通常是關於資料表中的特定專案
  - `UIButtonType.InfoDark` -表示設定資訊的可用性;深色色彩
  - `UIButtonType.InfoLight` -表示設定資訊的可用性;淺彩色
  - `UIButtonType..AddContact` -表示可以加入連絡人
  - `UIButtonType.Custom` -可自訂按鈕

  如需不同按鈕類型的詳細資訊，請參閱：
  
  - 本檔的 [自訂按鈕類型](#custom-button-types) 區段
  - [按鈕類型](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons)配方
  - Apple 的 [IOS 人體介面指導方針](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/)。

- 定義按鈕的大小和位置：

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- 設定按鈕的文字。 使用 `SetTitle` 需要文字和 `UIControlState` 值的方法，以取得按鈕狀態：

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```
  
  按鈕的狀態類型如下所示：
  
  - `UIControlState.Normal`
  - `UIControlState.Highlighted`
  - `UIControlState.Disabled`
  - `UIControlState.Selected`
  - `UIControlState.Focused`
  - `UIControlState.Application`
  - `UIControlState.Reserved`
  
  如需設定按鈕樣式和設定其文字的詳細資訊，請參閱：

  - 本檔中的[按鈕區段樣式](#styling-a-button)
  - [設定按鈕文字](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text)配方。

## <a name="handling-a-button-tap"></a>處理按鈕分流

若要回應按鈕的按鍵，請提供按鈕事件的處理常式 `TouchUpInside` ：

```csharp
myButton.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` 不是唯一可用的按鈕事件。 `UIButton` 是的子類別 `UIControl` ，它會定義 [許多不同的事件](xref:UIKit.UIControlEvent)。

## <a name="styling-a-button"></a>設定按鈕樣式

`UIButton` 控制項可以存在於許多不同的狀態中，每個狀態都是由 `UIControlState` 值（、、、等等）所指定 `Normal` `Disabled` `Focused` `Highlighted` 。每個狀態都可以指定為唯一的樣式，以程式設計方式或使用 iOS 設計工具來指定。

> [!NOTE]
> 如需所有值的完整清單 `UIControlState` ，請參閱 [`UIKit.UIControlState enumeration`](xref:UIKit.UIControlState)
> 文件。

例如，若要設定的標題色彩和陰影色彩 `UIControlState.Normal` ：

```csharp
myButton.SetTitleColor(UIColor.White, UIControlState.Normal);
myButton.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

下列程式碼會將按鈕標題設定為屬性化 (和的樣式) 字串 `UIControlState.Normal` `UIControlState.Highlighted` ：

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>自訂按鈕類型

具有 of `UIButtonType` 的按鈕 `Custom` 沒有預設樣式。 不過，您可以設定影像的不同狀態來設定按鈕的外觀：

```csharp
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
myButton.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

根據使用者是否觸及按鈕而定，它會轉譯為下列其中一個影像 (`UIControlState.Normal` `UIControlState.Highlighted` 和 `UIControlState.Selected` 狀態分別) ：

![UIControlState. Normal](buttons-images/image22.png "UIControlState. Normal") 
![UIControlState 反白顯示](buttons-images/image23.png "UIControlState 反白顯示") 
![UIControlState。已選取](buttons-images/image24.png "UIControlState。已選取")

如需使用自訂按鈕的詳細資訊，請參閱 [使用按鈕](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button) 配方的影像。
