---
標題：「將 SkiaSharp 點陣圖儲存至檔案」描述： < 探索 SkiaSharp 支援的各種檔案格式，以將點陣圖儲存在使用者的相片媒體櫃中。」
skiasharp 的 assetid： 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C author： davidbritch ms-chap： dabritch ms. date： 07/10/2018 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="saving-skiasharp-bitmaps-to-files"></a>將 SkiaSharp 點陣圖儲存至檔案

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

在 SkiaSharp 應用程式建立或修改點陣圖之後，應用程式可能會想要將點陣圖儲存至使用者的相片圖庫：

![儲存點陣圖](saving-images/SavingSample.png "儲存點陣圖")

這項工作包含兩個步驟：

- 將 SkiaSharp 點陣圖轉換成特定檔案格式的資料，例如 JPEG 或 PNG。
- 使用平臺專屬的程式碼，將結果儲存至相片媒體櫃。

## <a name="file-formats-and-codecs"></a>檔案格式和編解碼器

現今大多數常用的點陣圖檔案格式都使用壓縮來減少儲存空間。 這兩個廣泛的壓縮技術類別稱為「_損_組 _」和「_ 不失真」。 這些詞彙指出壓縮演算法是否會導致資料遺失。

最受歡迎的損失真格式是由「聯合攝影專家」群組所開發，稱為「JPEG」。 JPEG 壓縮演算法會使用稱為_離散余弦轉換_的數學工具來分析影像，並嘗試移除不重要的資料來保留影像的視覺效果精確度。 您可以使用通常稱為「_品質_」的設定來控制壓縮的程度。 品質較高的設定會產生較大的檔案。

相反地，不失真的壓縮演算法會分析影像中是否有重複和圖元的模式，這些可以用來減少資料的方式進行編碼，但不會導致遺失任何資訊。 原始的點陣圖資料可以完全從壓縮檔案還原。 今日使用的主要無失真壓縮檔案格式是可移植的網狀圖形（PNG）。

一般來說，JPEG 會用於相片，而 PNG 則用於已手動或以演算法方式產生的影像。 可減少某些檔案大小的任何不失真壓縮演算法，都必須增加其他檔案的大小。 幸好，這種大小的增加通常僅適用于包含大量隨機（或看似隨機）資訊的資料。

壓縮演算法的複雜性足以保證兩個描述壓縮和解壓縮程式的詞彙：

- _decode_解碼 &mdash;讀取點陣圖檔案格式並將它解壓縮
- _編碼_ &mdash;壓縮點陣圖並寫入點陣圖檔案格式

[`SKBitmap`](xref:SkiaSharp.SKBitmap)類別包含數個名為 `Decode` 的方法，可 `SKBitmap` 從壓縮的來源建立。 所有必要的都是提供檔案名、資料流程或位元組陣列。 此解碼器可以判斷檔案格式，並將其交給適當的內部解碼函式。

