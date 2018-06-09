---
title: 加入點選筆勢筆勢辨識器
description: 本文說明如何使用 Xamarin.Forms 應用程式中點選偵測的點選手勢。 點選偵測是使用 TapGestureRecognizer 類別實作。
ms.prod: xamarin
ms.assetid: 1D150BAF-4157-49BC-90A0-153323B8EBCF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: bbe4ca7a1080459b8aeb33640be5158b15e97715
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240661"
---
# <a name="adding-a-tap-gesture-gesture-recognizer"></a>加入點選筆勢筆勢辨識器

_點選手勢來進行點選偵測，並使用 TapGestureRecognizer 類別實作。_

## <a name="overview"></a>總覽

若要讓使用者介面項目可點按的點選手勢，建立[ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)執行個體，處理[ `Tapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.TapGestureRecognizer.Tapped/)事件並加入至新的筆勢辨識器[`GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/)使用者介面項目的集合。 下列程式碼範例示範`TapGestureRecognizer`附加至[ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/)項目：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.Tapped += (s, e) => {
    // handle the tap
};
image.GestureRecognizers.Add(tapGestureRecognizer);
```

根據預設影像會回應單一點選。 設定[ `NumberOfTapsRequired` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired/)等候點 （或多個必要的點選） 的屬性。

```csharp
tapGestureRecognizer.NumberOfTapsRequired = 2; // double-tap
```

當[ `NumberOfTapsRequired` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.NumberOfTapsRequired/)設定上述其中一個，事件處理常式將只時執行的時間 （這個週期是不可設定） 設定的期間內發生的點選。 如果在該期間內，第二個 （或後續） 點選並不會有效地略過並點選計數會重新啟動。

<a name="Using_Xaml" />

## <a name="using-xaml"></a>使用 Xaml

筆勢辨識器可以加入至控制項，以在 Xaml 中使用的附加的屬性。 若要加入語法[ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)影像如下所示 (在此情況下定義*連點兩下*事件):

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

通常使用 Mvvm 模式應用程式使用`ICommand`而不是直接連接事件處理常式。 [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)可以輕鬆地支援`ICommand`藉由在程式碼中設定繫結：

```csharp
var tapGestureRecognizer = new TapGestureRecognizer();
tapGestureRecognizer.SetBinding (TapGestureRecognizer.CommandProperty, "TapCommand");
image.GestureRecognizers.Add(tapGestureRecognizer);
```

或使用 Xaml:

```xaml
<Image Source="tapped.jpg">
    <Image.GestureRecognizers>
        <TapGestureRecognizer
            Command="{Binding TapCommand}"
            CommandParameter="Image1" />
    </Image.GestureRecognizers>
</Image>
```

此檢視模型的完整程式碼可以在此範例中找到。 相關`Command`實作詳細資料如下所示：

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

## <a name="summary"></a>總結

點選手勢用於點選偵測，而且透過實作[ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)類別。 若要識別點選兩下，可以指定分接器數目 (或三重下或多個點選) 的行為。


## <a name="related-links"></a>相關連結

- [TapGesture （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/TapGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [TapGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)
