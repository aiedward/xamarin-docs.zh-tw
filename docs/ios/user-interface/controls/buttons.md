---
title: 在 Xamarin.iOS 中的按鈕
description: 標記的 UIButton 類別用來代表各種不同的 iOS 畫面的按鈕樣式。 本指南介紹在 iOS 中的按鈕所使用的不同選項。
ms.prod: xamarin
ms.assetid: 304229E5-8FA8-41BD-8563-D19E1D2A0296
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/11/2018
ms.openlocfilehash: 35fc743944c04dd1fdb8e035ba94ad6aeb6156ea
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "38986000"
---
# <a name="buttons-in-xamarinios"></a>在 Xamarin.iOS 中的按鈕

在 iOS 中，`UIButton`類別表示的按鈕控制項。

可以修改按鈕的屬性，以程式設計方式或使用**Properties Pad**的 「 iOS 設計工具：

![IOS 設計工具的 Properties Pad](buttons-images/properties.png "Properties Pad 的 「 iOS 設計工具")

## <a name="creating-a-button-programmatically"></a>以程式設計方式建立按鈕

A`UIButton`可以建立只需幾行程式碼。

- 具現化按鈕，並指定其型別：

  ```csharp
  UIButton myButton = new UIButton(UIButtonType.System);
  ```

  按鈕的類型由`UIButtonType`:

  - `UIButtonType.System` -一般用途的按鈕
  - `UIButtonType.DetailDisclosure` -代表可用性的詳細資訊，通常都是關於在資料表中的特定項目
  - `UIButtonType.InfoDark` -代表可用性的組態資訊;暗色調
  - `UIButtonType.InfoLight` -代表可用性的組態資訊;淺色
  - `UIButtonType..AddContact` -表示可以加入連絡人
  - `UIButtonType.Custom` 自訂按鈕

  如需有關不同的按鈕類型的詳細資訊，請看看：
  
  - [自訂的按鈕類型](#custom-button-types)本文件章節
  - [按鈕類型](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/create_different_types_of_buttons)配方
  - Apple [iOS 人性化介面指導方針](https://developer.apple.com/design/human-interface-guidelines/ios/controls/buttons/)。

- 定義按鈕的大小和位置：

  ```csharp
  myButton.Frame = new CGRect(25, 25, 300, 150);
  ```

- 設定按鈕的文字。 使用`SetTitle`方法，它需要的文字和`UIControlState`值：

  ```csharp
  myButton.SetTitle("Hello, World!", UIControlState.Normal);
  ```

  如需有關設定按鈕的樣式，並設定它的文字的詳細資訊，請參閱：

  - [樣式按鈕](#styling-a-button)本文件章節
  - [設定按鈕文字](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/set_button_text)配方。

## <a name="handling-a-button-tap"></a>處理只要點選按鈕

若要回應點選按鈕，提供的處理常式的按鈕`TouchUpInside`事件：

```csharp
button.TouchUpInside += (sender, e) => {
    DoSomething();
};
```

> [!NOTE]
> `TouchUpInside` 不是唯一可用的按鈕事件。 `UIButton` 是的子類別`UIControl`，以定義[許多不同的事件](https://developer.xamarin.com/api/type/UIKit.UIControlEvent/)。

### <a name="using-the-ios-designer-to-specify-button-event-handlers"></a>使用 iOS 設計工具，來指定按鈕事件處理常式

使用**事件**索引標籤**Properties Pad**來指定按鈕的各種事件的事件處理常式。

為適當的事件中，鍵入新的事件處理常式的名稱，或從清單中選取一個。 如此一來，將按鈕的檢視控制器的程式碼在建立事件處理常式。

![事件索引標籤的 Properties Pad](buttons-images/image1.png "事件索引標籤的 [屬性] 面板")

## <a name="styling-a-button"></a>設定按鈕的樣式

`UIButton` 控制項可以位於不同的狀態數目，每個由`UIControlState`值 – `Normal`， `Disabled`， `Focused`，`Highlighted`等等。唯一的樣式，並指定以程式設計方式或使用 iOS 設計工具，可以指定每個狀態。

> [!NOTE]
> 如需完整清單，所有`UIControlState`值，看看 [`UIKit.UIControlState enumeration`](https://developer.xamarin.com/api/type/UIKit.UIControlState/)
> 文件。

例如，若要設定的標題色彩和陰影色彩`UIControlState.Normal`:

```csharp
button.SetTitleColor(UIColor.White, UIControlState.Normal);
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

下列程式碼設定按鈕標題 （樣式化） 的屬性化字串，如`UIControlState.Normal`和`UIControlState.Highlighted`:

```csharp
var normalAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle(normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString(buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle(highlightedAttributedTitle, UIControlState.Highlighted);
```

## <a name="custom-button-types"></a>自訂的按鈕類型

按鈕`UIButtonType`的`Custom`不有任何預設樣式。 不過，就可以藉由設定不同的狀態影像設定按鈕的外觀：

```csharp
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand.png"), UIControlState.Normal);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_Highlight.png"), UIControlState.Highlighted);
button4.SetImage (UIImage.FromBundle ("Buttons/MagicWand_On.png"), UIControlState.Selected);
```

根據是否在使用者觸碰按鈕或 不，它會轉譯為下列映像的其中一個 (`UIControlState.Normal`，`UIControlState.Highlighted`和`UIControlState.Selected`分別指出):

![UIControlState.Normal](buttons-images/image22.png "UIControlState.Normal")
![UIControlState.Highlighted](buttons-images/image23.png "UIControlState.Highlighted")
![UIControlState.Selected](buttons-images/image24.png "UIControlState.Selected")

如需有關使用自訂按鈕的詳細資訊，請參閱[使用的映像按鈕](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/buttons/use_an_image_for_a_button)配方。

