---
title: Xamarin.Forms 裝置類別
description: 這篇文章說明如何使用 Xamarin.Forms 裝置類別，精確控制功能和版面配置每個平台。
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: ff707cdf73665ae07881d2d17ec837a4cfacaca0
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935367"
---
# <a name="xamarinforms-device-class"></a>Xamarin.Forms 裝置類別

[ `Device` ](xref:Xamarin.Forms.Device)類別包含多種屬性和方法，以協助開發人員自訂版面配置和每個平台為基礎的功能。

除了方法與屬性，以在特定硬體類型和大小，目標程式碼`Device`類別包含[BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread)與 UI 互動的控制項從時應該使用的方法背景執行緒。

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>提供平台專屬值

在 Xamarin.Forms 2.3.4 之前, 的平台執行應用程式無法取得 dimension&gt [ `Device.OS` ](xref:Xamarin.Forms.Device.OS)屬性，並比較它[ `TargetPlatform.iOS` ](xref:Xamarin.Forms.TargetPlatform.iOS)， [`TargetPlatform.Android` ](xref:Xamarin.Forms.TargetPlatform.Android)， [ `TargetPlatform.WinPhone` ](xref:Xamarin.Forms.TargetPlatform.WinPhone)，和[ `TargetPlatform.Windows` ](xref:Xamarin.Forms.TargetPlatform.Windows)列舉值。 同樣地，其中[ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/)多載可用來提供控制項的平台特定值。

不過，因為 Xamarin.Forms 2.3.4 這些 Api 已被取代並取代。 [ `Device` ](xref:Xamarin.Forms.Device)類別現在包含公用的字串常數，用以識別平台 – [ `Device.iOS` ](xref:Xamarin.Forms.Device.iOS)， [ `Device.Android` ](xref:Xamarin.Forms.Device.Android)， `Device.WinPhone`(已被取代）， `Device.WinRT` （已過時） [ `Device.UWP` ](xref:Xamarin.Forms.Device.UWP)，並[ `Device.macOS` ](xref:Xamarin.Forms.Device.macOS)。 同樣地， [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/)多載已取代為[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)並[ `On` ](xref:Xamarin.Forms.On) Api。

在 C# 中，平台專屬值可供建立`switch`陳述式[ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/)屬性，然後再提供`case`適用於必要平台的陳述式：

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)並[ `On` ](xref:Xamarin.Forms.On)類別會提供相同的功能，在 XAML 中：

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)類別是泛型類別，因此必須具現化`x:TypeArguments`符合目標類型的屬性。 在  [ `On` ](xref:Xamarin.Forms.On)類別[ `Platform` ](xref:Xamarin.Forms.On.Platform)屬性可以接受單一`string`值或以逗號分隔的多個`string`值。

> [!IMPORTANT]
> 提供不正確`Platform`屬性中的值`On`類別不會導致錯誤。 相反地，程式碼會執行不含套用的平台特定值。

<a name="Device_Idiom" />

## <a name="deviceidiom"></a>Device.Idiom

`Device.Idiom`可以用來改變的版面配置或根據裝置執行應用程式的功能。 [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom)列舉型別包含下列值：

-  **電話**– iPhone、 iPod touch 和 Android 裝置比 600 dip 窄 ^
-  **Tablet** – iPad、 Windows 裝置和 Android 裝置的寬度超過 600 dip ^
-  **桌面**– 只有在傳回[UWP 應用程式](~/xamarin-forms/platform/windows/installation/index.md)在 Windows 10 desktop 電腦上 (傳回`Phone`行動 Windows 在裝置上，包括在連續體案例中)
-  **電視**– Tizen TV 裝置
-  **不支援**– 未使用

*^ dip 不一定是實體的像素計數*

`Idiom` 是特別適用於建置利用較大的螢幕，像這樣的配置：

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

## <a name="deviceflowdirection"></a>Device.FlowDirection

[ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)值擷取[ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection)列舉值，表示目前正由裝置的文字方向。 資料流程方向是所在頁面的 UI 項目會掃描出的方向。 這些列舉值為：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToRight`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

在 XAML 中， [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)可以擷取值，使用`x:Static`標記延伸模組：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

在 C# 對等的程式碼是：

```csharp
this.FlowDirection = Device.FlowDirection;
```

如需有關資料流程方向的詳細資訊，請參閱[由右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

<a name="Device_Styles" />

## <a name="devicestyles"></a>Device.Styles

[ `Styles`屬性](~/xamarin-forms/user-interface/styles/index.md)包含內建樣式定義，可以套用至某些控制項 (例如`Label`)`Style`屬性。 可用的樣式如下：

* BodyStyle
* CaptionStyle
* ListItemDetailTextStyle
* ListItemTextStyle
* SubtitleStyle
* TitleStyle

<a name="Device_GetNamedSize" />

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` 設定時，可以使用[ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md) C# 程式碼：

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

<a name="Device_OpenUri" />

## <a name="deviceopenuri"></a>Device.OpenUri

`OpenUri`方法可以用來觸發基礎平台，例如原生的網頁瀏覽器中開啟 URL 上的作業 (**Safari**在 iOS 上或**網際網路**在 Android 上)。

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

[WebView 範例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs)包含使用範例`OpenUri`開啟 Url，並也會觸發撥打電話。

[地圖範例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs)也會使用`Device.OpenUri`以顯示地圖與道路指引使用原生**對應**iOS 和 Android 上的應用程式。

<a name="Device_StartTimer" />

## <a name="devicestarttimer"></a>Device.StartTimer

`Device`類別也有`StartTimer`方法提供簡單的方式來觸發時間相依工作，在 Xamarin.Forms 常見程式碼，包括.NET Standard 程式庫中運作。 傳遞`TimeSpan`若要設定的間隔，並回到`true`保持執行的計時器或`false`停止在目前的引動過程後。

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

如果內部計時器的程式碼互動的使用者介面 (例如設定的文字`Label`或顯示警示) 應該在`BeginInvokeOnMainThread`運算式 （請參閱下文）。

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

由背景執行緒，例如在計時器或非同步作業，例如 web 要求的完成處理常式中執行的程式碼時，應該永遠不會存取使用者介面項目。 需要更新使用者介面的任何背景程式碼應該包裝在內[ `BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/)。 這相當於`InvokeOnMainThread`在 iOS 上，`RunOnUiThread`在 Android 上，和`Dispatcher.RunAsync`通用 Windows 平台上。

Xamarin.Forms 程式碼是：

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

請注意使用的方法`async/await`不需要使用`BeginInvokeOnMainThread`如果它們從主要 UI 執行緒執行。

## <a name="summary"></a>總結

Xamarin.Forms`Device`類別可讓功能和版面配置精細控制每個平台-即使在一般程式碼 （.NET Standard 程式庫專案或共用專案）。


## <a name="related-links"></a>相關連結

- [裝置範例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [設定樣式範例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [裝置](xref:Xamarin.Forms.Device)
