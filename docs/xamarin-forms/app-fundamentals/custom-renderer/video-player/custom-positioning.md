---
title: 自訂影片定位
description: 本文說明如何使用在影片播放程式應用程式中執行自訂位置列 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 6D792264-30FF-46F7-8C1B-2FEF9D277DF4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 15529520668f4d6353fe99c13ddb2e6cc2801500
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367227"
---
# <a name="custom-video-positioning"></a>自訂影片定位

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

每個平台實作的傳輸控制項都包括位置列。 此列類似於滑桿或捲軸，而且會顯示影片在其總持續時間內的目前位置。 此外，使用者可以操作位置列，向前或向後移至影片中的新位置。

本文說明如何實作您自己的自訂位置列。

## <a name="the-duration-property"></a>Duration 屬性

影片持續時間是 `VideoPlayer` 支援自訂位置列所需的其中一項資訊。 `VideoPlayer` 定義 `TimeSpan` 類型的唯讀 `Duration` 屬性：

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

如同[前一篇文章](custom-transport.md)中所述的 `Status` 屬性，這個 `Duration` 屬性為唯讀。 它是由私用 `BindablePropertyKey` 所定義，而且只能透過參考 `IVideoPlayerController` 介面 (其中包含這個 `Duration` 屬性) 來設定：

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

另請注意呼叫 `SetTimeToEnd` 方法的屬性變更處理常式，本文稍後將加以描述。

在設定 `VideoPlayer` 的 `Source` 屬性之後，「無法」立即取得影片的持續時間。 必須下載一部分的影片檔案，基礎影片播放程式才能判斷其持續時間。

以下說明每個平台轉譯器如何取得影片的持續時間：

### <a name="video-duration-in-ios"></a>iOS 中的影片持續時間

在 iOS 中，影片持續時間是從 `AVPlayerItem` 的 `Duration` 屬性取得，但無法在建立 `AVPlayerItem` 之後立即取得。 您可以為 `Duration` 屬性設定 iOS 觀察者，但 `VideoPlayerRenderer` 會在每秒呼叫 10 次的 `UpdateStatus` 方法中取得持續時間：

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

`ConvertTime` 方法會將 `CMTime` 物件轉換為 `TimeSpan` 值。

### <a name="video-duration-in-android"></a>Android 中的影片持續時間

Android `VideoView` 的 `Duration` 屬性會在引發 `VideoView` 的 `Prepared` 事件時，回報有效的持續時間 (以毫秒為單位)。 Android `VideoPlayerRenderer` 類別使用該處理常式來取得 `Duration` 屬性：

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

### <a name="video-duration-in-uwp"></a>UWP 中的影片持續時間

`MediaElement` 的 `NaturalDuration` 屬性是 `TimeSpan` 值，並會在 `MediaElement` 引發 `MediaOpened` 事件時生效：

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

## <a name="the-position-property"></a>Position 屬性

`VideoPlayer` 也需要 `Position` 屬性，這會隨著影片播放從零增加到 `Duration`。 `VideoPlayer` 實作此屬性的方式類似於 UWP `MediaElement` 中的 `Position` 屬性，這是具有公用 `set` 和 `get` 存取子的一般可繫結屬性：

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

`get` 存取子會傳回目前播放影片的位置，但 `set` 存取子目的則是透過前後移動影片位置來回應使用者對位置列的操作。

在 iOS 和 Android 中，取得目前位置的屬性只有 `get` 存取子，並有 `Seek` 方法可用來執行上述第二項工作。 如果您仔細想一下，個別 `Seek` 方法似乎是比單一 `Position` 屬性更明智的做法。 單一 `Position` 屬性的固有問題是：當影片播放時，`Position` 屬性必須持續更新以反映新位置。 但您不想要大部分的 `Position` 屬性變更使得影片播放程式移至影片中新位置。 如果發生這種情況，影片播放程式會搜尋 `Position` 屬性的最後一個值來回應，因此影片不會前進。

除了難以實作具有 `set` 和 `get` 存取子的 `Position` 屬性之外，選擇這個方法的原因還包括它與 UWP `MediaElement` 一致，而且在資料繫結方面有很大的優勢：`VideoPlayer` 的 `Position` 屬性可以繫結至用來顯示位置和搜尋新位置的滑桿。 不過，實作這個 `Position` 屬性時需要幾個預防措施，以避免回饋迴圈。

### <a name="setting-and-getting-ios-position"></a>設定和取得 iOS 位置

在 iOS 中，`AVPlayerItem` 物件的 `CurrentTime` 屬性會指出目前播放影片的位置。 iOS `VideoPlayerRenderer` 會在設定 `Duration` 屬性的同時，設定 `UpdateStatus` 處理常式中的 `Position` 屬性：

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

轉譯器會偵測到從 `VideoPlayer` 設定的 `Position` 屬性在 `OnElementPropertyChanged` 覆寫中已變更，並使用該新值在 `AVPlayer` 物件上呼叫 `Seek` 方法：

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

請記住，每次從 `OnUpdateStatus` 處理常式設定 `VideoPlayer` 中的 `Position` 屬性，`Position` 屬性都會引發 `PropertyChanged` 事件，並在 `OnElementPropertyChanged` 覆寫中偵測到此事件。 對於大部分變更，`OnElementPropertyChanged` 方法不應執行任何動作。 否則，每次影片的位置有所變更，其就會移至剛抵達的相同位置！

