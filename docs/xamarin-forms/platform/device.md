---
title: Xamarin.Forms 裝置類別
description: 這篇文章說明如何使用 Xamarin.Forms 裝置類別，精確控制功能和版面配置每個平台。
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: eb1358f039cc5d5a200f929fcc7dfa71ca863d2a
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121308"
---
# <a name="xamarinforms-device-class"></a>Xamarin.Forms 裝置類別

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

[ `Device` ](xref:Xamarin.Forms.Device)類別包含多種屬性和方法，以協助開發人員自訂版面配置和每個平台為基礎的功能。

除了在特定硬體類型和大小以程式碼為目標的`Device`方法和屬性之外, 類別還包含可用來從背景執行緒與 UI 控制項互動的方法。 如需詳細資訊, 請參閱[從背景執行緒與 UI 互動](#interact-with-the-ui-from-background-threads)。

## <a name="providing-platform-specific-values"></a>提供平臺特定的值

在 Xamarin.Forms 2.3.4 之前, 的平台執行應用程式無法取得 dimension&gt [ `Device.OS` ](xref:Xamarin.Forms.Device.OS)屬性，並比較它[ `TargetPlatform.iOS` ](xref:Xamarin.Forms.TargetPlatform.iOS)， [`TargetPlatform.Android` ](xref:Xamarin.Forms.TargetPlatform.Android)， [ `TargetPlatform.WinPhone` ](xref:Xamarin.Forms.TargetPlatform.WinPhone)，和[ `TargetPlatform.Windows` ](xref:Xamarin.Forms.TargetPlatform.Windows)列舉值。 同樣地，其中[ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))多載可用來提供控制項的平台特定值。

不過，因為 Xamarin.Forms 2.3.4 這些 Api 已被取代並取代。 [ `Device` ](xref:Xamarin.Forms.Device)類別現在包含公用的字串常數，用以識別平台 – [ `Device.iOS` ](xref:Xamarin.Forms.Device.iOS)， [ `Device.Android` ](xref:Xamarin.Forms.Device.Android)， `Device.WinPhone`(已被取代）， `Device.WinRT` （已過時） [ `Device.UWP` ](xref:Xamarin.Forms.Device.UWP)，並[ `Device.macOS` ](xref:Xamarin.Forms.Device.macOS)。 同樣地， [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))多載已取代為[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)並[ `On` ](xref:Xamarin.Forms.On) Api。

在C#，可提供平台特定的值，請建立`switch`陳述式[ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform)屬性，然後再提供`case`適用於必要平台的陳述式：

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

[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)並[ `On` ](xref:Xamarin.Forms.On)類別會提供相同的功能，在 XAML 中：

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

[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1)類別是必須使用具現化泛型類別`x:TypeArguments`符合目標類型的屬性。 在  [ `On` ](xref:Xamarin.Forms.On)類別[ `Platform` ](xref:Xamarin.Forms.On.Platform)屬性可以接受單一`string`值或以逗號分隔的多個`string`值。

> [!IMPORTANT]
> 提供不正確`Platform`屬性中的值`On`類別不會導致錯誤。 相反地，程式碼會執行不含套用的平台特定值。

或者，`OnPlatform`標記延伸可用於在 XAML 中自訂每個平台為基礎的 UI 外觀。 如需詳細資訊，請參閱 < [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform)。

## <a name="deviceidiom"></a>Device.Idiom

`Device.Idiom`屬性可以用來更改版面配置，或根據裝置的應用程式的功能在上執行。 [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom)列舉型別包含下列值：

- **電話**– iPhone、 iPod touch 和 Android 裝置比 600 dip 窄 ^
- **Tablet** – iPad、 Windows 裝置和 Android 裝置的寬度超過 600 dip ^
- **桌面**– 只有在傳回[UWP 應用程式](~/xamarin-forms/platform/windows/installation/index.md)在 Windows 10 desktop 電腦上 (傳回`Phone`行動 Windows 在裝置上，包括在連續體案例中)
- **電視**– Tizen TV 裝置
- **監看式**– Tizen 監看式裝置
- **不支援**– 未使用

*^ dip 不一定是實體的像素計數*

`Idiom`屬性是特別適用於建置利用較大的螢幕，像這樣的配置：

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

[ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1)類別會提供相同的功能，在 XAML 中：

