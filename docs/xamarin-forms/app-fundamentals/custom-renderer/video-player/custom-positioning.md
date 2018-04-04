---
title: 自訂視訊定位
ms.prod: xamarin
ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 51256f078411f0ade72867544ced3d9a3a91d7b4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="custom-video-positioning"></a>自訂視訊定位

每個平台所實作的傳輸控制項包括位置列。 此列類似的滑桿或捲軸，且其總持續時間內顯示視訊的目前位置。 此外，使用者可以操作位置列來前後移動瀏視訊中的新位置。

本文示範如何實作您自己的自訂位置列。

## <a name="the-duration-property"></a>Duration 屬性

資訊的一個項目，`VideoPlayer`必須支援自訂位置列是視訊的時間長度。 `VideoPlayer`定義唯讀`Duration`型別的屬性`TimeSpan`:

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Duration read-only property
        private static readonly BindablePropertyKey DurationPropertyKey =
            BindableProperty.CreateReadOnly(nameof(Duration), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public static readonly BindableProperty DurationProperty = DurationPropertyKey.BindableProperty;

        public TimeSpan Duration
        {
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        TimeSpan IVideoPlayerController.Duration
        {
            set { SetValue(DurationPropertyKey, value); }
            get { return Duration; }
        }
        ···
    }
}
```

像`Status`屬性中所述[前一篇文章](custom-transport.md)，這個`Duration`屬性是唯讀的。 它定義為具有私用`BindablePropertyKey`和只能藉由參考設定`IVideoPlayerController`介面，其中包含這`Duration`屬性：

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPlayerController
    {
        VideoStatus Status { set; get; }

        TimeSpan Duration { set; get; }
    }
}
```

同時也請注意屬性變更處理常式呼叫方法，名為`SetTimeToEnd`，會在本文稍後描述。

視訊的持續時間會*不*後立即可用`Source`屬性`VideoPlayer`設定。 視訊檔案必須先部分下載基礎視訊播放程式可以判斷其持續時間。

以下是每個平台轉譯器如何取得視訊的持續時間：

### <a name="video-duration-in-ios"></a>在 iOS 中視訊的持續時間

在 iOS 中，視訊的持續時間取自`Duration`屬性`AVPlayerItem`，但不是能立即晚於`AVPlayerItem`建立。 您可設定的 iOS 觀察者`Duration`屬性，但`VideoPlayerRenderer`取得持續時間，以`UpdateStatus`方法，這個方法會呼叫 10 次第二個：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ((IVideoPlayerController)Element).Duration = ConvertTime(playerItem.Duration);
                ···
            }
        }

        TimeSpan ConvertTime(CMTime cmTime)
        {
            return TimeSpan.FromSeconds(Double.IsNaN(cmTime.Seconds) ? 0 : cmTime.Seconds);

        }
        ···
    }
}
```

`ConvertTime`方法轉換`CMTime`物件`TimeSpan`值。

### <a name="video-duration-in-android"></a>在 Android 影片持續時間

`Duration`屬性 Android`VideoView`報告有效的持續時間，以毫秒為單位時`Prepared`事件`VideoView`引發。 在 Android`VideoPlayerRenderer`類別用來取得該處理常式`Duration`屬性：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnVideoViewPrepared(object sender, EventArgs args)
        {
            ···
            ((IVideoPlayerController)Element).Duration = TimeSpan.FromMilliseconds(videoView.Duration);
        }
        ···
    }
}
```

### <a name="video-duration-in-uwp"></a>在 UWP 中視訊的持續時間

