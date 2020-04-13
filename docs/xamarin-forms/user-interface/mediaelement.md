---
title: Xamarin.forms 媒體元素
description: 本文介紹如何使用 MediaElement 在 Xamarin.Forms 應用程式中播放視頻和音訊。
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2020
ms.openlocfilehash: 6f6c51c428de569ceb09ed6a26cfc36881c86dc5
ms.sourcegitcommit: b93754b220fca3d6e3d131341e3cfbe233d10f84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628332"
---
# <a name="xamarinforms-mediaelement"></a>Xamarin.forms 媒體元素

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)

[`MediaElement`](xref:Xamarin.Forms.MediaElement)是播放視頻和音訊的視圖。 基礎平台支援的媒體可以從以下來源播放:

- 使用 URI(HTTP 或 HTTPS)的 Web。
- 使用`ms-appx:///`URI方案嵌入到平臺應用程式中的資源。
- 來自應用的本地和臨時資料資料夾中的檔案,使用`ms-appdata:///`URI方案。
- 設備的庫。

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以使用平臺播放控件,稱為傳輸控制項。 但是,默認情況下,它們被禁用,並可以替換為您自己的傳輸控件。 以下螢幕截圖顯示`MediaElement`使用平台傳輸控制件播放影片:

[![在 iOS 和安卓系統播放影片的媒體元素的螢幕截圖](mediaelement-images/playback-controls.png "媒體元素播放影片")](mediaelement-images/playback-controls-large.png#lightbox "媒體元素播放影片")

[`MediaElement`](xref:Xamarin.Forms.MediaElement)有 Xamarin.表格 4.5 中的可用。 但是,它當前是實驗性的,只能通過將以下代碼行添加到*App.xaml.cs*檔來使用:

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement)可在 iOS、Android、通用 Windows 平臺 (UWP)、macOS、Windows 演示基金會和 Tizen 上提供。

[`MediaElement`](xref:Xamarin.Forms.MediaElement)定義以下屬性:

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect)的類型[`Aspect`](xref:Xamarin.Forms.Aspect),確定如何縮放媒體以適合顯示區域。 此屬性的預設值為 `AspectFit`。
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay)類型`bool`, 指示在[`Source`](xref:Xamarin.Forms.MediaElement.Source)設定 屬性時是否自動開始媒體播放。 此屬性的預設值為 `true`。
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress)類型`double`, 指示目前緩衝進度。 此屬性的預設值為 0.0。
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek)類型`bool`, 指示是否可以[`Position`](xref:Xamarin.Forms.MediaElement.Position)通過設定 屬性的值來重新置放媒體。 這是一個唯讀屬性。
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState)的類型[`MediaElementState`](xref:Xamarin.Forms.MediaElementState),指示控件的當前狀態。 這是唯讀屬性,預設值為`MediaElementState.Closed`。
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration)的類型`TimeSpan?`,表示當前打開的媒體的持續時間。 這是唯讀屬性,預設值為`null`。
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping)的類型`bool`, 描述目前載入的媒體來源是否應在到達其末尾後從頭開始恢復播放。 此屬性的預設值為 `false`。
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn)的類型`bool`,確定設備螢幕是否應在媒體播放期間保持打開狀態。 此屬性的預設值為 `false`。
- [`Position`](xref:Xamarin.Forms.MediaElement.Position)的類型`TimeSpan`,通過媒體的播放時間描述當前進度。 此屬性的預設值為 `TimeSpan.Zero`。
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls)的類型`bool`,確定是否顯示平臺播放控制項。 此屬性的預設值為 `false`。
- [`Source`](xref:Xamarin.Forms.MediaElement.Source)的類型[`MediaSource`](xref:Xamarin.Forms.MediaSource), 指示載入到控制檔中的媒體的來源。
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight)的類型`int`,指示控件的高度。 這是一個唯讀屬性。
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth)的類型`int`, 表示控件的寬度。 這是一個唯讀屬性。
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume)類型`double`, 確定媒體的體積, 以 0 和 1 之間的線性比例表示。 此屬性使用`TwoWay`綁定,其預設值為1。

這些屬性(屬性除外`CanSeek`)[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)由 物件支援,這意味著它們可以成為數據綁定的目標和樣式。

