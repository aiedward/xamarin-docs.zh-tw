---
title: Xamarin.Forms 雙螢幕裝置功能
description: 本指南說明如何使用 Xamarin.Forms DualScreenInfo 類別，將雙螢幕裝置（例如 Surface 雙核和 Surface Neo）的應用程式體驗優化。
ms.prod: xamarin
ms.assetid: dd5eb074-f4cb-4ab4-b47d-76f862ac7cfa
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4e6bbd40fb80c2884013647ced3c3660ab4af738
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562674"
---
# <a name="no-locxamarinforms-dualscreeninfo-helper-class"></a>Xamarin.Forms DualScreenInfo helper 類別

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

`DualScreenInfo` 類別可讓您決定檢視所在的窗格、窗格大小、裝置方向、鉸鏈角度等。

## <a name="configure-dualscreeninfo"></a>設定 DualScreenInfo

請遵循下列指示，在您的應用程式中建立雙螢幕版面配置：

1. 遵循 [快速](index.md) 入門指示來新增 NuGet 並設定 Android `MainActivity` 類別。
1. 新增 `using Xamarin.Forms.DualScreen;` 至您的類別檔案。
1. `DualScreenInfo.Current`在您的應用程式中使用類別。

## <a name="properties"></a>屬性

- 當跨越兩個螢幕時，`SpanningBounds` 會傳回兩個矩形，表示每個可見區域的界限。 如果未跨越視窗，則會傳回空陣列。
- `HingeBounds` 表示螢幕上的鉸鏈位置。
- `IsLandscape`：指出裝置是否為橫向。 因為在應用程式跨越兩個螢幕時，原生方向 API 並不會正確回報方向，所以這會非常有幫助。
- `SpanMode`：指出版面配置處於直向、橫向或單一窗格模式。

此外， `PropertyChanged` 當任何屬性變更時，就會引發事件，而 `HingeAngleChanged` 當轉軸角度變更時，就會引發此事件。

## <a name="poll-hinge-angle-on-android-and-uwp"></a>在 Android 和 UWP 上輪詢轉軸角度

`DualScreenInfo`從 Android 和 UWP 平臺專案存取時，可以使用下列方法：

- `GetHingeAngleAsync`：擷取裝置鉸鏈的目前角度。 使用模擬器時，可以藉由修改壓力感應器來設定 HingeAngle。

您可以從 Android 和 UWP 的自訂轉譯器叫用此方法。 下列程式碼顯示 Android 自訂轉譯器範例：

```csharp
public class HingeAngleLabelRenderer : Xamarin.Forms.Platform.Android.FastRenderers.LabelRenderer
{
    System.Timers.Timer _hingeTimer;
    public HingeAngleLabelRenderer(Context context) : base(context)
    {
    }

    async void OnTimerElapsed(object sender, System.Timers.ElapsedEventArgs e)
    {
        if (_hingeTimer == null)
            return;

        _hingeTimer.Stop();
        var hingeAngle = await DualScreenInfo.Current.GetHingeAngleAsync();

        Device.BeginInvokeOnMainThread(() =>
        {
            if (_hingeTimer != null)
                Element.Text = hingeAngle.ToString();
        });

        if (_hingeTimer != null)
            _hingeTimer.Start();
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Label> e)
    {
        base.OnElementChanged(e);

        if (_hingeTimer == null)
        {
            _hingeTimer = new System.Timers.Timer(100);
            _hingeTimer.Elapsed += OnTimerElapsed;
            _hingeTimer.Start();
        }
    }

    protected override void Dispose(bool disposing)
    {
        if (_hingeTimer != null)
        {
            _hingeTimer.Elapsed -= OnTimerElapsed;
            _hingeTimer.Stop();
            _hingeTimer = null;
        }

        base.Dispose(disposing);
    }
}
```

## <a name="access-dualscreeninfo-in-your-application-window"></a>在您的應用程式視窗存取 DualScreenInfo

下列程式碼示範如何在您的應用程式視窗存取 `DualScreenInfo`：

```csharp
DualScreenInfo currentWindow = DualScreenInfo.Current;

// Retrieve absolute position of the hinge on the screen
var hingeBounds = currentWindow.HingeBounds;

// check if app window is spanned across two screens
if(currentWindow.SpanMode == TwoPaneViewMode.SinglePane)
{
    // window is only on one screen
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Tall)
{
    // window is spanned across two screens and oriented top-bottom
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Wide)
{
    // window is spanned across two screens and oriented side-by-side
}

// Detect if any of the properties on DualScreenInfo change.
// This is useful to detect if the app window gets spanned
// across two screens or put on only one  
currentWindow.PropertyChanged += OnDualScreenInfoChanged;
```

## <a name="apply-dualscreeninfo-to-layouts"></a>將 DualScreenInfo 套用至版面配置

`DualScreenInfo` 類別包含一個可採用版面配置的建構函式，並會提供您裝置上兩個螢幕相對版面配置的相關資訊：

```xaml
<Grid x:Name="grid" ColumnSpacing="0">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="{Binding Column1Width}" />
        <ColumnDefinition Width="{Binding Column2Width}" />
        <ColumnDefinition Width="{Binding Column3Width}" />
    </Grid.ColumnDefinitions>
    <Label FontSize="Large"
           VerticalOptions="Center"
           HorizontalOptions="End"
           Text="I should be on the left side of the hinge" />
    <Label FontSize="Large"
           VerticalOptions="Center"
           HorizontalOptions="Start"
           Grid.Column="2"
           Text="I should be on the right side of the hinge" />
</Grid>
```

```csharp
public partial class GridUsingDualScreenInfo : ContentPage
{
    public DualScreenInfo DualScreenInfo { get; }
    public double Column1Width { get; set; }
    public double Column2Width { get; set; }
    public double Column3Width { get; set; }

    public GridUsingDualScreenInfo()
    {
        InitializeComponent();
        DualScreenInfo = new DualScreenInfo(grid);
        BindingContext = this;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        DualScreenInfo.PropertyChanged += OnInfoPropertyChanged;
        UpdateColumns();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        DualScreenInfo.PropertyChanged -= OnInfoPropertyChanged;
    }

    void UpdateColumns()
    {
        // Check if grid is on two screens
        if (DualScreenInfo.SpanningBounds.Length > 0)
        {
            // set the width of the first column to the width of the layout
            // that's on the left screen
            Column1Width = DualScreenInfo.SpanningBounds[0].Width;

            // set the middle column to the width of the hinge
            Column2Width = DualScreenInfo.HingeBounds.Width;

            // set the width of the third column to the width of the layout
            // that's on the right screen
            Column3Width = DualScreenInfo.SpanningBounds[1].Width;
        }
        else
        {
            Column1Width = 100;
            Column2Width = 0;
            Column3Width = 100;
        }

        OnPropertyChanged(nameof(Column1Width));
        OnPropertyChanged(nameof(Column2Width));
        OnPropertyChanged(nameof(Column3Width));

    }

    void OnInfoPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
    {
        UpdateColumns();
    }
}
```

下列螢幕擷取畫面顯示產生的版面配置：

![在兩個螢幕上定位格線](dual-screen-info-images/grid-on-two-screens.png)

## <a name="related-links"></a>相關連結

- [DualScreen (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)