---
title: 將 SkiaSharp 點陣圖儲存至檔案
description: 探索支援 SkiaSharp 將點陣圖儲存使用者的相片媒體櫃中的各種檔案格式。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C
author: charlespetzold
ms.author: chape
ms.date: 07/10/2018
ms.openlocfilehash: e957134ecceee84962e5a4fc153285ea0a2a5906
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615557"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>將 SkiaSharp 點陣圖儲存至檔案

SkiaSharp 應用程式已建立或修改點陣圖之後，應用程式可能會想要將點陣圖儲存到使用者的相片媒體櫃：

![將點陣圖儲存](saving-images/SavingSample.png "將點陣圖儲存")

這項工作包含兩個步驟：

- 將 SkiaSharp 點陣圖轉換成特定檔案格式，例如 JPEG 或 PNG 的資料。
- 將結果儲存到使用平台特定程式碼的相片媒體櫃。

## <a name="file-formats-and-codecs"></a>檔案格式和轉碼器

大部分的現今熱門的點陣圖檔案格式使用壓縮來減少儲存體空間。 壓縮技術兩大類統稱_失真_並_不失真_。 這些詞彙表示的壓縮演算法會導致資料遺失。

最受歡迎的失真格式由 Joint Photographic Experts Group 開發，並呼叫 JPEG。 JPEG 壓縮演算法會分析使用稱為數學工具的映像_離散的餘弦函數轉換_，並嘗試移除並不重要保留影像的視覺化精確度的資料。 壓縮程度可以控制與設定一般會簡稱_品質_。 較高的品質設定會導致較大的檔案。

相反地，不失真的壓縮演算法會分析重複和模式，可以減少資料，但不會導致遺失任何資訊的方式編碼的像素的影像。 可以完全從壓縮的檔案還原原始的點陣圖資料。 目前使用中的主要不失真的壓縮的檔格式會是可攜式網路圖形 (PNG)。

一般而言，JPEG 會用於相片，而 PNG 用於手動或以演算法產生的映像。 減少某些檔案大小的任何不失真的壓縮演算法必須一定會增加其他項目的大小。 幸運的是，這種增加量的大小通常只發生在包含許多隨機 （或隨機） 資訊的資料。

壓縮演算法都不夠複雜，無法保證兩個詞彙，描述的壓縮和解壓縮程序：

- _解碼_&mdash;讀取點陣圖的檔案格式，並將其解壓縮
- _編碼_&mdash;壓縮點陣圖，並寫入點陣圖檔案格式

[ `SKBitmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKBitmap/)類別包含數個方法，名為`Decode`，建立`SKBitmap`從壓縮的來源。 只需要是提供檔案名稱、 資料流或位元組陣列。 可以判斷檔案格式解碼器，並將它遞交給適當的內部解碼函式。

颾魤 ㄛ [ `SKCodec` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCodec/)類別有兩個方法，名為`Create`，可以建立`SKCodec`從壓縮的來源物件，並允許應用程式在解密程序中取得更複雜。 (`SKCodec`類別會顯示在 發行項[**動畫 SkiaSharp 點陣圖**](animating.md#gif-animation)與解碼的動畫的 GIF 檔案。)

當編碼點陣圖時的詳細資訊，:，編碼器必須知道應用程式想要使用 （JPEG 或 PNG 或其他項目） 的特定檔案格式。 如果需要失真格式，則編碼也必須知道所需的等級的品質。 

`SKBitmap`類別會定義其中一個[ `Encode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Encode/p/SkiaSharp.SKWStream/SkiaSharp.SKEncodedImageFormat/System.Int32/)方法，使用下列語法：

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

這個方法會更詳細描述短時間內。 寫入的資料流寫入編碼的點陣圖。 (在 'W'`SKWStream`代表 「 寫入 」。)第二個和第三個引數指定的檔案格式並 （適用於失真格式） 所需的品質，範圍從 0 到 100。

