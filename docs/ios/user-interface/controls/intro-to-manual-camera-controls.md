---
title: 在 Xamarin.iOS 中手動相機控制項
description: 本文件說明如何 iOS AVFoundation 架構可以搭配 Xamarin.iOS 以啟用手動相機控制項。 手動相機控制項可讓使用者控制項焦點、 白平衡，並公開的設定。
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c61b3fee9009afb86ccd3fd0e16d7812a8e90feb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384138"
---
# <a name="manual-camera-controls-in-xamarinios"></a>在 Xamarin.iOS 中手動相機控制項

所提供之手動相機控制項`AVFoundation Framework`在 iOS 8 中，允許行動裝置的應用程式，以便完全控制 iOS 裝置的相機。 這個更細緻的控制層級可以用於建立專業的層級相機應用程式，並提供調整觀景窗的參數，同時仍的映像或影片的演出者的組合。

開發科學或產業應用程式，結果較不受限的正確性或映像的優點，而且還有專為多反白顯示部分功能或項目所採取的映像時，這些控制項可以也很有用。

## <a name="avfoundation-capture-objects"></a>AVFoundation 擷取物件

是否花影片或仍在 iOS 裝置上使用相機的映像，用來擷取這些映像的程序大致相同。 這是使用預設的自動相機控制項或利用新的手動相機控制項的應用程式，則為 true:

 [![](intro-to-manual-camera-controls-images/image1.png "AVFoundation 擷取物件概觀")](intro-to-manual-camera-controls-images/image1.png#lightbox)

輸入取自`AVCaptureDeviceInput`成`AVCaptureSession`藉由`AVCaptureConnection`。 為靜止影像或視訊資料流，結果會是其中一個輸出。 整個程序會受到`AVCaptureDevice`。

## <a name="manual-controls-provided"></a>手動提供的控制項

使用 iOS 8 所提供的新 Api，應用程式可以採用下列相機功能的控制項：

-  **手動焦點**– 藉由允許使用者直接取得焦點的控制權，應用程式可以提供更充分掌控所花費的映像。
-  **手動曝光**– 應用程式可以藉由提供手動控制的曝光度，為使用者提供更多的自由，並允許以達到樣式化的外觀。
-  **手動的白平衡**– 白平衡用來調整影像的色彩，通常，看起來逼真。 不同光源有不同的色彩溫度，以及用來擷取映像的攝影機設定為調整來補償這些差異。 同樣地，允許使用者對白平衡的控制，使用者可以進行調整，無法自動完成。


iOS 8 提供的擴充功能和增強功能到現有的 iOS Api 用於提供此更細微的控制映像擷取程序。

## <a name="bracketed-capture"></a>以方括弧括住的擷取

加上括號擷取從上述手動相機控制項設定為基礎，並允許以各種不同的方式擷取時間點，應用程式。

簡言之，加上括號擷取是靜止映像，以各種不同的設定，從圖片以圖片拍攝的暴增。

## <a name="requirements"></a>需求

以下被必要設定來完成這篇文章所述的步驟：

-  **Xcode 7 + 和 iOS 8 或更新版本**– Apple 的 Xcode 7 和 iOS 8 或更新版本的 Api 需要安裝並設定開發人員電腦上。
-  **Visual Studio for Mac** – 應該安裝並設定使用者裝置上的 Visual Studio for Mac 的最新版本。
-  **iOS 8 裝置**– 執行 iOS 8 的最新版本的 iOS 裝置。 相機功能無法在 iOS 模擬器中測試。


## <a name="general-av-capture-setup"></a>一般 AV 擷取安裝程式

錄製的 iOS 裝置上的視訊，則一律為必要的一些一般設定程式碼。 本章節將涵蓋最基本的設定，才能從 iOS 裝置的相機的視訊錄製，並顯示該影片中即時`UIImageView`。

### <a name="output-sample-buffer-delegate"></a>輸出範例緩衝區委派

第一件事所需會委派來監視範例輸出緩衝區，並顯示從緩衝區來捕捉影像`UIImageView`應用程式 UI。

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

使用中的位置，此常式`AppDelegate`可以修改，以開啟的 AV 擷取工作階段錄製即時視訊摘要。

### <a name="creating-an-av-capture-session"></a>建立 AV 擷取工作階段

AV 擷取工作階段用來控制 iOS 裝置的相機的即時視訊錄製，也不需要 iOS 應用程式進入視訊。 因為此範例`ManualCameraControl`範例應用程式在幾個不同地方使用擷取工作階段，它會設定為`AppDelegate`，並可在整個應用程式。

執行下列工作來修改應用程式的`AppDelegate`並新增必要的程式碼：


1. 按兩下`AppDelegate.cs`檔案在方案總管 中，以開啟它進行編輯。
1. 新增下列 using 陳述式的檔案頂端：
    
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

1. 將下列的私用變數和計算的屬性，以新增`AppDelegate`類別：
    
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


使用此程式碼就緒之後，手動相機控制項可以輕鬆地實作進行試驗與測試。

## <a name="manual-focus"></a>手動焦點

允許使用者直接取得焦點的控制項，應用程式可以提供比較有藝術天份控制擷取的映像。

比方說，專業攝影師可以減輕的映像以達到焦點[散景效果](https://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "散景效果")](intro-to-manual-camera-controls-images/image2.png#lightbox)

或者，建立[焦點提取效果](http://www.mediacollege.com/video/camera/focus/pull.html)，例如：

[![](intro-to-manual-camera-controls-images/image3.png "焦點提取效果")](intro-to-manual-camera-controls-images/image3.png#lightbox)

科學家或醫療應用程式的寫入器，應用程式可能會想要以程式設計方式四處移動功能濾鏡的實驗。 新的 API 採用任一種方法可讓使用者或應用程式來掌控焦點時的映像不會採取。

### <a name="how-focus-works"></a>焦點的運作方式

之前討論的控制 IOS 8 應用程式中的焦點的詳細資料。 讓我們看一下焦點在 iOS 裝置的運作方式：

[![](intro-to-manual-camera-controls-images/image4.png "焦點在 iOS 裝置的運作方式")](intro-to-manual-camera-controls-images/image4.png#lightbox)

Light 進入 iOS 裝置上的相機功能濾鏡，並著重於映像感應器。 從感應器 功能濾鏡的距離會控制焦點 （映像會出現最銳利的區域） 所在位置，將感應器的關聯性。 距離越遠功能濾鏡是來自感應器、 距離物件看起來很清晰且愈接近物件附近看起來很清晰。

在 iOS 裝置，功能濾鏡移近，或感應器更磁鐵和 springs。 如此一來，確切的位置 功能濾鏡的不可能，將不同裝置，並可能會受到參數，例如裝置方向或裝置與 spring 的時代。

### <a name="important-focus-terms"></a>重要焦點條款

當處理具有焦點時，有幾個開發人員應該很熟悉的詞彙：

-  **景深**– 最接近以及最遠焦點中物件之間的距離。 
-  **巨集**-這是接近焦點範圍的結尾，而且最接近處可以專注於功能濾鏡的距離。
-  **無限大**– 這是焦點範圍的較遠端，而且處可以專注於功能濾鏡的最遠距離。
-  **Hyperfocal 距離**– 這是焦點範圍中的點只是查看的焦點是在框架中最遠的物件之處。 換句話說，這是達到最大深度的欄位的主要位置。 
-  **功能濾鏡位置**– 這是什麼控制上述所有其他條款。 這是從感應器，因此 功能濾鏡的距離的焦點的控制站。


這些條款，並記住的知識，新的手動焦點控制項可以成功實作的 iOS 8 應用程式。

### <a name="existing-focus-controls"></a>現有的焦點控制項

iOS 7 和舊版中，提供現有焦點的控制項，透過`FocusMode`屬性設為：

-   `AVCaptureFocusModeLocked` – 焦點會在單一鶗懘鎖定。
-   `AVCaptureFocusModeAutoFocus` – 相機掃掠所有焦點點功能濾鏡，直到找到聰明的焦點並會保持那里。
-   `AVCaptureFocusModeContinuousAutoFocus` – 相機 refocuses 每當它偵測到狀況失焦時。


現有的控制項也提供透過可設定景點`FocusPointOfInterest`屬性，以便使用者可以點選將焦點放在特定區域。 應用程式也可以藉由監視追蹤 功能濾鏡移動`IsAdjustingFocus`屬性。

此外，範圍限制由提供`AutoFocusRangeRestriction`屬性設為：

-   `AVCaptureAutoFocusRangeRestrictionNear` – 限制 autofocus 附近的深度。 在例如掃描 QR 代碼或條碼的情況下很有用。
-   `AVCaptureAutoFocusRangeRestrictionFar` – 會 autofocus 限制到遙遠的深度。 在已知為不相關的物件會處於視角 （比方說，視窗框架） 的情況下很有用。


最後還有`SmoothAutoFocus`自動焦點演算法變慢，且步驟較小的增量，以避免移動成品時錄製視訊內容。

### <a name="new-focus-controls-in-ios-8"></a>在 iOS 8 中的新焦點控制項

除了已經提供 ios 7 及更新版本的功能，現已可用來控制焦點在 iOS 8 中的下列功能：

-  完全手動的鏡頭位置時鎖定焦點的控制項。
-  在任何焦點模式中的鏡頭位置的索引鍵-值觀察。


若要實作上述功能，`AVCaptureDevice`類別已修改成包含唯讀`LensPosition`用來取得目前的位置，數位相機 功能濾鏡的屬性。

若要手動控制的鏡頭位置，擷取的裝置必須在鎖定的焦點模式中。 範例：

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

`SetFocusModeLocked`擷取裝置的方法用來調整相機 功能濾鏡的位置。 可以提供選擇性的回呼常式，當變更生效時收到通知。 範例：

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

上述程式碼所示，擷取的裝置必須鎖定設定，才能讓在鏡頭位置的變更。 功能濾鏡位置上的有效值為 0.0 到 1.0 之間。

### <a name="manual-focus-example"></a>手動焦點範例

一般 AV 擷取安裝程式中的程式碼的地方，`UIViewController`可以新增至應用程式的分鏡腳本，並設定，如下所示：

[![](intro-to-manual-camera-controls-images/image5.png "UIViewController 可以加入至分鏡腳本的應用程式，並設定如下所示")](intro-to-manual-camera-controls-images/image5.png#lightbox)

此檢視包含下列主要元素：

-  A`UIImageView`將顯示視訊的摘要。
-  A `UISegmentedControl` ，將會從自動變更焦點模式，為 已鎖定。
-  A `UISlider` ，將會顯示與更新目前的鏡頭位置。


執行下列命令以連線接的檢視控制器手動焦點的控制項：


1. 新增下列 using 陳述式：

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
  
1. 加入下列的私用變數：

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```  
  
1. 新增下列計算的屬性：

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
  
1. 覆寫`ViewDidAppear`方法，並新增下列命令以開始錄製於檢視載入時：

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
  
1. 使用 Auto 模式中的攝影機，滑桿會移動自動相機焦點調整：

    [![](intro-to-manual-camera-controls-images/image6.png "滑桿會移動會自動將觀景窗會調整的焦點放在此範例應用程式")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. 點選鎖定區段，然後拖曳位置滑桿手動調整鏡頭位置：

    [![](intro-to-manual-camera-controls-images/image7.png "手動調整 鏡頭位置")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. 停止應用程式。


上述程式碼顯示如何在攝影機處於自動模式時，監視透鏡位置，或使用滑桿來控制功能濾鏡位置處於鎖定模式時。

## <a name="manual-exposure"></a>手動曝光

風險是指相對於來源亮度的影像亮度，而決定由多少光線所達到的感應器，如需時間，以及由感應器 （ISO 對應） 的增益等級。 藉由提供手動控制的曝光度，應用程式可以為使用者提供更多的自由，並允許以達到樣式化的外觀。

使用手動公開控制項，使用者可以採取深色和 moody 誤以為光明的映像：

[![](intro-to-manual-camera-controls-images/image8.png "範例顯示遭到誤以為亮暗與 moody 映像")](intro-to-manual-camera-controls-images/image8.png#lightbox)

同樣地，這可自動使用程式設計的方式控制適用於科學應用程式或透過應用程式使用者介面所提供的手動控制項。 無論如何，新的 iOS 8 的曝光度 Api 提供更細微的控制相機的曝光度設定。

### <a name="how-exposure-works"></a>公開的運作方式

之前討論的詳細資料來控制 IOS 8 應用程式中的公開。 讓我們看一下曝光的運作方式：

[![](intro-to-manual-camera-controls-images/image9.png "公開的運作方式")](intro-to-manual-camera-controls-images/image9.png#lightbox)

三個基本項目一起控制風險︰

-  **快門速度**– 這是按下快門已開啟，讓相機感應器上的光的時間長度。 較短快門已開啟，時間較少的光讓和更清晰的影像是模糊 （較少影片程度）。 長按下快門開啟時，更多的光 let 在時間和更多動作，就會發生的模糊。
-  **ISO 對應**– 這是取自影片攝影的詞彙，指的中點亮影片化學藥品敏感度。 在影片中的低 ISO 值有較少的資料粒度和更細微的色彩重現;數位感應器上的低 ISO 值有剩餘的感應器干擾較少但較少的亮度。 的 ISO 值越高，較鮮亮的映像但具有多個感應器雜訊。 數位感應器上的"ISO"是指量[electronic 提升](https://en.wikipedia.org/wiki/Gain)，不是實體的功能。 
-  **功能濾鏡 Aperture** – 這是功能濾鏡開頭的大小。 所有 iOS 裝置上功能濾鏡 aperture 被固定的因此可以用來調整曝光只有兩個值的快門和 ISO。


### <a name="how-continuous-auto-exposure-works"></a>如何持續自動公開運作

學習如何手動曝光的運作方式，很好還是能了解如何持續自動曝光適用於 iOS 裝置。

[![](intro-to-manual-camera-controls-images/image10.png "在 iOS 裝置的連續自動曝光運作方式")](intro-to-manual-camera-controls-images/image10.png#lightbox)

第一個是 自動曝光區塊中，它的計算理想的曝光度的作業，而且會持續饋送計量統計資料。它會使用這項資訊來計算最佳混用 ISO 和快門，以取得場景也亮起。 這個循環被指 AE 迴圈。

### <a name="how-locked-exposure-works"></a>如何鎖定的曝光度運作方式

接下來，看看如何在 iOS 裝置的方式鎖定的曝光運作。

[![](intro-to-manual-camera-controls-images/image11.png "如何鎖定公開適用於 iOS 裝置")](intro-to-manual-camera-controls-images/image11.png#lightbox)

同樣地，您必須嘗試計算最佳的 iOS 和工期值自動曝光區塊。 不過，在此模式中 AE 區塊已中斷連接則計量統計資料引擎。

### <a name="existing-exposure-controls"></a>現有的曝光度控制項

iOS 7 及更新版本，提供下列現有的曝光度控制項透過`ExposureMode`屬性：

-   `AVCaptureExposureModeLocked` – 場景一次，並使用這些值在整個場景。
-   `AVCaptureExposureModeContinuousAutoExposure` – 取樣持續以確保妥善亮起場景。


`ExposurePointOfInterest`可用來點選以選取目標物件上，公開場景和應用程式可以監視`AdjustingExposure`屬性，以查看當正在調整曝光。

### <a name="new-exposure-controls-in-ios-8"></a>在 iOS 8 中的新風險控制項

除了已經提供 ios 7 及更新版本的功能，現已可用來控制 iOS 8 中的曝光度的下列功能：

-  完全手動的自訂曝光。
-  取得、 設定和索引鍵-值觀察 IOS 和快門 （持續時間）。


若要實作上述功能，新`AVCaptureExposureModeCustom`已新增模式。 自訂模式中的攝影機時，下列程式碼可用來調整風險持續時間和 ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

在自動和鎖定模式中，應用程式可以調整使用下列程式碼的自動公開常式的偏差：

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

最小和最大的設定範圍會視應用程式執行所在的裝置，因此他們應該永遠不會以硬式編碼。 相反地，若要取得最小和最大值的範圍中使用下列屬性：

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


上述程式碼所示，擷取的裝置必須鎖定組態，才能讓變更曝光度。

### <a name="manual-exposure-example"></a>手動曝光範例

一般 AV 擷取安裝程式中的程式碼的地方，`UIViewController`可以新增至應用程式的分鏡腳本，並設定，如下所示：

[![](intro-to-manual-camera-controls-images/image12.png "UIViewController 可以加入至分鏡腳本的應用程式，並設定如下所示")](intro-to-manual-camera-controls-images/image12.png#lightbox)

此檢視包含下列主要元素：

-  A`UIImageView`將顯示視訊的摘要。
-  A `UISegmentedControl` ，將會從自動變更焦點模式，為 已鎖定。
-  四個`UISlider`控制項，其會顯示並更新位移、 持續時間、 ISO 和偏差。


執行下列命令以連線接的檢視控制器手動公開控制項：


1. 新增下列 using 陳述式：
    
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
  
1. 加入下列的私用變數：

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```  
  
1. 新增下列計算的屬性：

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
  
1. 覆寫`ViewDidAppear`方法，並新增下列命令以開始錄製於檢視載入時：

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
  
1. 使用 Auto 模式中的攝影機，滑桿會移動自動相機調整曝光度：

    [![](intro-to-manual-camera-controls-images/image13.png "滑桿會自動移動，如相機調整曝光")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. 點選鎖定區段，然後拖曳偏差滑桿手動調整自動曝光的偏差：

    [![](intro-to-manual-camera-controls-images/image14.png "手動調整自動曝光的偏差")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. 點選 自訂區段，並拖曳 持續時間和 ISO 滑桿，以手動方式控制曝光度：

    [![](intro-to-manual-camera-controls-images/image15.png "將持續時間和 ISO 滑桿拖曳到手動控制的曝光度")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. 停止應用程式。


上述程式碼示範了如何在攝影機處於自動模式中，監視風險設定，以及如何控制曝光，處於已鎖定] 或 [自訂模式時使用滑桿。

## <a name="manual-white-balance"></a>手動的白平衡

白平衡控制項可讓使用者調整 colosr 来讓它們看起來更逼真的影像中的餘額。 不同光源有不同的色彩溫度，以及用來擷取映像的相機設定必須調整來補償這些差異。 同樣地，它們可以透過讓使用者控制項的白平衡進行自動常式來達成藝術的效果不具備專業調整。

[![](intro-to-manual-camera-controls-images/image16.png "顯示手動白平衡調整範例映像")](intro-to-manual-camera-controls-images/image16.png#lightbox)

比方說，日光節約都具有 blueish 轉型，而 tungsten 白熱光線有暖和、 黃色橙色濃淡。 （奇怪地，「 冷 」 色彩的較高的色彩溫度比 「 暖 」 色彩。 色彩溫度是實體的量值，不會感知的其中一個）。

人性化的考量是很好的色溫差異補償，但這一點相機無法執行。 將觀景窗的運作方式提升色彩相反的範圍，以便調整色彩差異。

新的 iOS 8 公開 API 可讓應用程式程序的控制，並提供更細微的控制相機的白平衡設定。

### <a name="how-white-balance-works"></a>如何白平衡的運作方式

之前討論的控制 IOS 8 應用程式中的白平衡的詳細資料。 讓我們看一下如何白平衡的運作方式：

在研究的色彩 perception [CIE 1931 RGB 色彩空間和 CIE 1931 XYZ 色彩空間](https://en.wikipedia.org/wiki/CIE_1931_color_space)是第一個數學上定義的色彩空間。 它們是由國際佣金上照明 (CIE) 建立 1931年中。

[![](intro-to-manual-camera-controls-images/image17.png "CIE 1931 RGB 色彩空間和 CIE 1931 XYZ 色彩空間")](intro-to-manual-camera-controls-images/image17.png#lightbox)

上述圖表會顯示我們的所有色彩可見人類的眼睛，從深度為亮綠色到紅光，而藍色。 在圖表上的任何時間點可以繪製 X 和 Y 值，在上圖所示。

為顯示在圖形中，有可超出範圍的人性化的願景，就是在圖形繪製的 X 和 Y 值，因此無法由相機重現這些色彩。

會呼叫上述圖表中較小的曲線[Planckian 出自於](https://en.wikipedia.org/wiki/Planckian_locus)，這表示 （以度為單位 kelvin) 更高的數字 （建築物） 藍色一端色溫和較低數字 （冷卻器） 」 的紅色一端。 這些是一般的光源相當有用。

在混合的光源條件的白平衡調整必須偏離 Planckian 出自於，來進行必要的變更。 在這些情況下，需要調整来移位到綠色或紅色/洋紅一端 CIE 縮放。

iOS 裝置會藉由提升相反的色彩獲得彌補色彩轉換。 比方說，如果場景有太多藍色，然後紅色的提升將會提升補償。 這些值會校正特定裝置，讓它們因裝置而異的改善。

### <a name="existing-white-balance-controls"></a>現有的白平衡控制項

iOS 7 及更新版本所提供的下列現有的白平衡控制項透過`WhiteBalanceMode`屬性：

-   `AVCapture WhiteBalance ModeLocked` – 取樣一次的場景，並使用這些值在整個場景。
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` – 取樣持續以確保順利平衡場景。


應用程式可以監視`AdjustingWhiteBalance`屬性，以查看當正在調整曝光。

### <a name="new-white-balance-controls-in-ios-8"></a>在 iOS 8 中新的白平衡控制項

除了已經提供 ios 7 及更新版本的功能，現在都可在 iOS 8 中的控制項白平衡的下列功能：

-  完全手動控制的裝置 RGB 取得。
-  取得、 設定和索引鍵-值觀察裝置 RGB 取得。
-  白平衡使用灰色卡的支援。
-  與裝置無關的色彩空間的轉換常式。


若要實作上述功能，`AVCaptureWhiteBalanceGain`結構已新增具有下列成員：

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


目前最大值的白平衡提升為四 （4)，而且可以從準備`MaxWhiteBalanceGain`屬性。 讓合法的範圍是從一 (1) 到`MaxWhiteBalanceGain`(4) 目前。

`DeviceWhiteBalanceGains`屬性可以用來觀察目前的值。 使用`SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`調整餘額獲得鎖定的白平衡模式相機時。

#### <a name="conversion-routines"></a>轉換常式

轉換常式已新增至以進行轉換，以及從 iOS 8 裝置獨立的色彩空間。 若要實作轉換常式`AVCaptureWhiteBalanceChromaticityValues`結構已新增具有下列成員：

-   `X` -是從 0 到 1 的值。
-   `Y` -是從 0 到 1 的值。


`AVCaptureWhiteBalanceTemperatureAndTintValues`結構也已新增具有下列成員：

-   `Temperature` -浮點數以度為單位 Kelvin 的值。
-   `Tint` -從綠色或洋紅色從 0 到 150 與綠色的方向朝向正值和負值朝洋紅是位移。


使用`CaptureDevice.GetTemperatureAndTintValues`而`CaptureDevice.GetDeviceWhiteBalanceGains`溫度及濃淡、 色度和 RGB 之間進行轉換的方法取得色彩空間。

> [!NOTE]
> 要轉換的值越接近 Planckian 出自於轉換常式是更精確。




#### <a name="gray-card-support"></a>灰色卡支援

Apple iOS 8 內建的灰色卡支援是指用以詞彙灰色世界。 它可讓使用者專注於實體的灰色卡涵蓋至少 50%的中央的框架，並使用該值來調整白平衡。 灰色卡旨在達到出現中性的白色。

這可以透過提示使用者將實體的灰色卡前方相機，實作應用程式中監視`GrayWorldDeviceWhiteBalanceGains`屬性，並等待，直到值都穩定下來。

應用程式接著會鎖定的白平衡提升`SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`方法使用來自值`GrayWorldDeviceWhiteBalanceGains`以套用變更的屬性。

在進行變更，以在白平衡之前，必須先擷取裝置鎖定組態。

### <a name="manual-white-balance-example"></a>手動的白平衡範例

一般 AV 擷取安裝程式中的程式碼的地方，`UIViewController`可以新增至應用程式的分鏡腳本，並設定，如下所示：

[![](intro-to-manual-camera-controls-images/image18.png "UIViewController 可以加入至分鏡腳本的應用程式，並設定如下所示")](intro-to-manual-camera-controls-images/image18.png#lightbox)

此檢視包含下列主要元素：

-  A`UIImageView`將顯示視訊的摘要。
-  A `UISegmentedControl` ，將會從自動變更焦點模式，為 已鎖定。
-  兩個`UISlider`會顯示和更新的溫度和濃淡的控制項。
-  A`UIButton`用來取樣灰色卡 （灰色世界） 空間，並設定使用這些值的白平衡。


執行下列命令以連線檢視控制器的總手動白平衡控制：


1. 新增下列 using 陳述式：

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
  
1. 加入下列的私用變數：

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    #endregion
    ```
  
1. 新增下列計算的屬性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. 加入下列的私用方法，以設定新的空白溫度與濃淡之間取得平衡：

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
  
1. 覆寫`ViewDidAppear`方法，並新增下列命令以開始錄製於檢視載入時：

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
1. 使用 Auto 模式中的攝影機，滑桿會移動會自動將觀景窗會調整的白平衡：

    [![](intro-to-manual-camera-controls-images/image19.png "滑桿會移動會自動將觀景窗會調整的白平衡")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. 點選鎖定區段，然後拖曳 Temp 和濃淡滑桿，來手動調整白平衡：

    [![](intro-to-manual-camera-controls-images/image20.png "將 Temp 和濃淡滑桿，來手動調整白平衡")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. 鎖定區段仍處於選取狀態的情況下，放在前面的觀景窗的實體灰色卡，並點選 [灰色卡片] 按鈕，以調整為灰色 World 的白平衡：

    [![](intro-to-manual-camera-controls-images/image21.png "點選 [灰色卡片] 按鈕，以調整為灰色 World 的白平衡")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. 停止應用程式。

上述程式碼顯示如何在攝影機處於自動模式時，監視的白平衡設定，或使用滑桿來控制何時處於鎖定模式的白平衡。

## <a name="bracketed-capture"></a>以方括弧括住的擷取

加上括號擷取從上述手動相機控制項設定為基礎，並允許以各種不同的方式擷取時間點，應用程式。

簡言之，加上括號擷取是靜止映像，以各種不同的設定，從圖片以圖片拍攝的暴增。

[![](intro-to-manual-camera-controls-images/image22.png "加上括號擷取的運作方式")](intro-to-manual-camera-controls-images/image22.png#lightbox)

使用加上括號擷取在 iOS 8 中，應用程式可以預先設定的一系列的手動相機控制項發出單一命令，已手動預先設定的每個會傳回一系列的映像目前場景。

### <a name="bracketed-capture-basics"></a>以方括弧括住的擷取基本概念

同樣地，加上括號擷取是靜態影像，以從圖片的各種設定，以圖片拍攝的暴增。 加上括號擷取可用的類型為：

-  **Auto 曝光括號**– 其中所有映像有不同的偏差數量。
-  **手動公開括號**– 其中所有映像有不同的快門 （持續時間） 和 ISO 數量。
-  **簡單的高載括號**– 一系列的還是採取快速執行中的映像。


### <a name="new-bracketed-capture-controls-in-ios-8"></a>新加上括號擷取控制項在 iOS 8 中

所有加上括號擷取的命令都實作於`AVCaptureStillImageOutput`類別。 使用`CaptureStillImageBracket`方法來取得具有指定的陣列，設定的一系列的映像。

兩個新的類別已實作來處理設定：

-   `AVCaptureAutoExposureBracketedStillImageSettings` – 它有一個屬性， `ExposureTargetBias`，用來設定為自動曝光括號的偏差。 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` – 它有兩個屬性：`ExposureDuration`和`ISO`，用來設定為手動曝光括號的快門和 ISO。 


### <a name="bracketed-capture-controls-dos-and-donts"></a>以方括弧括住的擷取控制建議與禁忌

#### <a name="dos"></a>建議

以下是應該使用括號起來擷取 iOS 8 中的控制項時完成的事項清單：

-  做好最壞情況的擷取這種情況中的應用程式，藉由呼叫`PrepareToCaptureStillImageBracket`方法。
-  假設範例緩衝區即將來自於相同的共用集區。
-  若要釋放先前準備呼叫所配置的記憶體，呼叫`PrepareToCaptureStillImageBracket`一次並傳送一個物件的陣列。


#### <a name="donts"></a>避免事項

以下是應該使用括號起來擷取 iOS 8 中的控制項時未完成的事項清單：

-  請勿混合加上括號擷取單一擷取中的設定類型。
-  不要求多個`MaxBracketedCaptureStillImageCount`單一擷取中的映像。


### <a name="bracketed-capture-details"></a>以方括弧括住的擷取詳細資料

下列詳細資料，在 iOS 8 中使用加上括號擷取時應該納入考量：

-  以方括弧括住的設定暫時覆寫`AVCaptureDevice`設定。
-  Flash 與仍影像穩定的設定會被忽略。
-  所有映像必須使用相同的輸出格式 （jpeg、 png）
-  影片預覽可能會降低影格數。
-  所有 ios 8 相容的裝置上支援以方括弧括住的擷取。


此資訊謹記在心，讓我們來看看使用加上括號擷取在 iOS 8 中的範例。

### <a name="bracket-capture-example"></a>括號擷取範例

一般 AV 擷取安裝程式中的程式碼的地方，`UIViewController`可以新增至應用程式的分鏡腳本，並設定，如下所示：

[![](intro-to-manual-camera-controls-images/image23.png "UIViewController 可以加入至分鏡腳本的應用程式，並設定如下所示")](intro-to-manual-camera-controls-images/image23.png#lightbox)

此檢視包含下列主要元素：

-  A`UIImageView`將顯示視訊的摘要。
-  三個`UIImageViews`，會顯示擷取的結果。
-  A`UIScrollView`以容納的視訊摘要與結果檢視表。
-  A`UIButton`需要加上括號擷取與某些預設設定。


執行下列命令以連線檢視控制器的總加上括號擷取：


1. 新增下列 using 陳述式：

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
  
1. 加入下列的私用變數：

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```    
  
1. 新增下列計算的屬性：

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
1. 框架場景，然後點選 [擷取括號] 按鈕：

    [![](intro-to-manual-camera-controls-images/image24.png "框架場景，然後點選 [擷取括號] 按鈕")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. 撥動由右至左來查看所括住擷取三個映像：

    [![](intro-to-manual-camera-controls-images/image25.png "揮擊由右至左來查看所括住擷取三個映像")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. 停止應用程式。


上述程式碼顯示如何設定，並需要在 iOS 8 中的 自動曝光加上括號擷取。

## <a name="summary"></a>總結

這篇文章中，我們已涵蓋 iOS 8 所提供之新手動相機控制項的簡介，並做什麼，以及其運作方式的基本概念。 我們已提供手動焦點、 手動曝光及手動白平衡的範例。 最後，我們提供給範例採用加上括號擷取使用先前所述的手動相機控制項

## <a name="related-links"></a>相關連結

- [ManualCameraControls （範例）](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [iOS 8 簡介](~/ios/platform/introduction-to-ios8.md)
