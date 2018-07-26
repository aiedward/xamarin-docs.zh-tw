---
title: 在 Xamarin.iOS 中的標籤
description: 本文討論如何在 Xamarin.iOS 中使用標籤。 它說明如何以程式設計方式及使用 iOS 設計工具建立的標籤。
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: b52bdbd41eaafbc5e6c78e1f8514b701fd78bd6b
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241909"
---
# <a name="labels-in-xamarinios"></a>在 Xamarin.iOS 中的標籤

`UILabel`控制項用來顯示單一和多行，唯讀文字。 

## <a name="implementing-a-label"></a>實作標籤

新的標籤由具現化[ `UILabel` ](https://developer.xamarin.com/api/type/UIKit.UILabel/):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>標籤和分鏡腳本

您也可以新增至您的 UI 標籤，使用 「 iOS 設計工具時。 搜尋**標籤**中**工具箱**並將它拖曳至您的檢視：

![在工具箱中加上標籤](labels-images/image3.png)

在 [屬性] 面板上，可以調整下列屬性：

![標籤屬性 面板](labels-images/image2.png)

- **文字內容**-純文字或屬性化。 純文字格式可讓您設定[格式化屬性](#Formatting_Text_and_Label)整個字串。 屬性化的文字，可讓您設定至不同的字元或文字字串中的格式。
- **色彩、 字型對齊**– 可以套用至標籤的格式設定屬性。
- **行**– 設定標籤可以跨的行數。 將此設為 0，以允許使用需要的行數的標籤。
- **行為**– 可以設定為 已啟用 或 反白顯示。 啟用已設定的預設值，請停用的文字會顯示在較淡灰色的色彩。 反白顯示預設會停用，並允許繪製反白顯示的狀態，當使用者選取標籤。
- **Baselane 和分行符號**– 
    - 基準可讓您決定的文字將會如何定位如果不同於指定的字型大小。
    - 換行符號會決定將如何包裝或如果名稱長度超過單行截斷字串。
- **Autoshrink** – 決定如何調整大小的字型為了盡量在標籤中，如有必要。
- **反白顯示、 陰影位移**– 可讓您設定的是反白顯示和陰影的色彩，並將陰影位移。

## <a name="truncating-and-wrapping"></a>截斷和換行

如需使用列中斷在 iOS 中，請參閱[截斷，並將文字換行](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text)配方。

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>格式化文字和標籤

若要格式化字串，您在標籤中您可以格式化整個字串上的屬性設定，或您可以使用屬性化的字串。 下列範例示範如何實作這些：

```csharp
label = new UILabel(){
                Text = "Hello, this is a string",
                Font = UIFont.FromName("Papyrus", 20f),
                TextColor = UIColor.Magenta,
                TextAlignment = UITextAlignment.Center
            };
```

```csharp
label.AttributedText = new NSAttributedString(
                "This is some formatted text",
                font: UIFont.FromName("GillSans", 16.0f),
                foregroundColor: UIColor.Blue,
                backgroundColor: UIColor.White
            );
```

如需有關樣式的文字使用`NSAttributedString`是指[樣式文字](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text)配方。

預設標籤具有`Enabled`設為 true，但它可將它設定為停用，以授與使用者某些控制項已停用的提示：

```csharp
label.Enabled = false;
```

這會將標籤設定為淺灰色的色彩，如下列範例畫面的影像限制在 iOS 中所示：

![在 iOS 中的已停用的按鈕](labels-images/image1.png)

您也可以設定您的標籤文字之其他效果的反白顯示和陰影的文字色彩：

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

這會顯示如下文字：

![反白顯示和設定上的文字陰影](labels-images/image4.png)

如需有關變更的 UILabel 字型的詳細資訊，請參閱[變更字型](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font)配方。





