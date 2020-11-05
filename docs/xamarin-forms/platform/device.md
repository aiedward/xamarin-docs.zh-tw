---
title: Xamarin.Forms 裝置類別
description: 本文說明如何使用 Xamarin.Forms 裝置類別，以對每個平臺的功能和版面配置進行細微的控制。
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c28f10441e624a316762c5e677f613335a3f0f94
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373259"
---
# <a name="no-locxamarinforms-device-class"></a>Xamarin.Forms 裝置類別

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithdevice)

[`Device`](xref:Xamarin.Forms.Device)類別包含數個屬性和方法，可協助開發人員以每個平臺為基礎來自訂版面配置和功能。

除了以特定硬體類型和大小的程式碼為目標的方法和屬性之外，此 `Device` 類別還包含可用來與背景執行緒的 UI 控制項互動的方法。 如需詳細資訊，請參閱 [使用背景執行緒與 UI 互動](#interact-with-the-ui-from-background-threads)。

## <a name="provide-platform-specific-values"></a>提供平臺特定值

在 Xamarin.Forms 2.3.4 之前，您可以藉由檢查 [`Device.OS`](xref:Xamarin.Forms.Device.OS) 屬性並將它與 [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS) 、 [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android) 、 [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone) 和 [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) 列舉值進行比較，來取得應用程式執行所在的平臺。 同樣地，您可以使用其中一個多載，將 [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) 平臺特定的值提供給控制項。

不過，由於 Xamarin.Forms 2.3.4 這些 api 已被取代並取代。 [`Device`](xref:Xamarin.Forms.Device)類別現在包含用來識別平臺的公用字串常數– [`Device.iOS`](xref:Xamarin.Forms.Device.iOS) 、 [`Device.Android`](xref:Xamarin.Forms.Device.Android) 、 (已淘汰的 `Device.WinPhone`) 、 `Device.WinRT` (取代的) 、 [`Device.UWP`](xref:Xamarin.Forms.Device.UWP) 和 [`Device.macOS`](xref:Xamarin.Forms.Device.macOS) 。 同樣地，多載已  [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) 取代為 [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) 和 [`On`](xref:Xamarin.Forms.On) api。

在 c # 中，您可以藉由在 `switch` 屬性上建立語句，然後 [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) 提供所需平臺的語句，來提供平臺特定值 `case` ：

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

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和 [`On`](xref:Xamarin.Forms.On) 類別會在 XAML 中提供相同的功能：

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

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)類別是泛型類別，必須使用 `x:TypeArguments` 符合目標型別的屬性來具現化。 在 [`On`](xref:Xamarin.Forms.On) 類別中， [`Platform`](xref:Xamarin.Forms.On.Platform) 屬性可以接受單一 `string` 值或多個逗號分隔 `string` 值。

> [!IMPORTANT]
> 在類別中提供不正確的 `Platform` 屬性值 `On` 將不會產生錯誤。 相反地，程式碼會在沒有套用平臺特定值的情況下執行。