該[`MediaElement`](xref:Xamarin.Forms.MediaElement)類別還定義了四個事件:

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened)在驗證並打開媒體流時觸發。
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded)在`MediaElement`播放其媒體后觸發。
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed)當存在與媒體源關聯的錯誤時,將觸發。
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted)當請求的尋用操作的搜索點已準備好進行播放時,將觸發。

此外,[`MediaElement`](xref:Xamarin.Forms.MediaElement)[`Play`](xref:Xamarin.Forms.MediaElement.Play)包括[`Pause`](xref:Xamarin.Forms.MediaElement.Pause)[`Stop`](xref:Xamarin.Forms.MediaElement.Stop)與

有關 Android 上受支援的媒體格式的資訊,請參閱 developer.android.com[支援的媒體格式](https://developer.android.com/guide/topics/media/media-formats)。 有關通用 Windows 平台 (UWP) 上受支援的媒體格式的資訊,請參閱[支援編解碼器](/windows/uwp/audio-video-camera/supported-codecs)。

## <a name="play-remote-media"></a>播放遠端媒體

A[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以使用 HTTP 與 HTTPS URI 方案播放遠端媒體檔。 這是透過屬性[`Source`](xref:Xamarin.Forms.MediaElement.Source)設定為媒體檔案的 URI 來實現的:

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

預設情況下,[`Source`](xref:Xamarin.Forms.MediaElement.Source)屬性定義的媒體在打開媒體後立即播放。 要關閉自動媒體播放,設定[`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay)值設定為`false`。

默認情況下禁用媒體播放控制項,並透過將[`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls)屬性設置`true`為啟用。 [`MediaElement`](xref:Xamarin.Forms.MediaElement)然後將使用平臺播放控件。

## <a name="play-local-media"></a>播放本地媒體

可以從以下來源播放本地媒體:

- 使用`ms-appx:///`URI方案嵌入到平臺應用程式中的資源。
- 來自應用的本地和臨時資料資料夾中的檔案,使用`ms-appdata:///`URI方案。
- 設備的庫。

有關這些 URI 方案的詳細資訊,請參閱[URI 方案](/windows/uwp/app-resources/uri-schemes)。

### <a name="play-media-embedded-in-the-app-package"></a>播放嵌入在套用套件中的媒體

A[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以`ms-appx:///`使用URI方案播放嵌入在應用包中的媒體檔。 媒體文件通過將它們放置在平臺專案中而嵌入到應用包中。

在平台項目中儲存媒體檔對於每個平臺是不同的:

- 在 iOS 上,媒體檔必須儲存在 **「資源」** 資料夾或 **「資源」** 資料夾的子資料夾中。 媒體檔案必須具有的`Build Action``BundleResource`。
- 在 Android 上,媒體檔必須**儲存在名為****raw**的資源的子資料夾中。 **raw** 資料夾不能包含子資料夾。 媒體檔案必須具有的`Build Action``AndroidResource`。
- 在 UWP 上,媒體檔可以存儲在專案中的任何資料夾中。 媒體檔案必須具有的`BuildAction``Content`。

然後可以使用 URI 專案播放滿足這些標準`ms-appx:///`的媒體 檔:

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

使用資料繫結時,可以使用值轉換器應用此 URI 方案:

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        if (Device.RuntimePlatform == Device.UWP)
            return new Uri($"ms-appx:///Assets/{value}");
        else
            return new Uri($"ms-appx:///{value}");
    }
    // ...
}
```

然後,可以使用`VideoSourceConverter`的實體檔案將`ms-appx:///`URI 專案應用於嵌入式媒體檔:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

有關 ms-appx URI 方案的詳細資訊,請參閱[ms-appx 和 ms-appx-web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web)。

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>從應用程式的本地與暫存資料夾播放媒體

可以[`MediaElement`](xref:Xamarin.Forms.MediaElement)使用`ms-appdata:///`URI 方案播放複製到應用的本地或暫存資料資料夾中的媒體檔。

下面的範例顯示設定為儲存在[`Source`](xref:Xamarin.Forms.MediaElement.Source)應用本地資料資料夾中的媒體檔案的屬性:

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

下面的範例向[`Source`](xref:Xamarin.Forms.MediaElement.Source)儲存在應用程式的暫存資料資料夾中的媒體檔案顯示該屬性:

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> 除了播放存儲在應用的本地或臨時數據資料夾中的媒體檔外,UWP 還可以播放位於應用漫遊資料夾中的媒體檔。 這可以通過將媒體檔的首碼`ms-appdata:///roaming/`與來實現。

使用資料繫結時,可以使用值轉換器應用此 URI 方案:

```csharp
public class VideoSourceConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        if (value == null)
            return null;

        if (string.IsNullOrWhiteSpace(value.ToString()))
            return null;

        return new Uri($"ms-appdata:///{value}");
    }
    // ...
}
```

然後,可以使用`VideoSourceConverter`的實體將`ms-appdata:///`URI 專案應用於應用的本地或暫存資料資料夾中的媒體檔:

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

有關 ms-appdata URI 方案的詳細資訊,請參閱[ms-appdata](/windows/uwp/app-resources/uri-schemes#ms-appdata)。

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>將媒體檔案複製到應用程式的本地或暫存資料資料夾

播放儲存在應用的本地或暫存資料資料夾中的媒體檔案需要應用將媒體檔案複製到該資料夾中。 例如,可以通過從應用程式複製媒體檔來實現:

```csharp
// This method copies the video from the app package to the app data
// directory for your app. To copy the video to the temp directory
// for your app, comment out the first line of code, and uncomment
// the second line of code.
public static async Task CopyVideoIfNotExists(string filename)
{
    string folder = FileSystem.AppDataDirectory;
    //string folder = Path.GetTempPath();
    string videoFile = Path.Combine(folder, "XamarinVideo.mp4");

    if (!File.Exists(videoFile))
    {
        using (Stream inputStream = await FileSystem.OpenAppPackageFileAsync(filename))
        {
            using (FileStream outputStream = File.Create(videoFile))
            {
                await inputStream.CopyToAsync(outputStream);
            }
        }
    }
}
```

> [!NOTE]
> 上面的代碼示例使用 Xamarin 中`FileSystem`包含的 類。 有關詳細資訊,請參閱[Xamarin.要點:檔案系統說明器](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android)。

### <a name="play-media-from-the-device-library"></a>從裝置庫中播放媒體

大多數現代行動裝置和臺式電腦都能夠使用設備的攝影機和麥克風錄製視頻和音訊。 然後,創建的介質將儲存為設備上的檔。 這些檔案可以從庫中取出索並由播放[`MediaElement`](xref:Xamarin.Forms.MediaElement)。

每個平臺都包含一個允許使用者從設備庫中選擇媒體的功能。 在 Xamarin.Forms 中,平臺專案可以調用此功能,[`DependencyService`](xref:Xamarin.Forms.DependencyService)並且可以通過 類調用它。

示例應用程式中使用的視頻選取依賴項服務與[從圖片庫中選取照片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)時定義的服務非常相似,只不過選取器返回檔名而不是`Stream`物件。 共用代碼項目定義名為`IVideoPicker`的介面,用於定義名為`GetVideoFileAsync`的 單個方法。 然後,每個平臺在`VideoPicker`類中實現此介面。

以下代碼範例簡報如何從裝置庫中檢索媒體檔:

```csharp
string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();
if (!string.IsNullOrWhiteSpace(filename))
{
    mediaElement.Source = new FileMediaSource
    {
        File = filename
    };
}
```

通過調用`DependencyService.Get`方法獲取平臺專案`IVideoPicker`中 介面的實現來調用視頻選取依賴項服務。 `GetVideoFileAsync`然後,該方法將呼叫該實體,傳回的檔案名稱用於建立[`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource)物件並將其設定[`Source`](xref:Xamarin.Forms.MediaElement.Source)為的[`MediaElement`](xref:Xamarin.Forms.MediaElement)屬性 。

## <a name="change-video-aspect-ratio"></a>變更視訊縱橫比

該[`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect)屬性確定如何縮放視頻媒體以適合顯示區域。 預設情況下,此屬性設置為`AspectFit`枚舉成員,但可以將其設置為[`Aspect`](xref:Xamarin.Forms.Aspect)任何 枚舉成員:

- `AspectFit`指示視頻將信箱(如果需要)以適合顯示區域,同時保持縱橫比。
- `AspectFill`表示將剪切視頻以填充顯示區域,同時保持縱橫比。
- `Fill`表示視頻將被拉伸以填充顯示區域。

## <a name="poll-for-position-data"></a>職位資料的輪詢

可[`Position`](xref:Xamarin.Forms.MediaElement.Position)綁定屬性的屬性更改通知僅在播放開始和結束等關鍵時刻觸發,併發生暫停。 因此,綁定到`Position`屬性的數據不會生成準確的位置數據。 相反,您必須設置計時器並輪詢屬性。

執行此動作的地方是在`OnAppearing`播放媒體時需要位置資料的頁面的重寫中:

```csharp
bool polling = true;

protected override void OnAppearing()
{
    base.OnAppearing();

    Device.StartTimer(TimeSpan.FromMilliseconds(1000), () =>
    {
        Device.BeginInvokeOnMainThread(() =>
        {
            positionLabel.Text = mediaElement.Position.ToString("hh\\:mm\\:ss");
        });
        return polling;
    });
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    polling = false;
}
```

在此範例中,`OnAppearing`重寫將啟動每`positionLabel`秒 更新該`Position`值的計時器。 計時器回檔每秒呼叫一次,直到回`false`檔返回 。 當發生頁面導航時`OnDisappearing`,將執行重寫,從而停止調用計時器回調。

## <a name="understand-mediasource-types"></a>瞭解媒體來源類型

通過將[`MediaElement`](xref:Xamarin.Forms.MediaElement)[`Source`](xref:Xamarin.Forms.MediaElement.Source)屬性 設置為遠端或本地媒體檔,可以播放媒體。 屬性`Source`的類型為[`MediaSource`](xref:Xamarin.Forms.MediaSource),並且此類定義兩個靜態方法:

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*)從`string`[`MediaSource`](xref:Xamarin.Forms.MediaSource)參數返回實例。
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*)從`Uri`[`MediaSource`](xref:Xamarin.Forms.MediaSource)參數返回實例。