此外， [`SKCodec`](xref:SkiaSharp.SKCodec) 類別有兩個名為 `Create` 的方法，可以 `SKCodec` 從壓縮的來源建立物件，並允許應用程式更深入地參與解碼進程。 （此 `SKCodec` 類別會顯示在以解碼動畫 GIF 檔案的方式建立[**SkiaSharp 點陣圖的動畫**](animating.md#gif-animation)文章中）。

為點陣圖編碼時，需要更多資訊：編碼器必須知道應用程式想要使用的特定檔案格式（JPEG 或 PNG 或其他專案）。 如果需要損失真格式，編碼也必須知道所需的品質等級。

`SKBitmap`類別會 [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) 使用下列語法來定義一個方法：

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

這個方法會在稍後更詳細地說明。 編碼的點陣圖會寫入至可寫入的資料流程。 （中的 ' W ' `SKWStream` 代表「可寫入」）。第二個和第三個引數指定檔案格式和（適用于損失真格式）所需的品質，範圍介於0到100之間。

此外， [`SKImage`](xref:SkiaSharp.SKImage) 和 [`SKPixmap`](xref:SkiaSharp.SKPixmap) 類別也會定義 `Encode` 更多用途的方法，而且您可能會偏好使用。 您可以使用靜態方法，輕鬆地 `SKImage` 從 `SKBitmap` 物件建立物件 [`SKImage.FromBitmap`](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) 。 您可以 `SKPixmap` 使用方法，從物件取得物件 `SKBitmap` [`PeekPixels`](xref:SkiaSharp.SKBitmap.PeekPixels) 。

所定義的其中一個 [`Encode`](xref:SkiaSharp.SKImage.Encode) 方法 `SKImage` 沒有參數，而且會自動儲存為 PNG 格式。 該無參數方法很容易使用。

## <a name="platform-specific-code-for-saving-bitmap-files"></a>儲存點陣圖檔案的平臺特定程式碼

當您將 `SKBitmap` 物件編碼成特定的檔案格式時，通常會留下某種類型的資料流程物件或資料的陣列。 某些 `Encode` 方法（包括沒有所定義之參數的方法 `SKImage` ） [`SKData`](xref:SkiaSharp.SKData) 會傳回物件，而這個物件可以使用方法轉換成位元組陣列 [`ToArray`](xref:SkiaSharp.SKData.ToArray) 。 然後必須將此資料儲存至檔案。

儲存至應用程式本機儲存體中的檔案相當簡單，因為您可以針對這項工作使用標準 `System.IO` 類別和方法。 這項技術會在以動畫顯示 Mandelbrot 集的一系列點陣圖建立動畫中的[**SkiaSharp 點陣圖**](animating.md#bitmap-animation)文章中示範。

如果您想要讓其他應用程式共用檔案，必須將檔案儲存到使用者的相片媒體櫃。 這項工作需要平臺特定的程式碼和使用 Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)應用程式中的**SkiaSharpFormsDemo**專案會定義 `IPhotoLibrary` 與類別搭配使用的介面 `DependencyService` 。 這會定義方法的語法 `SavePhotoAsync` ：

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

這個介面也會定義 `PickPhotoAsync` 方法，用來開啟裝置相片媒體櫃的平臺特定檔案選擇器。

針對 `SavePhotoAsync` ，第一個引數是位元組陣列，其中包含已編碼為特定檔案格式的點陣圖，例如 JPEG 或 PNG。 應用程式可能會想要將它所建立的所有點陣圖隔離到特定的資料夾中，這是在下一個參數中指定，後面加上檔案名。 方法會傳回表示成功的布林值。

下列各節將討論如何 `SavePhotoAsync` 在每個平臺上執行。

### <a name="the-ios-implementation"></a>IOS 執行

的 iOS 執行會 `SavePhotoAsync` 使用的 [`SaveToPhotosAlbum`](xref:UIKit.UIImage.SaveToPhotosAlbum*) 方法 `UIImage` ：

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        NSData nsData = NSData.FromArray(data);
        UIImage image = new UIImage(nsData);
        TaskCompletionSource<bool> taskCompletionSource = new TaskCompletionSource<bool>();

        image.SaveToPhotosAlbum((UIImage img, NSError error) =>
        {
            taskCompletionSource.SetResult(error == null);
        });

        return taskCompletionSource.Task;
    }
}
```

可惜的是，沒有辦法指定映射的檔案名或資料夾。

IOS 專案中的**plist**檔案需要一個金鑰，指出它會將影像新增至相片媒體櫃：

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

小心！ 只存取相片媒體櫃的許可權金鑰非常類似，但並不相同：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>Android 的執行

Android 的 `SavePhotoAsync` 第一次執行會檢查 `folder` 引數為 `null` 或空字串。 若是如此，點陣圖就會儲存在相片媒體櫃的根目錄中。 否則，會取得資料夾，如果不存在，則會建立：

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        try
        {
            File picturesDirectory = Environment.GetExternalStoragePublicDirectory(Environment.DirectoryPictures);
            File folderDirectory = picturesDirectory;

            if (!string.IsNullOrEmpty(folder))
            {
                folderDirectory = new File(picturesDirectory, folder);
                folderDirectory.Mkdirs();
            }

            using (File bitmapFile = new File(folderDirectory, filename))
            {
                bitmapFile.CreateNewFile();

                using (FileOutputStream outputStream = new FileOutputStream(bitmapFile))
                {
                    await outputStream.WriteAsync(data);
                }

                // Make sure it shows up in the Photos gallery promptly.
                MediaScannerConnection.ScanFile(MainActivity.Instance,
                                                new string[] { bitmapFile.Path },
                                                new string[] { "image/png", "image/jpeg" }, null);
            }
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

對的呼叫 `MediaScannerConnection.ScanFile` 並不是絕對必要的，但如果您是藉由立即檢查相片媒體櫃來測試程式，則更新程式庫資源庫視圖會有很大的説明。

**AndroidManifest.xml**檔案需要下列許可權標記：

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>UWP 的執行

UWP 的執行 `SavePhotoAsync` 在結構上非常類似于 Android 的執行：

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        StorageFolder picturesDirectory = KnownFolders.PicturesLibrary;
        StorageFolder folderDirectory = picturesDirectory;

        // Get the folder or create it if necessary
        if (!string.IsNullOrEmpty(folder))
        {
            try
            {
                folderDirectory = await picturesDirectory.GetFolderAsync(folder);
            }
            catch
            { }

            if (folderDirectory == null)
            {
                try
                {
                    folderDirectory = await picturesDirectory.CreateFolderAsync(folder);
                }
                catch
                {
                    return false;
                }
            }
        }

        try
        {
            // Create the file.
            StorageFile storageFile = await folderDirectory.CreateFileAsync(filename,
                                                CreationCollisionOption.GenerateUniqueName);

            // Convert byte[] to Windows buffer and write it out.
            IBuffer buffer = WindowsRuntimeBuffer.Create(data, 0, data.Length, data.Length);
            await FileIO.WriteBufferAsync(storageFile, buffer);
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

**Package.appxmanifest.xml**檔案的 [**功能**] 區段需要圖片文檔**庫**。

## <a name="exploring-the-image-formats"></a>探索影像格式

以下是再次提供的 [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) 方法 `SKImage` ：

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat)是一個列舉，其中包含參考11個位圖檔案格式的成員，其中有些是模糊的：

- `Astc`&mdash;彈性可擴充材質壓縮
- `Bmp`&mdash;Windows 點陣圖
- `Dng`&mdash;Adobe 數位負值
- `Gif`&mdash;圖形交換格式
- `Ico`&mdash;Windows 圖示影像
- `Jpeg`&mdash;聯合攝影專家群組
- `Ktx`&mdash;OpenGL 的 Khronos 材質格式
- `Pkm`&mdash;Pokémon 儲存檔案
- `Png`&mdash;可移植的網狀圖形
- `Wbmp`&mdash;無線應用程式協定點陣圖格式（每圖元1位）
- `Webp`&mdash;Google WebP 格式

您很快就會看到，SkiaSharp 實際上會支援其中三種檔案格式（ `Jpeg` 、 `Png` 和 `Webp` ）。

若要將 `SKBitmap` 名為 `bitmap` 的物件儲存至使用者的相片媒體櫃，您也需要一個 `SKEncodedImageFormat` 名為的列舉成員， `imageFormat` 以及一個整數變數（適用于有損的格式） `quality` 。 您可以使用下列程式碼，將該點陣圖儲存到資料夾中名稱為的檔案 `filename` `folder` ：

```csharp
using (MemoryStream memStream = new MemoryStream())
using (SKManagedWStream wstream = new SKManagedWStream(memStream))
{
    bitmap.Encode(wstream, imageFormat, quality);
    byte[] data = memStream.ToArray();

    // Check the data array for content!

    bool success = await DependencyService.Get<IPhotoLibrary>().SavePhotoAsync(data, folder, filename);

    // Check return value for success!
}
```

`SKManagedWStream`類別衍生自 `SKWStream` （代表「可寫入資料流程」）。 `Encode`方法會將編碼的點陣圖檔案寫入該資料流程中。 該程式碼中的批註會參考您可能需要執行的一些錯誤檢查。

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)應用程式中的 [**儲存檔案格式**] 頁面會使用類似的程式碼，讓您能夠試驗以各種格式儲存點陣圖。

XAML 檔案包含 `SKCanvasView` 會顯示點陣圖的，而頁面的其餘部分則包含應用程式呼叫方法時所需的所有專案 `Encode` `SKBitmap` 。 它有一個 `Picker` 用於列舉的成員 `SKEncodedImageFormat` 、一個適用于 `Slider` 損失真點陣圖格式的品質引數、兩個用於 `Entry` 檔案名和資料夾名稱的視圖，以及 `Button` 用於儲存檔案的。

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.SaveFileFormatsPage"
             Title="Save Bitmap Formats">

    <StackLayout Margin="10">
        <skiaforms:SKCanvasView PaintSurface="OnCanvasViewPaintSurface"
                                VerticalOptions="FillAndExpand" />

        <Picker x:Name="formatPicker"
                Title="image format"
                SelectedIndexChanged="OnFormatPickerChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKEncodedImageFormat}">
                    <x:Static Member="skia:SKEncodedImageFormat.Astc" />
                    <x:Static Member="skia:SKEncodedImageFormat.Bmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Dng" />
                    <x:Static Member="skia:SKEncodedImageFormat.Gif" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ico" />
                    <x:Static Member="skia:SKEncodedImageFormat.Jpeg" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ktx" />
                    <x:Static Member="skia:SKEncodedImageFormat.Pkm" />
                    <x:Static Member="skia:SKEncodedImageFormat.Png" />
                    <x:Static Member="skia:SKEncodedImageFormat.Wbmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Webp" />
                </x:Array>
            </Picker.ItemsSource>
        </Picker>

        <Slider x:Name="qualitySlider"
                Maximum="100"
                Value="50" />

        <Label Text="{Binding Source={x:Reference qualitySlider},
                              Path=Value,
                              StringFormat='Quality = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal">
            <Label Text="Folder Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="folderNameEntry"
                   Text="SaveFileFormats"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="File Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="fileNameEntry"
                   Text="Sample.xxx"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <Button Text="Save"
                Clicked="OnButtonClicked">
            <Button.Triggers>
                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference formatPicker},
                                               Path=SelectedIndex}"
                             Value="-1">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>

                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference fileNameEntry},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Button.Triggers>
        </Button>

        <Label x:Name="statusLabel"
               Text="OK"
               Margin="10, 0" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案會載入點陣圖資源，並使用 `SKCanvasView` 來顯示它。 該點陣圖永遠不會變更。 的 `SelectedIndexChanged` 處理常式 `Picker` 會使用與列舉成員相同的副檔名來修改檔案名：

```csharp
public partial class SaveFileFormatsPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(typeof(SaveFileFormatsPage),
        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    public SaveFileFormatsPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        args.Surface.Canvas.DrawBitmap(bitmap, args.Info.Rect, BitmapStretch.Uniform);
    }

    void OnFormatPickerChanged(object sender, EventArgs args)
    {
        if (formatPicker.SelectedIndex != -1)
        {
            SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
            fileNameEntry.Text = Path.ChangeExtension(fileNameEntry.Text, imageFormat.ToString());
            statusLabel.Text = "OK";
        }
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
        int quality = (int)qualitySlider.Value;

        using (MemoryStream memStream = new MemoryStream())
        using (SKManagedWStream wstream = new SKManagedWStream(memStream))
        {
            bitmap.Encode(wstream, imageFormat, quality);
            byte[] data = memStream.ToArray();

            if (data == null)
            {
                statusLabel.Text = "Encode returned null";
            }
            else if (data.Length == 0)
            {
                statusLabel.Text = "Encode returned empty array";
            }
            else
            {
                bool success = await DependencyService.Get<IPhotoLibrary>().
                    SavePhotoAsync(data, folderNameEntry.Text, fileNameEntry.Text);

                if (!success)
                {
                    statusLabel.Text = "SavePhotoAsync return false";
                }
                else
                {
                    statusLabel.Text = "Success!";
                }
            }
        }
    }
}
```

的 `Clicked` 處理常式 `Button` 會執行所有的實際工作。 它會從和取得的兩個引數 `Encode` `Picker` `Slider` ，然後使用稍早所示的程式碼，為 `SKManagedWStream` `Encode` 方法建立。 這兩個 `Entry` views 會提供方法的資料夾和檔案名 `SavePhotoAsync` 。

此方法大多是專門用來處理問題或錯誤。 如果 `Encode` 建立空陣列，則表示不支援特定的檔案格式。 如果傳回，則不會 `SavePhotoAsync` `false` 成功儲存檔案。

以下是正在執行的程式：

[![儲存檔案格式](saving-images/SaveFileFormats.png "儲存檔案格式")](saving-images/SaveFileFormats-Large.png#lightbox)

該螢幕擷取畫面顯示這些平臺上唯一支援的三種格式：

- JPEG
- PNG
- WebP

對於所有其他的格式， `Encode` 方法會將任何內容寫入資料流程，而結果位元組陣列則是空的。

儲存檔案**格式**頁面所節省的點陣圖是 600-圖元正方形。 每圖元4個位元組，在記憶體中總計1440000個位元組。 下表顯示各種檔案格式和品質組合的檔案大小：

|格式|品質|大小|
|------|------:|---:|
| PNG | N/A | 492K |
| JPEG | 0 | 2.95 k |
|      | 50 | 22.1 k |
|      | 100 | 206K |
| WebP | 0 | 2.71 k |
|      | 50 | 11.9 k |
|      | 100 | 101K |

您可以試驗各種品質設定並檢查結果。

## <a name="saving-finger-paint-art"></a>儲存手指繪製美工圖案

點陣圖的其中一個常見用法是在繪圖程式中，它的功能就像所謂的_陰影點陣圖_。 所有繪圖都會保留在點陣圖上，然後由程式顯示。 點陣圖也可以方便儲存繪圖。

[**SkiaSharp 文章中的手指繪製**](../paths/finger-paint.md)示範如何使用觸控追蹤來執行基本的手指繪製程式。 此程式只支援一種色彩，而只有一個筆觸寬度，但它會將整個繪圖保留在物件的集合中 `SKPath` 。

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的**手指繪製與儲存**頁面也會將整個繪圖保留在物件集合中 `SKPath` ，但它也會將繪圖轉譯成點陣圖，它可以儲存到您的相片媒體櫃。

此程式大部分與原始的**手指繪製**程式類似。 其中一個增強功能是 XAML 檔案現在會具現化標示為**Clear**和**Save**的按鈕：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Bitmaps.FingerPaintSavePage"
             Title="Finger Paint Save">

    <StackLayout>
        <Grid BackgroundColor="White"
              VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
        </Grid>

        <Button Text="Clear"
                Grid.Row="0"
                Margin="50, 5"
                Clicked="OnClearButtonClicked" />

        <Button Text="Save"
                Grid.Row="1"
                Margin="50, 5"
                Clicked="OnSaveButtonClicked" />

    </StackLayout>
</ContentPage>
```

