---
title: 句點和連字號
description: 主要的複雜性 SkiaSharp 中繪製虛線和破折線的線條
ms.prod: xamarin
ms.assetid: 8E9BCC13-830C-458C-9FC8-ECB4EAE66078
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 46ab21aa5156a6deab5952f165917cc299b500ac
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="dots-and-dashes"></a>句點和連字號

_主要的複雜性 SkiaSharp 中繪製虛線和破折線的線條_

SkiaSharp 可讓您繪製線條，不是實線，而是句點和連字號所組成：

![](dots-images/dottedlinesample.png "點線")

要取得這與*路徑效果*，這是執行個體[ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/)類別，您將設定為[ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/)屬性`SKPaint`。 您可以建立路徑效果 （或合併路徑效果） 使用靜態`Create`所定義的方法`SKPathEffect`。

若要繪製虛線的線條，請使用[ `SKPathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/)靜態方法。 有兩個引數： 這是第一個是陣列`float`值，指出句點和連字號的長度，以及它們之間的空格。 這個陣列必須有偶數的項目，並應該有至少兩個項目。 （可以有零個元素陣列中的，但該實線中。）如果有兩個項目，第一個是點或虛線的長度，且第二個間隔的長度之前的下一個點或虛線。 如果兩個以上的項目，則它們會依此順序： 虛線長度、 間距的長度、 虛線的長度、 間距長度等等。

一般而言，您要進行的虛線和間距的長度筆劃寬度的倍數。 如果筆劃寬度為 10 個像素，比方說，然後陣列 {10，10} 時，會繪製虛線點和間距的長度相同的筆劃粗細。

不過，`StrokeCap`設定`SKPaint`物件也會影響這些句點和連字號。 您會發現，這個陣列的元素，有影響。

點和虛線示範上**點和虛線**頁面。 [ **DotsAndDashesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml)檔案會呈現兩個`Picker`檢視時，可讓您選取的筆觸 cap 和選取虛線陣列的第二個的其中一個：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.DotsAndDashesPage"
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
            <Picker.Items>
                <x:String>Butt</x:String>
                <x:String>Round</x:String>
                <x:String>Square</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="dashArrayPicker"
                Title="Dash Array"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>10, 10</x:String>
                <x:String>30, 10</x:String>
                <x:String>10, 10, 30, 10</x:String>
                <x:String>0, 20</x:String>
                <x:String>20, 20</x:String>
                <x:String>0, 20, 20, 20</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

中的前三個項目`dashArrayPicker`假設筆劃寬度為 10 個像素為單位。 {10，10} 陣列為虛線，{30，10} 為虛線，和 {10，10，30，10} 是點虛線的線條。 （其他三個將討論很快。）

[ `DotsAndDashesPage`程式碼後置檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/DotsAndDashesPage.xaml.cs)包含`PaintSurface`事件處理常式和幾個 helper 常式存取`Picker`檢視：

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
        StrokeCap = (SKStrokeCap)Enum.Parse(typeof(SKStrokeCap),
                        strokeCapPicker.Items[strokeCapPicker.SelectedIndex]),

        PathEffect = SKPathEffect.CreateDash(GetPickerArray(dashArrayPicker), 20)
    };

    SKPath path = new SKPath();
    path.MoveTo(0.2f * info.Width, 0.2f * info.Height);
    path.LineTo(0.8f * info.Width, 0.8f * info.Height);
    path.LineTo(0.2f * info.Width, 0.8f * info.Height);
    path.LineTo(0.8f * info.Width, 0.2f * info.Height);

    canvas.DrawPath(path, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }

    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}

float[] GetPickerArray(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return new float[0];
    }

    string[] strs = picker.Items[picker.SelectedIndex].Split(new char[] { ' ', ',' },
                                                             StringSplitOptions.RemoveEmptyEntries);
    float[] array = new float[strs.Length];

    for (int i = 0; i < strs.Length; i++)
    {
        array[i] = Convert.ToSingle(strs[i]);
    }
    return array;
}
```

在下列螢幕擷取畫面中，最左邊的 [iOS] 畫面會顯示點線：

[![](dots-images/dotsanddashes-small.png "句點和連字號 頁面的三個螢幕擷取畫面")](dots-images/dotsanddashes-large.png#lightbox "句點和連字號 頁面的三個螢幕擷取畫面")

不過，[Android] 畫面也應該顯示使用陣列 {10，10} 虛線，但改為在列是可靠。 發生什麼情況？ 問題在於 [Android] 畫面也具有筆劃 cap 值`Square`。 這會擴充的筆劃寬度的一半，讓他們填滿間距的所有連字號。

若要使用的筆觸端點時，取得解決這個問題`Square`或`Round`，您必須減少陣列中的虛線長度筆劃長度 （有時導致虛線長度為 0），並增加筆劃長度的間隔長度。 這是最後三虛線陣列中的- `Picker` XAML 檔案中會計算：

- {10，10} 成為 {0，20} 的虛線
- {30，10} 成為 {20，20} 的虛線
- {10，10，30，10} 成為 {0，20，20，20} 的虛線和破折線線條

Windows 螢幕顯示點和虛線程式行及筆劃覆蓋的`Round`。 `Round`筆觸端點通常會提供最佳的外觀句點和連字號的粗線。

到目前為止沒有提及發出第二個參數的`SKPathEffect.CreateDash`方法。 此參數之所以名為`phase`它是指點和虛線圖樣的一行的開頭的位移。 例如，如果虛線陣列是 {10，10} 和`phase`為 10，則行開頭的間距，而不是一個點。

一個有趣的應用程式的`phase`參數是在動畫。 **動畫老舊**頁面是類似於**Archimedean 老舊**頁面上，不同處在於[ `AnimatedSpiralPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/AnimatedSpiralPage.cs)類別以動畫方式顯示`phase`參數。 此頁面也會示範另一個動畫的方法。 先前的範例[ `PulsatingEllipsePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml.cs)用`Task.Delay`方法，以控制動畫。 這個範例會改為使用 Xamarin.Forms`Device.Timer`方法：


```csharp
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
```

當然，您必須實際執行程式，請參閱動畫：

[![](dots-images/animatedspiral-small.png "三個螢幕擷取畫面顯示動畫老舊頁面的")](dots-images/animatedspiral-large.png#lightbox "動畫老舊頁面的三個螢幕擷取畫面")

您現在已經瞭解如何繪製線條，以及如何定義使用參數化的方程式的曲線。 等以後再發行一節將討論各種類型的曲線，`SKPath`支援。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