此外,[`MediaSource`](xref:Xamarin.Forms.MediaSource)類還具有`MediaSource``string`從`Uri`和返回實例的隱式運算符。

> [!NOTE]
> 在[`Source`](xref:Xamarin.Forms.MediaElement.Source)XAML 中設定該屬性時,將調用類型轉換[`MediaSource`](xref:Xamarin.Forms.MediaSource)`string`器`Uri`從 或返回實例。

該[`MediaSource`](xref:Xamarin.Forms.MediaSource)類還有兩個派生類:

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource),用於從URI指定遠端媒體檔。 此擁有[`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri)可設定為的屬性`Uri`。
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource)指定的本地媒體檔案`string`。 此擁有[`File`](xref:Xamarin.Forms.FileMediaSource.File)可設定為的屬性`string`。 此外,類別有`string`轉換為`FileMediaSource`物件的隱式運算符,並將`FileMediaSource`物件轉換為 。 `string`

> [!NOTE]
> 在[`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource)XAML 中建立物件時,將調用類型轉換器[`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource)`string`從返回 實例。

## <a name="determine-mediaelement-status"></a>確定媒體元素狀態

定義[`MediaElement`](xref:Xamarin.Forms.MediaElement)型態為[`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState)的唯讀可結合屬性, 類型[`MediaElementState`](xref:Xamarin.Forms.MediaElementState)。 此屬性指示控制項的當前狀態,例如媒體是正在播放還是暫停,或者它是否尚未準備好播放媒體。

