---
title: SkiaSharp 中的點和虛線
description: 本文將探討如何在 SkiaSharp 中，以複雜的方式繪製虛線和虛線，並使用範例程式碼示範這點。
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 47182578a6583dde34cb7f06e3433cdb2703f6ba
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937676"
---
# <a name="dots-and-dashes-in-skiasharp"></a>SkiaSharp 中的點和虛線

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_掌握在 SkiaSharp 中繪製虛線和虛線的複雜性_

SkiaSharp 可讓您繪製不是實心的線條，而是由點和虛線組成：

![點線](dots-images/dottedlinesample.png)

您可以使用*路徑效果*來執行此動作，這是 [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) 您設定為之屬性的類別實例 [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) `SKPaint` 。 您可以使用所定義的其中一個靜態建立方法來建立路徑效果（或結合路徑效果） `SKPathEffect` 。 （ `SKPathEffect` 這是 SkiaSharp 所支援的六種效果之一; 其他則會在[**SkiaSharp 效果**](../effects/index.md)一節中說明）。

若要繪製點線或虛線，請使用 [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) 靜態方法。 有兩個引數：第一個是值的陣列 `float` ，指出點和虛線的長度，以及它們之間的空格長度。 這個陣列必須有偶數的元素，而且應該至少有兩個元素。 （陣列中可以有零個元素，但這會導致實線）。如果有兩個元素，第一個是點或虛線的長度，而第二個是下一個點或虛線之前的間距長度。 如果有兩個以上的元素，則其順序如下：破折號長度、間距長度、破折號長度、間距長度等等。

一般來說，您會想要讓虛線和間距長度成為筆劃寬度的倍數。 例如，如果筆觸寬度為10圖元，則陣列 {10，10} 會繪製虛線，其中的點和間距會與筆觸粗細的長度相同。

不過， `StrokeCap` 物件的設定 `SKPaint` 也會影響這些點和虛線。 您很快就會看到，這會影響此陣列的元素。

**點**和虛線頁面上會示範點線和虛線。 [**DotsAndDashesPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml)會具現化兩個 `Picker` 視圖，一個用於讓您選取筆觸端點，而第二個則用來選取虛線陣列：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.DotsAndDashesPage"
             Title="Dots and Dashes">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>10, 10</x:String>
                    <x:String>30, 10</x:String>
                    <x:String>10, 10, 30, 10</x:String>
                    <x:String>0, 20</x:String>
                    <x:String>20, 20</x:String>
                    <x:String>0, 20, 20, 20</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

 中的前三個專案 `dashArrayPicker` 假設筆劃寬度為10圖元。 {10，10} 陣列適用于虛線，{30，10} 適用于虛線，而 {10，10，30，10} 則適用于點和虛線。 （很快就會討論其他三個）。

[`DotsAndDashesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/DotsAndDashesPage.xaml.cs)程式碼後置檔案包含 `PaintSurface` 事件處理常式，以及用來存取 views 的幾個 helper 常式 `Picker` ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem,
        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string str = (string)picker.SelectedItem;
    string[] strs = str.Split(new char[] { ' ', ',' }, StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

在下列螢幕擷取畫面中，最左側的 iOS 畫面會顯示虛線：

[![點和虛線頁面的三重螢幕擷取畫面](dots-images/dotsanddashes-small.png)](dots-images/dotsanddashes-large.png#lightbox "點和虛線頁面的三重螢幕擷取畫面")

不過，Android 螢幕也應該會使用陣列 {10，10} 顯示虛線，而線條則是實線。 發生什麼事？ 問題在於 Android 螢幕也有的 [筆觸] 上限設定 `Square` 。 這會將所有的虛線以筆劃寬度的一半延伸，使其填滿間距。

若要在使用或的筆劃端點時遇到此 `Square` 問題 `Round` ，您必須以筆劃長度減少陣列中的虛線長度（有時候會產生0的虛線長度），並依筆劃長度增加間距長度。 這是 XAML 檔案中最後三個虛線陣列的 `Picker` 計算方式：

- {10，10} 變成點線的 {0，20}
- {30，10} 變成一條虛線的 {20，20}
- {10，10，30，10} 變成點和虛線的 {0，20，20，20}

UWP 畫面會針對的筆劃端點顯示虛線和虛線 `Round` 。 `Round`筆劃端點通常會在粗線中提供點和虛線的最佳外觀。

到目前為止，此方法的第二個參數並未提及 `SKPathEffect.CreateDash` 。 這個參數的名稱是 `phase` ，它是指行開頭的點和虛線模式中的位移。 例如，如果虛線陣列是 {10，10}，而 `phase` 是10，則行的開頭會是間距，而不是點。

有一個有趣的參數應用程式 `phase` 是在動畫中。 **動畫的螺旋狀**頁面類似于**Archimedean 螺旋**頁面，不同之處在于 [`AnimatedSpiralPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs) 類別會使用方法來繪製參數的動畫 `phase` Xamarin.Forms `Device.Timer` ：

```csharp
public class AnimatedSpiralPage : ContentPage
{
    const double cycleTime = 250;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float dashPhase;

    public AnimatedSpiralPage()
    {
        Title = "Animated Spiral";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            dashPhase = (float)(10 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }
    ···  
}
```

當然，您必須實際執行程式來查看動畫：

[![動畫螺旋頁面的三重螢幕擷取畫面](dots-images/animatedspiral-small.png)](dots-images/animatedspiral-large.png#lightbox "動畫螺旋頁面的三重螢幕擷取畫面")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
