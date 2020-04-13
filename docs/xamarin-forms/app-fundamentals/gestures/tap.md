---
title: 新增點選手勢辨識器
description: 本文說明如何使用點選手勢在 Xamarin.Forms 應用程式中進行點選偵測。 點選偵測是使用 TapGestureRecognizer 類別實作。
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 95f25dbce55e2b960f604b6e304ffb6e8ed775e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771343"
---
# <a name="adding-a-tap-gesture-recognizer"></a>新增點選手勢辨識器

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_點選手勢可用於點選偵測，並使用 TapGestureRecognizer 類別實作。_

要使用點擊手勢使用戶介面元素可按一下,請建立實[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)例、[`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped)處理事件並將新的手勢辨識器添加[`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers)到使用者介面元素的集合中。 以下代碼範例顯示`TapGestureRecognizer`附加到元素的[`Image`](xref:Xamarin.Forms.Image):

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

根據預設，影像會回應單一點選。 將[`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired)屬性設置為等待雙擊(如果需要,請進行更多點擊)。

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

當[`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired)設置在一個上方時,僅當點擊在設定的時間段內發生(此時間段不可配置)時,才會執行事件處理程式。 如果在該期間內未點第二下 (或後續幾下)，則會有效忽略並重新開始「點選計數」。

<a name="Using_Xaml" />

## <a name="using-xaml"></a>使用 XAML

您可以在 XAML 中使用附加屬性將手勢辨識器新增至控制項。 向影像新增[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)的語法如下所示(在本例中定義*雙點接事件*):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

範例中事件處理常式的程式碼會遞增計數器，並將影像從彩色變成黑白。

```csharp
void OnTapGestureRecognizerTapped(object sender, EventArgs args)
{
    tapCount++;
    var imageSender = (Image)sender;
    // watch the monkey go from color to black&white!
    if (tapCount % 2 == 0) {
        imageSender.Source = "tapped.jpg";
    } else {
        imageSender.Source = "tapped_bw.jpg";
    }
}
```

## <a name="using-icommand"></a>使用 ICommand

使用 Model-View-ViewModel (MVVM) 模式的應用程式通常會使用 `ICommand`，而不是直接連接到事件處理常式。 透過程式[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)碼中設定`ICommand`繫結,可以輕鬆地支援:

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

或使用 XAML：

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

您可以在範例中找到此檢視模型的完整程式碼。 相關 `Command` 實作的詳細資料如下所示：

```csharp
public class TapViewModel : INotifyPropertyChanged
{
    int taps = 0;
    ICommand tapCommand;
    public TapViewModel () {
        // configure the TapCommand with a method
        tapCommand = new Command (OnTapped);
    }
    public ICommand TapCommand {
        get { return tapCommand; }
    }
    void OnTapped (object s)  {
        taps++;
        Debug.WriteLine ("parameter: " + s);
    }
    //region INotifyPropertyChanged code omitted
}
```

## <a name="related-links"></a>相關連結

- [TapGesture (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
