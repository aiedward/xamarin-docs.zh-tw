---
title: 新增 tap 的筆勢辨識器
description: 這篇文章說明如何使用 Xamarin.Forms 應用程式中點選偵測的點選手勢。 點選偵測是透過 TapGestureRecognizer 類別實作。
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: a28afb30770f15861aef06643e7f51070199ea9b
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "38994850"
---
# <a name="adding-a-tap-gesture-recognizer"></a>新增 tap 的筆勢辨識器

_點選手勢來點選偵測，並使用 TapGestureRecognizer 類別實作。_

若要讓使用者介面項目可點按以點選手勢，建立[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)執行個體，處理[ `Tapped` ](xref:Xamarin.Forms.TapGestureRecognizer.Tapped)事件，並新增至新的筆勢辨識器[`GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers)使用者介面項目的集合。 下列程式碼範例所示`TapGestureRecognizer`附加至[ `Image` ](xref:Xamarin.Forms.Image)項目：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

預設映像會回應單一點選。 設定[ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired)等候點 （或更多的點選，如有必要） 的屬性。

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

當[ `NumberOfTapsRequired` ](xref:Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired)設定上述其中一個，事件處理常式才會執行如果點選出現在一段時間 （這期間不是可設定） 內。 如果第二個 （或後續） 點選不會發生在該期間內有效地被忽略，並點選計數重新啟動。

<a name="Using_Xaml" />

## <a name="using-xaml"></a>使用 Xaml

筆勢辨識器可以加入至控制項，以在 Xaml 中使用附加的屬性。 若要新增的語法[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)影像如下所示 (在此情況下定義*連點兩下*事件):

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
                Tapped="OnTapGestureRecognizerTapped"
                NumberOfTapsRequired="2" />
  </Image.GestureRecognizers>
</Image>
```

（在此範例中） 的事件處理常式的程式碼會遞增計數器，以及從色彩的映像變更為黑色&amp;白色。

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

通常使用 Model View ViewModel (MVVM) 模式的應用程式使用`ICommand`而不是直接連接事件處理常式。 [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)可以輕鬆地支援`ICommand`藉由在程式碼中設定繫結：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

或者，使用 Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

在此範例中，可以找到此檢視模型的完整程式碼。 相關`Command`實作詳細資料如下所示：

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

- [TapGesture （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [TapGestureRecognizer](xref:Xamarin.Forms.TapGestureRecognizer)
