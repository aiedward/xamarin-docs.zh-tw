---
title: "標籤"
ms.topic: article
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/11/2017
ms.openlocfilehash: 0fdeecc4465aa5709b452ef0b591ec4e5c262e3d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="labels"></a>標籤

`UILabel`控制項用來顯示單一和多行，唯讀文字。 

## <a name="implementing-a-label"></a>實作標籤

具現化來建立新的標籤[ `UILabel` ](https://developer.xamarin.com/api/type/UIKit.UILabel/):

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>標籤和分鏡腳本

您也可以加入至您的 UI 標籤，當使用 iOS 設計工具。 搜尋**標籤**中**工具箱**並將它拖曳至您的檢視：

![在工具箱中加上標籤](labels-images/image3.png)

屬性板上，您可以調整下列屬性：

![標籤屬性 面板](labels-images/image2.png)

- **文字內容**– 純文字或屬性。 純文字，可讓您設定[格式屬性](#Formatting_Text_and_Label)整個字串。 屬性化的文字，可讓您設定不同的字元或文字字串中的格式。
- **色彩、 字型對齊**– 可以套用至標籤的格式設定屬性。
- **行**– 設定的標籤可以跨越的行數。 將此設為 0 可允許使用需要的行數的標籤。
- **行為**– 可以設定為已啟用] 或 [反白顯示。 啟用是預設設定，已停用的文字會顯示在淡灰色的色彩。 反白顯示預設會停用，並允許繪製反白顯示的狀態，當使用者選取標籤。
- **Baselane 和分行符號**– 
    - 基準您決定如何將文字字型的大小是否不同於指定的位置。
    - 行符號會決定將如何包裝或如果超過單一行截斷字串。
- **Autoshrink** – 決定大小的字型如何將最小化以內標籤，如有必要。
- **反白顯示，陰影位移**– 可讓您設定的反和陰影的色彩，以及陰影位移。

## <a name="truncating-and-wrapping"></a>截斷和換行

在 iOS 中會中斷使用列的詳細資訊，參閱[截斷，並將文字換行](https://developer.xamarin.com/recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text/)配方。

<a name="Formatting_Text_and_Label"/>

## <a name="formatting-text-and-label"></a>格式化文字和標籤

若要格式化您在標籤中使用此字串可以格式化整個字串上的屬性設定或您可以使用屬性化的字串。 下列範例會示範如何實作這些自訂：

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

如需有關樣式的文字使用`NSAttributedString`指[樣式文字](https://developer.xamarin.com/recipes/ios/standard_controls/text_field/style_text/)配方。

預設標籤具有`Enabled`設為 true，但會儘可能將它設定為停用來授與使用者已停用特定控制項的提示：

```csharp
label.Enabled = false;
```

這會將標籤設定為淺灰色的色彩，限制螢幕，在 iOS 中的下列範例映像中所示：

![在 iOS 中已停用的按鈕](labels-images/image1.png)

您也可以設定其他效果的標籤文字的反白顯示和陰影的文字色彩：

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

這會顯示的文字，像這樣：

![反白顯示和設定的文字陰影](labels-images/image4.png)

如需有關變更的 UILabel 字型的詳細資訊，請參閱[變更字型](https://developer.xamarin.com/recipes/ios/standard_controls/labels/change_the_font/)配方。





