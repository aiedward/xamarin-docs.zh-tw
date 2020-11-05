---
title: 將 SkiaSharp 點陣圖儲存至檔案
description: 探索 SkiaSharp 所支援的各種檔案格式，以將點陣圖儲存在使用者的相片圖庫中。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1d50c03fcea043c4b29db4a82ee3dc1712c288df
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372076"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>將 SkiaSharp 點陣圖儲存至檔案

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

在 SkiaSharp 應用程式建立或修改點陣圖之後，應用程式可能會想要將點陣圖儲存至使用者的照片媒體櫃：

![儲存點陣圖](saving-images/SavingSample.png "儲存點陣圖")

這項工作包含兩個步驟：

- 將 SkiaSharp 點陣圖轉換成特定檔案格式（例如 JPEG 或 PNG）的資料。
- 使用平臺特定程式碼將結果儲存至照片媒體櫃。

## <a name="file-formats-and-codecs"></a>檔案格式和編解碼器

現今大部分的熱門點陣圖檔案格式都會使用壓縮來減少儲存空間。 這兩個廣泛的壓縮技術類別稱為「失真 _」和「_ 無失真 _」。_ 這些條款指出壓縮演算法是否會導致資料遺失。

最受歡迎的失真格式是由聯合攝影專家群組所開發，稱為 JPEG。 JPEG 壓縮演算法會使用稱為 _離散余弦值_ 的數學工具來分析影像，並嘗試移除對保留影像的視覺精確度而言不重要的資料。 您可以使用通常稱為 _品質_ 的設定來控制壓縮的程度。 品質較高的設定會產生較大的檔案。

相反地，非失真壓縮演算法會分析影像中的重複和圖元模式，這些圖元可以用減少資料但不會導致遺失任何資訊的方式進行編碼。 原始點陣圖資料可以完全從壓縮檔案還原。 現今使用的主要不失真壓縮檔案格式是可移植網狀圖形 (PNG) 。

一般而言，JPEG 用於相片，而 PNG 則用於已手動或演算法產生的影像。 任何可縮減某些檔案大小的無失真壓縮演算法，都必須增加其他檔案的大小。 幸運的是，這種大小的增加通常僅適用于包含大量隨機 (或看似隨機) 資訊的資料。

壓縮演算法很複雜，足以保證描述壓縮和解壓縮程式的兩個詞彙：

- _decode_ 解碼 &mdash;讀取點陣圖檔案格式並將它解壓縮
- _編碼_ &mdash; 壓縮點陣圖並寫入點陣圖檔案格式

[`SKBitmap`](xref:SkiaSharp.SKBitmap)類別包含數個名稱為 `Decode` 的方法，可 `SKBitmap` 從壓縮的來源建立。 您只需要提供檔案名、資料流程或位元組陣列。 此解碼器可以判斷檔案格式，並將它交給適當的內部解碼函式。

