---
title: 從 Xamarin 啟動原生對應應用程式
description: 在每個平臺上的原生地圖應用程式，都可以透過 Xamarin 的啟動者類別，從 Xamarin. Forms 應用程式啟動。
ms.prod: xamarin
ms.assetid: 5CF7CD67-3F20-4D80-B99E-D35A5FD1019A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
ms.openlocfilehash: 54776d28bb75b152a6402e4d531d1baa4f724cba
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426199"
---
# <a name="launch-the-native-map-app-from-xamarinforms"></a>從 Xamarin 啟動原生對應應用程式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

在每個平臺上的原生對應應用程式，都可以由 Xamarin. Essentials `Launcher` 類別，從 Xamarin. Forms 應用程式中啟動。 此類別可讓應用程式透過其自訂 URI 配置來開啟另一個應用程式。 您可以使用 `OpenAsync` 方法來叫用啟動器功能，傳入 `string` 或 `Uri` 引數，表示要開啟的自訂 URL 配置。 如需有關 Xamarin 的詳細資訊，請參閱[xamarin. essentials](~/essentials/index.md?context=xamarin/xamarin-forms)。

> [!NOTE]
> 使用 Xamarin. Essentials `Launcher` 類別的替代方法是使用其 `Map` 類別。 如需詳細資訊，請參閱[Xamarin. Essentials： Map](~/essentials/maps.md?context=xamarin/xamarin-forms)。

每個平臺上的 maps 應用程式會使用唯一的自訂 URI 配置。 如需有關 iOS 上對應 URI 配置的詳細資訊，請參閱 developer.apple.com 上的[對應連結](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html)。 如需有關在 Android 上對應 URI 配置的詳細資訊，請參閱[Maps 開發人員指南](https://developer.android.com/guide/components/intents-common.html#Maps)和[適用于 android On Developers.android.com 的 Google maps 意圖](https://developers.google.com/maps/documentation/urls/android-intents)。 如需通用 Windows 平臺（UWP）上對應 URI 配置的詳細資訊，請參閱[啟動 Windows maps 應用程式](/windows/uwp/launch-resume/launch-maps-app)。

## <a name="launch-the-map-app-at-a-specific-location"></a>在特定位置啟動地圖應用程式

您可以藉由將適當的查詢參數新增至每個對應應用程式的自訂 URI 配置，來開啟原生地圖應用程式中的位置：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // open the maps app directly
    await Launcher.OpenAsync("geo:0,0?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?where=394 Pacific Ave San Francisco CA");
}
```

此範例程式碼會在每個平臺上啟動原生對應應用程式，並將地圖置中以代表指定位置的圖釘為中心：

[![原生對應應用程式在 iOS 和 Android 上的螢幕擷取畫面](native-map-app-images/location.png "原生對應應用程式")](native-map-app-images/location-large.png#lightbox "原生對應應用程式")

## <a name="launch-the-map-app-with-directions"></a>以方向啟動地圖應用程式

原生地圖應用程式可以藉由將適當的查詢參數新增至每個對應應用程式的自訂 URI 配置，來啟動顯示方向：

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?daddr=San+Francisco,+CA&saddr=cupertino");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // opens the 'task chooser' so the user can pick Maps, Chrome or other mapping app
    await Launcher.OpenAsync("http://maps.google.com/?daddr=San+Francisco,+CA&saddr=Mountain+View");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?rtp=adr.394 Pacific Ave San Francisco CA~adr.One Microsoft Way Redmond WA 98052");
}
```

此範例程式碼會在每個平臺上啟動原生對應應用程式，並將地圖置中于指定位置之間的路由：

[![原生對應應用程式路由（在 iOS 和 Android 上）的螢幕擷取畫面](native-map-app-images/directions.png "原生對應應用程式指示")](native-map-app-images/directions-large.png#lightbox "原生對應應用程式指示")

## <a name="related-links"></a>相關連結

- [Maps 範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms)
- [對應連結](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html)
- [Maps 開發人員指南](https://developer.android.com/guide/components/intents-common.html#Maps)
- [適用于 Android 的 Google Maps 意圖](https://developers.google.com/maps/documentation/)
- [啟動 Windows Maps 應用程式](/windows/uwp/launch-resume/launch-maps-app)