程式碼後置檔案會維護名為之類型的欄位 `SKBitmap` `saveBitmap` 。 每當顯示介面的大小變更時，就會在處理常式中建立或重建這個點陣圖 `PaintSurface` 。 如果必須重新建立點陣圖，則會將現有點陣圖的內容複寫到新的點陣圖，讓所有專案都能保留，無論顯示介面的大小變化為何：

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    SKBitmap saveBitmap;

    public FingerPaintSavePage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        // Create bitmap the size of the display surface
        if (saveBitmap == null)
        {
            saveBitmap = new SKBitmap(info.Width, info.Height);
        }
        // Or create new bitmap for a new size of display surface
        else if (saveBitmap.Width < info.Width || saveBitmap.Height < info.Height)
        {
            SKBitmap newBitmap = new SKBitmap(Math.Max(saveBitmap.Width, info.Width),
                                              Math.Max(saveBitmap.Height, info.Height));

            using (SKCanvas newCanvas = new SKCanvas(newBitmap))
            {
                newCanvas.Clear();
                newCanvas.DrawBitmap(saveBitmap, 0, 0);
            }

            saveBitmap = newBitmap;
        }

        // Render the bitmap
        canvas.Clear();
        canvas.DrawBitmap(saveBitmap, 0, 0);
    }
    ···
}
```

處理常式所完成的繪製 `PaintSurface` 會在結尾進行，而且只包含呈現點陣圖。

觸控處理類似于先前的程式。 程式會維護兩個集合： `inProgressPaths` 和 `completedPaths` ，其中包含使用者自從上次清除顯示之後所繪製的所有專案。 針對每個觸控事件， `OnTouchEffectAction` 處理常式會呼叫 `UpdateBitmap` ：

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                            (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }

    void UpdateBitmap()
    {
        using (SKCanvas saveBitmapCanvas = new SKCanvas(saveBitmap))
        {
            saveBitmapCanvas.Clear();

            foreach (SKPath path in completedPaths)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }

            foreach (SKPath path in inProgressPaths.Values)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }
        }

        canvasView.InvalidateSurface();
    }
    ···
}
```