此外，此 [`SKCodec`](xref:SkiaSharp.SKCodec) 類別具有兩個名為 `Create` 的方法，可 `SKCodec` 從壓縮的來源建立物件，並允許應用程式更進一步參與解碼程式。  (`SKCodec` 類別會顯示在將 [**SkiaSharp 點陣圖**](animating.md#gif-animation) 與解碼動畫 GIF 檔案的動畫文章中。 ) 

編碼點陣圖時需要更多資訊：編碼器必須知道應用程式想要使用的特定檔案格式 (JPEG 或 PNG，或是其他) 。 如果想要有遺失的格式，編碼也必須知道所需的品質等級。

`SKBitmap`類別會 [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) 使用下列語法定義一個方法：

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

稍後會更詳細地說明這個方法。 編碼的點陣圖會寫入至可寫入的資料流程。  (中的 ' W ' `SKWStream` 代表 "可寫入"。 ) 第二個和第三個引數會指定檔案格式，並針對失真格式 (，) 所需的品質範圍從0到100。

此外， [`SKImage`](xref:SkiaSharp.SKImage) 和 [`SKPixmap`](xref:SkiaSharp.SKPixmap) 類別也會定義 `Encode` 更具用途的方法，您可能會想要使用這些方法。 您可以使用靜態方法，輕鬆地 `SKImage` 從 `SKBitmap` 物件建立物件 [`SKImage.FromBitmap`](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) 。 您可以 `SKPixmap` `SKBitmap` 使用方法從物件取得物件 [`PeekPixels`](xref:SkiaSharp.SKBitmap.PeekPixels) 。

所定義的其中一個 [`Encode`](xref:SkiaSharp.SKImage.Encode) 方法沒有 `SKImage` 參數，而且會自動儲存為 PNG 格式。 這種無參數方法很容易使用。

## <a name="platform-specific-code-for-saving-bitmap-files"></a>儲存點陣圖檔案的平臺特定程式碼

當您將 `SKBitmap` 物件編碼為特定檔案格式時，通常會使用某種類型的資料流程物件或資料陣列。 某些 `Encode` 方法 (包括沒有由) 定義之參數的方法會傳回 `SKImage` [`SKData`](xref:SkiaSharp.SKData) 物件，您可以使用方法將它轉換為位元組陣列 [`ToArray`](xref:SkiaSharp.SKData.ToArray) 。 然後，您必須將此資料儲存至檔案。

儲存至應用程式本機儲存區中的檔案相當容易，因為您可以針對這項工作使用標準 `System.IO` 類別和方法。 這項技術會在文章中示範如何建立 [**SkiaSharp 點陣圖的動畫**](animating.md#bitmap-animation) ，並以動畫顯示 Mandelbrot 集的一連串點陣圖。

如果您想要讓其他應用程式共用檔案，則必須將檔案儲存到使用者的照片媒體櫃。 這項工作需要平臺特定程式碼和使用 Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) 。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)應用程式中的 **SkiaSharpFormsDemo** 專案會定義 `IPhotoLibrary` 與類別搭配使用的介面 `DependencyService` 。 這會定義方法的語法 `SavePhotoAsync` ：

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

此介面也會定義 `PickPhotoAsync` 方法，此方法可用來開啟適用于裝置之相片庫的平臺特定檔案選擇器。

若為 `SavePhotoAsync` ，第一個引數是位元組陣列，其中包含已編碼為特定檔案格式（例如 JPEG 或 PNG）的點陣圖。 應用程式可能會想要將它所建立的所有點陣圖隔離到特定資料夾中，並在下一個參數中指定，後面接著檔案名。 方法會傳回指出成功的布林值。

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

可惜的是，沒有任何方法可以指定映射的檔案名或資料夾。

IOS 專案中的 **plist** 檔案需要一個金鑰，表示它會將影像新增至照片庫：

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

小心！ 單純存取相片媒體櫃的許可權金鑰非常類似，但不是相同的：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>Android 執行

的 Android 執行 `SavePhotoAsync` 會先檢查 `folder` 引數是否為 `null` 或空字串。 如果是的話，則點陣圖會儲存在照片庫的根目錄中。 否則，會取得資料夾，如果不存在，則會建立該資料夾：

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

呼叫 `MediaScannerConnection.ScanFile` 不是絕對必要的，但如果您要立即檢查相片圖庫來測試程式，它會藉由更新程式庫資源庫視圖來協助您進行許多工作。

**AndroidManifest.xml** 檔案需要下列許可權標記：

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>UWP 的執行

的 UWP 執行 `SavePhotoAsync` 在結構上非常類似于 Android 的實作為：

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

**Package.appxmanifest** 檔案的 [ **功能** ] 區段需要 **圖片媒體** 櫃。

## <a name="exploring-the-image-formats"></a>探索映射格式

以下是 [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) 同樣的方法 `SKImage` ：

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat) 是一種列舉，其中有參考11個位圖檔案格式的成員，其中有些是模糊的：

- `Astc`&mdash;彈性可擴充紋理壓縮
- `Bmp`&mdash;Windows 點陣圖
- `Dng`&mdash;Adobe 數位負面
- `Gif`&mdash;圖形交換格式
- `Ico`&mdash;Windows 圖示影像
- `Jpeg`&mdash;聯合攝影專家群組
- `Ktx`&mdash;適用于 OpenGL 的 Khronos 材質格式
- `Pkm`&mdash;Pokémon 儲存檔案
- `Png`&mdash;可移植網狀圖形
- `Wbmp`&mdash;無線應用程式通訊協定點陣圖格式 (每圖元1位) 
- `Webp`&mdash;Google WebP 格式

您很快就會看到，SkiaSharp 實際上支援上述三種檔案格式 (`Jpeg` 、 `Png` 和 `Webp`) 。

若要將 `SKBitmap` 名為 `bitmap` 的物件儲存至使用者的照片媒體櫃，您也需要名為的列舉成員， `SKEncodedImageFormat` `imageFormat` 並 () 整數變數中的失真格式 `quality` 。 您可以使用下列程式碼，將該點陣圖儲存至資料夾中的檔案名 `filename` `folder` ：

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

`SKManagedWStream`類別衍生自 `SKWStream` (，其代表「可寫入的資料流程」 ) 。 `Encode`方法會將編碼的點陣圖檔案寫入該資料流程。 該程式碼中的批註是指您可能需要執行的一些錯誤檢查。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)應用程式中的 [儲存檔案 **格式** ] 頁面使用類似的程式碼，可讓您試驗如何以各種格式儲存點陣圖。

XAML 檔案包含 `SKCanvasView` 會顯示點陣圖的，而其餘的頁面則包含應用程式呼叫方法所需的所有專案 `Encode` `SKBitmap` 。 它擁有列舉的成員、適用于失真 `Picker` `SKEncodedImageFormat` `Slider` 點陣圖格式的 quality 引數、兩個 `Entry` 檔案名和資料夾名稱的視圖，以及用 `Button` 來儲存檔案的。

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

程式碼後端檔案會載入點陣圖資源，並使用 `SKCanvasView` 來顯示它。 該點陣圖永遠不會變更。 的 `SelectedIndexChanged` 處理常式 `Picker` 會使用與列舉成員相同的副檔名來修改檔案名：

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

