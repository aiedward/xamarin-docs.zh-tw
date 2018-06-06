---
title: Xamarin.iOS 手動攝影機控制項
description: 本文件說明如何 iOS AVFoundation framework 可以配合 Xamarin.iOS 若要啟用手動攝影機控制項。 手動攝影機控制項可讓使用者控制焦點、 白平衡，並公開設定。
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: a0f605a38117df87a03801c3b9d86b0b7361c232
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790821"
---
# <a name="manual-camera-controls-in-xamarinios"></a>Xamarin.iOS 手動攝影機控制項

提供的手冊攝影機控制項`AVFoundation Framework`中 iOS 8，允許行動裝置的應用程式，以便對於 iOS 裝置的相機的完整控制權。 這個更細緻的控制層級可以用於建立專業的層級相機應用程式，並提供演出者組合所佔用的靜態影像或視訊調整相機的參數。

開發科學或工業應用程式，結果會較不會導向到的映像，好處的正確性，而且會反白顯示的某些功能或所採取的映像的項目更能時，這些控制項可以也很有用。

## <a name="avfoundation-capture-objects"></a>AVFoundation 擷取物件

使視訊或仍然在 iOS 裝置上使用相機的映像，用來擷取這些映像的程序大致相同。 這是使用預設的自動攝影機控制項或以善用新的手動攝影機控制項的應用程式，則為 true:

 [![](intro-to-manual-camera-controls-images/image1.png "AVFoundation 擷取物件概觀")](intro-to-manual-camera-controls-images/image1.png#lightbox)

輸入來自`AVCaptureDeviceInput`到`AVCaptureSession`透過`AVCaptureConnection`。 以靜態影像或視訊資料流的形式，則結果為任一個輸出。 整個程序由`AVCaptureDevice`。

## <a name="manual-controls-provided"></a>手動提供的控制項

使用 iOS 8 所提供的新 Api，應用程式可以採取下列相機功能的控制項：

-  **手動焦點**– 藉由允許使用者直接取得焦點的控制權，應用程式可以提供更充分掌控採取的映像。
-  **手動曝光**– 應用程式可以藉由提供手動控制曝光，提供更多的自由，給使用者，以及允許達到樣式化的外觀。
-  **手動的白平衡**– 白平衡來調整影像的色彩，通常，讓它看來逼真。 不同的光源有不同的色彩溫度和相機設定用來擷取影像會調整為補償這些差異。 同樣地，允許使用者對白平衡的控制，使用者可以進行調整，無法自動完成。


iOS 8 提供擴充功能和增強功能至現有的 iOS 應用程式開發介面來提供此精細控制映像擷取程序。

## <a name="bracketed-capture"></a>以方括弧括住的擷取

括號起來擷取從上述手動攝影機控制項設定為基礎，並允許以各種不同的方式擷取目前的時段中，應用程式。

簡言之，括號起來擷取是一批突發之拍攝的圖片從圖片設定各種不同的靜態影像。

## <a name="requirements"></a>需求

下列完成所需呈現在本文中的步驟：

-  **Xcode 7 + 和 iOS 8 或更新版本**– Apple Xcode 7 和 iOS 8 或更新版本的應用程式開發介面需要安裝和設定開發人員的電腦上。
-  **Visual Studio for Mac** – 應安裝及設定使用者裝置上最新版本的 Visual Studio for Mac。
-  **iOS 8 裝置**– 執行 iOS 8 的最新版本的 iOS 裝置。 無法在 iOS 模擬器中測試相機功能。


## <a name="general-av-capture-setup"></a>一般 AV 擷取安裝程式

時錄製視訊的 iOS 裝置上會有一些一般安裝程式的程式碼，永遠是必要項。 本章節將說明如何將 iOS 裝置的相機中的視訊錄製，並顯示在該視訊所需的最小安裝中即時`UIImageView`。

### <a name="output-sample-buffer-delegate"></a>輸出範例緩衝區委派

其中一個首要之務需要將會監視範例輸出緩衝區，並顯示映像從緩衝區來捕捉委派`UIImageView`應用程式 UI 中。

下列常式會監視範例緩衝區，並更新 UI:

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

使用中的位置，此常式`AppDelegate`開啟錄製即時視訊摘要的 AV 擷取工作階段可以修改。

### <a name="creating-an-av-capture-session"></a>AV 擷取工作階段的建立

AV 擷取工作階段用來控制 iOS 裝置的相機中的即時視訊錄製，才可取得的 iOS 應用程式的視訊。 因為此範例`ManualCameraControl`範例應用程式在幾個不同的地方使用擷取工作階段，它會設定為`AppDelegate`，而且可在整個應用程式。

執行下列工作來修改應用程式的`AppDelegate`並新增必要的程式碼：


1. 按兩下`AppDelegate.cs`開啟進行編輯 [方案總管] 中的檔案。
1. 加入下列 using 陳述式加入檔案頂端：
    
    ```
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

1. 加入下列私用變數和計算的屬性，以`AppDelegate`類別：
    
    ```
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
  
1. 覆寫已完成的方法，並將它變更為：
    
    ```
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


就地這個程式碼，手動攝影機控制項可以輕鬆地實作進行的試驗與測試。

## <a name="manual-focus"></a>手動焦點

允許使用者直接取得焦點的控制項，應用程式可以提供更藝術控制採取的映像。

例如，專業攝影師可以來達成之影像的焦點[Bokeh 效果](http://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "Bokeh 效果")](intro-to-manual-camera-controls-images/image2.png#lightbox)

或者，建立[焦點提取效果](http://www.mediacollege.com/video/camera/focus/pull.html)，例如：

[![](intro-to-manual-camera-controls-images/image3.png "焦點提取效果")](intro-to-manual-camera-controls-images/image3.png#lightbox)

對於科學家或醫療應用程式的寫入器，應用程式可能想要以程式設計方式移動功能濾鏡的實驗中。 不管哪種方式新增應用程式開發介面可讓使用者或應用程式以便控制焦點時映像不會採取。

### <a name="how-focus-works"></a>焦點的運作方式

之前討論的控制焦點位於 IOS 8 應用程式詳細資料。 讓我們快速查看焦點的 iOS 裝置中的運作方式：

[![](intro-to-manual-camera-controls-images/image4.png "焦點在 iOS 裝置中的運作方式")](intro-to-manual-camera-controls-images/image4.png#lightbox)

淺色進入 iOS 裝置上的相機功能濾鏡與著重於映像感應器。 距離感應器控制項從濾鏡連絡窗口 （映像將會出現清晰區域），所在的感應器的關聯性。 較遠的方式是從感應器、 距離物件似乎清晰，並且愈接近物件附近似乎清晰。

在 iOS 裝置，透鏡會移近、 或之間的距離越遠，感應器磁鐵和 springs。 如此一來，透鏡的精確位置是不可能，而異的裝置，並可能會受到參數，例如裝置方向或裝置與 spring 的年齡。

### <a name="important-focus-terms"></a>重要焦點條款

當處理具有焦點時，有開發人員應該很熟悉的一些術語：

-  **景深**– 接近和最遠焦點中物件之間的距離。 
-  **巨集**-這是焦點頻譜接近到期，而且透鏡可以專注在其最接近的距離。
-  **Infinity** – 這是另一端的焦點廣泛，而且透鏡可以專注在其最遠距離。
-  **Hyperfocal 距離**– 這是一個焦點範圍中的點只在另一端的焦點是最遠的框架中物件之處。 換句話說，這是達到最大深度的欄位的主要位置。 
-  **透鏡位置**–，會控制上述所有其他條款。 這是感應器從因而透鏡的距離的焦點控制站。


這些條款及知識，請注意，新的手動焦點控制項可以成功實作 iOS 8 應用程式中。

### <a name="existing-focus-controls"></a>焦點的現有控制項

iOS 7 和舊版中，提供現有焦點的控制項，透過`FocusMode`屬性做為：

-   `AVCaptureFocusModeLocked` – 焦點會在單一焦點鎖定。
-   `AVCaptureFocusModeAutoFocus` – 相機掃掠透過所有焦點的點功能濾鏡，直到找到尖焦點並則停留那里。
-   `AVCaptureFocusModeContinuousAutoFocus` – 相機 refocuses 當偵測到失焦條件。


現有的控制項也提供可設定的透過點`FocusPointOfInterest`屬性，以便使用者可以點選以專注於特定區域。 應用程式也可以藉由監視追蹤功能濾鏡移動`IsAdjustingFocus`屬性。

此外，範圍限制由提供`AutoFocusRangeRestriction`屬性做為：

-   `AVCaptureAutoFocusRangeRestrictionNear` – 限制 autofocus 附近的深度。 例如掃描 QR 代碼或條碼的情況下很有用。
-   `AVCaptureAutoFocusRangeRestrictionFar` – 限制 autofocus 遠方的深度。 在已知為無關的物件欄位檢視 （例如，視窗框架） 中的情況下很有用。


最後還有`SmoothAutoFocus`自動焦點演算法變慢，且步驟以較小的增量，以避免移動成品時錄製視訊內容。

### <a name="new-focus-controls-in-ios-8"></a>IOS 8 中的新焦點控制項

除了已經提供 ios 7 和更新版本的功能，下列功能，現在也可用來控制焦點位於 iOS 8:

-  完整手動透鏡位置時鎖定焦點的控制項。
-  在任何焦點模式中的功能濾鏡位置的索引鍵-值觀察。


若要實作上述功能，`AVCaptureDevice`類別已修改成包含唯讀`LensPosition`屬性用來取得目前位置的相機功能濾鏡。

若要手動控制此功能濾鏡位置，擷取的裝置必須在鎖定焦點模式中。 範例：

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

`SetFocusModeLocked`擷取裝置的方法用來調整 [相機] 濾鏡的位置。 選擇性的回呼常式可以提供給收到通知時，變更才會生效。 範例：

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

上述程式碼所示，擷取的裝置必須鎖定以便設定後才能進行透鏡位置中的變更。 功能濾鏡位置有效值介於 0.0 到 1.0 之間。

### <a name="manual-focus-example"></a>手動焦點範例

一般 AV 擷取安裝程式中的程式碼的地方，`UIViewController`可以加入至應用程式的分鏡腳本，並設定，如下所示：

[![](intro-to-manual-camera-controls-images/image5.png "UIViewController 可以新增到應用程式的分鏡腳本，並設定如下所示")](intro-to-manual-camera-controls-images/image5.png#lightbox)

此檢視包含下列主要項目：

-  A`UIImageView`將顯示視訊的摘要。
-  A`UISegmentedControl`此舉會從自動變更焦點模式，為 已鎖定。
-  A `UISlider` ，將會顯示並更新目前的功能濾鏡位置。


執行下列命令以連線檢視控制器的總手動焦點控制：


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
  
1. 加入下列私用變數：

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```  
  
1. 加入下列計算的屬性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. 覆寫`ViewDidLoad`方法並加入下列程式碼：

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
  
1. 覆寫`ViewDidAppear`方法並加入下列命令以開始錄製 載入檢視時：

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
  
1. 在 Auto 模式中相機，滑桿會移動自動相機焦點調整：

    [![](intro-to-manual-camera-controls-images/image6.png "滑桿會移動會自動在此範例應用程式的焦點的相機調整")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. 點選鎖定區段，然後拖曳位置滑桿，以手動方式調整功能濾鏡位置：

    [![](intro-to-manual-camera-controls-images/image7.png "手動調整功能濾鏡位置")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. 停止應用程式。


上述程式碼顯示如何以自動模式會相機時，監視功能濾鏡位置，或使用滑動軸來控制功能濾鏡位置，以鎖定模式時。

## <a name="manual-exposure"></a>手動曝光

曝光指的是相對於來源亮度，影像的亮度及決定由多少光線所叫用方式的感應器，長時間，而且感應器 （ISO 對應） 的改善層級。 藉由提供手動控制曝光，應用程式可以提供更多的自由，給終端使用者，以及允許達到樣式化的外觀。

使用手動曝光控制項，使用者可以採取深色和 moody 洩密亮從映像：

[![](intro-to-manual-camera-controls-images/image8.png "範例顯示曝露洩密亮深色及 moody 映像")](intro-to-manual-camera-controls-images/image8.png#lightbox)

同樣地，作法是使用自動程式控制科學應用程式或透過手動控制應用程式使用者介面所提供。 無論如何，新的 iOS 8 的曝光 Api 提供相機的曝光設定更細微的控制。

### <a name="how-exposure-works"></a>曝光的運作方式

之前討論的控制 IOS 8 應用程式中的公開詳細資料。 讓我們快速查看曝光的運作方式：

[![](intro-to-manual-camera-controls-images/image9.png "曝光的運作方式")](intro-to-manual-camera-controls-images/image9.png#lightbox)

聚集在一起，來控制風險的三個基本項目包括：

-  **快門速度**– 這是快門處於開啟狀態，以便讓 light 到相機感應器的時間長度。 較短者快門是開啟的較輕的時間可讓和較清晰的影像是模糊 （較少影片程度）。 越長快門已開啟、 的更多的燈號可讓在和多影片，就會發生的柔邊。
-  **ISO 對應**– 這是取自底片攝影的詞彙，是指在淺色底片化學藥品的敏感度。 在影片中的低 ISO 值有較少的資料粒度和更精細的色彩重現;數位感應器上的低 ISO 值有較少但較少的亮度的感應器雜訊。 ISO 值越高，亮映像但具有更多的感應器雜訊。 數位感應器上的 「 ISO 」 是指量[電子改善](http://en.wikipedia.org/wiki/Gain)，非實體的功能。 
-  **透鏡光圈**– 這是功能濾鏡開頭的大小。 所有 iOS 裝置上被固定透鏡光圈，因此可用於調整曝光只有兩個值的快門和 ISO。


### <a name="how-continuous-auto-exposure-works"></a>如何連續自動曝光運作

之前學習如何手動曝光運作，有更佳了解如何連續自動曝光的概念適用於 iOS 裝置。

[![](intro-to-manual-camera-controls-images/image10.png "連續自動曝光的 iOS 裝置中的運作方式")](intro-to-manual-camera-controls-images/image10.png#lightbox)

第一個是自動曝光區塊中，它有計算理想風險的工作，並會持續饋送計量統計資料。它會使用這項資訊來計算最佳的 ISO 和混合的快門取得場景也亮起。 此週期被指 AE 迴圈。

### <a name="how-locked-exposure-works"></a>如何鎖定的曝光運作

接下來，讓我們來討論如何鎖定的曝光適用於 iOS 裝置。

[![](intro-to-manual-camera-controls-images/image11.png "如何鎖定曝光適用於 iOS 裝置")](intro-to-manual-camera-controls-images/image11.png#lightbox)

同樣地，您必須嘗試計算最佳的 iOS 和工期值自動曝光區塊。 不過，在此模式中 AE 區塊已中斷連線計量統計資料引擎。

### <a name="existing-exposure-controls"></a>現有的曝光控制項

iOS 7 和更新版本，提供下列現有曝光控制項，透過`ExposureMode`屬性：

-   `AVCaptureExposureModeLocked` – 一次取樣場景，並使用整個場景中的這些值。
-   `AVCaptureExposureModeContinuousAutoExposure` – 取樣持續以確保妥善亮起場景。


`ExposurePointOfInterest`可用來選取目標物件來公開，公開場景點選，而且應用程式可以監視`AdjustingExposure`內容以瞭解當調整曝光。

### <a name="new-exposure-controls-in-ios-8"></a>IOS 8 中的新風險控制項

除了已經提供 ios 7 和更新版本的功能，下列功能，現在也可用來控制在 iOS 8 的風險：

-  完全手動的自訂風險。
-  取得、 設定和索引鍵-值觀察 IOS 和快門 （持續時間）。


若要實作上述功能，新`AVCaptureExposureModeCustom`模式已新增。 自訂模式中的攝影機時，下列程式碼可用來調整曝光持續時間和 ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

在 Auto 和鎖定模式下，應用程式可以調整使用下列程式碼的自動曝光常式偏差：

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

設定最小和最大範圍而定，執行應用程式的裝置，它們應該永遠不會被硬式編碼。 相反地，若要取得最小和最大值的範圍是使用下列屬性：

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


上述程式碼所示，擷取裝置之前必須先鎖定組態才能進行變更，以在風險中。

### <a name="manual-exposure-example"></a>手動曝光範例

一般 AV 擷取安裝程式中的程式碼的地方，`UIViewController`可以加入至應用程式的分鏡腳本，並設定，如下所示：

[![](intro-to-manual-camera-controls-images/image12.png "UIViewController 可以新增到應用程式的分鏡腳本，並設定如下所示")](intro-to-manual-camera-controls-images/image12.png#lightbox)

此檢視包含下列主要項目：

-  A`UIImageView`將顯示視訊的摘要。
-  A`UISegmentedControl`此舉會從自動變更焦點模式，為 已鎖定。
-  四個`UISlider`控制項，將會顯示並更新位移、 持續時間、 ISO 和偏差。


執行下列命令以連線檢視控制器的總手動曝光控制：


1. 加入下列 using 陳述式：
    
    ```
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
  
1. 加入下列私用變數：

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```  
  
1. 加入下列計算的屬性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. 覆寫`ViewDidLoad`方法並加入下列程式碼：

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
  
1. 覆寫`ViewDidAppear`方法並加入下列命令以開始錄製 載入檢視時：

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
  
1. 在 Auto 模式中相機，滑桿會移動自動相機調整曝光：

    [![](intro-to-manual-camera-controls-images/image13.png "滑桿會移動會自動將相機調整曝光")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. 點選鎖定區段，並拖曳偏差滑桿，以手動方式調整自動曝光的偏差：

    [![](intro-to-manual-camera-controls-images/image14.png "手動調整自動曝光的偏差")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. 點選自訂區段，並拖曳 持續時間和 ISO 滑桿，來手動控制風險：

    [![](intro-to-manual-camera-controls-images/image15.png "將持續時間和 ISO 滑桿拖曳到手動控制風險")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. 停止應用程式。


上述程式碼示範了如何在自動模式中，會相機時，監視曝光設定，以及如何使用滑桿來控制風險處於已鎖定或自訂模式時。

## <a name="manual-white-balance"></a>手動白平衡

白平衡控制項可讓使用者調整 colosr 在映像，使其看起來更逼真的平衡。 不同的光源有不同的色彩溫度，而且用來擷取映像的相機設定必須加以調整來彌補這些差異。 同樣地，藉由允許使用者控制，透過白平衡進行專業的調整，以達到藝術自動常式所進行。

[![](intro-to-manual-camera-controls-images/image16.png "範例影像顯示手動白平衡調整")](intro-to-manual-camera-controls-images/image16.png#lightbox)

比方說，日光節約都有 blueish 轉型，而 tungsten incandescent 燈號暖和、 黃色橙色濃淡。 （困惑，「 冷 」 的色彩的較高的色彩溫度比 「 暖 」 的色彩。 色彩溫度是一個實體量值不會感知的其中一個）。

人力事項是非常好用在補償色溫的差異，但這是無法使用相機的項目。 相機的運作方式是提升，以便調整色彩差異相反的範圍上的色彩。

新的 iOS 8 曝光 API 可讓應用程式取得其控制權的程序，並提供更細微的控制相機的白平衡設定。

### <a name="how-white-balance-works"></a>如何白平衡運作

之前討論的控制 IOS 8 應用程式中的白平衡詳細資料。 讓我們快速查看如何白平衡運作：

中的色彩感覺研究[CIE 1931 RGB 色彩空間和 CIE 1931 XYZ 色彩空間](http://en.wikipedia.org/wiki/CIE_1931_color_space)是第一個數學上定義的色彩空間。 它們是由國際佣金上照明 (CIE) 建立 1931年中。

[![](intro-to-manual-camera-controls-images/image17.png "CIE 1931 RGB 色彩空間和 CIE 1931 XYZ 色彩空間")](intro-to-manual-camera-controls-images/image17.png#lightbox)

上述圖表會顯示我們色彩的所有可見人類的眼睛，從深藍色，淺綠亮紅色。 在圖表上的任何時間點可以一起繪製 X 和 Y 值上, 圖所示。

顯示圖形中，有可以超出範圍的人力目標，就是在圖形繪製的 X 和 Y 值並且由相機因此無法重現這些色彩。

會呼叫上述圖表中較小的曲線[Planckian Locus](http://en.wikipedia.org/wiki/Planckian_locus)，這表示 （以度為單位開氏） 具有較高的數字 （更高） 藍色一邊色溫和較低編號上的紅色端 （冷卻器）。 這些是典型的光源相當有用。

在混合的光源條件白平衡調整必須偏離 Planckian Locus，來進行必要的變更。 在這些情況下，將需要調整来移至綠色或紅色/洋紅 CIE 端調整。

iOS 裝置所提升的相反的色彩改善補償色彩轉換 （cast）。 比方說，如果場景中有太多藍色，然後紅色會提高補償。 這些改善值制定特定裝置，因此它們會視裝置而定。

### <a name="existing-white-balance-controls"></a>現有的白平衡控制項

iOS 7 及更新版本所提供的下列現有的白平衡控制項透過`WhiteBalanceMode`屬性：

-   `AVCapture WhiteBalance ModeLocked` – 取樣一次的場景，以及使用整個場景中的這些值。
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` – 取樣場景持續以確保良好平衡。


應用程式可以監視`AdjustingWhiteBalance`內容以瞭解當調整曝光。

### <a name="new-white-balance-controls-in-ios-8"></a>IOS 8 中新的白平衡控制項

除了已經提供 ios 7 和更新版本的功能，下列功能，現在也可在 iOS 8 的白平衡控制項：

-  取得裝置 RGB 的完全手動的控制項。
-  取得、 設定和索引鍵-值觀察裝置 RGB 取得。
-  白平衡使用灰色卡支援。
-  轉換常式與裝置無關的色彩空間。


若要實作上述功能，`AVCaptureWhiteBalanceGain`結構已加入具有下列成員：

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


最大值的白平衡改善目前是四 （4)，而且可以是就緒`MaxWhiteBalanceGain`屬性。 因此合法的範圍是從一 (1) 到`MaxWhiteBalanceGain`(4) 目前。

`DeviceWhiteBalanceGains`屬性可以用來觀察的目前值。 使用`SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`調整餘額提升會相機時鎖定的白平衡模式中。

#### <a name="conversion-routines"></a>轉換常式

轉換常式已新增至可協助的轉換，iOS 8 裝置獨立的色彩空間。 若要實作轉換常式`AVCaptureWhiteBalanceChromaticityValues`結構已加入具有下列成員：

-   `X` -是從 0 到 1 的值。
-   `Y` -是從 0 到 1 的值。


`AVCaptureWhiteBalanceTemperatureAndTintValues`結構也已加入具有下列成員：

-   `Temperature` -是浮點以度為單位開氏點值。
-   `Tint` -從綠色或洋紅色從 0 到 150 與綠色方向朝向正值和負值向洋紅是位移。


使用`CaptureDevice.GetTemperatureAndTintValues`和`CaptureDevice.GetDeviceWhiteBalanceGains`溫度和濃淡、 色度和 RGB 之間進行轉換的方法取得色彩空間。

> [!NOTE]
> 要轉換的值越接近 Planckian Locus 轉換常式是更精確。




#### <a name="gray-card-support"></a>灰色卡支援

Apple iOS 8 內建的灰色卡支援，請參閱用於詞彙灰色世界。 它可讓使用者將焦點放在至少 50%的中央的框架，並使用該值來調整白平衡實體的灰色卡上。 灰色卡片的目的是達成出現中性的白色。

這可以提示使用者插入實體灰色卡前方相機上，實作應用程式中監視`GrayWorldDeviceWhiteBalanceGains`屬性，並等待，直到值都穩定下來。

應用程式接著會鎖定白平衡增進`SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`方法使用來自值`GrayWorldDeviceWhiteBalanceGains`屬性，將套用所做的變更。

必須先設定鎖定擷取裝置，才能進行變更，以在白平衡。

### <a name="manual-white-balance-example"></a>手動白平衡範例

一般 AV 擷取安裝程式中的程式碼的地方，`UIViewController`可以加入至應用程式的分鏡腳本，並設定，如下所示：

[![](intro-to-manual-camera-controls-images/image18.png "UIViewController 可以新增到應用程式的分鏡腳本，並設定如下所示")](intro-to-manual-camera-controls-images/image18.png#lightbox)

此檢視包含下列主要項目：

-  A`UIImageView`將顯示視訊的摘要。
-  A`UISegmentedControl`此舉會從自動變更焦點模式，為 已鎖定。
-  兩個`UISlider`控制項，將會顯示並更新溫度和濃淡。
-  A`UIButton`用於範例灰色卡 （灰色世界） 空間及設定使用這些值的白平衡。


執行下列命令以連線檢視控制器的總手動白平衡控制：


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
  
1. 加入下列私用變數：

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    #endregion
    ```
  
1. 加入下列計算的屬性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. 加入下列的私用方法，以設定新的空白溫度和濃淡之間取得平衡：

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
  
1. 覆寫`ViewDidLoad`方法並加入下列程式碼：

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
  
1. 覆寫`ViewDidAppear`方法並加入下列命令以開始錄製 載入檢視時：

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
  
1. 將變更儲存至程式碼，並執行應用程式。
1. 在 Auto 模式中相機，滑桿會移動自動相機調整白平衡：

    [![](intro-to-manual-camera-controls-images/image19.png "滑桿會移動會自動將相機調整白平衡")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. 點選鎖定區段，並拖曳 Temp 和濃淡滑桿，來手動調整白平衡：

    [![](intro-to-manual-camera-controls-images/image20.png "拖曳 Temp 和濃淡滑桿，來手動調整白平衡")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. 與維持選取鎖定區段，將實體灰色卡在前面的相機並點選灰色卡按鈕以調整灰色世界的白平衡：

    [![](intro-to-manual-camera-controls-images/image21.png "點選灰色卡按鈕以調整灰色世界的白平衡")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. 停止應用程式。

上述程式碼顯示如何以自動模式會相機時，監視的白平衡設定，或使用滑桿來控制白平衡，以鎖定模式時。

## <a name="bracketed-capture"></a>以方括弧括住的擷取

括號起來擷取從上述手動攝影機控制項設定為基礎，並允許以各種不同的方式擷取目前的時段中，應用程式。

簡言之，括號起來擷取是一批突發之拍攝的圖片從圖片設定各種不同的靜態影像。

[![](intro-to-manual-camera-controls-images/image22.png "括號起來擷取的運作方式")](intro-to-manual-camera-controls-images/image22.png#lightbox)

使用括號起來擷取 iOS 8 中，應用程式可以預先設定的一系列的手動攝影機控制項，發出單一命令，且有傳回每一個手動預先設定的一系列的映像目前場景。

### <a name="bracketed-capture-basics"></a>以方括弧括住的擷取基本概念

同樣地，括號起來擷取是一批突發之拍攝的圖片從圖片的各種設定的靜態影像。 括號起來擷取可用的類型為：

-  **自動曝光括號**– 所有映像上具有不同的偏差數量。
-  **手動曝光括號**– 所有映像上具有不同的快門 （持續時間） 和 ISO 數量。
-  **簡單暴增的括號**– 一系列的靜態影像快速且連續的。


### <a name="new-bracketed-capture-controls-in-ios-8"></a>括號起來擷取中新控制項 iOS 8

括號起來擷取的所有命令都實作於`AVCaptureStillImageOutput`類別。 使用`CaptureStillImageBracket`方法來取得與指定的陣列設定的一系列的映像。

兩個新的類別已實作設定的處理：

-   `AVCaptureAutoExposureBracketedStillImageSettings` – 它有一個屬性， `ExposureTargetBias`，用來設定為自動曝光括號的偏差。 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` – 它有兩個屬性：`ExposureDuration`和`ISO`，用來設定手動曝光括號的快門和 ISO。 


### <a name="bracketed-capture-controls-dos-and-donts"></a>以方括弧括住的擷取控制準則

#### <a name="dos"></a>事

以下是您應該使用括號起來擷取 iOS 8 中的控制項時的作業的清單：

-  準備應用程式擷取最糟的情況，藉由呼叫`PrepareToCaptureStillImageBracket`方法。
-  假設範例緩衝區，將來自於相同的共用集區。
-  若要釋放由先前的準備呼叫已配置的記憶體，呼叫`PrepareToCaptureStillImageBracket`一次，並傳送一個物件的陣列。


#### <a name="donts"></a>準則

以下是不應該使用括號起來擷取 iOS 8 中的控制項時的作業的清單：

-  請勿混合括號起來擷取單一擷取中的設定類型。
-  不要求多個`MaxBracketedCaptureStillImageCount`單一擷取中的映像。


### <a name="bracketed-capture-details"></a>以方括弧括住的擷取詳細資料

下列詳細資料使用中 iOS 8 的括號起來擷取時應該納入考量：

-  以方括弧括住的設定暫時覆寫`AVCaptureDevice`設定。
-  Flash 和仍然影像穩定的設定都會被忽略。
-  所有映像必須使用相同的輸出格式 （jpeg、 png、 等等）。
-  預覽視訊可能會降低影格數。
-  支援以方括弧括住的擷取所有裝置與 iOS 8 相容。


請注意這項資訊，讓我們來看看使用括號起來擷取 iOS 8 中的範例。

### <a name="bracket-capture-example"></a>括號擷取範例

一般 AV 擷取安裝程式中的程式碼的地方，`UIViewController`可以加入至應用程式的分鏡腳本，並設定，如下所示：

[![](intro-to-manual-camera-controls-images/image23.png "UIViewController 可以新增到應用程式的分鏡腳本，並設定如下所示")](intro-to-manual-camera-controls-images/image23.png#lightbox)

此檢視包含下列主要項目：

-  A`UIImageView`將顯示視訊的摘要。
-  三個`UIImageViews`將顯示擷取的結果。
-  A`UIScrollView`以容納的視訊摘要和結果檢視。
-  A`UIButton`用來讓括號起來擷取與某些預設設定。


執行線上檢視控制器的總括號起來擷取下列作業：


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
  
1. 加入下列私用變數：

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```    
  
1. 加入下列計算的屬性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```  
  
1. 加入下列的私用方法，以建立所需的輸出映像檢視：

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
  
1. 覆寫`ViewDidLoad`方法並加入下列程式碼：
    
    ```
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
    
  
1. 覆寫`ViewDidAppear`方法並加入下列程式碼：

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
    
1. 將變更儲存至程式碼，並執行應用程式。
1. 框架場景，並點選 [擷取括號] 按鈕：

    [![](intro-to-manual-camera-controls-images/image24.png "框架場景，然後點選擷取括號按鈕")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. 由右至左括號起來擷取所採取的三個影像撥動：

    [![](intro-to-manual-camera-controls-images/image25.png "撥動由右至左括號起來擷取所採取的三個影像")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. 停止應用程式。


上述程式碼顯示如何設定及 iOS 8 採取自動曝光括號起來擷取。

## <a name="summary"></a>總結

本文章中我們已涵蓋新的手動攝影機控制項提供的 iOS 8 的簡介，涵蓋它們的用途與運作方式的基本概念。 我們已經提供手動焦點手動曝光及手動白平衡的範例。 最後，我們為範例花括號起來擷取使用先前所討論的手動攝影機控制項

## <a name="related-links"></a>相關連結

- [ManualCameraControls （範例）](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
