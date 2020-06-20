---
title: Xamarin.Forms裝置類別
description: 本文說明如何使用 Xamarin.Forms 裝置類別，針對每個平臺上的功能和配置進行精細的控制。
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ba4e93b8f364d6887439b05017a9cd373dce5985
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84572321"
---
# <a name="xamarinforms-device-class"></a>Xamarin.Forms裝置類別

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

[`Device`](xref:Xamarin.Forms.Device)類別包含數個屬性和方法，可協助開發人員自訂每個平臺的版面配置和功能。

除了在特定硬體類型和大小以程式碼為目標的方法和屬性之外， `Device` 類別還包含可用來從背景執行緒與 UI 控制項互動的方法。 如需詳細資訊，請參閱[從背景執行緒與 UI 互動](#interact-with-the-ui-from-background-threads)。

## <a name="provide-platform-specific-values"></a>提供平臺特定的值

在 Xamarin.Forms 2.3.4 之前，您可以藉由檢查 [`Device.OS`](xref:Xamarin.Forms.Device.OS) 屬性並將其與 [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS) 、 [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android) 、 [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone) 和 [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) 列舉值進行比較，來取得應用程式執行所在的平臺。 同樣地，其中一個多載 [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) 可用來將平臺特定的值提供給控制項。

不過，由於 Xamarin.Forms 2.3.4 這些 api 已被取代，並已被取代。 [`Device`](xref:Xamarin.Forms.Device)類別現在包含可識別平臺的公用字串常數– [`Device.iOS`](xref:Xamarin.Forms.Device.iOS) 、 [`Device.Android`](xref:Xamarin.Forms.Device.Android) 、 `Device.WinPhone` （已淘汰）、 `Device.WinRT` （已淘汰）、 [`Device.UWP`](xref:Xamarin.Forms.Device.UWP) 和 [`Device.macOS`](xref:Xamarin.Forms.Device.macOS) 。 同樣地，多載已由 [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) 和 api 取代 [`On`](xref:Xamarin.Forms.On) 。

在 c # 中，您可以藉由在 `switch` 屬性上建立語句 [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) ，然後提供 `case` 必要平臺的語句，來提供平臺特定的值：

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

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和 [`On`](xref:Xamarin.Forms.On) 類別在 XAML 中提供相同的功能：

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

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)類別是泛型類別，必須使用 `x:TypeArguments` 符合目標型別的屬性具現化。 在 [`On`](xref:Xamarin.Forms.On) 類別中， [`Platform`](xref:Xamarin.Forms.On.Platform) 屬性可以接受單一 `string` 值或多個逗號分隔 `string` 值。

> [!IMPORTANT]
> 在類別中提供不正確的 `Platform` 屬性值 `On` 不會產生錯誤。 相反地，程式碼會執行，而不會套用平臺特定的值。

