---
title： " Xamarin.Essentials ：應用程式主題" 描述： "本檔描述中要求的應用程式主題 API Xamarin.Essentials ，其提供針對執行中應用程式要求的主題樣式資訊。
assetid： F6F6D496-A8A9-4B9A-AF1A-370D937E5073 author： jamesmontemagno ms. custom： video ms. 作者： jamont ms. date： 01/06/2020 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials：應用程式主題

**Application.requestedtheme** API 是類別的一部分 [`AppInfo`](app-information.md) ，並提供系統為執行中的應用程式要求的主題的相關資訊。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>使用 Application.requestedtheme

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>取得主題資訊

使用下列 API 可以偵測到要求的應用程式主題：

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

這會為您的應用程式提供系統目前要求的主題。 傳回值將會是下列其中一項：

* [未指定]
* 輕量型
* 深色

當作業系統沒有要要求的特定使用者介面樣式時，將會傳回未指定的。 其中一個範例是在執行的 iOS 版本比13.0 舊的裝置上執行。


## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

Android 使用設定模式來指定要向使用者要求的主題類型。 根據 Android 版本，使用者可以加以變更，或在啟用節電模式時變更。

若要深入瞭解，請參閱[深色主題的官方 Android 檔](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme)。


# <a name="ios"></a>[iOS](#tab/ios)

在13.0 之前的版本上，一律會傳回未指定的


# <a name="uwp"></a>[UWP](#tab/uwp)

呼叫 `RequestedTheme` 必須在 UI 執行緒上呼叫，否則會擲回例外狀況。

UWP 應用程式會遵循您在 UWP 應用程式中的設定。 xaml 下的**application.requestedtheme**。 如果設定為特定主題， Xamarin.Essentials 一律會傳回此設定。 若要使用作業系統的動態主題，請從您的應用程式中移除此節點，然後在執行應用程式時，將會傳回使用者在 Windows [設定] 中設定的主題（**[設定] [> 個人化 > 色彩] > 選擇您的預設應用程式模式**）。

您可以在[UWP 要求的主題檔](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme)中閱讀更多。

--------------

## <a name="api"></a>API

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Theme-Detection-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
