---
title: 裝置類別
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 1fc3fb17ec97ce9028abbf63cdedbfc5fec12204
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="device-class"></a>裝置類別

[ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)類別包含許多屬性和方法，以協助開發人員自訂版面配置和每個平台為基礎的功能。

除了方法和屬性，以在特定硬體類型和大小，目標程式碼`Device`類別包含[BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread)從互動使用 UI 控制項時應該使用此方法背景執行緒。

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>提供平台特定值

在透過 Xamarin.Forms 2.3.4 之前, 的平台執行應用程式無法取得藉由檢查[ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/)屬性，並比較它[ `TargetPlatform.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/)， [`TargetPlatform.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/)， [ `TargetPlatform.WinPhone` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/)，和[ `TargetPlatform.Windows` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/)列舉值。 同樣地，其中[ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/)多載可用於提供特定平台值為控制項。

不過，因為 Xamarin.Forms 2.3.4 這些應用程式開發介面已被取代並取代。 [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)類別現在包含公用的字串常數，識別平台- [ `Device.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/)， [ `Device.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/)， [ `Device.WinPhone`](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/) （已過時） [ `Device.WinRT` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinRT/) （已過時） [ `Device.UWP` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/)，和[ `Device.macOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.macOS/)。 同樣地， [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/)多載已取代為[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)和[ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/)應用程式開發介面。

在 C# 中，可以提供特定平台值藉由建立`switch`陳述式上的[ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/)屬性，然後再提供`case`陳述式所需的平台：

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

[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)和[ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/)類別提供在 XAML 中相同的功能：

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

[ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/)類別是泛型類別，因此必須具現化`x:TypeArguments`符合目標類型的屬性。 在[ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/)類別[ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/)屬性可以接受單一`string`值或以逗號分隔的多個`string`值。

> [!IMPORTANT]
> 提供不正確`Platform`屬性值中的`On`類別不會導致錯誤。 相反地，程式碼會執行不含套用的平台專屬值。

<a name="Device_Idiom" />

## <a name="deviceidiom"></a>Device.Idiom

`Device.Idiom`可用來修改配置值或根據應用程式執行的裝置功能。 [ `TargetIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/)列舉型別包含下列值：

-  **電話**– iPhone 或 iPod touch 和 Android 裝置比 600 dip 窄 ^
-  **Tablet** – iPad，Windows 裝置和 Android 裝置寬度超過 600 dip ^
-  **桌面**– 只有在傳回[UWP 應用程式](~/xamarin-forms/platform/windows/installation/index.md)Windows 10 桌上型電腦上 (傳回`Phone`行動 Windows 在裝置上，包括連續體案例中)
-  **電視**– Tizen 電視裝置
-  **不支援**– 未使用

*^ dip 不一定是實體像素計數*

`Idiom` 是特別適用於建置的配置，利用較大的螢幕，像這樣：

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

## <a name="deviceflowdirection"></a>Device.FlowDirection

[ `Device.FlowDirection` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.FlowDirection/)值擷取[ `FlowDirection` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlowDirection/)列舉值，表示目前正在使用裝置的文字方向。 資料流程方向是所在頁面的 UI 元素會掃描該眼睛的方向。 這些列舉值為：

- [`LeftToRight`](https://developer.xamarin.com/api/field/Xamarin.Forms.FlowDirection.LeftToRight/)
- [`RightToRight`](https://developer.xamarin.com/api/field/Xamarin.Forms.FlowDirection.RightToLeft/)
- [`MatchParent`](https://developer.xamarin.com/api/field/Xamarin.Forms.FlowDirection.MatchParent/)

在 XAML 中、 [ `Device.FlowDirection` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.FlowDirection/)可以擷取值，使用`x:Static`標記延伸：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

在 C# 中對等的程式碼是：

```csharp
this.FlowDirection = Device.FlowDirection;
```

如需文字方向的詳細資訊，請參閱[由右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

<a name="Device_Styles" />

## <a name="devicestyles"></a>Device.Styles

[ `Styles`屬性](~/xamarin-forms/user-interface/styles/index.md)包含內建樣式定義，可以套用至某些控制項 (例如`Label`)`Style`屬性。 可用樣式如下：

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

`OpenUri`方法可以用來觸發等原生 web 瀏覽器中開啟 URL 基礎平台上的作業 (**Safari**在 iOS 上或**網際網路**在 Android 上)。

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

[WebView 範例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs)包含使用範例`OpenUri`開啟 Url，也會觸發撥打電話。

[地圖範例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs)也會使用`Device.OpenUri`顯示地圖和使用原生方向**對應**iOS 和 Android 上的應用程式。

<a name="Device_StartTimer" />

## <a name="devicestarttimer"></a>Device.StartTimer

`Device`類別也有`StartTimer`方法可提供簡單的方式來觸發時間相依工作，在 Xamarin.Forms 通用程式碼 （包括 PCLs） 中運作。 傳遞`TimeSpan`來設定的間隔，並傳回`true`保持執行計時器或`false`停止目前的引動過程之後。

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

如果內部計時器的程式碼互動的使用者介面 (例如，設定的文字`Label`或顯示的警告) 應該內`BeginInvokeOnMainThread`運算式 （請參閱下文）。

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

背景執行緒，例如在計時器或非同步作業，像是 web 要求完成處理常式中執行的程式碼應該永遠不會存取使用者介面項目。 需要更新的使用者介面的任何背景程式碼應該包裝在之內[ `BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/)。 這就相當於`InvokeOnMainThread`在 iOS、`RunOnUiThread`在 Android 上，和`Dispatcher.RunAsync`通用 Windows 平台上。

Xamarin.Forms 程式碼是：

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

請注意使用該方法`async/await`不需要使用`BeginInvokeOnMainThread`如果它們由主要 UI 執行緒執行。

## <a name="summary"></a>總結

Xamarin.Forms`Device`類別可讓更細微的控制功能和配置每個平台為基礎-即使在一般程式碼 （PCL 或共用專案）。


## <a name="related-links"></a>相關連結

- [裝置範例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [樣式範例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [裝置](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)