颾魤 ㄛ [ `SKImage` ](https://developer.xamarin.com/api/type/SkiaSharp.SKImage/)並[ `SKPixmap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPixmap/)類別也定義`Encode`稍微更具彈性，而且這可能會偏好的方法。 您可以輕鬆地建立`SKImage`物件從`SKBitmap`物件使用靜態[ `SKImage.FromBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKImage.FromBitmap/p/SkiaSharp.SKBitmap/)方法。 您可以取得`SKPixmap`物件從`SKBitmp`物件使用[ `PeekPixels` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.PeekPixels()/)方法。

其中一個[ `Encode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKImage.Encode()/)所定義的方法`SKImage`沒有參數並會自動儲存成 PNG 格式。 無參數的方法是非常好用。

## <a name="platform-specific-code-for-saving-bitmap-files"></a>平台特定程式碼，來儲存點陣圖檔

當您想要編碼`SKBitmap`物件至特定檔案格式，通常您會得到一些排序的資料流物件或資料的陣列。 部份`Encode`方法 (包括不含任何參數所定義的一個`SKImage`) 會傳回[ `SKData` ](https://developer.xamarin.com/api/type/SkiaSharp.SKData/)物件，它可以轉換成使用的位元組陣列[ `ToArray` ](https://developer.xamarin.com/api/member/SkiaSharp.SKData.ToArray()/)方法。 此資料必須儲存至檔案。 

正在儲存至應用程式的本機儲存體中的檔案是相當簡單，因為您可以使用標準`System.IO`類別和方法，這項工作。 本文會示範這項技術[**動畫 SkiaSharp 點陣圖**](animating.md#bitmap-animation)與以動畫顯示一連串點陣圖的 Mandelbrot 集合。

如果您想要由其他應用程式共用的檔案時，它必須儲存到使用者的相片媒體櫃中。 這項工作需要平台特定程式碼並使用 Xamarin.Forms [ `DependencyService` ](xref:Xamarin.Forms.DependencyService)。

**SkiaSharpFormsDemo**專案中[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)應用程式會定義`IPhotoLibrary`搭配使用的介面`DependencyService`類別。 這會定義的語法`SavePhotoAsync`方法：

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

此介面也會定義`PickPhotoAsync`方法，用來開啟裝置的相片媒體櫃的平台專屬檔案選擇器。

針對`SavePhotoAsync`，第一個引數是位元組陣列，包含已編碼為特定檔案格式，例如 JPEG 或 PNG 的點陣圖。 就可以應用程式可能會想要找出它的下一個參數，後面接著檔案名稱中指定的特定資料夾中建立的所有點陣圖。 方法會傳回布林值，指出成功與否。

以下是如何`SavePhotoAsync`三個平台上實作：

### <a name="the-ios-implementation"></a>IOS 實作

IOS 實作`SavePhotoAsync`會使用[ `SaveToPhotosAlbum` ](https://developer.xamarin.com/api/member/UIKit.UIImage.SaveToPhotosAlbum/)方法`UIImage`:

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

不幸的是，沒有辦法指定檔案名稱或映像的資料夾。 

**Info.plist** iOS 專案中的檔案需要索引鍵，以指定它將映像新增至相片媒體櫃：

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

請留意 ！ 直接存取的相片媒體櫃的權限索引鍵是非常類似但不同：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>Android 的實作

Android 的實作`SavePhotoAsync`先檢查是否有`folder`引數是`null`或空字串。 如果是這樣，點陣圖會儲存相片庫的根目錄。 否則，取得資料夾時，，而且如果不存在，則會建立：

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

若要呼叫`MediaScannerConnection.ScanFile`不是絕對必要，但如果您要測試您的程式立即檢查相片媒體櫃，就可以協助更藉由更新程式庫的 [資源庫] 檢視。

**AndroidManifest.xml**檔案需要下列權限標記：

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>UWP 實作

UWP 實作`SavePhotoAsync`結構中的 Android 的實作非常類似：

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

**能力**一節**Package.appxmanifest**檔案需要**圖片媒體櫃**。

## <a name="exploring-the-image-formats"></a>探索映像格式

以下是[ `Encode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Encode/p/SkiaSharp.SKWStream/SkiaSharp.SKEncodedImageFormat/System.Int32/)方法`SKImage`一次：

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](https://developer.xamarin.com/api/type/SkiaSharp.SKEncodedImageFormat/) 是列舉型別參考為 11 個點陣圖檔案格式，其中有些是相當晦澀難懂的成員：

- `Astc` &mdash; 自適性調整紋理壓縮
- `Bmp` &mdash; Windows 點陣圖
- `Dng` &mdash; Adobe Digital Negative
- `Gif` &mdash; 圖形交換格式
- `Ico` &mdash; Windows 圖示影像
- `Jpeg` &mdash; Joint Photographic Experts Group
- `Ktx` &mdash; OpenGL 的 Khronos 紋理格式
- `Pkm` &mdash; 將檔案儲存 Pokémon
- `Png` &mdash; 可攜式網路圖形
- `Wbmp` &mdash; 無線應用通訊協定的點陣圖格式 （每像素 1 位元）
- `Webp` &mdash; Google WebP 格式

如您所見，只有三種檔案格式 (`Jpeg`， `Png`，和`Webp`) SkiaSharp 實際上支援。

若要儲存`SKBitmap`名為物件`bitmap`到使用者的相片媒體櫃，您也需要的成員`SKEncodedImageFormat`列舉具名`imageFormat`並 （適用於失真格式） 整數`quality`變數。 您可以使用下列程式碼，將該點陣圖儲存到具有名稱的檔案`filename`在`folder`資料夾：

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

`SKManagedWStream`類別衍生自`SKWStream`（這代表 「 可寫入資料流 」）。 `Encode`方法會寫入該資料流中的編碼的點陣圖檔案。 該程式碼中的註解，請參閱檢查可能需要執行一些錯誤。 

**儲存的檔案格式**頁面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)應用程式會使用類似的程式碼可讓您試驗不同的格式儲存點陣圖。