```xaml
<StackLayout>
    <StackLayout.Margin>
        <OnIdiom x:TypeArguments="Thickness">
            <OnIdiom.Phone>0,20,0,0</OnIdiom.Phone>
            <OnIdiom.Tablet>0,40,0,0</OnIdiom.Tablet>
            <OnIdiom.Desktop>0,60,0,0</OnIdiom.Desktop>
        </OnIdiom>
    </StackLayout.Margin>
    ...
</StackLayout>
```

[ `OnIdiom` ](xref:Xamarin.Forms.OnPlatform`1)類別是必須使用具現化泛型類別`x:TypeArguments`符合目標類型的屬性。

或者，`OnIdiom`標記延伸模組可以在 XAML 中使用自訂 UI 的外觀，根據的裝置執行應用程式的慣用語。 如需詳細資訊，請參閱 < [OnIdiom 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom)。

## <a name="deviceflowdirection"></a>Device.FlowDirection

[ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)值擷取[ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection)列舉值，表示目前正由裝置的文字方向。 資料流程方向是所在頁面的 UI 項目會掃描出的方向。 這些列舉值為：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToRight`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

在 XAML 中， [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)可以擷取值，使用`x:Static`標記延伸模組：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

對等的程式碼，在C#是：

```csharp
this.FlowDirection = Device.FlowDirection;
```

如需有關資料流程方向的詳細資訊，請參閱[由右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="devicestyles"></a>Device.Styles

[ `Styles`屬性](~/xamarin-forms/user-interface/styles/index.md)包含內建樣式定義，可以套用至某些控制項 (例如`Label`)`Style`屬性。 可用的樣式如下：

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` 設定時，可以使用[ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md)在C#程式碼：

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="deviceopenuri"></a>Device.OpenUri

`OpenUri`方法可以用來觸發基礎平台，例如原生的網頁瀏覽器中開啟 URL 上的作業 (**Safari**在 iOS 上或**網際網路**在 Android 上)。

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

[WebView 範例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs)包含使用範例`OpenUri`開啟 Url，並也會觸發撥打電話。

[地圖範例](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs)也會使用`Device.OpenUri`以顯示地圖與道路指引使用原生**對應**iOS 和 Android 上的應用程式。

## <a name="devicestarttimer"></a>Device.StartTimer

`Device`類別也有`StartTimer`方法提供簡單的方式來觸發時間相依工作，在 Xamarin.Forms 常見程式碼，包括.NET Standard 程式庫中運作。 傳遞`TimeSpan`若要設定的間隔，並回到`true`保持執行的計時器或`false`停止在目前的引動過程後。

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

如果內部計時器的程式碼互動的使用者介面 (例如設定的文字`Label`或顯示警示) 應該在`BeginInvokeOnMainThread`運算式 （請參閱下文）。

## <a name="interact-with-the-ui-from-background-threads"></a>從背景執行緒與 UI 互動

大部分的作業系統 (包括 iOS、Android 和通用 Windows 平臺) 都會針對涉及使用者介面的程式碼使用單一執行緒模型。 這個執行緒通常稱為*主要執行緒*或*UI 執行緒*。 此模型的結果是, 存取使用者介面專案的所有程式碼都必須在應用程式的主執行緒上執行。

應用程式有時會使用背景執行緒來執行可能長時間執行的作業, 例如從 web 服務抓取資料。 如果在背景執行緒上執行的程式碼需要存取使用者介面元素, 則必須在主執行緒上執行該程式碼。

類別包含下列`static`方法, 可以用來與背景執行緒中的使用者介面專案互動: `Device`

| 方法 | 引數 | 傳回值 | 用途 |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | `Action`在主執行緒上叫用, 並不等待它完成。 |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | 在主`Func<T>`執行緒上叫用, 並等候它完成。 |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | `Action`在主執行緒上叫用, 並等候它完成。 |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | 在主`Func<Task<T>>`執行緒上叫用, 並等候它完成。 |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | 在主`Func<Task>`執行緒上叫用, 並等候它完成。 |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | `SynchronizationContext`傳回主執行緒的。 |

下列程式碼顯示使用`BeginInvokeOnMainThread`方法的範例:

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>相關連結

- [裝置範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [設定樣式範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [裝置](xref:Xamarin.Forms.Device)
