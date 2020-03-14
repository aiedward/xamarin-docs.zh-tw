---
title: Xamarin. Forms 裝置類別
description: 本文說明如何使用 Xamarin 裝置類別，以更精細的方式控制每個平臺的功能和版面配置。
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: 25ddbea75d0fd6858f848499281da5d5f0b68171
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305322"
---
# <a name="xamarinforms-device-class"></a>Xamarin. Forms 裝置類別

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

[`Device`](xref:Xamarin.Forms.Device)類別包含一些屬性和方法，可協助開發人員根據每個平臺自訂版面配置和功能。

除了在特定硬體類型和大小以程式碼為目標的方法和屬性之外，`Device` 類別還包含可用來從背景執行緒與 UI 控制項互動的方法。 如需詳細資訊，請參閱[從背景執行緒與 UI 互動](#interact-with-the-ui-from-background-threads)。

## <a name="providing-platform-specific-values"></a>提供平臺特定的值

在2.3.4 之前，您可以藉由檢查[`Device.OS`](xref:Xamarin.Forms.Device.OS)屬性並將其與[`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)、 [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android)、 [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone)和[`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows)列舉值進行比較，來取得應用程式執行所在的平臺。 同樣地，其中一個[`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))多載可用來將平臺特定的值提供給控制項。

不過，由於2.3.4 這些 Api 的形式已淘汰並取代。 [`Device`](xref:Xamarin.Forms.Device)類別現在包含可識別平臺的公用字串常數– [`Device.iOS`](xref:Xamarin.Forms.Device.iOS)、 [`Device.Android`](xref:Xamarin.Forms.Device.Android)、`Device.WinPhone`（已淘汰）、`Device.WinRT` （已淘汰）、 [`Device.UWP`](xref:Xamarin.Forms.Device.UWP)和[`Device.macOS`](xref:Xamarin.Forms.Device.macOS)。 同樣地， [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action))多載已由[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和[`On`](xref:Xamarin.Forms.On) api 取代。

在C#中，您可以在[`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform)屬性上建立 `switch` 語句，然後為必要的平臺提供 `case` 語句，藉以提供平臺特定的值：

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

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和[`On`](xref:Xamarin.Forms.On)類別在 XAML 中提供相同的功能：

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

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)類別是泛型類別，必須使用符合目標型別的 `x:TypeArguments` 屬性具現化。 在[`On`](xref:Xamarin.Forms.On)類別中， [`Platform`](xref:Xamarin.Forms.On.Platform)屬性可以接受單一 `string` 值，或以逗號分隔的多個 `string` 值。

> [!IMPORTANT]
> 在 `On` 類別中提供不正確的 `Platform` 屬性值，將不會產生錯誤。 相反地，程式碼會執行，而不會套用平臺特定的值。

或者，您也可以在 XAML 中使用 `OnPlatform` 標記延伸，自訂每個平臺的 UI 外觀。 如需詳細資訊，請參閱[OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform)。

## <a name="deviceidiom"></a>Device. 用法

`Device.Idiom` 屬性可用來根據應用程式執行所在的裝置來改變版面配置或功能。 [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom)列舉包含下列值：

- **電話**-IPhone、iPod Touch 和 Android 裝置的寬度低於 600 dip ^
- **平板**電腦– IPad、Windows 裝置和 Android 裝置寬于 600 dip ^
- **Desktop** –只會在 Windows 10 桌上型電腦上的[UWP 應用程式](~/xamarin-forms/platform/windows/installation/index.md)中傳回（會在行動 Windows 裝置上傳回 `Phone`，包括在 Continuum 案例中）
- **電視**– Tizen tv 裝置
- **監看**式– Tizen 監看裝置
- **不支援**-未使用

*^ dip 不一定是實體圖元計數*

`Idiom` 屬性特別適合用來建立利用較大螢幕的版面配置，如下所示：

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

[`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1)類別是泛型類別，必須使用符合目標型別的 `x:TypeArguments` 屬性具現化。

或者，您也可以在 XAML 中使用 `OnIdiom` 標記延伸，根據應用程式執行所在裝置的用法來自訂 UI 外觀。 如需詳細資訊，請參閱[OnIdiom 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom)。

## <a name="deviceflowdirection"></a>Device. System.windows.frameworkelement.flowdirection

[`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)值會抓取[`FlowDirection`](xref:Xamarin.Forms.FlowDirection)列舉值，表示裝置目前正在使用的流程方向。 流動方向即為眼睛掃描頁面 UI 元素的方向。 這些列舉值為：

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

在 XAML 中，可以使用 `x:Static` 標記延伸來抓取[`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)值：

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

中C#的對等程式碼為：

```csharp
this.FlowDirection = Device.FlowDirection;
```

如需流程方向的詳細資訊，請參閱由[右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="devicestyles"></a>裝置。樣式

[`Styles` 屬性](~/xamarin-forms/user-interface/styles/index.md)包含內建樣式定義，可以套用至某些控制項（例如 `Label`） `Style` 屬性。 可用的樣式如下：

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device. GetNamedSize

在程式碼中C#設定[`FontSize`](~/xamarin-forms/user-interface/text/fonts.md)時，可以使用 `GetNamedSize`：

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicestarttimer"></a>Device. StartTimer

`Device` 類別也具有 `StartTimer` 方法，可提供簡單的方式來觸發在 Xamarin 中運作的時間相依工作。這些通用程式碼包括 .NET Standard 程式庫。 傳遞 `TimeSpan` 以設定間隔，並傳回 `true`，讓計時器保持執行或 `false`，以在目前的叫用之後將其停止。

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

如果計時器內的程式碼與使用者介面互動（例如設定 `Label` 的文字或顯示警示），則應該在 `BeginInvokeOnMainThread` 運算式中完成（請參閱下文）。

> [!NOTE]
> `System.Timers.Timer` 和 `System.Threading.Timer` 類別 .NET Standard 使用 `Device.StartTimer` 方法的替代專案。

## <a name="interact-with-the-ui-from-background-threads"></a>從背景執行緒與 UI 互動

大部分的作業系統（包括 iOS、Android 和通用 Windows 平臺）都會針對涉及使用者介面的程式碼使用單一執行緒模型。 這個執行緒通常稱為*主要執行緒*或*UI 執行緒*。 此模型的結果是，存取使用者介面專案的所有程式碼都必須在應用程式的主執行緒上執行。

應用程式有時會使用背景執行緒來執行可能長時間執行的作業，例如從 web 服務抓取資料。 如果在背景執行緒上執行的程式碼需要存取使用者介面元素，則必須在主執行緒上執行該程式碼。

`Device` 類別包含下列 `static` 方法，可用來與背景執行緒中的使用者介面元素互動：

| 方法 | 引數 | 傳回值 | 目的 |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | 在主執行緒上叫用 `Action`，而不等候它完成。 |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | 在主要執行緒上叫用 `Func<T>`，並等候其完成。 |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | 在主要執行緒上叫用 `Action`，並等候其完成。 |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | 在主要執行緒上叫用 `Func<Task<T>>`，並等候其完成。 |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | 在主要執行緒上叫用 `Func<Task>`，並等候其完成。 |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | 傳回主要執行緒的 `SynchronizationContext`。 |

下列程式碼顯示使用 `BeginInvokeOnMainThread` 方法的範例：

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>相關連結

- [裝置範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [樣式範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [裝置](xref:Xamarin.Forms.Device)
