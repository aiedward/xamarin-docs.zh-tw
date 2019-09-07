---
title: 點和虛線 SkiaSharp
description: 本文將探討如何精通複雜的 SkiaSharp，在繪製虛線和破折線的線，並示範此範例程式碼。
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 2d02e79ff51468572250d1a7ce7c6d3da103c03a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770520"
---
# <a name="dots-and-dashes-in-skiasharp"></a>點和虛線 SkiaSharp

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_精通複雜的 SkiaSharp 中繪製虛線和破折線的線條_

SkiaSharp 可讓您繪製線條，不是純色，而是句點和連字號所組成：

![](dots-images/dottedlinesample.png "點線")

即可達到這個*路徑效果*，這是執行個體[ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect)類別，您將設定為[ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect)屬性`SKPaint`。 您可以建立路徑的效果 （或合併路徑效果） 使用靜態建立方法所定義的其中一個`SKPathEffect`。 (`SKPathEffect`是其中一種六個效果支援 SkiaSharp; 有些則是一節中所述[ **SkiaSharp 效果**](../effects/index.md)。)

若要繪製虛線的線條，您使用[ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single))靜態方法。 有兩個引數：第一個是`float`值的陣列，表示點和虛線的長度，以及它們之間的空格長度。 此陣列必須有偶數數目的項目，並應該有至少兩個項目。 （可以有零個元素陣列中的，但該導致一條實線。）如果有兩個項目，第一個點或虛線的長度而第二個間距的長度之前的下一個點或虛線。 如果有兩個以上的項目，則它們會依此順序： 虛線長度、 間距長度、 虛線長度、 間距長度等等。

一般而言，您會想要的虛線和間距長度筆觸寬度的倍數。 如果筆觸粗細是 10 個像素，比方說，然後 {10，10} 的陣列將會繪製一條虛線的點和間距是筆劃粗細長度相同。

不過，`StrokeCap`設定`SKPaint`物件也會影響這些點和虛線。 如您所見，這個陣列的元素，會影響。

點線和虛線示範上**點和虛線**頁面。 [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml)檔案具現化兩個`Picker`檢視，可讓您選取的筆觸端點和第二個選取的 dash 陣列的其中一個：

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

 中的前三個項目`dashArrayPicker`假設筆觸粗細是 10 個像素。 {10，10} 陣列是一條虛線，{30，10} 為虛線，和 {10，10，30，10} 是點虛線的線條。 （其他三個將會討論短時間內）。

[ `DotsAndDashesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs)程式碼後置檔案包含`PaintSurface`事件處理常式和 helper 常式，用於存取數個`Picker`檢視：

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

在下列螢幕擷取畫面中，最左邊的 iOS 畫面會顯示一條虛線：

[![](dots-images/dotsanddashes-small.png "點和虛線頁面的三個螢幕擷取畫面")](dots-images/dotsanddashes-large.png#lightbox "點和虛線頁面的三個螢幕擷取畫面")

不過，Android 的畫面也應該顯示使用陣列 {10，10} 一條虛線，但改用這條線是純色。 發生什麼情況？ 問題在於 Android 畫面也具有筆劃 cap 值`Square`。 這會擴充一半筆觸粗細，造成填滿間距的所有連字的號。

若要解決這個問題時使用的筆觸端點`Square`或`Round`，您必須減少陣列中的虛線長度筆劃的長度 （有時導致虛線長度為 0），並增加間隔長度，筆劃的長度。 這是最終的三個破折號陣列中的如何`Picker`計算 XAML 檔案中：

- {10，10} 成為 {0，20} 的一條虛線
- {30，10} 成為 {20，20} 虛線的線條
- {10，10，30，10} 變成虛線和破折線列 {0、 20、 20、 20}

UWP 螢幕顯示點和虛線筆劃列上限的`Round`。 `Round`筆觸端點通常提供最佳的外觀的點和虛線粗線中。

本文至今尚未提到到目前為止進行的第二個參數`SKPathEffect.CreateDash`方法。 此參數之所以名為`phase`它是指在一行的開頭的點和虛線模式內的位移。 比方說，如果 dash 陣列為 {10，10} 和`phase`為 10，則一行的開頭有間距，而不是一個點。

一個有趣的應用程式的`phase`參數處於動畫。 **動畫螺旋**頁面大致**Archimedean 螺旋**頁面上，不同之處在於[ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/AnimatedSpiralPage.cs)類別建立動畫`phase`參數使用Xamarin.Forms`Device.Timer`方法：

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

當然，您必須實際執行程式，以查看動畫：

[![](dots-images/animatedspiral-small.png "以動畫顯示螺旋頁面的三個螢幕擷取畫面")](dots-images/animatedspiral-large.png#lightbox "動畫螺旋頁面的三個螢幕擷取畫面")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
