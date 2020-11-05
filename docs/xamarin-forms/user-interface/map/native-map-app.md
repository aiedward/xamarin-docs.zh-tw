---
title: 啟動原生地圖應用程式 Xamarin.Forms
description: 每個平臺上的原生地圖應用程式，都可以 Xamarin.Forms 由啟動器類別從應用程式啟動 Xamarin.Essentials 。
ms.prod: xamarin
ms.assetid: 5CF7CD67-3F20-4D80-B99E-D35A5FD1019A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cbd61d956b17df556cb4bb452f60b0e41b31b707
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368241"
---
# <a name="launch-the-native-map-app-from-no-locxamarinforms"></a>啟動原生地圖應用程式 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)

每個平臺上的原生地圖應用程式都可以 Xamarin.Forms 由類別從應用程式啟動 Xamarin.Essentials `Launcher` 。 此類別可讓應用程式透過其自訂 URI 配置來開啟另一個應用程式。 您可以使用方法來叫用啟動器功能 `OpenAsync` ，傳遞 `string` `Uri` 代表要開啟之自訂 URL 配置的或引數。 如需的詳細資訊 Xamarin.Essentials ，請參閱 [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms) 。

> [!NOTE]
> 使用類別的替代方法 Xamarin.Essentials `Launcher` 是使用它的 `Map` 類別。 如需詳細資訊，請參閱[ Xamarin.Essentials ： Map](~/essentials/maps.md?context=xamarin/xamarin-forms)。

每個平臺上的 maps 應用程式都會使用唯一的自訂 URI 配置。 如需 iOS 上 maps URI 配置的詳細資訊，請參閱 developer.apple.com 上的 [地圖連結](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html) 。 如需有關 Android 上 maps URI 配置的詳細資訊，請參閱 [地圖開發人員指南](https://developer.android.com/guide/components/intents-common.html#Maps) 和 [適用于 Android 的 Google maps 意圖](https://developers.google.com/maps/documentation/urls/android-intents) developers.android.com。 如需通用 Windows 平臺 (UWP) 上地圖 URI 配置的相關資訊，請參閱 [啟動 Windows 地圖應用程式](/windows/uwp/launch-resume/launch-maps-app)。

## <a name="launch-the-map-app-at-a-specific-location"></a>在特定位置啟動地圖應用程式

您可以藉由將適當的查詢參數加入至每個地圖應用程式的自訂 URI 配置，來開啟原生地圖應用程式中的位置：

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

此範例程式碼會導致在每個平臺上啟動原生地圖應用程式，並將地圖置中為代表指定位置的圖釘：

[![原生地圖應用程式在 iOS 和 Android 上的螢幕擷取畫面](native-map-app-images/location.png "原生地圖應用程式")](native-map-app-images/location-large.png#lightbox "原生地圖應用程式")

## <a name="launch-the-map-app-with-directions"></a>使用指示啟動地圖應用程式

您可以藉由將適當的查詢參數加入至每個地圖應用程式的自訂 URI 配置，來啟動原生地圖應用程式顯示方向：

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

此範例程式碼會導致在每個平臺上啟動原生地圖應用程式，並將地圖置中在指定位置之間的路線：

[![螢幕擷取畫面： iOS 和 Android 上的原生地圖應用程式路由](native-map-app-images/directions.png "原生地圖應用程式指示")](native-map-app-images/directions-large.png#lightbox "原生地圖應用程式指示")

## <a name="related-links"></a>相關連結

- [Maps 範例](/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms)
- [地圖連結](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html)
- [Maps 開發人員指南](https://developer.android.com/guide/components/intents-common.html#Maps)
- [適用于 Android 的 Google Maps 意圖](https://developers.google.com/maps/documentation/)
- [啟動 Windows 地圖應用程式](/windows/uwp/launch-resume/launch-maps-app)