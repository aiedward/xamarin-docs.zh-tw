---
title: Xamarin.FormsMediaElement
description: 本文說明如何使用 MediaElement 在應用程式中播放影片和音訊 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: e65f1e56-a80d-46c7-9ff4-7ae6650a3165
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/18/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4a8ca74fc12b59100cc60b72d3c2287cffadfd18
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918110"
---
# <a name="no-locxamarinforms-mediaelement"></a>Xamarin.FormsMediaElement

![發行前版本 API](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)

[`MediaElement`](xref:Xamarin.Forms.MediaElement)是播放影片和音訊的視圖。 基礎平臺支援的媒體可以從下列來源進行播放：

- Web，使用 URI (HTTP 或 HTTPS) 。
- 使用 URI 配置，內嵌在平臺應用程式中的資源 `ms-appx:///` 。
- 來自應用程式本機和暫存資料夾的檔案，使用 `ms-appdata:///` URI 配置。
- 裝置的程式庫。

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以使用平臺播放控制項，稱為傳輸控制項。 不過，它們預設為停用，而且可以使用您自己的傳輸控制來取代。 下列螢幕擷取畫面顯示 `MediaElement` 使用平臺傳輸控制項播放影片：

[![在 iOS 和 Android 上播放影片的 MediaElement 螢幕擷取畫面](mediaelement-images/playback-controls.png "MediaElement 播放影片")](mediaelement-images/playback-controls-large.png#lightbox "MediaElement 播放影片")

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可在4.5 中取得 Xamarin.Forms 。 不過，它目前是實驗性，而且只能透過將下列程式程式碼新增至*App.xaml.cs*檔案來使用：

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

> [!NOTE]
> [`MediaElement`](xref:Xamarin.Forms.MediaElement)適用于 iOS、Android、通用 Windows 平臺 (UWP) 、macOS、Windows Presentation Foundation 和 Tizen。

[`MediaElement`](xref:Xamarin.Forms.MediaElement)定義下列屬性：

- [`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect)，屬於類型 [`Aspect`](xref:Xamarin.Forms.Aspect) ，會決定如何調整媒體以符合顯示區域。 此屬性的預設值為 `AspectFit`。
- [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay)，屬於類型 `bool` ，表示當設定屬性時，是否會自動開始播放媒體 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 。 此屬性的預設值為 `true`。
- [`BufferingProgress`](xref:Xamarin.Forms.MediaElement.BufferingProgress)，屬於類型 `double` ，表示目前的緩衝處理進度。 這個屬性的預設值為0.0。
- [`CanSeek`](xref:Xamarin.Forms.MediaElement.CanSeek)，屬於類型 `bool` ，表示是否可以藉由設定屬性的值來重新置放媒體 [`Position`](xref:Xamarin.Forms.MediaElement.Position) 。 這是一個唯讀屬性。
- [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState)，屬於類型 [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) ，表示控制項的目前狀態。 這是唯讀屬性，其預設值為 `MediaElementState.Closed` 。
- [`Duration`](xref:Xamarin.Forms.MediaElement.Duration)，屬於類型 `TimeSpan?` ，表示目前開啟之媒體的持續時間。 這是唯讀屬性，其預設值為 `null` 。
- [`IsLooping`](xref:Xamarin.Forms.MediaElement.IsLooping)，屬於類型 `bool` ，描述目前載入的媒體來源是否應該在到達其結尾後，繼續從啟動播放。 此屬性的預設值為 `false`。
- [`KeepScreenOn`](xref:Xamarin.Forms.MediaElement.KeepScreenOn)，屬於類型 `bool` ，可決定裝置畫面是否應在媒體播放期間保持開啟。 此屬性的預設值為 `false`。
- [`Position`](xref:Xamarin.Forms.MediaElement.Position)，屬於類型 `TimeSpan` ，描述透過媒體播放時間的目前進度。 此屬性的預設值為 `TimeSpan.Zero`。
- [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls)，屬於類型 `bool` ，可決定是否要顯示平臺播放控制項。 此屬性的預設值為 `false`。
- [`Source`](xref:Xamarin.Forms.MediaElement.Source)，屬於類型 [`MediaSource`](xref:Xamarin.Forms.MediaSource) ，表示載入控制項的媒體來源。
- [`VideoHeight`](xref:Xamarin.Forms.MediaElement.VideoHeight)，屬於類型 `int` ，表示控制項的高度。 這是一個唯讀屬性。
- [`VideoWidth`](xref:Xamarin.Forms.MediaElement.VideoWidth)，屬於類型 `int` ，表示控制項的寬度。 這是一個唯讀屬性。
- [`Volume`](xref:Xamarin.Forms.MediaElement.Volume)，屬於類型 `double` ，可決定媒體的磁片區，這是以介於0和1之間的線性尺規表示。 這個屬性會使用系結 `TwoWay` ，而其預設值為1。

這些屬性（ `CanSeek` property 除外）是由物件所支援， [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 這表示它們可以是資料系結的目標，以及樣式化的。

[`MediaElement`](xref:Xamarin.Forms.MediaElement)類別也會定義四個事件：

- [`MediaOpened`](xref:Xamarin.Forms.MediaElement.MediaOpened)當媒體資料流程已經過驗證並開啟時，就會引發。
- [`MediaEnded`](xref:Xamarin.Forms.MediaElement.MediaEnded)會在 `MediaElement` 完成播放其媒體時引發。
- [`MediaFailed`](xref:Xamarin.Forms.MediaElement.MediaFailed)當有與媒體來源相關聯的錯誤時，就會引發。
- [`SeekCompleted`](xref:Xamarin.Forms.MediaElement.SeekCompleted)當要求的搜尋作業的搜尋點準備好播放時，就會引發。

此外， [`MediaElement`](xref:Xamarin.Forms.MediaElement) 包括 [`Play`](xref:Xamarin.Forms.MediaElement.Play) 、 [`Pause`](xref:Xamarin.Forms.MediaElement.Pause) 和 [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) 方法。

如需 Android 上支援之媒體格式的相關資訊，請參閱 developer.android.com 上[支援的媒體格式](https://developer.android.com/guide/topics/media/media-formats)。 如需通用 Windows 平臺 (UWP) 上支援之媒體格式的詳細資訊，請參閱[支援的編解碼器](/windows/uwp/audio-video-camera/supported-codecs)。

## <a name="play-remote-media"></a>播放遠端媒體

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以使用 HTTP 和 HTTPS URI 配置來播放遠端媒體檔案。 將 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 屬性設定為媒體檔案的 URI 即可完成這項作業：

```xaml
<MediaElement Source="https://sec.ch9.ms/ch9/5d93/a1eab4bf-3288-4faf-81c4-294402a85d93/XamarinShow_mid.mp4"
              ShowsPlaybackControls="True" />
```

根據預設，屬性所定義的媒體會在 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 媒體開啟後立即播放。 若要隱藏自動播放媒體，請將 [`AutoPlay`](xref:Xamarin.Forms.MediaElement.AutoPlay) 屬性設定為 `false` 。

媒體播放控制項預設為停用，並藉由將屬性設定 [`ShowsPlaybackControls`](xref:Xamarin.Forms.MediaElement.ShowsPlaybackControls) 為來啟用 `true` 。 [`MediaElement`](xref:Xamarin.Forms.MediaElement)接著會使用平臺播放控制項。

## <a name="play-local-media"></a>播放本機媒體

本機媒體可以從下列來源播放：

- 使用 URI 配置，內嵌在平臺應用程式中的資源 `ms-appx:///` 。
- 來自應用程式本機和暫存資料夾的檔案，使用 `ms-appdata:///` URI 配置。
- 裝置的程式庫。

如需這些 URI 配置的詳細資訊，請參閱[uri](/windows/uwp/app-resources/uri-schemes)配置。

### <a name="play-media-embedded-in-the-app-package"></a>播放內嵌于應用程式套件中的媒體

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以使用 URI 配置來播放內嵌于應用程式套件中的媒體檔案 `ms-appx:///` 。 媒體檔案會內嵌在應用程式套件中，方法是將它們放在平臺專案中。

將媒體檔案儲存在平臺專案中，每個平臺各有不同：

- 在 iOS 上，媒體檔案必須儲存在**resources**資料夾或**resources**資料夾的子資料夾中。 媒體檔案必須具有 `Build Action` 的 `BundleResource` 。
- 在 Android 上，媒體檔案必須儲存在名為**raw**之**資源**的子資料夾中。 **raw** 資料夾不能包含子資料夾。 媒體檔案必須具有 `Build Action` 的 `AndroidResource` 。
- 在 UWP 上，媒體檔案可以儲存在專案的任何資料夾中。 媒體檔案必須具有 `BuildAction` 的 `Content` 。

接著，您可以使用 URI 配置來播放符合這些準則的媒體檔案 `ms-appx:///` ：

```xaml
<MediaElement Source="ms-appx:///XamarinForms101UsingEmbeddedImages.mp4"
              ShowsPlaybackControls="True" />
```

使用資料系結時，可以使用值轉換器來套用此 URI 配置：

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

然後，可以使用的實例將 `VideoSourceConverter` `ms-appx:///` URI 配置套用至內嵌媒體檔案：

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

如需有關 ms-chap URI 配置的詳細資訊，請參閱[ms-appx 和 ms-chap-web](/windows/uwp/app-resources/uri-schemes#ms-appx-and-ms-appx-web)。

### <a name="play-media-from-the-apps-local-and-temporary-folders"></a>從應用程式的本機和暫存資料夾播放媒體

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以使用 URI 配置來播放複製到應用程式本機或暫存資料夾的媒體檔案 `ms-appdata:///` 。

下列範例顯示將 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 屬性設定為儲存在應用程式本機 data 資料夾中的媒體檔案：

```xaml
<MediaElement Source="ms-appdata:///local/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

下列範例 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 會針對儲存在應用程式暫存資料夾中的媒體檔案顯示內容：

```xaml
<MediaElement Source="ms-appdata:///temp/XamarinVideo.mp4"
              ShowsPlaybackControls="True" />
```

> [!IMPORTANT]
> 除了播放儲存在應用程式本機資料夾或暫存資料夾中的媒體檔案以外，UWP 也可以播放位於應用程式漫遊資料夾中的媒體檔案。 這可以藉由在媒體檔案前面加上來達成 `ms-appdata:///roaming/` 。

使用資料系結時，可以使用值轉換器來套用此 URI 配置：

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

`VideoSourceConverter`然後，可以使用的實例，將 `ms-appdata:///` URI 配置套用至應用程式本機或暫存資料夾中的媒體檔案：

```xaml
<MediaElement Source="{Binding MediaSource, Converter={StaticResource VideoSourceConverter}}"
              ShowsPlaybackControls="True" />
```

如需 ms appdata URI 配置的詳細資訊，請參閱[ms-appdata](/windows/uwp/app-resources/uri-schemes#ms-appdata)。

#### <a name="copying-a-media-file-to-the-apps-local-or-temporary-data-folder"></a>將媒體檔案複製到應用程式的本機或暫存資料夾

播放儲存在應用程式本機或暫存資料夾中的媒體檔案，需要由應用程式將媒體檔案複製到該處。 例如，從應用程式套件複製媒體檔案，即可完成這項作業：

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
> 上述程式碼範例會使用 `FileSystem` 包含在中的類別 Xamarin.Essentials 。 如需詳細資訊，請參閱[ Xamarin.Essentials 檔案系統](~/essentials/file-system-helpers.md?context=xamarin%2Fxamarin-forms&tabs=android)協助程式。

### <a name="play-media-from-the-device-library"></a>從裝置媒體櫃播放媒體

大部分的新式行動裝置和桌上型電腦都能夠使用裝置的相機和麥克風錄製影片和音訊。 然後，所建立的媒體會儲存為裝置上的檔案。 這些檔案可以從程式庫抓取並由播放 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 。

每個平臺都包含一個可讓使用者從裝置媒體櫃中選取媒體的設備。 在中 Xamarin.Forms ，平臺專案可以叫用這項功能，而且可以由 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 類別呼叫。

範例應用程式中所使用的「挑選相依性服務」影片，與[從圖片庫挑選相片](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)的方式非常類似，不同之處在于選擇器會傳回檔案名，而不是 `Stream` 物件。 共用程式碼專案會定義名為的介面 `IVideoPicker` ，它會定義名為的單一方法 `GetVideoFileAsync` 。 然後，每個平臺會在類別中執行此介面 `VideoPicker` 。

下列程式碼範例示範如何從裝置程式庫取出媒體檔案：

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

藉由呼叫方法來叫用影片挑選相依性服務， `DependencyService.Get` 以取得 `IVideoPicker` 平臺專案中介面的執行。 `GetVideoFileAsync`接著會在該實例上呼叫方法，並使用傳回的 filename 來建立 [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) 物件，並將它設定為的 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 屬性 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 。

## <a name="change-video-aspect-ratio"></a>變更影片外觀比例

[`Aspect`](xref:Xamarin.Forms.MediaElement.Aspect)屬性會決定如何調整影片媒體以符合顯示區域。 根據預設，這個屬性會設定為 `AspectFit` 列舉成員，但它可以設定為任何 [`Aspect`](xref:Xamarin.Forms.Aspect) 列舉成員：

- `AspectFit`表示影片會在需要時 letterboxed，以符合顯示區域，同時保留外觀比例。
- `AspectFill`表示將裁剪影片，使其填滿顯示區域，同時保留外觀比例。
- `Fill`指出影片將會伸展以填滿顯示區域。

## <a name="poll-for-position-data"></a>輪詢位置資料

可系結屬性的屬性變更通知， [`Position`](xref:Xamarin.Forms.MediaElement.Position) 只會在關鍵時刻引發，例如播放開始和結束，以及暫停發生。 因此，與屬性的資料系結 `Position` 將不會產生精確的位置資料。 相反地，您必須設定計時器並輪詢屬性。

若要執行這項操作，最好是在 `OnAppearing` 播放媒體時需要位置資料的頁面覆寫中：

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

在此範例中，覆 `OnAppearing` 寫會啟動 `positionLabel` 每秒以值更新的計時器 `Position` 。 計時器回呼會每秒叫用，直到回呼傳回為止 `false` 。 當頁面導覽發生時 `OnDisappearing` ，會執行覆寫，這會停止叫用的計時器回呼。

## <a name="understand-mediasource-types"></a>瞭解 MediaSource 類型

[`MediaElement`](xref:Xamarin.Forms.MediaElement)可以藉由將媒體的 [`Source`](xref:Xamarin.Forms.MediaElement.Source) 屬性設定為遠端或本機媒體檔案來播放媒體。 `Source`屬性的類型為 [`MediaSource`](xref:Xamarin.Forms.MediaSource) ，而這個類別會定義兩個靜態方法：

- [`FromFile`](xref:Xamarin.Forms.MediaSource.FromFile*)會 [`MediaSource`](xref:Xamarin.Forms.MediaSource) 從引數傳回實例 `string` 。
- [`FromUri`](xref:Xamarin.Forms.MediaSource.FromUri*)會 [`MediaSource`](xref:Xamarin.Forms.MediaSource) 從引數傳回實例 `Uri` 。

此外， [`MediaSource`](xref:Xamarin.Forms.MediaSource) 類別也具有 `MediaSource` 從 `string` 和引數傳回實例的隱含運算子 `Uri` 。

> [!NOTE]
> 在 [`Source`](xref:Xamarin.Forms.MediaElement.Source) XAML 中設定屬性時，會叫用類型轉換子，以 [`MediaSource`](xref:Xamarin.Forms.MediaSource) 從或傳回實例 `string` `Uri` 。

[`MediaSource`](xref:Xamarin.Forms.MediaSource)類別也有兩個衍生的類別：

- [`UriMediaSource`](xref:Xamarin.Forms.UriMediaSource)，用來指定來自 URI 的遠端媒體檔案。 這個類別具有 [`Uri`](xref:Xamarin.Forms.UriMediaSource.Uri) 可設定為的屬性 `Uri` 。
- [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource)，用來指定來自的本機媒體檔案 `string` 。 這個類別具有 [`File`](xref:Xamarin.Forms.FileMediaSource.File) 可設定為的屬性 `string` 。 此外，這個類別具有隱含運算子，可將轉換成 `string` `FileMediaSource` 物件，並將 `FileMediaSource` 物件 `string` 轉換為。

> [!NOTE]
> [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource)在 XAML 中建立物件時，會叫用類型轉換子，以 [`FileMediaSource`](xref:Xamarin.Forms.FileMediaSource) 從傳回實例 `string` 。

## <a name="determine-mediaelement-status"></a>判斷 MediaElement 狀態

[`MediaElement`](xref:Xamarin.Forms.MediaElement)類別會定義名為的唯讀可系結 [`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState) 屬性，其類型為 [`MediaElementState`](xref:Xamarin.Forms.MediaElementState) 。 此屬性指出控制項的目前狀態，例如媒體是否現正播放或已暫停，或者是否尚未準備好播放媒體。

[`MediaElementState`](xref:Xamarin.Forms.MediaElementState)列舉會定義下列成員：

- `Closed`表示 `MediaElement` 不包含任何媒體。
- `Opening`表示 `MediaElement` 正在驗證，並嘗試載入指定的來源。
- `Buffering`表示 `MediaElement` 正在載入媒體以進行播放。 其 [`Position`](xref:Xamarin.Forms.MediaElement.Position) 屬性不會在此狀態下前進。 如果 `MediaElement` 播放影片，它會繼續顯示最後顯示的畫面。
- `Playing`表示 `MediaElement` 現正播放媒體來源。
- `Paused`表示不 `MediaElement` 會推進其 [`Position`](xref:Xamarin.Forms.MediaElement.Position) 屬性。 如果 `MediaElement` 播放影片，它會繼續顯示目前的畫面格。
- `Stopped`表示 `MediaElement` 包含媒體，但未播放或暫停。 其 [`Position`](xref:Xamarin.Forms.MediaElement.Position) 屬性為0，且不會前進。 如果載入的媒體是影片，則會 `MediaElement` 顯示第一個框架。

[`CurrentState`](xref:Xamarin.Forms.MediaElement.CurrentState)使用傳輸控制項時，通常不需要檢查屬性 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 。 不過，在執行您自己的傳輸控制項時，這個屬性會變得很重要。

## <a name="implement-custom-transport-controls"></a>執行自訂傳輸控制項

Media player 的傳輸控制包括執行**播放**、**暫停**和**停止**功能的按鈕。 這些按鈕通常會以熟悉的圖示而非文字呈現，且 [播放]**** 及 [暫停]**** 功能常會合併為一個按鈕。

預設 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 會停用播放控制項。 這可讓您以程式設計 `MediaElement` 方式或藉由提供自己的傳輸控制項來控制。 為了支援此， `MediaElement` 包含 [`Play`](xref:Xamarin.Forms.MediaElement.Play) 、 [`Pause`](xref:Xamarin.Forms.MediaElement.Pause) 和 [`Stop`](xref:Xamarin.Forms.MediaElement.Stop) 方法。

下列 XAML 範例顯示包含 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 和自訂傳輸控制項的頁面：

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

在此範例中，自訂傳輸控制項會定義為 [`Button`](xref:Xamarin.Forms.Button) 物件。 不過，只有兩個 `Button` 物件，第一個代表 [ `Button` **播放**] 和 [**暫停**]，而第二個代表 [ `Button` **停止**]。 [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)物件是用來啟用和停用按鈕，以及切換 [**播放**] 和 [**暫停**] 之間的第一個按鈕。 如需資料觸發程式的詳細資訊，請參閱[ Xamarin.Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

程式碼後置檔案具有事件的處理常式 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) ：

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

[**播放**] 按鈕可在啟用後按下，以開始播放：

[![IOS 和 Android 上具有自訂傳輸控制項之 MediaElement 的螢幕擷取畫面](mediaelement-images/custom-transport-playback.png "MediaElement 播放影片")](mediaelement-images/custom-transport-playback-large.png#lightbox "MediaElement 播放影片")

按下 [**暫停**] 按鈕會導致播放暫停：

[![在 iOS 和 Android 上暫停播放的 MediaElement 螢幕擷取畫面](mediaelement-images/custom-transport-paused.png "具有暫停影片的 MediaElement")](mediaelement-images/custom-transport-paused-large.png#lightbox "具有暫停影片的 MediaElement")

按下 [**停止**] 按鈕會停止播放，並將媒體檔案的位置傳回到一開始。

## <a name="implement-a-custom-position-bar"></a>執行自訂位置列

每個平台實作的傳輸控制項都包括位置列。 此橫條類似于滑杆，並顯示媒體的總持續時間內的目前位置。 此外，您可以操作位置列，在影片中向前或向後移動至新位置。

若要執行自訂位置列，必須知道媒體的持續時間，以及目前的播放位置。 這項資料可在 [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) 和屬性中取得 [`Position`](xref:Xamarin.Forms.MediaElement.Position) 。

> [!IMPORTANT]
> [`Position`](xref:Xamarin.Forms.MediaElement.Position)必須輪詢，才能取得正確的位置資料。 如需詳細資訊，請參閱[輪詢位置資料](#poll-for-position-data)。

您可以使用來執行自訂位置列 [`Slider`](xref:Xamarin.Forms.Slider) ，如下列範例所示：

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

類別會定義它自己和可系結的屬性，以及可系結 `PositionSlider` `Duration` `Position` 的 `TimeToEnd` 屬性。 這三個屬性都屬於型別 `TimeSpan` 。 屬性的屬性變更處理常式會 `Duration` 將的 `Maximum` 屬性設定 [`Slider`](xref:Xamarin.Forms.Slider) 為值的 `TotalSeconds` 屬性 `TimeSpan` 。 `TimeToEnd`屬性的計算方式是根據 `Duration` 和屬性的變更 `Position` ，並從媒體的持續時間開始，並在播放繼續時減少為零。

`PositionSlider`當移動時，會從基礎更新， [`Slider`](xref:Xamarin.Forms.Slider) `Slider` 表示媒體應該是最先進的，或反轉到新的位置。 這會在函式的 `PropertyChanged` 處理常式中偵測到 `PositionSlider` 。 處理常式會檢查 `Value` 屬性中的變更，如果不同於 `Position` 屬性，則會從 `Value` 屬性設定 `Position` 屬性。 如需使用的詳細資訊 [`Slider`](xref:Xamarin.Forms.Slider) ，請參閱[ Xamarin.Forms 滑杆](~/xamarin-forms/user-interface/slider.md)

> [!NOTE]
> 在 Android 上， [`Slider`](xref:Xamarin.Forms.Slider) 只有1000個離散步驟，不論 `Minimum` 和設定為何 `Maximum` 。 如果媒體長度大於1000秒，則兩個不同的 `Position` 值會對應至相同的 `Value` `Slider` 。 這就是為什麼上述程式碼會檢查新的位置和現有的位置是否大於整體持續時間的1百分之一。

下列範例顯示在 `PositionSlider` 頁面上使用的：

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

在此範例中，的 `Duration` 屬性 `PositionSlider` 是資料系結至的 [`Duration`](xref:Xamarin.Forms.MediaElement.Duration) 屬性 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 。 當的 [`Value`](xref:Xamarin.Forms.Slider.Value) 屬性變更時 [`Slider`](xref:Xamarin.Forms.Slider) ， `ValueChanged` 就會引發事件並 `OnPositionSliderValueChanged` 執行處理常式。 這個處理常式會將 [`MediaElement.Position`](xref:Xamarin.Forms.MediaElement.Position) 屬性設定為屬性的值 `PositionSlider.Position` 。 因此， `Slider` 將媒體播放位置中的結果拖曳到變更：

[![在 iOS 和 Android 上具有自訂位置列的 MediaElement 螢幕擷取畫面](mediaelement-images/custom-position-bar.png "具有自訂位置列的 MediaElement")](mediaelement-images/custom-position-bar-large.png#lightbox "具有自訂位置列的 MediaElement")

此外， [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) `PositionSlider` 當媒體正在緩衝處理時，也會使用物件來停用。 如需資料觸發程式的詳細資訊，請參閱[ Xamarin.Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

## <a name="implement-a-custom-volume-control"></a>執行自訂磁片區控制項

每個平臺所執行的媒體播放控制項都包含一個音量列。 此橫條類似滑杆，並顯示媒體的音量。 此外，您可以操作音量列來增加或減少音量。

您可以使用來執行自訂磁片區橫條 [`Slider`](xref:Xamarin.Forms.Slider) ，如下列範例所示：

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

在此範例中， [`Slider`](xref:Xamarin.Forms.Slider) 資料會將其屬性系結 `Value` 至的 [`Volume`](xref:Xamarin.Forms.MediaElement.Volume) 屬性 [`MediaElement`](xref:Xamarin.Forms.MediaElement) 。 這是可能的，因為屬性會使用系結 `Volume` `TwoWay` 。 因此，變更 `Value` 屬性會導致 `Volume` 屬性變更。

> [!NOTE]
> [`Volume`](xref:Xamarin.Forms.MediaElement.Volume)屬性具有 vlidation 回呼，可確保其值大於或等於0.0，且小於或等於1.0。

如需使用的詳細資訊 [`Slider`](xref:Xamarin.Forms.Slider) ，請參閱[ Xamarin.Forms 滑杆](~/xamarin-forms/user-interface/slider.md)

## <a name="related-links"></a>相關連結

- [MediaElementDemos (範例) ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-mediaelementdemos/)
- [URI 配置](/windows/uwp/app-resources/uri-schemes)
- [Xamarin.Forms導致](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms一直](~/xamarin-forms/user-interface/slider.md)
- [Android：支援的媒體格式](https://developer.android.com/guide/topics/media/media-formats)
- [UWP：支援的編解碼器](/windows/uwp/audio-video-camera/supported-codecs)