或者，您也 `OnPlatform` 可以在 XAML 中使用標記延伸，自訂每個平臺的 UI 外觀。 如需詳細資訊，請參閱[OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="deviceidiom"></a>Device. 用法

`Device.Idiom`屬性可以用來根據應用程式執行所在的裝置來改變版面配置或功能。 [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom)列舉包含下列值：

- **電話**-IPhone、iPod Touch 和 Android 裝置的寬度低於 600 dip ^
- **平板**電腦– IPad、Windows 裝置和 Android 裝置寬于 600 dip ^
- **Desktop** –僅在 Windows 10 桌上型電腦上的[UWP 應用程式](~/xamarin-forms/platform/windows/installation/index.md)中傳回（在行動 `Phone` Windows 裝置上，包括在 Continuum 案例中）
- **電視**– Tizen tv 裝置
- **監看**式– Tizen 監看裝置
- **不支援**-未使用

*^ dip 不一定是實體圖元計數*

`Idiom`屬性特別適合用來建立利用較大螢幕的配置，如下所示：

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

[`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1)類別在 XAML 中提供相同的功能：

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

[`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1)類別是泛型類別，必須使用 `x:TypeArguments` 符合目標型別的屬性具現化。

或者，您也 `OnIdiom` 可以在 XAML 中使用標記延伸，根據應用程式執行所在裝置的用法來自訂 UI 外觀。 如需詳細資訊，請參閱[OnIdiom 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom-markup-extension)。

## <a name="deviceflowdirection"></a>Device. System.windows.frameworkelement.flowdirection

[`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)值 [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) 會抓取列舉值，表示裝置目前正在使用的流程方向。 文字方向即為眼睛瀏覽頁面 UI 項目的方向。 這些列舉值為：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

在 XAML 中，您 [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 可以使用標記延伸來抓取此值 `x:Static` ：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

C # 中的對等程式碼為：

```csharp
this.FlowDirection = Device.FlowDirection;
```

如需流程方向的詳細資訊，請參閱由[右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="devicestyles"></a>裝置。樣式

[ `Styles` 屬性](~/xamarin-forms/user-interface/styles/index.md)包含內建樣式定義，可套用至某些控制項的（例如 `Label` ） `Style` 屬性。 可用的樣式如下：

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device. GetNamedSize

`GetNamedSize`在 c # 程式碼中設定時，可以使用 [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) ：

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicegetnamedcolor"></a>Device. GetNamedColor

Xamarin.Forms4.6 引進了已命名色彩的支援。 [已命名色彩] 是具有不同值的色彩，視裝置上的作用中系統模式（例如，淺色或深色）而定。 在 Android 上，會透過[R. Color](https://developer.android.com/reference/android/R.color#constants_2)類別來存取命名的色彩。 在 iOS 上，命名的色彩稱為[系統色彩](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#system-colors)。 在通用 Windows 平臺上，命名的色彩稱為[XAML 主題資源](/windows/uwp/design/controls-and-patterns/xaml-theme-resources)。

`GetNamedColor`方法可以用來在 Android、iOS 和 UWP 上取出已命名的色彩。 方法會接受 `string` 引數，並傳回 [`Color`](xref:Xamarin.Forms.Color) ：

```csharp
// Retrieve an Android named color
Color color = Device.GetNamedColor(NamedPlatformColor.HoloBlueBright);
```

`Color.Default`當找不到色彩名稱，或在不支援的平臺上叫用時，將會傳回 `GetNamedColor` 。

> [!NOTE]
> 因為方法會傳回 `GetNamedColor` `Color` 平臺特有的，所以通常應該與屬性搭配使用 [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) 。

`NamedPlatformColor`類別包含定義 Android、iOS 和 UWP 之已命名色彩的常數：

| Android | iOS | UWP |
| --- | --- | --- |
| `BackgroundDark` | `Label` | `SystemAltHighColor` |
| `BackgroundLight` | `Link` | `SystemAltLowColor` |
| `Black` | `OpaqueSeparator` | `SystemAltMediumColor` |
| `DarkerGray` | `PlaceholderText` | `SystemAltMediumHighColor` |
| `HoloBlueBright` | `QuaternaryLabel` | `SystemAltMediumLowColor` |
| `HoloBlueDark` | `SecondaryLabel` | `SystemBaseHighColor` |
| `HoloBlueLight` | `Separator` | `SystemBaseLowColor` |
| `HoloGreenDark` | `SystemBlue` | `SystemBaseMediumColor` |
| `HoloGreenLight` | `SystemGray` | `SystemBaseMediumHighColor` |
| `HoloOrangeDark` | `SystemGray2` | `SystemBaseMediumLowColor` |
| `HoloOrangeLight` | `SystemGray3` | `SystemChromeAltLowColor` |
| `HoloPurple` | `SystemGray4` | `SystemChromeBlackHighColor` |
| `HoloRedDark` | `SystemGray5` | `SystemChromeBlackLowColor` |
| `HoloRedLight` | `SystemGray6` | `SystemChromeBlackMediumColor` |
| `TabIndicatorText` | `SystemGreen` | `SystemChromeBlackMediumLowColor` |
| `Transparent` | `SystemIndigo` | `SystemChromeDisabledHighColor` |
| `White` | `SystemListLowColor` | `SystemChromeDisabledLowColor` |
| `WidgetEditTextDark` | `SystemListMediumColor` | `SystemChromeHighColor` |
| | `SystemPink` | `SystemChromeLowColor` |
| | `SystemPurple` | `SystemChromeMediumColor` |
| | `SystemRed` | `SystemChromeMediumLowColor` |
| | `SystemTeal` | `SystemChromeWhiteColor` |
| | `SystemYellow` |
| | `TertiaryLabel` |

## <a name="devicestarttimer"></a>Device. StartTimer

`Device`類別也有方法，可 `StartTimer` 提供簡單的方式來觸發可在通用程式碼中運作的時間相依工作 Xamarin.Forms ，包括 .NET Standard 程式庫。 傳遞 `TimeSpan` 以設定間隔並傳回， `true` 讓計時器保持執行狀態，或在 `false` 目前的叫用之後停止。

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

如果計時器內的程式碼與使用者介面互動（例如設定的文字 `Label` 或顯示警示），則應該在運算式中完成 `BeginInvokeOnMainThread` （請參閱下文）。

> [!NOTE]
> `System.Timers.Timer`和 `System.Threading.Timer` 類別是使用方法的 .NET Standard 替代方案 `Device.StartTimer` 。

## <a name="interact-with-the-ui-from-background-threads"></a>從背景執行緒與 UI 互動

大部分的作業系統（包括 iOS、Android 和通用 Windows 平臺）都會針對涉及使用者介面的程式碼使用單一執行緒模型。 這個執行緒通常稱為*主要執行緒*或*UI 執行緒*。 此模型的結果是，存取使用者介面專案的所有程式碼都必須在應用程式的主執行緒上執行。

應用程式有時會使用背景執行緒來執行可能長時間執行的作業，例如從 web 服務抓取資料。 如果在背景執行緒上執行的程式碼需要存取使用者介面元素，則必須在主執行緒上執行該程式碼。

`Device`類別包含下列 `static` 方法，可以用來與背景執行緒中的使用者介面專案互動：

| 方法 | 引數 | 傳回 | 目的 |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | `Action`在主執行緒上叫用，並不等待它完成。 |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | 在主要執行緒上叫用 `Func<T>`，並等候其完成。 |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | 在主要執行緒上叫用 `Action`，並等候其完成。 |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | 在主要執行緒上叫用 `Func<Task<T>>`，並等候其完成。 |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | 在主要執行緒上叫用 `Func<Task>`，並等候其完成。 |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | 傳回主要執行緒的 `SynchronizationContext`。 |

下列程式碼顯示使用方法的範例 `BeginInvokeOnMainThread` ：

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>相關連結

- [裝置範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [樣式範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [裝置 API](xref:Xamarin.Forms.Device)
