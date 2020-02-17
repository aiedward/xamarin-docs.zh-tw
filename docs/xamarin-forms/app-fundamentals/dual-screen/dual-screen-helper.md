---
title: Xamarin.Forms DualScreenHelper
description: 本指南說明如何使用 Xamarin.Forms DualScreenHelper，將雙螢幕裝置 (例如 Surface Duo 和 Surface Neo) 的應用程式體驗最佳化。
ms.prod: xamarin
ms.assetid: 5aa184c2-5611-427d-85c7-1c56486c3e1b
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: b06e105560de635a21b42e8366bb47842372cf6a
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145542"
---
# <a name="xamarinforms-dualscreenhelper"></a>Xamarin.Forms DualScreenHelper
[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/UserInterface/DualScreenDemos)
_DualScreenHelper 可用來偵測裝置是否支援子母畫面模式，然後讓您以子母畫面視窗形式開啟 ContentPage。如果您在 Neo 裝置上執行，當處於撰寫模式時，則會在觸控條中開啟頁面。_
## <a name="properties"></a>屬性
- `HasCompactModeSupport`：用來檢查平台是否支援在 CompactMode 中開啟 ContentPage。
- `OpenCompactMode`：如果平台支援此項，則會在 CompactMode 中開啟 ContentPage。
## <a name="example-usage"></a>使用範例
```c#
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