列[`MediaElementState`](xref:Xamarin.Forms.MediaElementState)舉定義以下成員:

- `Closed`表示不包含`MediaElement`任何介質。
- `Opening`指示正在`MediaElement`驗證並嘗試載入指定的源。
- `Buffering`指示正在`MediaElement`載入媒體以進行播放。 其[`Position`](xref:Xamarin.Forms.MediaElement.Position)屬性在此狀態期間不會前進。 `MediaElement`如果 播放影片,則它將繼續顯示最後顯示的幀。
- `Playing`指示正在播放`MediaElement`的媒體源。
- `Paused`表示`MediaElement`不預支[`Position`](xref:Xamarin.Forms.MediaElement.Position)其 屬性。 `MediaElement`如果 正在播放視頻,則它將繼續顯示當前幀。
- `Stopped`指示`MediaElement`包含的媒體,但它未播放或暫停。 其[`Position`](xref:Xamarin.Forms.MediaElement.Position)屬性為 0,不前進。 如果載入的媒體是視頻,則`MediaElement`顯示第一幀。

使用傳輸控制項時,[`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState)[`MediaElement`](xref:Xamarin.Forms.MediaElement)通常不需要檢查屬性。 但是,在實現自己的傳輸控件時,此屬性變得非常重要。

## <a name="implement-custom-transport-controls"></a>自訂傳輸控制項

媒體播放器的傳輸控制件包括執行**功能"播放**"、"**暫停**"和 **"停止"** 的按鈕。 這些按鈕通常會以熟悉的圖示而非文字呈現，且 [播放]**** 及 [暫停]**** 功能常會合併為一個按鈕。

默認情況下,[`MediaElement`](xref:Xamarin.Forms.MediaElement)播放控件被禁用。 這使您能夠以程式設計方式控制`MediaElement`,或者通過提供您自己的傳輸控制件來控制。 為此,`MediaElement`[`Play`](xref:Xamarin.Forms.MediaElement.Play)包括[`Pause`](xref:Xamarin.Forms.MediaElement.Pause),[`Stop`](xref:Xamarin.Forms.MediaElement.Stop)和 方法。

下面的 XAML 範例[`MediaElement`](xref:Xamarin.Forms.MediaElement)顯示包含 與自訂傳輸控制件的頁面:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MediaElementDemos.CustomTransportPage"
             Title="Custom transport">
    <Grid>
        ...
        <MediaElement x:Name="mediaElement"
                      AutoPlay="False"
                      ... />
        <StackLayout BindingContext="{x:Reference mediaElement}"
                     ...>
            <Button Text="&#x25B6;&#xFE0F; Play"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnPlayPauseButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Playing}">
                        <Setter Property="Text"
                                Value="&#x23F8; Pause" />
                    </DataTrigger>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Buffering}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
            <Button Text="&#x23F9; Stop"
                    HorizontalOptions="CenterAndExpand"
                    Clicked="OnStopButtonClicked">
                <Button.Triggers>
                    <DataTrigger TargetType="Button"
                                 Binding="{Binding CurrentState}"
                                 Value="{x:Static MediaElementState.Stopped}">
                        <Setter Property="IsEnabled"
                                Value="False" />
                    </DataTrigger>
                </Button.Triggers>
            </Button>
        </StackLayout>
    </Grid>
</ContentPage>
```

在此示例中,自定義傳輸控制件定義為[`Button`](xref:Xamarin.Forms.Button)物件。 `Button`但是,只有兩個物件,第一個`Button`表示**播放**和**暫停**,`Button`第二 個表示**停止**。 [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)物件用於啟用和禁用按鈕,並在 **「播放」** 和 **「暫停**」之間切換第一個按鈕。 有關資料觸發器的詳細資訊,請參閱[Xamarin.窗體觸發器](~/xamarin-forms/app-fundamentals/triggers.md)。

程式碼背後的檔案[`Clicked`](xref:Xamarin.Forms.Button.Clicked)有事件處理程式:

```csharp
void OnPlayPauseButtonClicked(object sender, EventArgs args)
{
    if (mediaElement.CurrentState == MediaElementState.Stopped ||
        mediaElement.CurrentState == MediaElementState.Paused)
    {
        mediaElement.Play();
    }
    else if (mediaElement.CurrentState == MediaElementState.Playing)
    {
        mediaElement.Pause();
    }
}

void OnStopButtonClicked(object sender, EventArgs args)
{
    mediaElement.Stop();
}
```

開啟「**播放」** 按鈕後,可以按這個按鈕開始播放:

[![在 iOS 和 Android 上使用自訂傳輸控制元件的 MediaElement 的螢幕截圖](mediaelement-images/custom-transport-playback.png "媒體元素播放影片")](mediaelement-images/custom-transport-playback-large.png#lightbox "媒體元素播放影片")

按下 **「暫停」** 按鈕會將播放暫停:

[![在 iOS 和 Android 上暫停播放的媒體元素的螢幕截圖](mediaelement-images/custom-transport-paused.png "顯示暫停視訊的媒體項目")](mediaelement-images/custom-transport-paused-large.png#lightbox "顯示暫停視訊的媒體項目")

按下 **「停止」** 按鈕將停止播放,並將媒體檔的位置返回到開頭。

## <a name="implement-a-custom-position-bar"></a>實現自訂位置列

每個平台實作的傳輸控制項都包括位置列。 此條形類似於滑塊,並顯示媒體在其總持續時間內的當前位置。 此外,您可以操作位置列以向前或向後移動到視頻中的新位置。

實現自定義位置列需要瞭解媒體的持續時間和當前播放位置。 此數據在[`Duration`](xref:Xamarin.Forms.MediaElement.Duration)和[`Position`](xref:Xamarin.Forms.MediaElement.Position)屬性中可用。

> [!IMPORTANT]
> [`Position`](xref:Xamarin.Forms.MediaElement.Position)必須輪詢以獲取準確的位置數據。 有關詳細資訊,請參閱[輪詢位置資料](#poll-for-position-data)。

可以使用 實現自定義位置[`Slider`](xref:Xamarin.Forms.Slider)列 ,如以下範例所示:

```csharp
public class PositionSlider : Slider
{
    public static readonly BindableProperty DurationProperty =
        BindableProperty.Create(nameof(Duration), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(1),
                                propertyChanged: (bindable, oldValue, newValue) =>
                                {
                                    ((PositionSlider)bindable).SetTimeToEnd();
                                    double seconds = ((TimeSpan)newValue).TotalSeconds;
                                    ((Slider)bindable).Maximum = seconds <= 0 ? 1 : seconds;
                                });

    public TimeSpan Duration
    {
        get { return (TimeSpan)GetValue(DurationProperty); }
        set { SetValue(DurationProperty, value); }
    }

    public static readonly BindableProperty PositionProperty =
        BindableProperty.Create(nameof(Position), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan(0),
                                propertyChanged: (bindable, oldValue, newValue) => ((PositionSlider)bindable).SetTimeToEnd());

    public TimeSpan Position
    {
        get { return (TimeSpan)GetValue(PositionProperty); }
        set { SetValue(PositionProperty, value); }
    }

    static readonly BindablePropertyKey TimeToEndPropertyKey =
        BindableProperty.CreateReadOnly(nameof(TimeToEnd), typeof(TimeSpan), typeof(PositionSlider), new TimeSpan());

    public static readonly BindableProperty TimeToEndProperty = TimeToEndPropertyKey.BindableProperty;

    public TimeSpan TimeToEnd
    {
        get { return (TimeSpan)GetValue(TimeToEndProperty); }
        private set { SetValue(TimeToEndPropertyKey, value); }
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

    void SetTimeToEnd()
    {
        TimeToEnd = Duration - Position;
    }
}
```

類`PositionSlider`定義其`Duration`自己的`Position`可綁定屬性和`TimeToEnd`可綁定屬性。 所有三個屬性都是類型的`TimeSpan`。 `Duration`屬性的屬性`Maximum`更改處理程式將[`Slider`](xref:Xamarin.Forms.Slider)的屬性`TotalSeconds`設置到值的屬性`TimeSpan`的屬性。 屬性`TimeToEnd`根據`Duration`對`Position`和 屬性的更改計算,從媒體的持續時間開始,隨著播放的進行,該屬性將減少到零。

`PositionSlider`移動時[`Slider`](xref:Xamarin.Forms.Slider)`Slider`從基礎更新,以指示介質應前進或反轉到新位置。 這在`PropertyChanged``PositionSlider`建構函數中的處理程式中檢測到。 處理常式會檢查 `Value` 屬性中的變更，如果不同於 `Position` 屬性，則會從 `Value` 屬性設定 `Position` 屬性。 有關使用[`Slider`](xref:Xamarin.Forms.Slider)請參閱的詳細資訊[,Xamarin.窗體滑塊](~/xamarin-forms/user-interface/slider.md)

> [!NOTE]
> 在[`Slider`](xref:Xamarin.Forms.Slider)Android 上,只有 1000`Minimum`個`Maximum`離散步驟 , 而不考慮 和 設置。 如果媒體長度大於 1000 秒,則兩`Position`個不同的 值`Value`將對應`Slider`於的相同 。 這就是為什麼上面的代碼檢查新職位和現有職位是否大於總持續時間的百分之一。

下面的範例顯示了`PositionSlider`在頁面上使用:

```xaml
<controls:PositionSlider x:Name="positionSlider"
                         BindingContext="{x:Reference mediaElement}"
                         Duration="{Binding Duration}"
                         ValueChanged="OnPositionSliderValueChanged">
    <controls:PositionSlider.Triggers>
        <DataTrigger TargetType="controls:PositionSlider"
                     Binding="{Binding CurrentState}"
                     Value="{x:Static MediaElementState.Buffering}">
            <Setter Property="IsEnabled" Value="False" />
        </DataTrigger>
    </controls:PositionSlider.Triggers>
</controls:PositionSlider>
```

在此範例中,`Duration``PositionSlider`屬性 的資料[`Duration`](xref:Xamarin.Forms.MediaElement.Duration)綁定到[`MediaElement`](xref:Xamarin.Forms.MediaElement)的屬性。 更改的屬性[`Value`](xref:Xamarin.Forms.Slider.Value)[`Slider`](xref:Xamarin.Forms.Slider)時`ValueChanged`, 事件將觸`OnPositionSliderValueChanged`發並 執行處理程式。 此處理程式將[`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position)屬性設置`PositionSlider.Position`到 屬性的值。 因此,拖動`Slider`媒體播放位置的結果將發生變化:

[![在 iOS 和 Android 上使用自訂位置列的 MediaElement 的螢幕截圖](mediaelement-images/custom-position-bar.png "自訂位置列的媒體元素")](mediaelement-images/custom-position-bar-large.png#lightbox "自訂位置列的媒體元素")

此外,物件[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)用於關閉媒體緩衝`PositionSlider`時的 。 有關資料觸發器的詳細資訊,請參閱[Xamarin.窗體觸發器](~/xamarin-forms/app-fundamentals/triggers.md)。

## <a name="implement-a-custom-volume-control"></a>實現自訂音量控制

每個平台實現的媒體播放控制項包括一個音量列。 此條形類似於滑塊,顯示介質的音量。 此外,您可以操作音量條以增加或減少音量。

可以使用 實現自定義卷[`Slider`](xref:Xamarin.Forms.Slider)欄 ,如以下範例所示:

```xaml
<StackLayout>
    <MediaElement AutoPlay="False"
                  Source="{StaticResource AdvancedAsync}" />
    <Slider Maximum="1.0"
            Minimum="0.0"
            Value="{Binding Volume}"
            Rotation="270"
            WidthRequest="100" />
</StackLayout>
```

在此範例中,[`Slider`](xref:Xamarin.Forms.Slider)資料`Value`將其 屬性繫[`Volume`](xref:Xamarin.Forms.MediaElement.Volume)結到的[`MediaElement`](xref:Xamarin.Forms.MediaElement)屬性。 這是可能的,`Volume`因為屬性`TwoWay`使用 綁定。 因此,更改屬性`Value`將導致`Volume`屬性更改。

> [!NOTE]
> 屬性[`Volume`](xref:Xamarin.Forms.MediaElement.Volume)具有 vlidation 回調,可確保其值大於或等於 0.0,小於或等於 1.0。

有關使用[`Slider`](xref:Xamarin.Forms.Slider)請參閱的詳細資訊[,Xamarin.窗體滑塊](~/xamarin-forms/user-interface/slider.md)

## <a name="related-links"></a>相關連結

- [媒體元素示範(範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)
- [URI 配置](/windows/uwp/app-resources/uri-schemes)
- [Xamarin.Forms 觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.表單滑動](~/xamarin-forms/user-interface/slider.md)
- [安卓:支援的媒體格式](https://developer.android.com/guide/topics/media/media-formats)
- [UWP:支援的編解碼器](/windows/uwp/audio-video-camera/supported-codecs)