`NaturalDuration`屬性`MediaElement`是`TimeSpan`值和生效時`MediaElement`引發`MediaOpened`事件：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        void OnMediaElementMediaOpened(object sender, RoutedEventArgs args)
        {
            ((IVideoPlayerController)Element).Duration = Control.NaturalDuration.TimeSpan;
        }
        ···
    }
}
```

## <a name="the-position-property"></a>位置屬性

`VideoPlayer` 也需要`Position`屬性，從零到提高`Duration`為播放視訊的。 `VideoPlayer` 實作此屬性視為`Position`屬性在 UWP `MediaElement`，這是一般可繫結屬性與公用`set`和`get`存取子：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        // Position property
        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan(),
                propertyChanged: (bindable, oldValue, newValue) => ((VideoPlayer)bindable).SetTimeToEnd());

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }
        ···
    }
}
```

`get`存取子會傳回目前位置的視訊，因為它正在執行，但`set`存取子為了回應使用者的位置列操作的向前或向後移動視訊的位置。

在 iOS 和 Android，取得目前位置的屬性只剩`get`存取子和`Seek`方法可用於執行此第二個工作。 如果您認為其個別相關`Seek`方法似乎是更實用的方法比單一個`Position`屬性。 單一`Position`屬性具有固有的問題： 播放視訊時`Position`屬性必須持續更新來反映新的位置。 您不想大多數的變更，但`Position`屬性，以移至新位置視訊中視訊播放程式。 如果發生這種情況，視訊播放程式就會搜尋到最後一個值的回應`Position`屬性，與視訊不會前進。

儘管實作的困難`Position`屬性`set`和`get`存取子中，這個方法已選擇，因為它是與 UWP 一致`MediaElement`，而且它具有資料繫結很大的好處： `Position`屬性`VideoPlayer`可以繫結至滑桿用來顯示位置及搜尋到新位置。 不過，幾個預防措施，就需要實作此`Position`屬性，以避免迴圈意見反應。

### <a name="setting-and-getting-ios-position"></a>設定和取得 iOS 位置

在 iOS 中，`CurrentTime`屬性`AVPlayerItem`物件可指出播放視訊的目前位置。 IOS`VideoPlayerRenderer`設定`Position`屬性`UpdateStatus`處理常式，它會設定的同時`Duration`屬性：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            if (playerItem != null)
            {
                ···
                ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, ConvertTime(playerItem.CurrentTime));
            }
        }
        ···
    }
}
```

轉譯器會偵測何時`Position`屬性設定為`VideoPlayer`中已經變更`OnElementPropertyChanged`覆寫，並使用該新值來呼叫`Seek`方法`AVPlayer`物件：

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                TimeSpan controlPosition = ConvertTime(player.CurrentTime);

                if (Math.Abs((controlPosition - Element.Position).TotalSeconds) > 1)
                {
                    player.Seek(CMTime.FromSeconds(Element.Position.TotalSeconds, 1));
                }
            }
        }
        ···
    }
}
```

請注意，每次`Position`屬性`VideoPlayer`設定從`OnUpdateStatus`處理常式，`Position`屬性引發`PropertyChanged`中偵測到的事件`OnElementPropertyChanged`覆寫。 這些變更，大部分`OnElementPropertyChanged`方法應該執行任何動作。 否則，每次變更視訊的位置中，它會移到它達到相同的位置 ！

若要避免這種意見，`OnElementPropertyChanged`方法只會呼叫`Seek`時之間的差異`Position`屬性和目前的位置`AVPlayer`大於一秒。

### <a name="setting-and-getting-android-position"></a>設定和取得 Android 位置

如同 iOS 轉譯器，在 Android`VideoPlayerRenderer`設定的新值`Position`屬性`OnUpdateStatus`處理常式。 `CurrentPosition`屬性`VideoView`包含毫秒的單位中的新位置：

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ···
            TimeSpan timeSpan = TimeSpan.FromMilliseconds(videoView.CurrentPosition);
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, timeSpan);
        }
        ···
    }
}
```

此外，如同 iOS 轉譯器中，Android 轉譯器會呼叫`SeekTo`方法`VideoView`時`Position`屬性已變更，但僅限於時變更為一秒從不同`CurrentPosition`值`VideoView`:

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs(videoView.CurrentPosition - Element.Position.TotalMilliseconds) > 1000)
                {
                    videoView.SeekTo((int)Element.Position.TotalMilliseconds);
                }
            }
        }
        ···
    }
}
```

