---
title: Xamarin.Forms雙畫面平臺協助程式
description: 本指南說明如何使用 Xamarin.Forms DualScreenHelper 類別，將您的應用程式體驗優化，例如 Surface 雙核處理器技術和 Surface Neo 等雙畫面裝置。
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9daf5a24c0dcfd07d529955c411259f4c1359df
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138940"
---
# <a name="xamarinforms-dual-screen-platform-helpers"></a>Xamarin.Forms雙畫面平臺協助程式

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

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
