---
title: Xamarin.Forms DualScreenHelper
description: 本指南說明如何使用 Xamarin.Forms DualScreenHelper 類別，將雙螢幕裝置 (例如 Surface Duo 和 Surface Neo) 的應用程式體驗最佳化。
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 26e6389dc7effd52fad8307e1e3191fc4b760fe6
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77480573"
---
# <a name="xamarinforms-dualscreenhelper"></a>Xamarin.Forms DualScreenHelper

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/UserInterface/DualScreenDemos)

`DualScreenHelper` 類別可用於偵測裝置是否支援子母畫面模式，然後讓您以子母畫面視窗形式開啟 `ContentPage`。 如果您在 Neo 裝置上執行，當處於撰寫模式時，其會在 WonderBar 中開啟頁面。

## <a name="properties"></a>屬性

- `HasCompactModeSupport` 用於確認平台是否支援在 CompactMode 中開啟 `ContentPage`。
- `OpenCompactMode` 會在 CompactMode 中開啟 `ContentPage` (如果該平台支援)。

## <a name="example"></a>範例

```csharp
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if(!DualScreenHelper.HasCompactModeSupport())
    {
        await DisplayAlert("Unsupported", "This platform doesn't support this feature", "Ok");
        return;
    }

    ContentPage page = new ContentPage() { BackgroundColor = Color.Purple };

    var button = new Button()
    {
        Text = "Close this Window"
    };

    var layout = new StackLayout()
    {
        Children =
        {
            new Label(){ Text = "Welcome to your Compact Mode Window" },
            button
        },
        BackgroundColor = Color.Yellow,
        HorizontalOptions = LayoutOptions.Fill,
        VerticalOptions = LayoutOptions.Fill
    };

    page.Content = new ScrollView() { Content = layout };
    var args = await DualScreenHelper.OpenCompactMode(page);

    button.Command = new Command(async () =>
    {
        await args.CloseAsync();
    });
}
```