### <a name="setting-and-getting-uwp-position"></a>設定和取得 UWP 位置

UWP`VideoPlayerRenderer`控點`Position`iOS 和 Android 的轉譯器，為相同的方式，但因為`Position`屬性 UWP`MediaElement`也`TimeSpan`值，不不需要任何轉換：

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        ···
        protected override void OnElementPropertyChanged(object sender, PropertyChangedEventArgs args)
        {
            ···
            else if (args.PropertyName == VideoPlayer.PositionProperty.PropertyName)
            {
                if (Math.Abs((Control.Position - Element.Position).TotalSeconds) > 1)
                {
                    Control.Position = Element.Position;
                }
            }
        }
        ···
        void OnUpdateStatus(object sender, EventArgs args)
        {
            ((IElementController)Element).SetValueFromRenderer(VideoPlayer.PositionProperty, Control.Position);
        }
        ···
    }
}
```

## <a name="calculating-a-timetoend-property"></a>計算 TimeToEnd 屬性

有時視訊播放程式顯示視訊中的剩餘時間。 這個值會開始於何時會開始將視訊及視訊結束時，會減少向零的視訊的持續時間。

`VideoPlayer` 包含唯讀`TimeToEnd`處理完全類別內的屬性會根據變更`Duration`和`Position`屬性：

```csharp
namespace FormsVideoLibrary
{
    public class VideoPlayer : View, IVideoPlayerController
    {
        ···
        private static readonly BindablePropertyKey TimeToEndPropertyKey =
            BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(VideoPlayer), new TimeSpan());

        public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

        public TimeSpan TimeToEnd
        {
            private set { SetValue(TimeToEndPropertyKey, value); }
            get { return (TimeSpan)GetValue(TimeToEndProperty); }
        }

