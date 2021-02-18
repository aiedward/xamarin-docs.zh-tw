---
title: 在 Xamarin 中手動相機控制項
description: 本檔說明如何搭配使用 iOS AVFoundation 架構與 Xamarin 來啟用手動相機控制。 手動相機控制項可讓使用者控制焦點、白色餘額和曝光設定。
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 9162754dd41190a14ce1b385d63e94f7c8c1ced7
ms.sourcegitcommit: e7a5d1ec9e50a09b3b24f4c57850a4763c3406d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2021
ms.locfileid: "101087500"
---
# <a name="manual-camera-controls-in-xamarinios"></a>在 Xamarin 中手動相機控制項

IOS 8 提供的手動攝影機控制項， `AVFoundation Framework` 可讓行動應用程式完全掌控 ios 裝置的相機。 這種精細的控制層級可用來建立專業級的相機應用程式，並在拍攝靜止影像或影片的同時，藉由調整相機的參數來提供演出者組合。

當開發科學或產業應用程式時，這些控制項也很有用，其中的結果較不適合影像的正確性或美點，而且會有更多強調顯示所拍攝影像的部分功能或元素。

## <a name="avfoundation-capture-objects"></a>AVFoundation Capture 物件

無論在 iOS 裝置上使用相機拍攝影片或靜止影像，用來捕獲這些映射的程式大致相同。 這適用于使用預設自動化相機控制項的應用程式，或利用新的手動相機控制項的應用程式：

 [![AVFoundation Capture 物件總覽](intro-to-manual-camera-controls-images/image1.png)](intro-to-manual-camera-controls-images/image1.png#lightbox)

輸入是透過進行的 `AVCaptureDeviceInput` `AVCaptureSession` `AVCaptureConnection` 。 結果會輸出為靜止影像或影片串流。 整個進程由控制 `AVCaptureDevice` 。

## <a name="manual-controls-provided"></a>已提供手動控制項

使用 iOS 8 提供的新 Api，應用程式可以控制下列相機功能：

- **手動焦點** –藉由讓使用者直接掌控焦點，讓應用程式能夠更充分掌控拍攝的影像。
- **手動曝光** -藉由提供手動控制曝光，應用程式可以為使用者提供更多的自由，讓他們能夠達成樣式的外觀。
- **手動平衡** –白色餘額可用來調整影像中的色彩，通常會讓它看起來很實際。 不同的燈光來源具有不同的色溫度，而用來捕捉影像的相機設定則會調整以彌補這些差異。 同樣地，藉由允許使用者控制白色餘額，使用者可以進行無法自動執行的調整。

iOS 8 提供現有 iOS Api 的擴充功能和增強功能，可讓您更精細地控制映射捕獲進程。

## <a name="requirements"></a>規格需求

若要完成本文中所述的步驟，必須具備下列專案：

- **Xcode 7 + 和 ios 8 或更新版本** – Apple 的 Xcode 7 和 ios 8 或更新版本的 api 必須安裝並設定于開發人員的電腦上。
- **Visual Studio for Mac** –應該在使用者裝置上安裝並設定最新版本的 Visual Studio for Mac。
- **ios 8 裝置** –執行最新版本 ios 8 的 ios 裝置。 無法在 iOS 模擬器中測試攝影機功能。

## <a name="general-av-capture-setup"></a>一般 AV 捕獲設定

當您在 iOS 裝置上錄製影片時，有一些一般的設定程式碼一律是必要的。 本節將涵蓋從 iOS 裝置的相機錄製影片，並在中即時顯示該影片所需的最基本設定 `UIImageView` 。

### <a name="output-sample-buffer-delegate"></a>輸出範例緩衝區委派

其中一個需要的專案是一個委派，用來監視範例輸出緩衝區，並 `UIImageView` 在應用程式 UI 中顯示從緩衝區抓取到的影像。

下列常式會監視範例緩衝區並更新 UI：

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

使用這個常式時， `AppDelegate` 可以修改來開啟 AV 捕捉會話，以記錄即時影片摘要。

### <a name="creating-an-av-capture-session"></a>建立 AV 捕獲會話

AV 捕捉會話可用來控制從 iOS 裝置相機錄製的實況影片，而且必須將影片放入 iOS 應用程式。 由於範例 `ManualCameraControl` 應用程式範例是在數個不同的位置使用 capture 會話，因此它會在中設定， `AppDelegate` 並可供整個應用程式使用。

請執行下列動作來修改應用程式 `AppDelegate` ，並新增必要的程式碼：

1. 按兩下 `AppDelegate.cs` 方案總管中的檔案，將它開啟以供編輯。
1. 將下列 using 陳述式加入至檔案頂端：

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

1. 將下列私用變數和計算屬性新增至 `AppDelegate` 類別：

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

1. 覆寫完成的方法，並將其變更為：

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

1. 將變更儲存至檔案。

有了這段程式碼之後，就可以輕鬆地執行手動相機控制項以進行實驗和測試。

## <a name="manual-focus"></a>手動焦點

藉由讓終端使用者直接取得焦點的控制權，應用程式可以對拍攝的影像提供更多的藝術。

例如，專業攝影師可以柔化影像的焦點，以達成 [Bokeh 效果](https://en.wikipedia.org/wiki/Bokeh)：

[![Bokeh 效果](intro-to-manual-camera-controls-images/image2.png)](intro-to-manual-camera-controls-images/image2.png#lightbox)

或者，建立 [焦點提取效果](http://www.mediacollege.com/video/camera/focus/pull.html)，例如：

[![焦點提取效果](intro-to-manual-camera-controls-images/image3.png)](intro-to-manual-camera-controls-images/image3.png#lightbox)

對於科學家或醫院的醫療應用程式，應用程式可能會想要以程式設計的方式移動適用于實驗的鏡頭。 這兩種方式都可讓使用者或應用程式在拍攝影像時取得焦點的控制權。

### <a name="how-focus-works"></a>焦點的運作方式

討論如何在 IOS 8 應用程式中控制焦點的詳細資料。 讓我們快速查看焦點在 iOS 裝置中的運作方式：

[![焦點在 iOS 裝置中的運作方式](intro-to-manual-camera-controls-images/image4.png)](intro-to-manual-camera-controls-images/image4.png#lightbox)

Light 進入 iOS 裝置上的相機鏡頭，並著重于映射感應器。 在感應器的關聯性中，焦點與感應器之間的距離會 (影像將出現最清晰) 的區域。 從感應器的角度來看，距離物件看似最清晰，接近的物件則看似最清晰。

在 iOS 裝置中，會以磁鐵和彈簧從感應器移動更接近或更接近的鏡頭。 如此一來，就無法精確放置鏡頭，因為它會因裝置而異，而且可能會受到參數的影響，例如裝置的方向或裝置的年齡和春季。

### <a name="important-focus-terms"></a>重要焦點字詞

處理焦點時，開發人員應該熟悉一些詞彙：

- [**欄位深度**] –最接近或最左邊的焦點物件之間的距離。
- **宏** -這是焦點頻譜的近距離，而且是最接近鏡頭焦點的距離。
- **無限大** –這是焦點頻譜的最一端，而且是最遠的鏡頭距離。
- **Hyperfocal 距離** –這是位於焦點色譜中的點，其中框架中最左邊的物件只是位於焦點的最一端。 換句話說，這是最大化欄位深度的焦點位置。
- **鏡頭位置** –這是控制上述所有其他詞彙的內容。 這是距感應器和焦點控制站之間的距離。

有了這些詞彙和知識，新的手動焦點控制項就可以在 iOS 8 應用程式中成功執行。

### <a name="existing-focus-controls"></a>現有的焦點控制項

iOS 7 及更早版本，透過屬性提供現有的焦點控制項， `FocusMode` 如下所示：

- `AVCaptureFocusModeLocked` –焦點會在單一焦點點鎖定。
- `AVCaptureFocusModeAutoFocus` –相機會透過所有焦點來掃描鏡頭，直到找到明確的焦點，然後將它留在那裡。
- `AVCaptureFocusModeContinuousAutoFocus` –相機會在偵測到不專注的情況時 refocuses。

現有的控制項也會透過屬性提供可設定的重點 `FocusPointOfInterest` ，讓使用者可以點擊以專注在特定區域。 應用程式也可以藉由監視屬性來追蹤鏡頭移動 `IsAdjustingFocus` 。

此外，屬性提供的範圍限制 `AutoFocusRangeRestriction` 為：

- `AVCaptureAutoFocusRangeRestrictionNear` –將 autofocus 限制為附近的深度。 適用于掃描 QR 代碼或條碼等情況。
- `AVCaptureAutoFocusRangeRestrictionFar` –將 autofocus 限制在較遠的深度。 適用于已知不相關的物件位於 view (的欄位中，例如視窗框架) 。

最後，屬性會 `SmoothAutoFocus` 減緩自動焦點演算法，並以較小的增量逐步執行，以避免在錄製影片時移動成品。

### <a name="new-focus-controls-in-ios-8"></a>IOS 8 中的新焦點控制項

除了 iOS 7 和更新版本所提供的功能之外，現在還提供下列功能來控制 iOS 8 中的焦點：

- 鎖定焦點時，完全手動控制鏡頭的位置。
- 在任何焦點模式中，以索引鍵/值觀察到的鏡頭位置。

為了執行上述功能， `AVCaptureDevice` 類別已經過修改，以包含 `LensPosition` 用來取得相機鏡頭目前位置的唯讀屬性。

若要手動控制鏡頭位置，Capture 裝置必須處於鎖定焦點模式。 範例：

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

`SetFocusModeLocked`擷取裝置的方法是用來調整相機鏡頭的位置。 您可以提供選擇性的回呼常式，在變更生效時取得通知。 範例：

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

如上述程式碼所示，您必須先鎖定 Capture 裝置進行設定，才能進行變更的鏡頭位置。 有效的鏡頭位置值介於0.0 到1.0 之間。

### <a name="manual-focus-example"></a>手動焦點範例

設定好一般 AV Capture 安裝程式程式碼之後， `UIViewController` 就可以新增到應用程式的腳本，並設定如下：

[![您可以將 UIViewController 新增至應用程式分鏡腳本，並設定為手動焦點範例，如下所示。](intro-to-manual-camera-controls-images/image5.png)](intro-to-manual-camera-controls-images/image5.png#lightbox)

此視圖包含下列主要元素：

- `UIImageView`，會顯示影片摘要。
- `UISegmentedControl`將焦點模式從自動變更為鎖定的。
- `UISlider`，會顯示並更新目前的鏡頭位置。

請執行下列動作以連接視圖控制器以進行手動焦點控制：

1. 加入下列 using 陳述式：

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

1. 新增下列私用變數：

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```

1. 新增下列計算屬性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. 覆寫 `ViewDidLoad` 方法並加入下列程式碼：

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

1. 覆寫 `ViewDidAppear` 方法並加入下列內容，以在 view 載入時開始錄製：

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

1. 當相機處於自動模式時，滑杆會在攝影機調整焦點時自動移動：

    [![當相機在此範例應用程式中調整焦點時，滑杆會自動移動](intro-to-manual-camera-controls-images/image6.png)](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. 請點一下鎖定的區段，並拖曳位置滑杆以手動調整鏡頭位置：

    [![手動調整鏡頭位置](intro-to-manual-camera-controls-images/image7.png)](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. 停止應用程式。

上述程式碼顯示如何在相機處於自動模式時監視鏡頭位置，或使用滑杆來控制處於鎖定模式時的鏡頭位置。

## <a name="manual-exposure"></a>手動曝光

公開是指相對於來源亮度的影像亮度，並取決於感應器的光線點擊率、時間長度，以及感應器 (ISO 對應) 的取得程度。 藉由對曝光提供手動控制，應用程式可以為使用者提供更多的自由，讓他們能夠達成樣式的外觀。

使用者可以使用手動曝光控制項，將影像從誤以為的亮度帶到深色和 moody：

[![顯示從誤以為亮到深色和 moody 之曝光的影像範例](intro-to-manual-camera-controls-images/image8.png)](intro-to-manual-camera-controls-images/image8.png#lightbox)

同樣地，您也可以使用程式設計控制項來自動完成這項工作，或透過應用程式使用者介面所提供的手動控制項來完成這項操作。 無論採用哪一種方式，新的 iOS 8 公開 Api 都能針對相機的曝光設定提供更精細的控制。

### <a name="how-exposure-works"></a>公開運作方式

討論如何在 IOS 8 應用程式中控制曝光的詳細資料。 讓我們快速查看曝光的運作方式：

[![公開運作方式](intro-to-manual-camera-controls-images/image9.png)](intro-to-manual-camera-controls-images/image9.png#lightbox)

合併公開的三個基本元素如下：

- **快門速度** –這是打開快門的時間長度，讓相機感應器上的燈亮起。 開啟快門的時間越短，就越少越好，影像就越 (減少運動模糊) 。 快門的開啟時間越長，就越亮越好，而且會有更多的動作模糊。
- **ISO 對應** –這是從膠捲攝影中借用的詞彙，是指膠捲中化學化學的機密。 電影中的低 ISO 值具有較少的資料細微性和更細微的色彩複製;數位感應器的低 ISO 值的感應器噪音較少，但亮度較低。 ISO 值愈高，影像越亮，但有更多感應器噪音。 「數位感應器」上的「ISO」是 [電子增益](https://en.wikipedia.org/wiki/Gain)的量值，而非實體功能。
- **鏡頭光圈** –這是開啟的鏡頭大小。 所有 iOS 裝置上的鏡頭光圈都會固定，因此唯一可用來調整曝光的兩個值為快門速度和 ISO。

### <a name="how-continuous-auto-exposure-works"></a>連續自動曝光的運作方式

在瞭解手動曝光的運作方式之前，最好先瞭解如何在 iOS 裝置上進行連續自動曝光。

[![在 iOS 裝置中連續自動曝光的運作方式](intro-to-manual-camera-controls-images/image10.png)](intro-to-manual-camera-controls-images/image10.png#lightbox)

第一個是自動公開區塊，它有計算理想風險的工作，而且持續進行計量統計資料的測量。它會使用這項資訊來計算 ISO 和快門速度的最佳混合，讓場景看起來很亮。 這個週期稱為 AE 迴圈。

### <a name="how-locked-exposure-works"></a>鎖定曝光的運作方式

接下來，讓我們來看看如何在 iOS 裝置中使用鎖定的曝光。

[![鎖定曝光在 iOS 裝置中的運作方式](intro-to-manual-camera-controls-images/image11.png)](intro-to-manual-camera-controls-images/image11.png#lightbox)

同樣地，您會有嘗試計算最佳 iOS 和持續時間值的自動公開區塊。 不過，在此模式中，AE 區塊會與計量統計資料引擎中斷連接。

### <a name="existing-exposure-controls"></a>現有的公開控制項

iOS 7 及更新版本，透過屬性提供下列現有的公開控制項 `ExposureMode` ：

- `AVCaptureExposureModeLocked` –將場景取樣一次，並在整個場景中使用這些值。
- `AVCaptureExposureModeContinuousAutoExposure` –持續為場景取樣，以確保它的光線很亮。

`ExposurePointOfInterest`可以用來藉由選取要公開的目標物件來用來公開場景，應用程式可以監視 `AdjustingExposure` 屬性，以查看何時調整曝光。

### <a name="new-exposure-controls-in-ios-8"></a>IOS 8 中的新公開控制項

除了 iOS 7 和更新版本所提供的功能之外，下列功能現在也可在 iOS 8 中控制曝光：

- 完全手動自訂公開。
- 取得、設定和 Key-Value 觀察 IOS 和快門速度 (持續時間) 。

為了執行上述功能，已新增新的 `AVCaptureExposureModeCustom` 模式。 當中的攝影機是自訂模式時，可以使用下列程式碼來調整曝光持續時間和 ISO：

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

在自動和鎖定模式中，應用程式可以使用下列程式碼來調整自動曝光常式的偏差：

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

最小和最大設定範圍取決於應用程式執行所在的裝置，因此永遠不會進行硬式編碼。 相反地，請使用下列屬性來取得最小值和最大值範圍：

- `CaptureDevice.MinExposureTargetBias`
- `CaptureDevice.MaxExposureTargetBias`
- `CaptureDevice.ActiveFormat.MinISO`
- `CaptureDevice.ActiveFormat.MaxISO`
- `CaptureDevice.ActiveFormat.MinExposureDuration`
- `CaptureDevice.ActiveFormat.MaxExposureDuration`

如上述程式碼所示，您必須先鎖定 Capture 裝置進行設定，才能進行曝光變更。

### <a name="manual-exposure-example"></a>手動公開範例

設定好一般 AV Capture 安裝程式程式碼之後， `UIViewController` 就可以新增到應用程式的腳本，並設定如下：

[![您可以將 UIViewController 新增至應用程式分鏡腳本，並設定為手動公開範例。](intro-to-manual-camera-controls-images/image12.png)](intro-to-manual-camera-controls-images/image12.png#lightbox)

此視圖包含下列主要元素：

- `UIImageView`，會顯示影片摘要。
- `UISegmentedControl`將焦點模式從自動變更為鎖定的。
- 四個  `UISlider` 控制項，會顯示並更新位移、持續時間、ISO 和偏差。

請執行下列動作以連接視圖控制器以進行手動公開控制：

1. 加入下列 using 陳述式：

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

1. 新增下列私用變數：

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```

1. 新增下列計算屬性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. 覆寫 `ViewDidLoad` 方法並加入下列程式碼：

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

1. 覆寫 `ViewDidAppear` 方法並加入下列內容，以在 view 載入時開始錄製：

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

1. 當相機處於自動模式時，滑杆會在攝影機調整曝光時自動移動：

    [![當相機調整曝光時，滑杆會自動移動](intro-to-manual-camera-controls-images/image13.png)](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. 請點一下鎖定的區段，並拖曳偏差滑杆以手動調整自動曝光的偏差：

    [![手動調整自動曝光的偏差](intro-to-manual-camera-controls-images/image14.png)](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. 點擊自訂區段，並拖曳持續時間和 ISO 滑杆以手動控制曝光：

    [![拖曳持續時間和 ISO 滑杆以手動控制曝光](intro-to-manual-camera-controls-images/image15.png)](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. 停止應用程式。

上述程式碼顯示如何在相機處於自動模式時監視曝光設定，以及如何使用滑杆來控制處於鎖定或自訂模式時的曝光。

## <a name="manual-white-balance"></a>手動白色平衡

白色餘額控制可讓使用者調整影像中的 colosr 平衡，讓它們看起來更逼真。 不同的燈光來源具有不同的色溫度，而用來捕捉影像的相機設定則必須調整以彌補這些差異。 同樣地，藉由允許使用者控制白色餘額，就能讓自動常式無法達成美術效果的專業調整。

[![顯示手動白色餘額調整的範例影像](intro-to-manual-camera-controls-images/image16.png)](intro-to-manual-camera-controls-images/image16.png#lightbox)

比方說，日光有一個 blueish 轉型，而 tungsten incandescent 燈有一個暖、黃色橙色的色調。  (Confusingly，「非經常性」色彩的色彩溫度高於「暖」色彩。 色溫度是一種實體量值，而不是可感知的。 ) 

人們的想法很適合用來彌補顏色溫度的差異，但這是相機無法進行的動作。 攝影機的運作方式是在相反的頻譜上提高色彩，以針對色彩差異進行調整。

新的 iOS 8 公開 API 可讓應用程式控制流程，並提供對相機的白色餘額設定的精細控制。

### <a name="how-white-balance-works"></a>白色餘額的運作方式

在討論 IOS 8 應用程式中控制白色餘額的詳細資料之前。 讓我們快速查看白色餘額的運作方式：

在色彩感知研究中， [CIE 1931 RGB 色彩空間和 CIE 1931 XYZ 色彩空間](https://en.wikipedia.org/wiki/CIE_1931_color_space) 是第一個以數學方式定義的色彩空間。 它們是由國際委員會在1931中 (CIE) 所建立。

[![CIE 1931 RGB 色彩空間和 CIE 1931 XYZ 色彩空間](intro-to-manual-camera-controls-images/image17.png)](intro-to-manual-camera-controls-images/image17.png#lightbox)

上圖顯示人類眼看到的所有色彩，從深藍色到鮮綠，到淺紅色。 圖表上的任何點都可以使用 X 和 Y 值繪製，如上圖所示。

如圖中所示，有 X 和 Y 值可在圖形上繪製，而不在人類視覺範圍內，因此相機無法重現這些色彩。

上圖中較小的曲線稱為「 [Planckian Locus](https://en.wikipedia.org/wiki/Planckian_locus)，表示溫度 (（以度數為) 單位），在藍色側邊的數位較高 (高) 和紅色邊的較低數位 (熱) 。 這些適用于一般照明情況。

在混合光源的情況下，白色餘額的調整將需要偏離 Planckian Locus，以進行必要的變更。 在這些情況下，必須將調整移至 CIE 尺規的綠色或紅色/洋紅邊。

iOS 裝置會藉由提高相反的色彩增益來補償色彩轉換。 比方說，如果場景具有太多藍色，則會提升紅色增益以彌補。 這些增益值會針對特定裝置校正，因此裝置會相依。

### <a name="existing-white-balance-controls"></a>現有的白色餘額控制項

iOS 7 和更新版本透過屬性提供下列現有的白色餘額控制 `WhiteBalanceMode` ：

- `AVCapture WhiteBalance ModeLocked` –將場景取樣一次，並在整個場景中使用這些值。
- `AVCapture WhiteBalance ModeContinuousAutoExposure` –持續為場景取樣，以確保它的平衡程度。

而且應用程式可以監視 `AdjustingWhiteBalance` 屬性，以查看何時調整曝光。

### <a name="new-white-balance-controls-in-ios-8"></a>IOS 8 中的新白平衡控制項

除了 iOS 7 和更新版本所提供的功能之外，現在還提供下列功能來控制 iOS 8 中的白平衡：

- 完全手動控制裝置 RGB 增益。
- 取得、設定和 Key-Value 觀察裝置 RGB 增益。
- 支援使用灰色卡片的白色平衡。
- 與裝置無關的色彩空間之間的轉換常式。

若要執行上述功能，已 `AVCaptureWhiteBalanceGain` 使用下列成員新增結構：

- `RedGain`
- `GreenGain`
- `BlueGain`

最大的白色餘額增益目前為四 (4) ，而且可以從屬性進行準備 `MaxWhiteBalanceGain` 。 因此，法律範圍是從一個 (1) 到 `MaxWhiteBalanceGain` (4) 目前。

`DeviceWhiteBalanceGains`屬性可以用來觀察目前的值。 `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`當相機處於鎖定的白色餘額模式時，用來調整餘額增益。

#### <a name="conversion-routines"></a>轉換常式

轉換常式已新增至 iOS 8，以協助轉換成裝置與裝置無關的色彩空間。 若要執行轉換常式，已 `AVCaptureWhiteBalanceChromaticityValues` 新增具有下列成員的結構：

- `X` -是介於0到1之間的值。
- `Y` -是介於0到1之間的值。

`AVCaptureWhiteBalanceTemperatureAndTintValues`此外，也已新增下列成員的結構：

- `Temperature` -這是以度為單位的浮點值。
- `Tint` -這是從0到150的綠色或洋紅的位移，且正值朝綠色方向，負值朝洋紅。

使用 `CaptureDevice.GetTemperatureAndTintValues` 和 `CaptureDevice.GetDeviceWhiteBalanceGains` 方法來轉換溫度和色調、CHROMATICITY 和 RGB 增益色空間。

> [!NOTE]
> 轉換常式越精確，要轉換的值越接近 Planckian Locus。

#### <a name="gray-card-support"></a>灰色卡支援

Apple 使用灰色的世界來表示 iOS 8 內建的灰色卡支援。 它可讓使用者將焦點放在涵蓋框架中心內至少50% 的實體灰色卡片上，並使用該卡片來調整白色餘額。 灰色卡片的目的是要達到白色，看起來是中立的。

您可以在應用程式中執行這項工作，方法是提示使用者將實體灰色卡片放在相機前方、監視 `GrayWorldDeviceWhiteBalanceGains` 屬性，並等候值向下調整。

然後，應用程式會 `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` 使用屬性中的值來套用變更，以鎖定方法的白色餘額增益 `GrayWorldDeviceWhiteBalanceGains` 。

必須先鎖定 Capture 裝置進行設定，才能進行白色餘額變更。

### <a name="manual-white-balance-example"></a>手動白色餘額範例

設定好一般 AV Capture 安裝程式程式碼之後， `UIViewController` 就可以新增到應用程式的腳本，並設定如下：

[![您可以將 UIViewController 新增至應用程式分鏡腳本，並將其設定為以手動的白色餘額範例顯示。](intro-to-manual-camera-controls-images/image18.png)](intro-to-manual-camera-controls-images/image18.png#lightbox)

此視圖包含下列主要元素：

- `UIImageView`，會顯示影片摘要。
- `UISegmentedControl`將焦點模式從自動變更為鎖定的。
- 有兩個  `UISlider` 控制項會顯示並更新溫度和色調。
- `UIButton`用來取樣灰色的 (灰色世界) 空間，並使用這些值設定白色餘額。

執行下列動作，以連接視圖控制器以進行手動的白色平衡控制：

1. 加入下列 using 陳述式：

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

1. 新增下列私用變數：

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```

1. 新增下列計算屬性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. 新增下列私用方法，以設定新的白色平衡溫度和色調：

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

1. 覆寫 `ViewDidLoad` 方法並加入下列程式碼：

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

1. 覆寫 `ViewDidAppear` 方法並加入下列內容，以在 view 載入時開始錄製：

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

1. 將變更儲存至程式碼，然後執行應用程式。
1. 當相機處於自動模式時，滑杆會在攝影機調整白平衡時自動移動：

    [![當攝影機調整白色餘額時，滑杆會自動移動](intro-to-manual-camera-controls-images/image19.png)](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. 請點一下鎖定的區段，並拖曳 Temp 和淡色滑杆以手動調整白色餘額：

    [![拖曳 Temp 和淡色滑杆以手動調整白色餘額](intro-to-manual-camera-controls-images/image20.png)](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. 在仍選取鎖定區段的情況下，請將實體灰色卡放在相機前方，然後按下灰色卡片按鈕，將白色的平衡調整為灰色：

    [![點一下 [灰色卡片] 按鈕，將白色的平衡調整為灰色](intro-to-manual-camera-controls-images/image21.png)](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. 停止應用程式。

上述程式碼顯示如何在相機處於自動模式時監視白色餘額設定，或使用滑杆來控制處於鎖定模式時的白色餘額。

## <a name="bracketed-capture"></a>有括弧的捕獲

括弧內的捕獲是以上述手動相機控制項的設定為基礎，可讓應用程式以各種不同的方式來捕捉時間點。

單純地說，有括弧的捕捉是一種高載，也就是以圖片和圖片的各種設定拍攝的影像。

[![有括弧的 Capture 如何運作](intro-to-manual-camera-controls-images/image22.png)](intro-to-manual-camera-controls-images/image22.png#lightbox)

在 iOS 8 中使用以括弧括住的捕獲，應用程式可以預設為一系列的手動攝影機控制項，發出單一命令，並讓目前的場景針對每個手動預設值傳回一系列的影像。

### <a name="bracketed-capture-basics"></a>有括弧的捕獲基本概念

同樣地，以括弧括住的捕獲是指使用不同設定從圖片移至圖片的靜止影像。 可用的內括弧捕捉類型如下：

- **自動曝光括弧** –所有影像都有不同的偏差數量。
- **手動公開括弧** -其中所有映射都有不同的快門速度 (持續時間) 和 ISO 數量。
- **簡單** 的高載括弧–一系列的靜止影像，快速地連續執行。

### <a name="new-bracketed-capture-controls-in-ios-8"></a>IOS 8 中新的有括弧的 Capture 控制項

所有以括弧括住的 Capture 命令都會在類別中執行 `AVCaptureStillImageOutput` 。 您 `CaptureStillImageBracket` 可以使用方法，取得具有指定之設定陣列的一系列影像。

已實行兩個新的類別來處理設定：

- `AVCaptureAutoExposureBracketedStillImageSettings` –它有一個屬性，  `ExposureTargetBias` 可用來設定自動曝光括弧的偏差。
- `AVCaptureManual`  `ExposureBracketedStillImageSettings` –它有兩個屬性，  `ExposureDuration` 以及  `ISO` 用來設定手動曝光括弧的快門速度和 ISO。

### <a name="bracketed-capture-controls-dos-and-donts"></a>以括弧括住的 Capture 控制項

#### <a name="dos"></a>可行事項

以下是在 iOS 8 中使用以括弧括住的控制項時應完成的事項清單：

- 藉由呼叫方法來準備應用程式，以達到最糟的案例捕捉狀況  `PrepareToCaptureStillImageBracket` 。
- 假設範例緩衝區即將來自相同的共用集區。
- 若要釋放先前的「準備」呼叫所配置的記憶體，請再次呼叫，  `PrepareToCaptureStillImageBracket` 然後將一個物件的陣列傳送給它。

#### <a name="donts"></a>禁止事項

以下是在 iOS 8 中使用以括弧括住的控制項時，不應完成的事項清單：

- 請勿在單一捕獲中混合使用括弧的捕獲設定類型。
- 請勿  `MaxBracketedCaptureStillImageCount` 在單一捕獲中要求超過影像。

### <a name="bracketed-capture-details"></a>有括弧的捕獲詳細資料

在 iOS 8 中使用以括弧括住的捕獲時，應考慮下列詳細資料：

- 以括弧括住的設定會暫時覆寫  `AVCaptureDevice` 設定。
- Flash 和靜止影像穩定設定會被忽略。
- 所有影像都必須使用相同的輸出格式 (jpeg、png 等 ) 
- 影片預覽可能會捨棄框架。
- 所有與 iOS 8 相容的裝置都支援以括弧括住的捕獲。

記住這項資訊後，讓我們看看在 iOS 8 中使用以括弧括住的捕獲範例。

### <a name="bracket-capture-example"></a>括弧捕獲範例

設定好一般 AV Capture 安裝程式程式碼之後， `UIViewController` 就可以新增到應用程式的腳本，並設定如下：

[![您可以將 UIViewController 新增至應用程式分鏡腳本，並設定為如以下的括弧捕獲範例所示。](intro-to-manual-camera-controls-images/image23.png)](intro-to-manual-camera-controls-images/image23.png#lightbox)

此視圖包含下列主要元素：

- `UIImageView`，會顯示影片摘要。
- 三個  `UIImageViews` 會顯示 capture 的結果。
- 用  `UIScrollView` 來存放影片摘要和結果檢視的。
- ，  `UIButton` 用來取得具有部分預設設定的有括弧的捕捉。

請執行下列動作來連接視圖控制器以進行有括弧的捕獲：

1. 加入下列 using 陳述式：

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

1. 新增下列私用變數：

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```

1. 新增下列計算屬性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```

1. 新增下列私用方法，以建立所需的輸出影像視圖：

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

1. 覆寫 `ViewDidLoad` 方法並加入下列程式碼：

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

1. 覆寫 `ViewDidAppear` 方法並加入下列程式碼：

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

1. 將變更儲存至程式碼，然後執行應用程式。
1. 建立場景的框架，並點擊 [捕捉括弧] 按鈕：

    [![建立場景的框架，並點擊 [捕捉括弧] 按鈕](intro-to-manual-camera-controls-images/image24.png)](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. 從右至左滑動以查看有括弧的捕獲所採用的三個影像：

    [![向左滑動以查看有括弧的捕獲所採用的三個影像](intro-to-manual-camera-controls-images/image25.png)](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. 停止應用程式。

上述程式碼示範了如何在 iOS 8 中設定和採用自動曝光的已括住捕捉。

## <a name="summary"></a>摘要

在本文中，我們已介紹過 iOS 8 所提供的新手動攝影機控制項，並涵蓋其功能及其運作方式的基本概念。 我們已提供手動焦點、手動曝光和手動白色平衡的範例。 最後，我們提供了一個範例，讓我們使用先前討論的手動攝影機控制項來進行有括弧的捕獲

## <a name="related-links"></a>相關連結

- [ManualCameraControls (範例) ](/samples/xamarin/ios-samples/manualcameracontrols)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)