`UpdateBitmap`方法會藉 `saveBitmap` 由建立新的 `SKCanvas` 、清除它，然後呈現點陣圖上的所有路徑來重新繪製。 其結束的結論是 `canvasView` 讓點陣圖可以繪製在顯示上。

以下是這兩個按鈕的處理常式。 [**清除**] 按鈕會同時清除路徑集合、更新 `saveBitmap` （這會導致清除點陣圖），並使失效 `SKCanvasView` ：

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    void OnClearButtonClicked(object sender, EventArgs args)
    {
        completedPaths.Clear();
        inProgressPaths.Clear();
        UpdateBitmap();
        canvasView.InvalidateSurface();
    }

    async void OnSaveButtonClicked(object sender, EventArgs args)
    {
        using (SKImage image = SKImage.FromBitmap(saveBitmap))
        {
            SKData data = image.Encode();
            DateTime dt = DateTime.Now;
            string filename = String.Format("FingerPaint-{0:D4}{1:D2}{2:D2}-{3:D2}{4:D2}{5:D2}{6:D3}.png",
                                            dt.Year, dt.Month, dt.Day, dt.Hour, dt.Minute, dt.Second, dt.Millisecond);

            IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
            bool result = await photoLibrary.SavePhotoAsync(data.ToArray(), "FingerPaint", filename);

            if (!result)
            {
                await DisplayAlert("FingerPaint", "Artwork could not be saved. Sorry!", "OK");
            }
        }
    }
}
```

[**儲存**] 按鈕處理常式會使用的簡化 [`Encode`](xref:SkiaSharp.SKImage.Encode) 方法 `SKImage` 。 這個方法會使用 PNG 格式進行編碼。 `SKImage`物件是根據所建立 `saveBitmap` ，而 `SKData` 物件包含已編碼的 PNG 檔案。

的 `ToArray` 方法會取得 `SKData` 位元組陣列。 這是傳遞給方法的內容 `SavePhotoAsync` ，以及固定的資料夾名稱，以及從目前的日期和時間所建立的唯一檔案名。

以下是運作中的程式：

[![手指繪製儲存](saving-images/FingerPaintSave.png "手指繪製儲存")](saving-images/FingerPaintSave-Large.png#lightbox)

[**SpinPaint**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)範例中使用非常類似的技巧。 這也是一種手指繪製程式，不同之處在于使用者會繪製旋轉的磁片，然後在其他四個象限重現設計。 當磁片旋轉時，手指繪製的色彩會改變：

[![微調繪製](saving-images/SpinPaint.png "微調繪製")](saving-images/SpinPaint-Large.png#lightbox)

類別的 [**儲存**] 按鈕類似于 [ `SpinPaint` **手指繪製**]，它會將影像儲存到固定資料夾名稱（**SpainPaint**），以及從日期和時間所結構化的檔案名。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SpinPaint （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)
