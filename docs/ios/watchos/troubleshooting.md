---
title: watchOS 疑難排解
description: 本文件討論的已知的問題和因應措施 watchOS 使用 Xamarin 進行開發。 它會描述問題，以手動方式加入介面控制器檔案，啟動監看式應用程式，從命令列中，以及更多的影像。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 70ef341c066c77e214761d75c173faef00266e4c
ms.sourcegitcommit: 2713f2c1d74e3582704c3d0ca65b6651119ed489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56321151"
---
# <a name="watchos-troubleshooting"></a>watchOS 疑難排解

此頁面包含其他資訊和因應措施，您可能會遇到的問題。

- [已知問題](#knownissues)

- [移除圖示影像的 Alpha 色板](#noalpha)

- [手動將介面控制器檔](#add)Xcode 介面產生器。

- [啟動從命令列 WatchApp](#command_line)。

<a name="knownissues" />

## <a name="known-issues"></a>已知問題

### <a name="general"></a>一般

<a name="deploy" />

- 舊版的 Visual Studio for Mac 不正確地顯示其中一個**AppleCompanionSettings**圖示，為 88 x 88 像素為單位; 這會導致**遺漏圖示錯誤**如果您嘗試提交至 App Store。
    此圖示應為 87 x 87 個像素 (29 單位 **@3x** Retina 畫面)。 您無法修正此問題在 Visual Studio for Mac-影像資產在 Xcode 中編輯，或以手動方式編輯**Contents.json**檔案 (比對[本例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132))。

- 如果監看式延伸模組專案的**Info.plist > WKApp 套件組合識別碼**不是[正確設定](~/ios/watchos/get-started/project-references.md)符合 Watch 應用程式**套件組合識別碼**，偵錯工具將無法連線和視覺化Studio for Mac 將會以訊息等候 *「 正在等候偵錯工具連線 「*。

- 支援在偵錯**通知**模式，但可以是不可靠。 重試將有時運作。 確認監看式應用程式的**Info.plist** `WKCompanionAppBundleIdentifier`會設定為符合 iOS 父代/容器應用程式的套件組合識別碼 (ie。 在 iPhone 執行的一個)。

- iOS 設計工具不會顯示摘要或通知介面控制器的進入點箭號。

- 您無法將兩個`WKNotificationControllers`的分鏡腳本。
    因應措施：`notificationCategory`分鏡腳本 XML 中的項目永遠會插入具有相同`id`。 若要解決此問題，您可以新增兩個 （含） 以上的通知控制站、 文字編輯器中開啟分鏡腳本檔案，然後以手動方式變更`id`是唯一的項目。

    [![](troubleshooting-images/duplicate-id-sml.png "開啟分鏡腳本檔案的文字編輯器，並手動變更為唯一的 id 項目")](troubleshooting-images/duplicate-id.png#lightbox)

- 您可能會看到錯誤 「 應用程式尚未建置 」 時嘗試啟動應用程式。 這會發生**清除**當啟始專案設定為 監看式延伸模組專案。
    修正方法是選取**建置 > Rebuild All** ，然後重新啟動應用程式。

### <a name="visual-studio"></a>Visual Studio

IOS 設計工具支援監看式 Kit*需要*正確設定的解決方案。 如果未設定專案參考 (請參閱[如何將參考設定](~/ios/watchos/get-started/project-references.md)) 則設計介面將無法正常運作。

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>移除圖示影像的 Alpha 色板

圖示不應包含 alpha 色頻 （alpha 色板會定義影像的透明區域），否則應用程式將會遭到拒絕，錯誤如下所示的 App Store 提交期間：

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

就可以輕鬆地移除 Mac OS X 使用 alpha 色板**預覽**應用程式：

1. 開啟中的圖示影像**Preview** ，然後選擇**檔案 > 匯出**。

2. 出現的對話方塊會包含**Alpha** alpha 色頻是否存在 核取方塊。

    ![](troubleshooting-images/remove-alpha-sml.png "出現的對話方塊會包含 Alpha 核取方塊，alpha 色頻是否存在")

3. *Untick* **Alpha**核取方塊並**儲存**檔案的正確位置。

4. 圖示的影像現在應該通過 Apple 的驗證檢查。


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>手動新增介面控制器檔案

> [!IMPORTANT]
> Xamarin 的 WatchKit 支援包括設計 iOS 設計工具 （在 Visual Studio for Mac 和 Visual Studio），不需要以下概述的步驟中的監看式分鏡腳本。 只要提供介面控制器類別名稱在 Visual Studio for Mac 屬性填補和C#程式碼檔案將會自動建立。


*如果*您使用 Xcode 的 Interface Builder，請遵循下列步驟來建立監看式應用程式的新介面控制站，並啟用與 Xcode 同步處理，以便輸出和動作可在C#:

1. 開啟 監看式應用程式**Interface.storyboard**中**Xcode Interface Builder**。
    
    ![](troubleshooting-images/add-6.png "在 Xcode Interface Builder 中開啟分鏡腳本")

2. 拖曳新`InterfaceController`到分鏡腳本：

    ![](troubleshooting-images/add-1.png "A InterfaceController")

3. 您可以現在將控制項拖曳至介面控制器 （例如。 標籤和按鈕），但您無法建立輸出或動作，因為沒有任何 **.h**標頭檔。 下列步驟會導致所需 **.h**建立標頭檔。

    ![](troubleshooting-images/add-2.png "版面配置中的按鈕")

4. 關閉分鏡腳本，並返回 Visual Studio for mac。 建立新的C#檔案**MyInterfaceController.cs** （或是您想要的任何名稱） 中**監看應用程式擴充功能**專案 （不監看式應用程式本身所在的分鏡腳本）。 新增下列程式碼 （更新命名空間、 類別名稱和建構函式名稱）：

        using System;
        using WatchKit;
        using Foundation;
        
        namespace WatchAppExtension  // remember to update this
        {
            public partial class MyInterfaceController // remember to update this
            : WKInterfaceController
            {
                public MyInterfaceController // remember to update this
                (IntPtr handle) : base (handle)
                {
                }
                public override void Awake (NSObject context)
                {
                    base.Awake (context);
                    // Configure interface objects here.
                    Console.WriteLine ("{0} awake with context", this);
                }
                public override void WillActivate ()
                {
                    // This method is called when the watch view controller is about to be visible to the user.
                    Console.WriteLine ("{0} will activate", this);
                }
                public override void DidDeactivate ()
                {
                    // This method is called when the watch view controller is no longer visible to the user.
                    Console.WriteLine ("{0} did deactivate", this);
                }
            }
        }

5. 建立另一個新的C#檔案**MyInterfaceController.designer.cs**中**監看應用程式擴充功能**專案，並新增下列程式碼。 請務必更新命名空間、 類別名稱和`Register`屬性：

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;
    
    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```
    
    提示：您可以 （選擇性） 將這個檔案將它拖曳至其他子節點的第一個檔案C#在 Visual Studio for Mac 的 Solution Pad 中的檔案。 它看起來像這樣：
    
    ![](troubleshooting-images/add-5.png "Solution pad")

6. 選取 **建置 > 全部建置**使 Xcode 同步處理會辨識新的類別 (透過`Register`屬性)，我們使用。

7. 重新開啟 監看式應用程式的分鏡腳本檔案上按一下滑鼠右鍵，然後選取 分鏡腳本**開啟 > Xcode Interface Builder**:

    ![](troubleshooting-images/add-6.png "在 介面產生器中開啟分鏡腳本")

8. 選取您的新介面控制站，並提供您定義上方，例如類別名稱。 `MyInterfaceController`.
如果所有項目已正確運作，它應該會自動顯示在**類別：** 下拉式清單中，您可以從該處選取它。

    ![](troubleshooting-images/add-4.png "設定的自訂類別")

9. 選擇**輔助編輯器**檢視在 Xcode （使用兩個重疊圓形圖示） 中，讓您可以看到分鏡腳本和程式碼-並存：

    ![](troubleshooting-images/add-7.png "[助理編輯器] 工具列項目")

    當焦點是在程式碼 窗格時，請確定您正在查看 **.h**標頭檔，以及如果不在階層連結列上按一下滑鼠右鍵，然後選取正確的檔案 (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "選取 MyInterfaceController")

10. 您現在可以建立 「 輸出 」 和動作**Ctrl + 拖曳**從到分鏡腳本 **.h**標頭檔。

    ![](troubleshooting-images/add-9.png "建立輸出和動作")

    當您釋放拖曳時，您將會提示您選取是否要建立輸出或動作，，然後選擇其名稱：

    ![](troubleshooting-images/add-a.png "輸出和動作 對話方塊")

11. 一旦儲存分鏡腳本的變更，並已關閉 Xcode，返回 Visual Studio for mac。 它會偵測標頭檔的變更，並自動將程式碼加入 **。 designer.cs**檔案：


        [Register ("MyInterfaceController")]
        partial class MyInterfaceController
        {
            [Outlet]
            WatchKit.WKInterfaceButton myButton { get; set; }
        
            void ReleaseDesignerOutlets ()
            {
                if (myButton != null) {
                    myButton.Dispose ();
                    myButton = null;
                }
            }
        }


現在可以參考的控制項 （或實作的動作） 中C#！


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>啟動監看式應用程式，從命令列

> [!IMPORTANT]
> 根據預設，而且也是在中，您可以在一般應用程式模式中啟動 Watch 應用程式**概覽**或是**通知**使用模式[自訂執行中參數](~/ios/watchos/get-started/installation.md#custommodes)在 Visual Studio for Mac 和Visual Studio。


您也可以使用命令列來控制 iOS 模擬器。 用來啟動監看式應用程式的命令列工具是**mtouch**。

以下是完整的範例 （以單行終端機中執行）：

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

您必須更新以反映您的應用程式的參數是`launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

主要的應用程式套件組合的完整路徑*iOS 應用程式，其中包含監看式應用程式和延伸模組*。

> [!NOTE]
> 您必須提供的路徑是*iPhone 應用程式的.app 檔*，也就是一個將會部署至 iOS 模擬器，並包含將監看式副檔名] 和 [監看式應用程式。

範例：

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>通知模式

若要測試的應用程式[**通知**模式](~/ios/watchos/platform/notifications.md)，將`watchlaunchmode`參數`Notification`並提供包含測試通知承載的 JSON 檔案的路徑。

內容參數是*必要*通知模式。

比方說，mtouch 命令將這些引數：

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>其他引數

其餘的引數的說明如下：

### <a name="--sdkroot"></a>--sdkroot

必要項。 指定 Xcode （6.2 或更新版本） 的路徑。

範例：

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--device

要執行的模擬器裝置。 這可以指定兩種方式，使用特定裝置的 udid，或使用執行階段和裝置類型的組合。

實際值的機器，而異，而且可使用 Apple 的查詢**simctl**工具：

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

範例：

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**執行階段和裝置類型**

範例：

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