        void SetTimeToEnd()
        {
            TimeToEnd = Duration - Position;
        }
        ···
    }
}
```

`SetTimeToEnd`從這兩者的屬性變更處理常式呼叫方法`Duration`和`Position`。

## <a name="a-custom-slider-for-video"></a>自訂的滑桿，以視訊

您可撰寫自訂控制項位置列，或使用 Xamarin.Forms`Slider`或衍生自類別`Slider`，如下所示`PositionSlider`類別。 類別會定義兩個新的內容，名為`Duration`和`Position`型別的`TimeSpan`是資料繫結中的相同名稱的兩個屬性要`VideoPlayer`。 請注意，預設的繫結模式`Position`屬性是雙向：

```csharp
namespace FormsVideoLibrary
{
    public class PositionSlider : Slider
    {
        public static readonly BindableProperty DurationProperty =
            BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                    });

        public TimeSpan Duration
        {
            set { SetValue(DurationProperty, value); }
            get { return (TimeSpan)GetValue(DurationProperty); }
        }

        public static readonly BindableProperty PositionProperty =
            BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                    defaultBindingMode: BindingMode.TwoWay,
                                    propertyChanged: (bindable, oldValue, newValue) =>
                                    {
                                        double seconds = ((TimeSpan)newValue).TotalSeconds;
                                        ((PositionSlider)bindable).Value = seconds;
                                    });

        public TimeSpan Position
        {
            set { SetValue(PositionProperty, value); }
            get { return (TimeSpan)GetValue(PositionProperty); }
        }

        public PositionSlider()
        {
            PropertyChanged += (sender, args) =>
            {
                if (args.PropertyName == "Value")
                {
                    TimeSpan newPosition = TimeSpan.FromSeconds(Value);

                    if (Math.Abs(newPosition.TotalSeconds - Position.TotalSeconds) / Duration.TotalSeconds > 0.01)
                    {
                        Position = newPosition;
                    }
                }
            };
        }
    }
}
```

屬性變更處理常式，如`Duration`屬性集`Maximum`基礎屬性`Slider`至`TotalSeconds`屬性`TimeSpan`值。 同樣地，屬性變更處理常式`Position`設定`Value`屬性`Slider`。 如此一來，基礎`Slider`追蹤的位置`PositionSlider`。

`PositionSlider`會從基礎更新`Slider`中只有一個執行個體： 當使用者操控`Slider`，表示應該進階視訊，或還原到新位置。 這中偵測到`PropertyChanged`的建構函式中的處理常式`PositionSlider`。 此處理常式會檢查在變更`Value`屬性，而且如果它是不同於`Position`屬性，則`Position`屬性會設定從`Value`屬性。

理論上來說，內部`if`陳述式，可以撰寫如下：

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

不過，Android 實作`Slider`具有只有 1,000 個獨立的步驟，不論`Minimum`和`Maximum`設定。 視訊的長度大於 1000 秒，則兩個不同`Position`值就會對應至相同`Value`設定`Slider`，而這`if`陳述式會觸發使用者操作的誤判`Slider`。 它是安全的作法是檢查新的位置和現有的位置會大於百分之一整體持續期間。

## <a name="using-the-positionslider"></a>使用 PositionSlider

文件 UWP [ `MediaElement` ](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/)繫結會警告`Position`屬性因為經常更新的屬性。 文件建議用於計時器查詢`Position`屬性。

這是很好的建議，但是三個`VideoPlayerRenderer`類別會間接已更新使用計時器`Position`屬性。 `Position`中的處理常式屬性變更`UpdateStatus`只有 10 的次秒引發的事件。

因此，`Position`屬性`VideoPlayer`可以繫結至`Position`屬性`PositionSlider`沒有效能問題，如下所示**自訂位置列**頁面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.CustomPositionBarPage"
             Title="Custom Position Bar">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <video:VideoPlayer x:Name="videoPlayer"
                           Grid.Row="0"
                           AreTransportControlsEnabled="False"
                           Source="{StaticResource ElephantsDream}" />

        ···

        <StackLayout Grid.Row="1"
                     Orientation="Horizontal"
                     Margin="10, 0"
                     BindingContext="{x:Reference videoPlayer}">

            <Label Text="{Binding Path=Position,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center"/>

            ···

            <Label Text="{Binding Path=TimeToEnd,
                                  StringFormat='{0:hh\\:mm\\:ss}'}"
                   VerticalOptions="Center" />
        </StackLayout>

        <video:PositionSlider Grid.Row="2"
                              Margin="10, 0, 10, 10"
                              BindingContext="{x:Reference videoPlayer}"
                              Duration="{Binding Duration}"           
                              Position="{Binding Position}">
            <video:PositionSlider.Triggers>
                <DataTrigger TargetType="video:PositionSlider"
                             Binding="{Binding Status}"
                             Value="{x:Static video:VideoStatus.NotReady}">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </video:PositionSlider.Triggers>
        </video:PositionSlider>
    </Grid>
</ContentPage>
```

第一個的省略符號 （···） 會隱藏`ActivityIndicator`; 它會與先前相同**自訂傳輸**頁面。 請注意兩個`Label`顯示的項目`Position`和`TimeToEnd`屬性。 這兩者之間的省略符號`Label`項目會隱藏兩個`Button`中顯示的項目**自訂傳輸**頁面播放、 暫停和停止。 程式碼後置邏輯也是相同**自訂傳輸**頁面。

[![自訂定位](custom-positioning-images/custompositioning-small.png "自訂定位")](custom-positioning-images/custompositioning-large.png#lightbox "自訂定位")

以上總結的討論`VideoPlayer`。

## <a name="related-links"></a>相關連結

- [視訊播放程式示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
