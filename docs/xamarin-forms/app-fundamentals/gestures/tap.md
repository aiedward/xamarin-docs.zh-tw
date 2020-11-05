---
title: 新增點一下手勢辨識器
description: 本文說明如何在應用程式中使用點擊手勢來進行點一下偵測 Xamarin.Forms 。 點選偵測是使用 TapGestureRecognizer 類別實作。
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d0b9b5a568a94208d5e177c88d2e73edb54b735c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375183"
---
# <a name="add-a-tap-gesture-recognizer"></a>新增點一下手勢辨識器

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)

_點選手勢可用於點選偵測，並使用 TapGestureRecognizer 類別實作。_

若要讓使用者介面專案可利用點一下手勢來按一下，請建立 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 實例、處理 [`Tapped`](xref:Xamarin.Forms.TapGestureRecognizer.Tapped) 事件，然後將新的手勢辨識器新增至 [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) 使用者介面專案上的集合。 下列程式碼範例顯示 `TapGestureRecognizer` 附加至專案的 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

根據預設，影像會回應單一點選。 設定 [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) 屬性以等候按兩下 (，或在必要時按多下按鍵) 。

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

當 [`NumberOfTapsRequired`](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired) 設定在一個以上的時候，只有在一段時間內發生點按時，才會執行事件處理常式 (這段期間無法設定) 。 如果在該期間內未點第二下 (或後續幾下)，則會有效忽略並重新開始「點選計數」。

## <a name="using-xaml"></a>使用 XAML

您可以在 XAML 中使用附加屬性將手勢辨識器新增至控制項。 將加入 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 至映射的語法如下所示 (在此案例中，定義了 *兩* 個點一下事件) ：

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

使用 Model-View-ViewModel (MVVM) 模式的應用程式通常會使用 `ICommand`，而不是直接連接到事件處理常式。 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)可以透過在程式碼中設定系結，輕鬆地支援 `ICommand` 任何一項：

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

- [TapGesture (Samples)](/samples/xamarin/xamarin-forms-samples/workingwithgestures-tapgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)