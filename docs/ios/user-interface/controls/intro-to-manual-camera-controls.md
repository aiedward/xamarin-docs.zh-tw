---
title: 在 Xamarin 中手動相機控制項
description: 本檔說明如何搭配使用 iOS AVFoundation 架構與 Xamarin 來啟用手動相機控制項。 手動相機控制項可讓使用者控制焦點、白色餘額和曝光設定。
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 6f60b52d4fd29aacf319f9de94051e28c9876e33
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226694"
---
# <a name="manual-camera-controls-in-xamarinios"></a>在 Xamarin 中手動相機控制項

Ios 8 `AVFoundation Framework`中提供的手動相機控制項可讓行動應用程式完全控制 ios 裝置的相機。 這個精細的控制層級可用來建立專業層級的相機應用程式, 並在拍攝靜止影像或影片時, 藉由調整相機的參數來提供演出者組合。

開發科學或產業應用程式時, 這些控制項也非常有用, 其中的結果較不適合影像的正確性或美, 而是特別著重于反白顯示所採用影像的某些功能或元素。

## <a name="avfoundation-capture-objects"></a>AVFoundation Capture 物件

不論是在 iOS 裝置上使用相機拍攝影片還是靜止影像, 用來捕捉這些映射的程式基本上都相同。 這適用于使用預設自動化相機控制項的應用程式, 或利用新的手動相機控制項的應用程式:

 [![](intro-to-manual-camera-controls-images/image1.png "AVFoundation Capture 物件總覽")](intro-to-manual-camera-controls-images/image1.png#lightbox)

輸入是透過的方式`AVCaptureDeviceInput` `AVCaptureConnection`從`AVCaptureSession`取得。 結果可能會輸出為靜止影像或影片串流。 整個流程是由`AVCaptureDevice`所控制。

## <a name="manual-controls-provided"></a>提供的手動控制項

使用 iOS 8 提供的新 Api, 應用程式可以控制下列相機功能:

- **手動焦點**–藉由允許使用者直接控制焦點, 應用程式可以更充分掌控所採用的影像。
- **手動曝光**–藉由提供對公開的手動控制, 應用程式可以為使用者提供更多自由, 並讓他們能夠達到風格的外觀。
- **手動白平衡**–使用白色餘額來調整影像中的色彩, 通常是為了讓它看起來很逼真。 不同光源的色彩溫度不同, 用來捕捉影像的相機設定會經過調整, 以彌補這些差異。 同樣地, 藉由允許使用者控制白平衡, 使用者可以進行無法自動進行的調整。


iOS 8 提供現有 iOS Api 的延伸和增強功能, 可讓您更精細地控制映射捕捉程式。

## <a name="bracketed-capture"></a>括住的 Capture

括住的捕捉是以上述手動相機控制項的設定為基礎, 並可讓應用程式以各種不同的方式來捕捉時間點。

簡單地說, 加上括弧的 Capture 是一種高載的靜止影像, 其中包含各種不同的設定, 從圖片到圖片。

## <a name="requirements"></a>需求

需要下列專案, 才能完成本文中顯示的步驟:

- **Xcode 7 + 和 ios 8 或更新版本**– Apple 的 Xcode 7 和 ios 8 或更新版本的 api 必須在開發人員的電腦上安裝及設定。
- **Visual Studio for Mac** –應該在使用者裝置上安裝和設定最新版本的 Visual Studio for Mac。
- **ios 8 裝置**–執行最新版 ios 8 的 ios 裝置。 無法在 iOS 模擬器中測試相機功能。


## <a name="general-av-capture-setup"></a>一般 AV 捕捉設定

在 iOS 裝置上錄製影片時, 有一些一般的設定程式碼一定是必要的。 本節將涵蓋從 iOS 裝置的相機錄製影片所需的最基本設定, 並在中`UIImageView`即時顯示該影片。

### <a name="output-sample-buffer-delegate"></a>輸出範例緩衝區委派

第一個需要的專案是用來監視範例輸出緩衝區的委派, 並在應用程式 UI 中顯示從緩衝區抓取至`UIImageView`的影像。

下列常式會監視範例緩衝區, 並更新 UI:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using AVFoundation;
using CoreVideo;
using CoreMedia;
using CoreGraphics;

namespace ManualCameraControls
{
    public class OutputRecorder : AVCaptureVideoDataOutputSampleBufferDelegate
    {
        #region Computed Properties
        public UIImageView DisplayView { get; set; }
        #endregion

        #region Constructors
        public OutputRecorder ()
        {

        }
        #endregion

        #region Private Methods
        private UIImage GetImageFromSampleBuffer(CMSampleBuffer sampleBuffer) {

            // Get a pixel buffer from the sample buffer
            using (var pixelBuffer = sampleBuffer.GetImageBuffer () as CVPixelBuffer) {
                // Lock the base address
                pixelBuffer.Lock (0);

                // Prepare to decode buffer
                var flags = CGBitmapFlags.PremultipliedFirst | CGBitmapFlags.ByteOrder32Little;

                // Decode buffer - Create a new colorspace
                using (var cs = CGColorSpace.CreateDeviceRGB ()) {

                    // Create new context from buffer
                    using (var context = new CGBitmapContext (pixelBuffer.BaseAddress,
                        pixelBuffer.Width,
                        pixelBuffer.Height,
                        8,
                        pixelBuffer.BytesPerRow,
                        cs,
                        (CGImageAlphaInfo)flags)) {

                        // Get the image from the context
                        using (var cgImage = context.ToImage ()) {

                            // Unlock and return image
                            pixelBuffer.Unlock (0);
                            return UIImage.FromImage (cgImage);
                        }
                    }
                }
            }
        }
        #endregion

        #region Override Methods
        public override void DidOutputSampleBuffer (AVCaptureOutput captureOutput, CMSampleBuffer sampleBuffer, AVCaptureConnection connection)
        {
            // Trap all errors
            try {
                // Grab an image from the buffer
                var image = GetImageFromSampleBuffer(sampleBuffer);

                // Display the image
                if (DisplayView !=null) {
                    DisplayView.BeginInvokeOnMainThread(() => {
                        // Set the image
                        if (DisplayView.Image != null) DisplayView.Image.Dispose();
                        DisplayView.Image = image;

                        // Rotate image to the correct display orientation
                        DisplayView.Transform = CGAffineTransform.MakeRotation((float)Math.PI/2);
                    });
                }

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            }
            catch(Exception e) {
                // Report error
                Console.WriteLine ("Error sampling buffer: {0}", e.Message);
            }
        }
        #endregion
    }
}
```

當此常式備妥時, `AppDelegate`可以修改來開啟 AV 捕捉會話, 以記錄即時影片摘要。

### <a name="creating-an-av-capture-session"></a>建立 AV 捕獲會話

AV 捕捉會話是用來控制從 iOS 裝置相機播放即時影片的記錄, 而且必須將影片帶入 iOS 應用程式中。 由於範例`ManualCameraControl`範例應用程式會在數個不同的位置中使用 capture 會話, 因此它會`AppDelegate`在中設定, 並可供整個應用程式使用。

請執行下列動作來修改應用程式`AppDelegate`的, 並新增必要的程式碼:


1. 按兩下 方案總管中`AppDelegate.cs`的檔案以開啟它進行編輯。
1. 將下列 using 語句新增至檔案頂端:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    ```

1. 將下列私用變數和計算屬性加入至`AppDelegate`類別:

    ```csharp
    #region Private Variables
    private NSError Error;
    #endregion

    #region Computed Properties
    public override UIWindow Window {get;set;}
    public bool CameraAvailable { get; set; }
    public AVCaptureSession Session { get; set; }
    public AVCaptureDevice CaptureDevice { get; set; }
    public OutputRecorder Recorder { get; set; }
    public DispatchQueue Queue { get; set; }
    public AVCaptureDeviceInput Input { get; set; }
    #endregion
    ```

1. 覆寫已完成的方法, 並將它變更為:

    ```csharp
    public override void FinishedLaunching (UIApplication application)
    {
        // Create a new capture session
        Session = new AVCaptureSession ();
        Session.SessionPreset = AVCaptureSession.PresetMedium;

        // Create a device input
        CaptureDevice = AVCaptureDevice.DefaultDeviceWithMediaType (AVMediaType.Video);
        if (CaptureDevice == null) {
            // Video capture not supported, abort
            Console.WriteLine ("Video recording not supported on this device");
            CameraAvailable = false;
            return;
        }

        // Prepare device for configuration
        CaptureDevice.LockForConfiguration (out Error);
        if (Error != null) {
            // There has been an issue, abort
            Console.WriteLine ("Error: {0}", Error.LocalizedDescription);
            CaptureDevice.UnlockForConfiguration ();
            return;
        }

        // Configure stream for 15 frames per second (fps)
        CaptureDevice.ActiveVideoMinFrameDuration = new CMTime (1, 15);

        // Unlock configuration
        CaptureDevice.UnlockForConfiguration ();

        // Get input from capture device
        Input = AVCaptureDeviceInput.FromDevice (CaptureDevice);
        if (Input == null) {
            // Error, report and abort
            Console.WriteLine ("Unable to gain input from capture device.");
            CameraAvailable = false;
            return;
        }

        // Attach input to session
        Session.AddInput (Input);

        // Create a new output
        var output = new AVCaptureVideoDataOutput ();
        var settings = new AVVideoSettingsUncompressed ();
        settings.PixelFormatType = CVPixelFormatType.CV32BGRA;
        output.WeakVideoSettings = settings.Dictionary;

        // Configure and attach to the output to the session
        Queue = new DispatchQueue ("ManCamQueue");
        Recorder = new OutputRecorder ();
        output.SetSampleBufferDelegate (Recorder, Queue);
        Session.AddOutput (output);

        // Let tabs know that a camera is available
        CameraAvailable = true;
    }
    ```

1. 將變更儲存到檔案。


有了這段程式碼, 您就可以輕鬆地執行手動相機控制項來進行實驗和測試。

## <a name="manual-focus"></a>手動焦點

藉由允許使用者直接取得焦點的控制權, 應用程式可以為所採用的影像提供更多的藝術。

例如, 專業的攝影師可以柔化影像的焦點來達到[Bokeh 效果](https://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Bokeh 效果")](intro-to-manual-camera-controls-images/image2.png#lightbox)

或者, 建立[焦點提取效果](http://www.mediacollege.com/video/camera/focus/pull.html), 例如:

[![](intro-to-manual-camera-controls-images/image3.png "焦點提取效果")](intro-to-manual-camera-controls-images/image3.png#lightbox)

針對科學家或醫療應用程式的撰寫者, 應用程式可能會想要以程式設計的方式, 針對實驗移動其周圍的鏡頭。 無論是哪種方式, 新的 API 都可讓使用者或應用程式在拍攝影像時接管焦點。

### <a name="how-focus-works"></a>焦點的運作方式

討論在 IOS 8 應用程式中控制焦點的詳細資料之前。 讓我們快速瞭解焦點在 iOS 裝置中的運作方式:

[![](intro-to-manual-camera-controls-images/image4.png "IOS 裝置中的焦點運作方式")](intro-to-manual-camera-controls-images/image4.png#lightbox)

Light 進入 iOS 裝置上的相機鏡頭, 並著重于影像感應器。 與感應器之間的關聯性, 從感應器控制到焦點 (影像顯示最清晰的區域) 的距離。 從感應器放大的角度, 距離物件看起來會最清晰, 而接近的物件看起來就像最清晰。

在 iOS 裝置中, 會使用磁鐵和彈簧, 從感應器中更靠近或進一步移動鏡頭。 如此一來, 就無法精確定位鏡頭, 因為它會因裝置而異, 而且可能會受到參數的影響, 例如裝置的方向或裝置的時間和彈簧。

### <a name="important-focus-terms"></a>重要焦點詞彙

處理焦點時, 開發人員應該熟悉幾個詞彙:

- [**欄位深度**] –最接近和最左邊焦點物件之間的距離。
- **宏**-這是焦點範圍的近部, 而且是最靠近鏡頭焦點的距離。
- **無限大**–這是焦點範圍的最長, 也是最遠的距離。
- **Hyperfocal 距離**–這是在焦點範圍中, 畫面中最左邊的物件只是焦點的最下方。 換句話說, 這是最大化欄位深度的焦點位置。
- **透鏡位置**–這就是控制上述所有其他詞彙的內容。 這是從感應器到鏡頭的距離, 因而成為焦點的控制器。


有了這些詞彙和知識後, 新的手動焦點控制項就可以成功地在 iOS 8 應用程式中執行。

### <a name="existing-focus-controls"></a>現有的焦點控制項

iOS 7 和更早版本, 透過`FocusMode`屬性提供現有的焦點控制項, 如下所示:

- `AVCaptureFocusModeLocked`–焦點會在單一焦點點鎖定。
- `AVCaptureFocusModeAutoFocus`–相機會透過所有焦點來掃描鏡頭, 直到找到明顯的焦點, 然後再繼續。
- `AVCaptureFocusModeContinuousAutoFocus`–攝影機會在每次偵測到聚焦外的情況時 refocuses。


現有的控制項也會透過`FocusPointOfInterest`屬性提供可設定的相關點, 讓使用者可以利用焦點放在特定區域。 應用程式也可以藉由監視`IsAdjustingFocus`屬性來追蹤鏡頭移動。

此外, 屬性會提供範圍限制, 如下`AutoFocusRangeRestriction`所示:

- `AVCaptureAutoFocusRangeRestrictionNear`–將自動對焦限制為附近的深度。 在掃描 QR 代碼或條碼之類的情況下很有用。
- `AVCaptureAutoFocusRangeRestrictionFar`–將自動對焦限制為較遠的深度。 適用于已知不相關的物件位於視圖的欄位 (例如, 視窗框架) 的情況。


最後, `SmoothAutoFocus`屬性會減緩自動焦點演算法的速度, 並以較小的增量步驟進行, 以避免在錄製影片時移動構件。

### <a name="new-focus-controls-in-ios-8"></a>IOS 8 中的新焦點控制項

除了 iOS 7 和更新版本已提供的功能之外, 下列功能現在可以在 iOS 8 中控制焦點:

- 鎖定焦點時, 完全手動控制鏡頭位置。
- 任何焦點模式中的透鏡位置索引鍵/值觀察。


若要執行上述功能, `AVCaptureDevice`類別已經過修改, 以包含用來取得相機鏡頭目前位置的唯讀`LensPosition`屬性。

若要手動控制鏡頭位置, Capture 裝置必須處於鎖定的焦點模式。 範例：

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

Capture `SetFocusModeLocked`裝置的方法可用來調整相機鏡頭的位置。 當變更生效時, 可以提供選擇性的回呼常式來取得通知。 範例：

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

如上述程式碼所示, 您必須先鎖定 Capture 裝置進行設定, 才能進行鏡頭位置的變更。 有效的透鏡位置值介於0.0 和1.0 之間。

### <a name="manual-focus-example"></a>手動焦點範例

使用一般的 AV 捕捉設定程式碼, `UIViewController`可以將新增至應用程式的腳本, 並設定如下:

[![](intro-to-manual-camera-controls-images/image5.png "UIViewController 可以加入至應用程式分鏡腳本, 並加以設定, 如下所示")](intro-to-manual-camera-controls-images/image5.png#lightbox)

此視圖包含下列主要元素:

- `UIImageView`會顯示影片摘要的。
- `UISegmentedControl` , 會將焦點模式從自動變更為鎖定。
- `UISlider` , 會顯示並更新目前的鏡頭位置。


執行下列動作以連線到 view controller 以進行手動焦點控制:


1. 新增下列 using 語句:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```

1. 新增下列私用變數:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```

1. 新增下列計算屬性:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. 覆寫`ViewDidLoad`方法, 並新增下列程式碼:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Position.BeginInvokeOnMainThread(() =>{
                Position.Value = ThisApp.Input.Device.LensPosition;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {

            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);

            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto focus and start monitoring position
                Position.Enabled = false;
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.ContinuousAutoFocus;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Stop auto focus and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;
                Automatic = false;
                Position.Enabled = true;
                break;
            }

            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        // Monitor position changes
        Position.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic) return;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```

1. 覆寫`ViewDidAppear`方法, 並新增下列內容, 以便在視圖載入時開始錄製:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```

1. 當相機處於自動模式時, 滑杆會在相機調整焦點時自動移動:

    [![](intro-to-manual-camera-controls-images/image6.png "當相機調整此範例應用程式中的焦點時, 滑杆會自動移動")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. 請點一下鎖定的區段, 然後拖曳 [位置] 滑杆來手動調整鏡頭位置:

    [![](intro-to-manual-camera-controls-images/image7.png "手動調整鏡頭位置")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. 停止應用程式。


上述程式碼已示範如何在相機處於自動模式時監視鏡頭位置, 或在其處於鎖定模式時使用滑杆來控制鏡頭位置。

## <a name="manual-exposure"></a>手動曝光

曝光指的是相對於來源亮度之影像的亮度, 並取決於感應器的達到頻率、長時間, 以及感應器的增益層級 (ISO 對應)。 藉由提供對公開的手動控制, 應用程式可以為使用者提供更多自由, 並讓他們能夠達到有風格的外觀。

使用者可以使用手動公開控制, 將影像從誤以為明亮到深色和 moody:

[![](intro-to-manual-camera-controls-images/image8.png "顯示從誤以為明亮到深色和 moody 的影像範例")](intro-to-manual-camera-controls-images/image8.png#lightbox)

同樣地, 您可以使用程式設計的方式控制科學應用程式, 或透過應用程式使用者介面所提供的手動控制項來完成這項作業。 無論哪種方式, 新的 iOS 8 公開應用程式開發介面都可讓您更精細地控制相機的曝光設定。

### <a name="how-exposure-works"></a>曝光的運作方式

討論在 IOS 8 應用程式中控制公開的詳細資料。 讓我們快速查看曝光的運作方式:

[![](intro-to-manual-camera-controls-images/image9.png "曝光的運作方式")](intro-to-manual-camera-controls-images/image9.png#lightbox)

組成控制公開的三個基本元素如下:

- **快門速度**–這是打開快門以讓光線進入相機感應器的時間長度。 快門開啟的時間愈短, 越少越好, 影像就愈好 (較小的動作模糊)。 快門開啟的時間愈久, 就越少越好, 出現的動作模糊就越多。
- **ISO 對應**–這是從電影攝影借用的詞彙, 並指的是電影中的化學物的敏感度。 電影中的低 ISO 值具有較少的細微性和更細微的色彩複製;數位感應器的低 ISO 值的感應器噪音較少, 但亮度較少。 ISO 值越高, 影像愈亮, 但感應器雜訊愈多。 數位感應器上的「ISO」是一種[電子增益](https://en.wikipedia.org/wiki/Gain)量, 而不是實體功能。
- **透鏡光圈**–這是開啟的「鏡頭」大小。 在所有 iOS 裝置上, 會修正 [透鏡] 光圈, 因此唯一可用來調整曝光的兩個值為 [快門速度] 和 [ISO]。


### <a name="how-continuous-auto-exposure-works"></a>連續自動曝光的運作方式

在瞭解手動曝光如何運作之前, 最好先瞭解如何在 iOS 裝置中進行連續自動曝光。

[![](intro-to-manual-camera-controls-images/image10.png "在 iOS 裝置中連續自動曝光的運作方式")](intro-to-manual-camera-controls-images/image10.png#lightbox)

第一種是自動曝光區塊, 它的工作是計算理想的曝光, 並持續不斷地測量計量統計資料。它會使用這項資訊來計算 ISO 和快門速度的最佳混合, 讓場景變得更亮。 此迴圈稱為「AE」迴圈。

### <a name="how-locked-exposure-works"></a>鎖定曝光的運作方式

接下來, 讓我們來檢查鎖定的曝光在 iOS 裝置中的運作方式。

[![](intro-to-manual-camera-controls-images/image11.png "在 iOS 裝置中鎖定曝光的運作方式")](intro-to-manual-camera-controls-images/image11.png#lightbox)

同樣地, 您有嘗試計算最佳 iOS 和持續時間值的自動曝光區塊。 不過, 在此模式中, AE 區塊會與計量統計資料引擎中斷連接。

### <a name="existing-exposure-controls"></a>現有的曝光控制項

iOS 7 和更新版本, 透過`ExposureMode`屬性提供下列現有的公開控制:

- `AVCaptureExposureModeLocked`-範例場景一次, 並在整個場景中使用這些值。
- `AVCaptureExposureModeContinuousAutoExposure`–會持續取樣場景, 以確保它的光線良好。


可以用來透過選取要公開的目標物件來公開場景, 而應用程式可以`AdjustingExposure`監視屬性, 以便在調整曝光時查看。 `ExposurePointOfInterest`

### <a name="new-exposure-controls-in-ios-8"></a>IOS 8 中的新公開控制

除了 iOS 7 和更新版本已提供的功能之外, 下列功能現已開放在 iOS 8 中控制曝光:

- 完全手動的自訂曝光。
- 取得、設定和索引鍵-值會觀察 IOS 和快門速度 (持續時間)。


若要執行上述功能, 已新增`AVCaptureExposureModeCustom`新的模式。 當中的相機是自訂模式時, 可以使用下列程式碼來調整曝光持續時間和 ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

在 [自動] 和 [已鎖定] 模式中, 應用程式可以使用下列程式碼來調整自動曝光常式的偏差:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

最小和最大設定範圍取決於應用程式執行所在的裝置, 因此絕對不應該硬式編碼。 請改用下列屬性來取得最小和最大值範圍:

- `CaptureDevice.MinExposureTargetBias`
- `CaptureDevice.MaxExposureTargetBias`
- `CaptureDevice.ActiveFormat.MinISO`
- `CaptureDevice.ActiveFormat.MaxISO`
- `CaptureDevice.ActiveFormat.MinExposureDuration`
- `CaptureDevice.ActiveFormat.MaxExposureDuration`


如上述程式碼所示, 必須鎖定 Capture 裝置進行設定, 才能進行曝光變更。

### <a name="manual-exposure-example"></a>手動公開範例

使用一般的 AV 捕捉設定程式碼, `UIViewController`可以將新增至應用程式的腳本, 並設定如下:

[![](intro-to-manual-camera-controls-images/image12.png "UIViewController 可以加入至應用程式分鏡腳本, 並加以設定, 如下所示")](intro-to-manual-camera-controls-images/image12.png#lightbox)

此視圖包含下列主要元素:

- `UIImageView`會顯示影片摘要的。
- `UISegmentedControl` , 會將焦點模式從自動變更為鎖定。
- 四`UISlider`個會顯示並更新位移、持續時間、ISO 和偏差的控制項。


請執行下列動作, 以連接視圖控制器以進行手動公開控制:


1. 新增下列 using 語句:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```

1. 新增下列私用變數:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```

1. 新增下列計算屬性:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. 覆寫`ViewDidLoad`方法, 並新增下列程式碼:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Offset.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Offset.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;

        Duration.MinValue = 0.0f;
        Duration.MaxValue = 1.0f;

        ISO.MinValue = ThisApp.CaptureDevice.ActiveFormat.MinISO;
        ISO.MaxValue = ThisApp.CaptureDevice.ActiveFormat.MaxISO;

        Bias.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Bias.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Offset.BeginInvokeOnMainThread(() =>{
                Offset.Value = ThisApp.Input.Device.ExposureTargetOffset;
            });

            Duration.BeginInvokeOnMainThread(() =>{
                var newDurationSeconds = CMTimeGetSeconds(ThisApp.Input.Device.ExposureDuration);
                var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration), ExposureMinimumDuration);
                var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
                var p = (newDurationSeconds - minDurationSeconds) / (maxDurationSeconds - minDurationSeconds);
                Duration.Value = (float)Math.Pow(p, 1.0f/ExposureDurationPower);
            });

            ISO.BeginInvokeOnMainThread(() => {
                ISO.Value = ThisApp.Input.Device.ISO;
            });

            Bias.BeginInvokeOnMainThread(() => {
                Bias.Value = ThisApp.Input.Device.ExposureTargetBias;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {

            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);

            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto exposure and start monitoring position
                Duration.Enabled = false;
                ISO.Enabled = false;
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.ContinuousAutoExposure;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Lock exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Locked;
                Automatic = false;
                Duration.Enabled = false;
                ISO.Enabled = false;
                break;
            case 2:
                // Custom exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Custom;
                Automatic = false;
                Duration.Enabled = true;
                ISO.Enabled = true;
                break;
            }

            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        // Monitor position changes
        Duration.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic) return;

            // Calculate value
            var p = Math.Pow(Duration.Value,ExposureDurationPower);
            var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration),ExposureMinimumDuration);
            var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
            var newDurationSeconds = p * (maxDurationSeconds - minDurationSeconds) +minDurationSeconds;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(CMTime.FromSeconds(p,1000*1000*1000),ThisApp.CaptureDevice.ISO,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        ISO.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic) return;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(ThisApp.CaptureDevice.ExposureDuration,ISO.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        Bias.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            // if (Automatic) return;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetExposureTargetBias(Bias.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```

1. 覆寫`ViewDidAppear`方法, 並新增下列內容, 以便在視圖載入時開始錄製:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```

1. 當相機處於自動模式時, 滑杆會在相機調整曝光時自動移動:

    [![](intro-to-manual-camera-controls-images/image13.png "當相機調整曝光時, 滑杆會自動移動")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. 請點一下鎖定的區段並拖曳偏差滑杆, 以手動調整自動曝光的偏差:

    [![](intro-to-manual-camera-controls-images/image14.png "手動調整自動曝光的偏差")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. 按一下 [自訂] 區段, 然後拖曳 [持續時間] 和 [ISO] 滑杆以手動控制曝光:

    [![](intro-to-manual-camera-controls-images/image15.png "拖曳 [持續時間] 和 [ISO] 滑杆以手動控制曝光")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. 停止應用程式。


上述程式碼已示範如何在相機處於自動模式時監視曝光設定, 以及如何使用滑杆來控制其處於鎖定或自訂模式時的曝光。

## <a name="manual-white-balance"></a>手動白餘額

白色餘額控制項可讓使用者調整影像中的 colosr 平衡, 讓它們看起來更逼真。 不同的光源具有不同的顏色溫度, 而用來捕獲影像的相機設定則必須經過調整, 才能彌補這些差異。 同樣地, 藉由允許使用者控制白平衡, 他們就可以進行專業調整, 讓自動常式無法達成美術效果。

[![](intro-to-manual-camera-controls-images/image16.png "顯示手動白色餘額調整的範例影像")](intro-to-manual-camera-controls-images/image16.png#lightbox)

比方說, 日光具有 blueish 轉型, 而 tungsten incandescent 燈則具有暖、黃色-橙色色。 (Confusingly, 「酷炫」色彩的色彩溫度高於「暖」色彩。 顏色溫度是實體量值, 而不是可感知的。)

人們的想法在補償顏色溫度的差異時非常有用, 但這是相機無法執行的動作。 相機的運作方式是在相反的頻譜上提高色彩, 以調整色彩差異。

新的 iOS 8 公開 API 可讓應用程式控制進程, 並提供對相機的白色餘額設定的精細控制。

### <a name="how-white-balance-works"></a>白色餘額的運作方式

討論在 IOS 8 應用程式中控制白色餘額的詳細資料之前。 讓我們快速瞭解白色餘額的運作方式:

在色彩感知的研究中, [CIE 1931 RGB 色彩空間和 CIE 1931 XYZ 色彩空間](https://en.wikipedia.org/wiki/CIE_1931_color_space)是第一個以數學方式定義的色彩空間。 這些是由1931中的國際委員會 (CIE) 所建立。

[![](intro-to-manual-camera-controls-images/image17.png "CIE 1931 RGB 色彩空間和 CIE 1931 XYZ 色彩空間")](intro-to-manual-camera-controls-images/image17.png#lightbox)

上圖顯示人類眼看到的所有色彩, 從深藍色到鮮綠色到鮮紅色。 圖表上的任何點都可以使用 X 和 Y 值來繪製, 如上圖所示。

如圖中所示, 有 X 和 Y 值可在圖表上繪製, 而不在人為願景的範圍內, 因此相機無法重現這些色彩。

上圖中較小的曲線稱為「 [Planckian Locus](https://en.wikipedia.org/wiki/Planckian_locus), 表示色溫 (以度為單位), 藍色邊 (高) 上的數位較高, 紅色側邊的數位較低。 這些適用于一般光源狀況。

在混合光源條件中, 白色餘額調整必須偏離 Planckian Locus, 才能進行必要的變更。 在這些情況下, 必須將調整移至 CIE 尺規的綠色或紅色/洋紅色端。

iOS 裝置會藉由提高色彩增益來補償色彩轉換。 比方說, 如果場景的藍色太多, 則會提升紅色增益以進行補償。 這些增益值會針對特定裝置進行校正, 使其與裝置相依。

### <a name="existing-white-balance-controls"></a>現有的白色餘額控制項

iOS 7 和更新版本透過`WhiteBalanceMode`屬性提供下列現有的白色餘額控制項:

- `AVCapture WhiteBalance ModeLocked`-範例場景一次, 並在整個場景中使用這些值。
- `AVCapture WhiteBalance ModeContinuousAutoExposure`-持續取樣場景, 以確保其平衡。


而且應用程式可以監視`AdjustingWhiteBalance`屬性, 以查看何時調整曝光。

### <a name="new-white-balance-controls-in-ios-8"></a>IOS 8 中的新白餘額控制項

除了 iOS 7 和更新版本已提供的功能之外, 下列功能現在可用於控制 iOS 8 中的白色餘額:

- 完全手動控制裝置 RGB 增益。
- 取得、設定和索引鍵-值觀察到裝置 RGB 增益。
- 支援使用灰色卡片進行白平衡。
- 與裝置無關的色彩空間之間的轉換常式。


若要執行上述功能, `AVCaptureWhiteBalanceGain`已加入具有下列成員的結構:

- `RedGain`
- `GreenGain`
- `BlueGain`


最大的白色餘額增益目前是四 (4), 而且可以從`MaxWhiteBalanceGain`屬性進行準備。 因此合法範圍是從一 (1) 到`MaxWhiteBalanceGain` (4) 目前。

`DeviceWhiteBalanceGains`屬性可以用來觀察目前的值。 當`SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`相機處於鎖定的白色餘額模式時, 使用來調整餘額增益。

#### <a name="conversion-routines"></a>轉換常式

轉換常式已新增至 iOS 8, 以協助轉換與裝置無關的色彩空間。 若要執行轉換常式, `AVCaptureWhiteBalanceChromaticityValues`已加入具有下列成員的結構:

- `X`-是介於0到1之間的值。
- `Y`-是介於0到1之間的值。


`AVCaptureWhiteBalanceTemperatureAndTintValues`結構也已加入下列成員:

- `Temperature`-是以度數為單位的浮點值。
- `Tint`-這是從0到150的綠色或洋紅的位移, 其中正值是以綠色方向表示, 而向洋紅色則是負值。


`CaptureDevice.GetTemperatureAndTintValues`使用`CaptureDevice.GetDeviceWhiteBalanceGains`和方法, 在溫度和色調、色度和 RGB 增益色彩空間之間進行轉換。

> [!NOTE]
> 轉換常式更精確地, 要轉換的值愈接近 Planckian Locus。




#### <a name="gray-card-support"></a>灰色卡片支援

Apple 使用「灰色世界」一詞來參考 iOS 8 內建的灰色卡片支援。 它可讓使用者專注于實體灰色卡, 其中至少涵蓋框架中央的 50%, 並用來調整白色餘額。 灰色記憶卡的目的是要讓白色顯示成中性。

這可以在應用程式中執行, 方法是提示使用者將實體灰色卡片放在相機前方, 監視`GrayWorldDeviceWhiteBalanceGains`屬性並等待直到值向下。

然後, 應用程式會使用`SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` `GrayWorldDeviceWhiteBalanceGains`屬性的值來套用變更, 以鎖定方法的白色餘額增益。

必須先鎖定 Capture 裝置進行設定, 才能進行白餘額的變更。

### <a name="manual-white-balance-example"></a>手動白平衡範例

使用一般的 AV 捕捉設定程式碼, `UIViewController`可以將新增至應用程式的腳本, 並設定如下:

[![](intro-to-manual-camera-controls-images/image18.png "UIViewController 可以加入至應用程式分鏡腳本, 並加以設定, 如下所示")](intro-to-manual-camera-controls-images/image18.png#lightbox)

此視圖包含下列主要元素:

- `UIImageView`會顯示影片摘要的。
- `UISegmentedControl` , 會將焦點模式從自動變更為鎖定。
- 兩`UISlider`個會顯示並更新溫度和色調的控制項。
- , `UIButton`用來取樣灰色卡片 (灰色世界) 空間, 並使用這些值來設定白色餘額。


執行下列動作以連線到 view controller 以進行手動白餘額控制:


1. 新增下列 using 語句:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```

1. 新增下列私用變數:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```

1. 新增下列計算屬性:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. 新增下列私用方法以設定新的白色平衡溫度和色調:

    ```csharp
    #region Private Methods
    void SetTemperatureAndTint() {
        // Grab current temp and tint
        var TempAndTint = new AVCaptureWhiteBalanceTemperatureAndTintValues (Temperature.Value, Tint.Value);

        // Convert Color space
        var gains = ThisApp.CaptureDevice.GetDeviceWhiteBalanceGains (TempAndTint);

        // Set the new values
        if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
            gains = NomralizeGains (gains);
            ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
            ThisApp.CaptureDevice.UnlockForConfiguration ();
        }
    }

    AVCaptureWhiteBalanceGains NomralizeGains (AVCaptureWhiteBalanceGains gains)
    {
        gains.RedGain = Math.Max (1, gains.RedGain);
        gains.BlueGain = Math.Max (1, gains.BlueGain);
        gains.GreenGain = Math.Max (1, gains.GreenGain);

        float maxGain = ThisApp.CaptureDevice.MaxWhiteBalanceGain;
        gains.RedGain = Math.Min (maxGain, gains.RedGain);
        gains.BlueGain = Math.Min (maxGain, gains.BlueGain);
        gains.GreenGain = Math.Min (maxGain, gains.GreenGain);

        return gains;
    }
    #endregion
    ```

1. 覆寫`ViewDidLoad`方法, 並新增下列程式碼:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Temperature.MinValue = 1000f;
        Temperature.MaxValue = 10000f;

        Tint.MinValue = -150f;
        Tint.MaxValue = 150f;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Convert color space
            var TempAndTint = ThisApp.CaptureDevice.GetTemperatureAndTintValues (ThisApp.CaptureDevice.DeviceWhiteBalanceGains);

            // Update slider positions
            Temperature.BeginInvokeOnMainThread (() => {
                Temperature.Value = TempAndTint.Temperature;
            });

            Tint.BeginInvokeOnMainThread (() => {
                Tint.Value = TempAndTint.Tint;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (sender, e) => {
            // Lock device for change
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {

                // Take action based on the segment selected
                switch (Segments.SelectedSegment) {
                case 0:
                // Activate auto focus and start monitoring position
                    Temperature.Enabled = false;
                    Tint.Enabled = false;
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.ContinuousAutoWhiteBalance;
                    SampleTimer.Start ();
                    Automatic = true;
                    break;
                case 1:
                // Stop auto focus and allow the user to control the camera
                    SampleTimer.Stop ();
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.Locked;
                    Automatic = false;
                    Temperature.Enabled = true;
                    Tint.Enabled = true;
                    break;
                }

                // Unlock device
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };

        // Monitor position changes
        Temperature.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        Tint.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        GrayCardButton.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Get gray card values
            var gains = ThisApp.CaptureDevice.GrayWorldDeviceWhiteBalanceGains;

            // Set the new values
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
                ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };
    }
    ```

1. 覆寫`ViewDidAppear`方法, 並新增下列內容, 以便在視圖載入時開始錄製:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```

1. 將變更儲存至程式碼, 並執行應用程式。
1. 當相機處於自動模式時, 滑杆會在相機調整白平衡時自動移動:

    [![](intro-to-manual-camera-controls-images/image19.png "當相機調整白平衡時, 滑杆會自動移動")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. 請點一下鎖定的區段, 然後拖曳 [Temp] 和 [色調] 滑杆來手動調整白色餘額:

    [![](intro-to-manual-camera-controls-images/image20.png "拖曳 [Temp] 和 [色調] 滑杆以手動調整白色餘額")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. 在仍選取 [鎖定區段] 的情況下, 將實體灰色卡放在相機前方, 然後按 [灰色卡片] 按鈕, 以調整灰階的白色平衡:

    [![](intro-to-manual-camera-controls-images/image21.png "請按 [灰色卡片] 按鈕, 調整灰階的白色平衡")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. 停止應用程式。

上述程式碼已示範如何在相機處於自動模式時監視白色餘額設定, 或使用滑杆控制處於鎖定模式的白色餘額。

## <a name="bracketed-capture"></a>括住的 Capture

括住的捕捉是以上述手動相機控制項的設定為基礎, 並可讓應用程式以各種不同的方式來捕捉時間點。

簡單地說, 加上括弧的 Capture 是一種高載的靜止影像, 其中包含各種不同的設定, 從圖片到圖片。

[![](intro-to-manual-camera-controls-images/image22.png "有括弧的 Capture 如何運作")](intro-to-manual-camera-controls-images/image22.png#lightbox)

在 iOS 8 中使用加上括弧的 Capture, 應用程式可以預先設定一系列的手動相機控制項、發出單一命令, 並讓目前的場景傳回每個手動預設的一系列影像。

### <a name="bracketed-capture-basics"></a>括住的 Capture 基本概念

同樣地, 加上括弧的 Capture 是以不同的設定, 從圖片到圖片的靜止影像。 有括弧的 Capture 類型可供使用:

- **自動曝光括弧**–其中所有影像都有不同的偏差數量。
- **手動曝光括弧**–所有影像的快門速度 (持續時間) 和 ISO 數量都不同。
- **簡單**的高載括弧–一系列快速連續取得的靜止影像。


### <a name="new-bracketed-capture-controls-in-ios-8"></a>IOS 8 中的新加上括弧的 Capture 控制項

所有括住的`AVCaptureStillImageOutput` Capture 命令都會實作為類別。 `CaptureStillImageBracket`使用方法, 即可取得具有指定之設定陣列的一系列影像。

已執行兩個新類別來處理設定:

- `AVCaptureAutoExposureBracketedStillImageSettings`–它有一個屬性, `ExposureTargetBias`可用來設定自動曝光括弧的偏差。
- `AVCaptureManual`  `ExposureBracketedStillImageSettings`–它有兩個屬性`ExposureDuration` ( `ISO`和), 可用來設定手動曝光括弧的快門速度和 ISO。


### <a name="bracketed-capture-controls-dos-and-donts"></a>以括弧括住的 Capture 控制項確實不會

#### <a name="dos"></a>Do

以下是在 iOS 8 中使用括住的 Capture 控制項時應完成的事項清單:

- 藉由呼叫`PrepareToCaptureStillImageBracket`方法來準備應用程式, 以達到最壞案例的捕捉狀況。
- 假設範例緩衝區即將來自相同的共用集區。
- 若要釋放先前的準備呼叫所配置的記憶體, 請再次`PrepareToCaptureStillImageBracket`呼叫, 並將一個物件的陣列傳送給它。


#### <a name="donts"></a>注意事項

以下是在 iOS 8 中使用括住的 Capture 控制項時不應完成的事項清單:

- 請不要在單一捕獲中混用加上括弧的 Capture 設定類型。
- 不要在單一捕獲`MaxBracketedCaptureStillImageCount`中要求超過個影像。


### <a name="bracketed-capture-details"></a>括住的捕捉詳細資料

在 iOS 8 中使用括住的 Capture 時, 應將下列詳細資料納入考慮:

- 加上括弧的設定`AVCaptureDevice`會暫時覆寫設定。
- Flash 和仍然會略過影像穩定設定。
- 所有影像都必須使用相同的輸出格式 (jpeg、png 等等)
- 影片預覽可能會捨棄畫面格。
- 所有與 iOS 8 相容的裝置都支援括住的 Capture。


記住這項資訊之後, 讓我們來看一個在 iOS 8 中使用有括弧的 Capture 的範例。

### <a name="bracket-capture-example"></a>括弧捕捉範例

使用一般的 AV 捕捉設定程式碼, `UIViewController`可以將新增至應用程式的腳本, 並設定如下:

[![](intro-to-manual-camera-controls-images/image23.png "UIViewController 可以加入至應用程式分鏡腳本, 並加以設定, 如下所示")](intro-to-manual-camera-controls-images/image23.png#lightbox)

此視圖包含下列主要元素:

- `UIImageView`會顯示影片摘要的。
- 三`UIImageViews`個將顯示捕捉結果的。
- , `UIScrollView`用來存放影片摘要和結果檢視。
- , `UIButton`用來以一些預設設定來拍攝加上括弧的 Capture。


請執行下列動作, 以將 view controller 連接到有括弧的 Capture:


1. 新增下列 using 語句:

    ```csharp
    using System;
    using System.Drawing;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using CoreImage;
    ```

1. 新增下列私用變數:

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```

1. 新增下列計算屬性:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```

1. 新增下列私用方法, 以建立必要的輸出影像視圖:

    ```csharp
    #region Private Methods
    private UIImageView BuildOutputView(nint n) {

        // Create a new image view controller
        var imageView = new UIImageView (new CGRect (CameraView.Frame.Width * n, 0, CameraView.Frame.Width, CameraView.Frame.Height));

        // Load a temp image
        imageView.Image = UIImage.FromFile ("Default-568h@2x.png");

        // Add a label
        UILabel label = new UILabel (new CGRect (0, 20, CameraView.Frame.Width, 24));
        label.TextColor = UIColor.White;
        label.Text = string.Format ("Bracketed Image {0}", n);
        imageView.AddSubview (label);

        // Add to scrolling view
        ScrollView.AddSubview (imageView);

        // Return new image view
        return imageView;
    }
    #endregion
    ```

1. 覆寫`ViewDidLoad`方法, 並新增下列程式碼:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Setup scrolling area
        ScrollView.ContentSize = new SizeF (CameraView.Frame.Width * 4, CameraView.Frame.Height);

        // Add output views
        Output.Add (BuildOutputView (1));
        Output.Add (BuildOutputView (2));
        Output.Add (BuildOutputView (3));

        // Create preset settings
        var Settings = new AVCaptureBracketedStillImageSettings[] {
            AVCaptureAutoExposureBracketedStillImageSettings.Create(-2.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(0.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(2.0f)
        };

        // Wireup capture button
        CaptureButton.TouchUpInside += (sender, e) => {
            // Reset output index
            OutputIndex = 0;

            // Tell the camera that we are getting ready to do a bracketed capture
            ThisApp.StillImageOutput.PrepareToCaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings,async (bool ready, NSError err) => {
                // Was there an error, if so report it
                if (err!=null) {
                    Console.WriteLine("Error: {0}",err.LocalizedDescription);
                }
            });

            // Ask the camera to snap a bracketed capture
            ThisApp.StillImageOutput.CaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings, (sampleBuffer, settings, err) =>{
                // Convert raw image stream into a Core Image Image
                var imageData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
                var image = CIImage.FromData(imageData);

                // Display the resulting image
                Output[OutputIndex++].Image = UIImage.FromImage(image);

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            });
        };
    }
    ```


1. 覆寫`ViewDidAppear`方法, 並新增下列程式碼:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
        }
    }

    ```

1. 將變更儲存至程式碼, 並執行應用程式。
1. 將場景框住, 然後按 [捕捉括弧] 按鈕:

    [![](intro-to-manual-camera-controls-images/image24.png "將場景框住, 然後按 [捕捉括弧] 按鈕")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. 向右滑動以查看加上括弧的 Capture 所拍攝的三個影像:

    [![](intro-to-manual-camera-controls-images/image25.png "向右滑動以查看加上括弧的 Capture 所拍攝的三個影像")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. 停止應用程式。


上述程式碼已示範如何在 iOS 8 中設定和接受自動曝光括住的 Capture。

## <a name="summary"></a>總結

在本文中, 我們已介紹 iOS 8 所提供的新手動相機控制項簡介, 並涵蓋其用途和操作方式的基本概念。 我們已提供手動焦點、手動曝光和手動白餘額的範例。 最後, 我們提供了一個範例, 讓您使用先前所討論的手動相機控制項來進行有括弧的 Capture

## <a name="related-links"></a>相關連結

- [ManualCameraControls (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/manualcameracontrols)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
