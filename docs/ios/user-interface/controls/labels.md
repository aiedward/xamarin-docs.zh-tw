---
title: Xamarin 中的標籤
description: 本檔討論如何在 Xamarin 中使用標籤。 其中說明如何以程式設計方式和 iOS 設計工具建立標籤。
ms.prod: xamarin
ms.assetid: 54DA1221-13E4-4D45-B263-5F22A0AC7B53
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/11/2017
ms.openlocfilehash: 0957220ace9facee590a76953de6dd52be438582
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570839"
---
# <a name="labels-in-xamarinios"></a>Xamarin 中的標籤

`UILabel`控制項用於顯示單一和多行的唯讀文字。

## <a name="implementing-a-label"></a>執行標籤

藉由具現化來建立新的標籤 [`UILabel`](xref:UIKit.UILabel) ：

```csharp
UILabel label = new UILabel();
```

### <a name="labels-and-storyboards"></a>標籤和分鏡腳本

您也可以在使用 iOS 設計工具時，將標籤新增至您的 UI。 在 [**工具箱**] 中搜尋**標籤**，並將它拖曳至您的視圖：

![工具箱中的標籤](labels-images/image3.png)

您可以在 properties pad 上調整下列屬性：

![標籤屬性面板](labels-images/image2.png)

- **文字內容**-簡單或屬性化。 純文字可讓您設定整個字串的[格式屬性](#Formatting_Text_and_Label)。 屬性化文字可讓您將格式設定為字串中的不同字元或文字。
- **色彩、字型、對齊**–可套用至標籤的格式化屬性。
- **線條**–設定標籤可以跨越的行數。 將此設為0，可讓標籤視需要使用任意數目的行。
- **行為**–可以設定為 [啟用] 或 [反白顯示]。 [已啟用] 是預設設定，已停用的文字會以較淺的灰色顯示。 預設會停用反白顯示，並允許使用者選取標籤時，以反白顯示的狀態重新繪製。
- **Baselane 與分行符號**–
  - 如果字型大小與指定的字型大小不同，高於基準會決定文字的定位方式。
  - 分行符號會決定當字串長度超過單一行時，如何將它換行或截斷。
- 自動**壓縮**–決定如何在標籤內將字型大小最小化的大小（如有需要）。
- 反**白顯示、陰影、位移**–可讓您設定醒目提示和陰影色彩，以及陰影位移。

## <a name="truncating-and-wrapping"></a>截斷和換行

如需在 iOS 中使用分行符號的詳細資訊，請參閱[截斷和換行文字](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/uilabel-truncate-wrap-text)配方。

<a name="Formatting_Text_and_Label"></a>

## <a name="formatting-text-and-label"></a>格式化文字和標籤

若要格式化您在標籤中使用的字串，您可以在整個字串上設定格式屬性，或者可以使用屬性化字串。 下列範例示範如何執行這些動作：

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

如需使用來設定文字樣式的詳細資訊， `NSAttributedString` 請參閱[樣式文字](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/text_field/style_text)配方。

根據預設，標籤的 `Enabled` 設定為 true，但您可以將其設定為 [停用]，以提供使用者一個已停用特定控制項的提示：

```csharp
label.Enabled = false;
```

這會將標籤設定為淺灰色色彩，如 iOS 中限制畫面的下列範例影像所示：

![IOS 中的已停用按鈕](labels-images/image1.png)

您也可以將反白顯示和陰影文字色彩設定為標籤文字，以取得額外的效果：

```csharp
label.Highlighted = true;
label.HighlightedTextColor = UIColor.Cyan;

label.ShadowColor = UIColor.Black;
label.ShadowOffset = new CoreGraphics.CGSize(1.0f, 1.0f);
```

這會顯示文字，如下所示：

![在文字上反白顯示並設定陰影](labels-images/image4.png)

如需變更 UILabel 字型的詳細資訊，請參閱[變更字型](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/labels/change_the_font)配方。
