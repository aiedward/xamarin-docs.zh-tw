---
title: Xamarin.Forms 雙屏平台協助器
description: 本指南說明如何使用 Xamarin.Forms DualScreenHelper 類別，將雙螢幕裝置 (例如 Surface Duo 和 Surface Neo) 的應用程式體驗最佳化。
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 9ab6106b8b92660d416e8a22cc3b1bdf1b41c5cf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "80628270"
---
# <a name="xamarinforms-dual-screen-platform-helpers"></a>Xamarin.Forms 雙屏平台協助器

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

`DualScreenHelper` 類別可用於偵測裝置是否支援子母畫面模式，然後讓您以子母畫面視窗形式開啟 `ContentPage`。 如果您在 Neo 裝置上執行，當處於撰寫模式時，其會在 WonderBar 中開啟頁面。

## <a name="properties"></a>屬性

- `HasCompactModeSupport` 用於確認平台是否支援在 CompactMode 中開啟 `ContentPage`。
- `OpenCompactMode` 會在 CompactMode 中開啟 `ContentPage` (如果該平台支援)。

## <a name="example"></a>範例

```csharp
async void OpenCompactWindowClicked(object sender, EventArgs e)
{
    if (!DualScreenHelper.HasCompactModeSupport())
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