XAML 檔案中包含`SKCanvasView`顯示點陣圖、 應用程式頁面的其餘部分包含的所有項目時要呼叫`Encode`方法`SKBitmap`。 它有`Picker`成員的`SKEncodedImageFormat`列舉型別`Slider`失真的點陣圖格式的品質引數的兩個`Entry`檔案名稱和資料夾名稱時，請檢視和`Button`以儲存檔案。

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

程式碼後置檔案載入點陣圖資源，並使用`SKCanvasView`來顯示它。 點陣圖永遠不會變更。 `SelectedIndexChanged`處理常式`Picker`修改延伸模組已列舉成員相同的檔案名稱：

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

`Clicked`處理常式`Button`所有的實際運作。 它會取得兩個引數`Encode`從`Picker`並`Slider`，然後使用 建立稍早所示的程式碼`SKManagedWStream`如`Encode`方法。 這兩個`Entry`檢視提供資料夾和檔案名稱`SavePhotoAsync`方法。

這個方法大部分被用來處理問題或發生錯誤。 如果`Encode`會建立空的陣列，這表示，不支援的特定檔案格式。 如果`SavePhotoAsync`傳回`false`，則檔案不成功儲存。 

以下是三個平台上執行的程式：

[![儲存檔案格式](saving-images/SaveFileFormats.png "儲存檔案格式")](saving-images/SaveFileFormats-Large.png#lightbox)

該螢幕擷取畫面顯示在這些平台支援的只有三個格式：

- JPEG
- PNG
- WebP

對於所有其他的格式，`Encode`方法會將執行任何動作寫入資料流，並產生的位元組陣列是空的。

點陣圖所**儲存的檔案格式**頁面儲存為 600 像素的正方形。 使用每像素的 4 個位元組，這是 1,440,000 記憶體中的位元組總計。 下表顯示各種組合的檔案格式和品質的檔案大小：

|格式|品質|大小|
|------|------:|---:|
| PNG | N/A | 492 K |
| JPEG | 0 | 2.95 K |
|      | 50 | 22.1 K |
|      | 100 | 206 K |
| WebP | 0 | 2.71 K |
|      | 50 | 11.9 K |
|      | 100 | 101 K |

您可以試驗不同的品質設定，並檢查結果。

## <a name="saving-finger-paint-art"></a>正在儲存 finger-paint 美工圖案

點陣圖的常見用法是以繪圖程式，它可在此函數，稱為 項目_陰影點陣圖_。 點陣圖，然後顯示該程式上不會保留所有繪圖。 點陣圖也派上用場儲存繪圖。

[**手指繪製在 SkiaSharp** ](../paths/finger-paint.md)文章示範如何使用追蹤來實作基本的手指繪製程式的觸控。 程式支援只有一個色彩] 和 [只有一個筆觸寬度，但保留集合中的整個繪圖`SKPath`物件。

**使用手指繪製儲存**頁面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例也會保留整個集合中的繪圖`SKPath`物件，但它也呈現的點陣圖，它可以將它儲存到您的相片媒體櫃中的繪圖。

此程式大多是類似於原始**手指繪製**程式。 加強的部分是，XAML 檔案現在會具現化標示的按鈕**清楚**並**儲存**:

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

程式碼後置檔案中會保留類型的欄位`SKBitmap`名為`saveBitmap`。 建立或在重新建立此點陣圖`PaintSurface`處理常式時顯示的大小呈現的變更。 如果需要重新建立點陣圖，現有的點陣圖的內容複製到新的點陣圖，因此不論在顯示介面的大小變更的方式保留的所有項目：

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

繪製由`PaintSurface`處理常式就會發生在結尾處，且僅包含轉譯點陣圖。

觸控處理將會類似於舊版的程式。 程式會維護兩個集合`inProgressPaths`和`completedPaths`，包含自前次已清除顯示畫了條使用者的所有項目。 每個觸控事件，如`OnTouchEffectAction`處理常式會呼叫`UpdateBitmap`:

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

`UpdateBitmap`方法會重繪`saveBitmap`藉由建立新`SKCanvas`，清除它，然後轉譯點陣圖上的所有路徑。 它結束時，會使失效`canvasView`，以便顯示畫面上，可以繪製點陣圖。

以下是兩個按鈕的處理常式。 **清楚** 按鈕會清除這兩個路徑的集合，更新`saveBitmap`（這會導致清除點陣圖），則`SKCanvasView`:

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

**儲存** 按鈕處理常式會使用簡化[ `Encode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKImage.Encode()/)方法從`SKImage`。 這個方法會將編碼使用 PNG 格式。 `SKImage`物件會根據建立`saveBitmap`，和`SKData`物件包含已編碼的 PNG 檔案。 

`ToArray`方法的`SKData`取得位元組陣列。 這是傳遞至`SavePhotoAsync`方法中，固定的資料夾名稱，以及唯一的檔名，建構自目前的日期和時間。

以下是作用中的程式：

[![手指儲存的小畫家](saving-images/FingerPaintSave.png "手指繪製儲存")](saving-images/FingerPaintSave-Large.png#lightbox)

用於非常類似的技巧[ **SpinPaint** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SpinPaint/)範例。 這也是手指繪製程式不同之處在於使用者繪製然後重現上其其他四個象限的設計轉動式磁碟上。 旋轉的手指繪製變更色彩的磁碟：

[![啟動 小畫家](saving-images/SpinPaint.png "微調 [小畫家]")](saving-images/SpinPaint-Large.png#lightbox)

**儲存**按鈕`SpinPaint`就如同**手指繪製**，因為它會將影像儲存為固定的資料夾名稱 (**SpainPaint**) 和從建構檔案名稱日期和時間。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [SpinPaint （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SpinPaint/)