的 `Clicked` 處理常式 `Button` 會執行所有的實際工作。 它會從和取得兩個 `Encode` 引數 `Picker` `Slider` ，然後使用稍早所示的程式碼來建立 `SKManagedWStream` 方法的 `Encode` 。 這兩個 `Entry` 視圖會提供方法的資料夾和檔案名 `SavePhotoAsync` 。

這種方法大多是專門用來處理問題或錯誤。 如果 `Encode` 建立空的陣列，則表示不支援特定的檔案格式。 如果 `SavePhotoAsync` 傳回 `false` ，則表示檔案未成功儲存。

以下是正在執行的程式：

[![儲存檔案格式](saving-images/SaveFileFormats.png "儲存檔案格式")](saving-images/SaveFileFormats-Large.png#lightbox)

該螢幕擷取畫面顯示這些平臺上唯一支援的三種格式：

- JPEG
- PNG
- WebP

針對所有其他格式，此 `Encode` 方法不會將任何內容寫入資料流程，而結果位元組陣列是空的。

儲存檔案 **格式頁面儲存** 的點陣圖是 600-圖元的正方形。 每個圖元4個位元組，在記憶體中的總計為1440000個位元組。 下表顯示檔案格式和品質的各種組合檔案大小：

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

## <a name="saving-finger-paint-art"></a>正在儲存手指油漆藝術

點陣圖的其中一種常見用法是在繪圖程式中，它會作為所謂的 _陰影點陣圖_ 。 所有繪圖都會保留在點陣圖上，然後由程式顯示。 點陣圖也方便儲存繪圖。

[**SkiaSharp 文章中的手指繪製**](../paths/finger-paint.md)示範如何使用觸控追蹤來執行基本的手指繪製程式。 此程式只支援一個色彩，而只有一個筆觸寬度，但它會將整個繪圖保留在物件的集合中 `SKPath` 。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的 **手指繪圖與儲存** 頁面也會將整個繪圖保留在物件的集合中 `SKPath` ，但它也會將繪圖轉譯成點陣圖，以便儲存到您的相片媒體櫃。

此程式大多與原始的 **手指油漆** 程式很相似。 其中一個增強功能是 XAML 檔案現在會具現化標示為 **Clear** 和 **Save** 的按鈕：

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

程式碼後端檔案會維護名為的類型欄位 `SKBitmap` `saveBitmap` 。 每當顯示介面的大小變更時，就會在處理常式中建立或重新建立此點陣圖 `PaintSurface` 。 如果需要重新建立點陣圖，則會將現有點陣圖的內容複寫到新的點陣圖，如此就不會保留所有專案，不論顯示器介面大小的變更為何：

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

處理常式所完成的繪圖 `PaintSurface` 會在結束時執行，而且只會包含轉譯點陣圖。

觸控處理類似于先前的程式。 此程式會維護兩個集合， `inProgressPaths` 以及 `completedPaths` 包含使用者自從上次清除顯示之後繪製的所有專案。 針對每個觸控事件， `OnTouchEffectAction` 處理常式會呼叫 `UpdateBitmap` ：

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

`UpdateBitmap`方法會藉 `saveBitmap` 由建立新的 `SKCanvas` 、清除它，然後轉譯點陣圖上的所有路徑來進行重新繪製。 它會藉由 `canvasView` 讓點陣圖在顯示器上繪製，來結束。

以下是兩個按鈕的處理常式。 [ **清除** ] 按鈕會清除兩個路徑集合、更新 `saveBitmap` (，這會導致清除點陣圖) ，並使 `SKCanvasView` ：

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

**儲存** 按鈕處理常式使用的簡化 [`Encode`](xref:SkiaSharp.SKImage.Encode) 方法 `SKImage` 。 這個方法會使用 PNG 格式進行編碼。 `SKImage`物件是根據所建立 `saveBitmap` ，而物件則 `SKData` 包含編碼的 PNG 檔案。

取得 `ToArray` `SKData` 位元組陣列的方法。 這是傳遞給方法的內容 `SavePhotoAsync` ，以及固定的資料夾名稱，以及從目前日期和時間所建立的唯一檔案名。

以下是運作中的程式：

[![手指油漆儲存](saving-images/FingerPaintSave.png "手指油漆儲存")](saving-images/FingerPaintSave-Large.png#lightbox)

[**SpinPaint**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)範例中使用了非常類似的技巧。 這也是手指繪製程式，不同之處在于使用者繪製在旋轉磁片上，然後再將設計重新產生于其他四個象限上。 手指繪圖的色彩會隨著磁片的旋轉而改變：

[![微調繪圖](saving-images/SpinPaint.png "微調繪圖")](saving-images/SpinPaint-Large.png#lightbox)

類別的 [ **儲存** ] 按鈕類似于 [ `SpinPaint` **手指繪製** ]，其會將影像儲存至固定的資料夾名稱 ( **SpainPaint** ) 以及從日期和時間所建立的檔案名。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SpinPaint (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)