或者， `OnPlatform` 標記延伸可以在 XAML 中用來自訂每個平臺的 UI 外觀。 如需詳細資訊，請參閱 [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="deviceidiom"></a>Device. 用法

您 `Device.Idiom` 可以根據應用程式執行所在的裝置，使用屬性來改變版面配置或功能。 [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom)列舉包含下列值：

- **手機** – IPhone、iPod Touch 和 Android 裝置的寬度低於 600 dip ^
- **平板** 電腦– IPad、Windows 裝置和 Android 裝置寬度超過 600 dip ^
- **Desktop** –只會在 Windows 10 桌上型電腦上的 [UWP 應用程式](~/xamarin-forms/platform/windows/installation/index.md) 中傳回 (在行動 `Phone` Windows 裝置上傳回，包括在 Continuum 案例中) 
- **電視** – Tizen tv 裝置
- **觀賞** – Tizen 監看裝置
- **不支援** -未使用

*^ dip 不一定是實體圖元計數*

`Idiom`屬性特別適合用來建立利用較大畫面的版面配置，如下所示：

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

[`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1)類別會在 XAML 中提供相同的功能：

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

[`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1)類別是泛型類別，必須使用 `x:TypeArguments` 符合目標型別的屬性來具現化。

或者，您也 `OnIdiom` 可以在 XAML 中使用標記延伸，根據應用程式執行所在裝置的用法來自訂 UI 外觀。 如需詳細資訊，請參閱 [OnIdiom 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom-markup-extension)。

## <a name="deviceflowdirection"></a>System.windows.frameworkelement.flowdirection

此 [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 值會抓取 [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) 代表裝置目前所使用之流動方向的列舉值。 文字方向即為眼睛瀏覽頁面 UI 項目的方向。 這些列舉值為：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

在 XAML 中，您 [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 可以使用標記延伸來取出值 `x:Static` ：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

C # 中的對等程式碼為：

```csharp
this.FlowDirection = Device.FlowDirection;
```

如需流程方向的詳細資訊，請參閱由 [右至左的當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="devicestyles"></a>裝置樣式

[ `Styles` 屬性](~/xamarin-forms/user-interface/styles/index.md)包含內建的樣式定義，可以套用至某些控制項的 (例如 `Label`) `Style` 屬性。 可用的樣式如下：

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>GetNamedSize

`GetNamedSize`[`FontSize`](~/xamarin-forms/user-interface/text/fonts.md)在 c # 程式碼中設定時可使用：

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicegetnamedcolor"></a>GetNamedColor

Xamarin.Forms 4.6 導入了對命名色彩的支援。 命名色彩是具有不同值的色彩，例如 (的系統模式、淺色或深色) 在裝置上作用中。 在 Android 上，會透過 [R. Color](https://developer.android.com/reference/android/R.color#constants_2) 類別來存取命名色彩。 在 iOS 上，命名色彩稱為 [系統色彩](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#system-colors)。 在通用 Windows 平臺上，名為 [色彩] 稱為 [ [XAML 主題資源](/windows/uwp/design/controls-and-patterns/xaml-theme-resources)]。

`GetNamedColor`方法可以用來在 Android、iOS 和 UWP 上取出命名的色彩。 方法會採用 `string` 引數，並傳回 [`Color`](xref:Xamarin.Forms.Color) ：

```csharp
// Retrieve an Android named color
Color color = Device.GetNamedColor(NamedPlatformColor.HoloBlueBright);
```

`Color.Default` 當找不到色彩名稱，或 `GetNamedColor` 在不支援的平臺上叫用時，將會傳回。

> [!NOTE]
> 由於方法會傳回 `GetNamedColor` `Color` 平臺特定的，因此通常應該搭配 [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) 屬性使用。

`NamedPlatformColor`類別包含定義 Android、iOS 和 UWP 之命名色彩的常數：

| Android | iOS | macOS | UWP |
| --- | --- | --- | --- |
| `BackgroundDark` | `Label` | `AlternateSelectedControlTextColor` | `SystemAltHighColor` |
| `BackgroundLight` | `Link` | `ControlAccent` | `SystemAltLowColor` |
| `Black` | `OpaqueSeparator` | `ControlBackgroundColor` | `SystemAltMediumColor` |
| `DarkerGray` | `PlaceholderText` | `ControlColor` | `SystemAltMediumHighColor` |
| `HoloBlueBright` | `QuaternaryLabel` | `DisabledControlTextColor` | `SystemAltMediumLowColor` |
| `HoloBlueDark` | `SecondaryLabel` | `FindHighlightColor` | `SystemBaseHighColor` |
| `HoloBlueLight` | `Separator` | `GridColor` | `SystemBaseLowColor` |
| `HoloGreenDark` | `SystemBlue` | `HeaderTextColor` | `SystemBaseMediumColor` |
| `HoloGreenLight` | `SystemGray` | `HighlightColor` | `SystemBaseMediumHighColor` |
| `HoloOrangeDark` | `SystemGray2` | `KeyboardFocusIndicatorColor` | `SystemBaseMediumLowColor` |
| `HoloOrangeLight` | `SystemGray3` | `Label` | `SystemChromeAltLowColor` |
| `HoloPurple` | `SystemGray4` | `LabelColor` | `SystemChromeBlackHighColor` |
| `HoloRedDark` | `SystemGray5` | `Link` | `SystemChromeBlackLowColor` |
| `HoloRedLight` | `SystemGray6` | `LinkColor` | `SystemChromeBlackMediumColor` |
| `TabIndicatorText` | `SystemGreen` | `PlaceholderText` | `SystemChromeBlackMediumLowColor` |
| `Transparent` | `SystemIndigo` | `PlaceholderTextColor` | `SystemChromeDisabledHighColor` |
| `White` | `SystemOrange` | `QuaternaryLabel`| `SystemChromeDisabledLowColor` |
| `WidgetEditTextDark` | `SystemPink` | `QuaternaryLabelColor` | `SystemChromeHighColor` |
| | `SystemPurple` | `SecondaryLabel` | `SystemChromeLowColor` |
| | `SystemRed` | `SecondaryLabelColor` | `SystemChromeMediumColor` |
| | `SystemTeal` | `SelectedContentBackgroundColor` | `SystemChromeMediumLowColor` |
| | `SystemYellow` | `SelectedControlColor` | `SystemChromeWhiteColor` |
| | `TertiaryLabel` | `SelectedControlTextColor` | `SystemListLowColor` |
| | | `SelectedMenuItemTextColor` | `SystemListMediumColor`|
| | | `SelectedTextBackgroundColor` | |
| | | `SelectedTextColor` | |
| | | `Separator` | |
| | | `SeparatorColor` | |
| | | `ShadowColor` | |
| | | `SystemBlue` | |
| | | `SystemGray` | |
| | | `SystemGreen` | |
| | | `SystemIndigo` | |
| | | `SystemOrange` | |
| | | `SystemPink` | |
| | | `SystemPurple` | |
| | | `SystemRed` | |
| | | `SystemTeal` | |
| | | `SystemYellow` | |
| | | `TertiaryLabel` | |
| | | `TertiaryLabelColor` | |
| | | `TextBackgroundColor` | |
| | | `TextColor` | |
| | | `UnderPageBackgroundColor` | |
| | | `UnemphasizedSelectedContentBackgroundColor` | |
| | | `UnemphasizedSelectedTextBackgroundColor` | |
| | | `UnemphasizedSelectedTextColor` | |
| | | `WindowBackgroundColor` | |
| | | `WindowFrameTextColor` | |

## <a name="devicestarttimer"></a>StartTimer

此 `Device` 類別也有一 `StartTimer` 種方法，可提供簡單的方式來觸發在 Xamarin.Forms 通用程式碼（包括 .NET Standard 程式庫）中運作的時間相依工作。 傳遞 `TimeSpan` 來設定間隔，並傳回 `true` 以保持計時器執行，或在 `false` 目前的調用之後停止。

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

如果計時器內的程式碼與使用者介面互動 (例如設定的文字 `Label` 或顯示警示) 應該在運算式內進行， `BeginInvokeOnMainThread` (請參閱下面的) 。

> [!NOTE]
> `System.Timers.Timer`和 `System.Threading.Timer` 類別是使用方法的 .NET Standard 替代方案 `Device.StartTimer` 。

## <a name="interact-with-the-ui-from-background-threads"></a>從背景執行緒與 UI 互動

大部分的作業系統（包括 iOS、Android 和通用 Windows 平臺）都會針對牽涉到使用者介面的程式碼使用單一執行緒模型。 這個執行緒通常稱為 *主執行緒* 或 *UI 執行緒* 。 這個模型的結果是，所有存取使用者介面專案的程式碼都必須在應用程式的主執行緒上執行。

應用程式有時會使用背景執行緒來執行可能長時間執行的作業，例如從 web 服務中取出資料。 如果在背景執行緒上執行的程式碼需要存取使用者介面元素，它必須在主執行緒上執行該程式碼。

`Device`類別包含下列 `static` 方法，這些方法可以用來與背景執行緒中的使用者介面元素互動：

| 方法 | 引數 | 傳回 | 目的 |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | `Action`在主執行緒上叫用，而不會等候它完成。 |
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

- [裝置範例](/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [樣式範例](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [裝置 API](xref:Xamarin.Forms.Device)