為了避免此回饋迴圈，只有在 `Position` 屬性與 `AVPlayer` 的目前位置相差大於一秒時，`OnElementPropertyChanged` 方法才會呼叫 `Seek`。

### <a name="setting-and-getting-android-position"></a>設定和取得 Android 位置

如同 iOS 轉譯器，Android `VideoPlayerRenderer` 會在 `OnUpdateStatus` 處理常式中設定 `Position` 屬性的新值。 `VideoView` 的 `CurrentPosition` 屬性包含新位置 (以毫秒為單位)：

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

此外，如同 iOS 轉譯器，Android 轉譯器會在 `Position` 屬性變更時呼叫 `VideoView` 的 `SeekTo` 方法，但僅限於變更與 `VideoView` 的 `CurrentPosition` 值相差大於一秒時：

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

UWP `VideoPlayerRenderer` 處理 `Position` 的方式如同 iOS 和 Android 轉譯器，但由於 UWP `MediaElement` 的 `Position` 屬性同時為 `TimeSpan` 值，因此不需要轉換：

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

有時影片播放程式會顯示影片中的剩餘時間。 這個值在影片開始時是以影片的持續時間起始，並會在影片結束時減少至零。

`VideoPlayer` 包含唯讀 `TimeToEnd` 屬性，會根據 `Duration` 和 `Position` 屬性的變更，完全在類別內進行處理：

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

`SetTimeToEnd` 方法是從 `Duration` 和 `Position` 的屬性變更處理常式呼叫。

## <a name="a-custom-slider-for-video"></a>影片的自訂滑桿

您可以撰寫位置列的自訂控制項，也可以使用衍生自的 Xamarin.Forms `Slider` 或類別 `Slider` ，例如下列 `PositionSlider` 類別。 此類別會定義 `TimeSpan` 類型的兩個新屬性 `Duration` 和 `Position`，旨在將資料繫結至 `VideoPlayer` 中同名的兩個屬性。 請注意，`Position` 屬性的預設繫結模式為雙向：

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

`Duration` 屬性的屬性變更處理常式會將基礎 `Slider` 之 `Maximum` 屬性設定為 `TimeSpan` 值的 `TotalSeconds` 屬性。 同樣地，`Position` 屬性之屬性變更處理常式會設定 `Slider` 的 `Value` 屬性。 如此一來，基礎 `Slider` 就會追蹤 `PositionSlider` 的位置。

從基礎 `Slider` 更新 `PositionSlider` 的情況只有一種：當使用者操作 `Slider` 指定影片應該前進或倒退到新位置時。 這是在 `PositionSlider` 建構函式的 `PropertyChanged` 處理常式中進行偵測。 處理常式會檢查 `Value` 屬性中的變更，如果不同於 `Position` 屬性，則會從 `Value` 屬性設定 `Position` 屬性。

理論上，內部 `if` 陳述式可以撰寫如下：

```csharp
if (newPosition.Seconds != Position.Seconds)
{
    Position = newPosition;
}
```

不過，無論 `Minimum` 和 `Maximum` 設定為何，Android `Slider` 實作都只有 1,000 個離散步驟。 如果影片長度大於 1,000 秒，則兩個不同 `Position` 值會對應至 `Slider` 的相同 `Value` 設定，而且這個 `if` 陳述式針對使用者對 `Slider` 的操作會觸發誤判。 較安全的做法是改為確保新位置和現有位置大於整體持續時間的百分之一。

## <a name="using-the-positionslider"></a>使用 PositionSlider

UWP 的檔會 [`MediaElement`](/uwp/api/Windows.UI.Xaml.Controls.MediaElement/) 警告有關系結至屬性的警告， `Position` 因為屬性會經常更新。 文件建議使用計時器來查詢 `Position` 屬性。

這是很好的建議，但已有三個 `VideoPlayerRenderer` 類別間接使用計時器來更新 `Position` 屬性。 若發生 `UpdateStatus` 事件 (每秒僅引發 10 次)，則會變更處理常式中的 `Position` 屬性。

因此，`VideoPlayer` 的 `Position` 屬性可以繫結至 `PositionSlider` 的 `Position` 屬性，而不會有效能問題，如 [Custom Position Bar] \(自訂位置列\) 頁面所示：

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

第一個省略符號 (···) 會隱藏 `ActivityIndicator`，這與上一頁 [Custom Transport] \(自訂傳輸\) 相同。 注意顯示 `Position` 和 `TimeToEnd` 屬性的兩個 `Label` 項目。 這兩個 `Label` 項目之間的省略符號會隱藏 [Custom Transport] \(自訂傳輸\) 頁面中所顯示兩個 `Button` 項目 (用於播放、暫停和停止)。 程式碼後置邏輯也與 [Custom Transport] \(自訂傳輸\) 頁面相同。

[![自訂定位](custom-positioning-images/custompositioning-small.png "自訂定位")](custom-positioning-images/custompositioning-large.png#lightbox "自訂定位")

`VideoPlayer` 的討論到此結束。

## <a name="related-links"></a>相關連結

- [影片播放程式示範 (範例)](/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)