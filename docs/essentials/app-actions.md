---
title: Xamarin.Essentials：應用程式動作
description: 中的加速計類別 Xamarin.Essentials 可讓您建立並回應應用程式圖示的應用程式快捷方式。
ms.assetid: 5edf9bc5-b721-448c-a8a2-0a9d4d0c792c
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8b1da7d07c042ff10b48948c4f411d65c6c05002
ms.sourcegitcommit: d4d293174a8324ce82b8f961ae6eadce294cafd7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194883"
---
# <a name="no-locxamarinessentials-app-actions"></a>Xamarin.Essentials：應用程式動作

**AppActions** 類別可讓您建立並回應應用程式圖示的應用程式快捷方式。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取 **AppActions** 功能，需要下列平臺特定設定。

# <a name="android"></a>[Android](#tab/android)

將意圖篩選新增至您的 `MainActivity` 類別：

```csharp
[IntentFilter(
        new[] { Xamarin.Essentials.Platform.Intent.ActionAppAction },
        Categories = new[] { Android.Content.Intent.CategoryDefault })]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    ...
```

然後，新增下列邏輯以處理動作：

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume(this);
}

protected override void OnNewIntent(Android.Content.Intent intent)
{
    base.OnNewIntent(intent);

    Xamarin.Essentials.Platform.OnNewIntent(intent);
}
```

# <a name="ios"></a>[iOS](#tab/ios)

在中， `AppDelegate.cs` 新增下列邏輯以處理動作：

```csharp
public override void PerformActionForShortcutItem(UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
    => Xamarin.Essentials.Platform.PerformActionForShortcutItem(application, shortcutItem, completionHandler);
```

# <a name="uwp"></a>[UWP](#tab/uwp)

在方法的檔案中，于 `App.xaml.cs` `OnLaunched` 方法的底部新增下列邏輯：

```csharp
Xamarin.Essentials.Platform.OnLaunched(e);
```

-----

## <a name="create-actions"></a>建立動作

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```
您可以隨時建立應用程式動作，但通常會在應用程式啟動時建立。 呼叫 `SetAsync` 方法來建立應用程式的動作清單。


```csharp
try
{
    await AppActions.SetAsync(
        new AppAction("app_info", "App Info", icon: "app_info_action_icon"),
        new AppAction("battery_info", "Battery Info"));
}
catch (FeatureNotSupportedException ex)
{
    Debug.WriteLine("App Actions not supported");
}
```

如果特定版本的作業系統上不支援應用程式動作，則會擲回 `FeatureNotSupportedException` 。 

下列屬性可以在上設定 `AppAction` ：

* 識別碼：用來回應動作點的唯一識別碼。
* 標題：要顯示的可見標題。
* 子標題：如果支援在標題下顯示副標題。
* 圖示：必須符合每個平臺上對應資原始目錄中的圖示。

![Homescreen 上的應用程式動作](images/appactions.png)

## <a name="responding-to-actions"></a>回應動作

當您的應用程式開始註冊 `OnAppAction` 事件時。 選取應用程式動作時，系統會將事件傳送至所選動作的資訊。

```csharp
public App()
{
    //...
    AppActions.OnAppAction += AppActions_OnAppAction;
}

void AppActions_OnAppAction(object sender, AppActionEventArgs e)
{
    // Don't handle events fired for old application instances
    // and cleanup the old instance's event handler
    if (Application.Current != this && Application.Current is App app)
    {
        AppActions.OnAppAction -= app.AppActions_OnAppAction;
        return;
    }
    MainThread.BeginInvokeOnMainThread(async () =>
    {
        await Shell.Current.GoToAsync($"//{e.AppAction.Id}");
    });
}
```

## <a name="getactions"></a>GetActions
您可以藉由呼叫來取得目前的應用程式動作清單 `AppActions.GetAsync()` 。

## <a name="api"></a>API

- [AppActions 來源程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/AppActions)
- [AppActions API 檔](xref:Xamarin.Essentials.AppActions)
