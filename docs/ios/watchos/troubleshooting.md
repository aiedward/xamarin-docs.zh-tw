---
title: watchOS 疑難排解
description: 本檔討論使用 Xamarin 進行 watchOS 開發的已知問題和因應措施。 其中描述具有問題的映射、手動新增介面控制器檔案、從命令列啟動監看式應用程式等等。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 1777bfe627d0dc579169c954f7799348befbf6b8
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227022"
---
# <a name="watchos-troubleshooting"></a>watchOS 疑難排解

此頁面包含您可能會遇到之問題的其他資訊和因應措施。

- [已知問題](#knownissues)

- [從圖示影像中移除 Alpha 色板](#noalpha)

- [手動新增](#add)Xcode Interface Builder 的介面控制器檔案。

- [從命令列啟動 WatchApp](#command_line)。

<a name="knownissues" />

## <a name="known-issues"></a>已知問題

### <a name="general"></a>一般

<a name="deploy" />

- 舊版的 Visual Studio for Mac 不正確地將其中一個**AppleCompanionSettings**圖示顯示為88x88 圖元;如果您嘗試提交至 App Store, 這會導致**遺失圖示錯誤**。
    此圖示應為 87 x 87 個像素 (29 單位 **@3x** Retina 畫面)。 您無法在 Visual Studio for Mac 中修正此問題-請在 Xcode 中編輯映射資產, 或手動編輯**內容 json**檔案 (以符合[此範例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132))。

- 如果 Watch 延伸模組專案的**Info. plist > WKApp 配套識別碼**未[正確設定](~/ios/watchos/get-started/project-references.md)為符合監看式應用程式的套件組合**識別碼**, 偵錯工具將無法連線, 而且 Visual Studio for Mac 會等候訊息「*正在等候偵錯工具connect "* 。

- **通知**模式支援調試, 但可能不可靠。 重試有時會有效。 確認 Watch 應用程式的**plist** `WKCompanionAppBundleIdentifier`已設定為符合 iOS 父系/容器應用程式的套件組合識別碼 (即 iPhone 上執行的套件)。

- iOS 設計工具不會顯示 [概覽] 或 [通知] 介面控制器的進入點箭號。

- 您不能將`WKNotificationControllers`兩個加入至分鏡腳本。
    因應措施：分`notificationCategory`鏡腳本 XML 中的專案一律會以相同`id`的方式插入。 若要解決這個問題, 您可以加入兩個 (或更多) 通知控制器, 在文字編輯器中開啟分鏡腳本檔案, `id`然後手動將元素變更為唯一的。

    [![](troubleshooting-images/duplicate-id-sml.png "在文字編輯器中開啟分鏡腳本檔案, 並以手動方式將 id 元素變更為唯一")](troubleshooting-images/duplicate-id.png#lightbox)

- 嘗試啟動應用程式時, 您可能會看到「應用程式尚未建立」錯誤。 當啟始專案設定為 watch 擴充功能專案時, 這會在**清除**之後發生。
    修正方法是選取 [**組建] > [全部重建**], 然後重新開機應用程式。

### <a name="visual-studio"></a>Visual Studio

IOS Designer 對 Watch 套件的支援*需要*正確設定解決方案。 如果未設定專案參考 (請參閱[如何設定參考](~/ios/watchos/get-started/project-references.md)), 則設計介面將無法正確運作。

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>從圖示影像中移除 Alpha 色板

圖示不應包含 Alpha 色板 (Alpha 色板會定義影像的透明區域), 否則應用程式將會在 App Store 提交期間遭到拒絕, 並出現類似如下的錯誤:

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

使用**預覽**應用程式, 即可輕鬆地移除 Mac OS X 上的 Alpha 色板:

1. 開啟**預覽**中的圖示影像, 然後選擇 [檔案] **> [匯出**]。

2. 出現的對話方塊會包含 Alpha 複選框 (如果有 Alpha 色板的話)。

    ![](troubleshooting-images/remove-alpha-sml.png "出現的對話方塊會包含 Alpha 核取方塊 (如果有 Alpha 色板)")

3. *Untick* [ **Alpha** ] 核取方塊, 並將檔案**儲存**至正確的位置。

4. 圖示影像現在應會通過 Apple 的驗證檢查。


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>手動新增介面控制器檔案

> [!IMPORTANT]
> Xamarin 的 WatchKit 支援包括在 iOS 設計工具中設計監看式分鏡腳本 (Visual Studio for Mac 和 Visual Studio), 這不需要以下所述的步驟。 只要在 [Visual Studio for Mac Properties] pad 中為介面控制器提供類別名稱, C#就會自動建立程式碼檔案。


*如果*您使用 Xcode Interface Builder, 請遵循下列步驟來為您的 watch 應用程式建立新的介面控制器, 並啟用與 Xcode 的同步處理, 以便在C#中提供輸出和動作:

1. 在**Xcode Interface Builder**中開啟監看式應用程式的**介面。**

    ![](troubleshooting-images/add-6.png "在 Xcode 中開啟腳本 Interface Builder")

2. 將新`InterfaceController`的拖曳至分鏡腳本:

    ![](troubleshooting-images/add-1.png "InterfaceController")

3. 您現在可以將控制項拖曳至介面控制器 (例如, 標籤和按鈕), 但因為沒有 **.h**標頭檔, 所以您無法建立輸出或動作。 下列步驟將會建立所需的 **.h**標頭檔。

    ![](troubleshooting-images/add-2.png "版面配置中的按鈕")

4. 關閉分鏡腳本, 並返回 Visual Studio for Mac。 在C# **監看式應用程式擴充**功能專案中, 建立新的檔案**MyInterfaceController.cs** (或任何您喜歡的名稱) (不是監看式應用程式本身的腳本)。 新增下列程式碼 (更新命名空間、classname 和函數名稱):

    ```csharp
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
    ```

5. 在C# **監看式應用程式擴充**功能專案中, 建立另一個新的檔案**MyInterfaceController.designer.cs** , 並新增下列程式碼。 請務必更新命名空間、classname 和`Register`屬性:

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

    > [!TIP]
    > 您可以 (選擇性地) 將此檔案設為第一個檔案的子節點, 方法是將C#它拖曳至 Visual Studio for Mac Solution Pad 中的另一個檔案。 它會如下所示:

    ![](troubleshooting-images/add-5.png "Solution pad")

6. 選取 [**組建] > [組建全部**], 讓 Xcode 同步處理能夠辨識我們`Register`使用的新類別 (透過屬性)。

7. 以滑鼠右鍵按一下 [監看式應用程式分鏡腳本檔案], 然後選取 [**開啟方式] > Xcode Interface Builder, 以**重新開啟分鏡腳本:

    ![](troubleshooting-images/add-6.png "在 Interface Builder 中開啟腳本")

8. 選取您的新介面控制器, 並為它提供您在上方定義的 classname, 例如 `MyInterfaceController`.
    如果一切都正常運作, 它應該會自動出現在 [**類別:** ] 下拉式清單中, 而且您可以從該處選取它。

    ![](troubleshooting-images/add-4.png "設定自訂類別")

9. 選擇 Xcode 中的 [**助理編輯器**] 視圖 (具有兩個重迭圓形的圖示), 讓您可以並排查看腳本和程式碼:

    ![](troubleshooting-images/add-7.png "[助理編輯器] 工具列專案")

    當焦點在 [程式碼] 窗格中時, 請確定您查看的是 **.h**標頭檔, 如果不是以滑鼠右鍵按一下階層連結列, 請選取正確的檔案 (**MyInterfaceController .h**)

    ![](troubleshooting-images/add-8.png "選取 MyInterfaceController")

10. 您現在可以從分鏡腳本中, **Ctrl + 拖曳**至 **.h**標頭檔, 以建立輸出和動作。

    ![](troubleshooting-images/add-9.png "建立輸出和動作")

    當您放開拖曳時, 系統會提示您選取是否要建立輸出或動作, 並選擇其名稱:

    ![](troubleshooting-images/add-a.png "[輸出] 和 [動作] 對話方塊")

11. 儲存分鏡腳本變更並關閉 Xcode 之後, 請返回 Visual Studio for Mac。 它會偵測標頭檔變更, 並自動將程式碼新增至**designer.cs**檔案:

    ```csharp
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
    ```

您現在可以在中C#參考控制項 (或執行動作)!


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>從命令列啟動 Watch 應用程式

> [!IMPORTANT]
> 您可以使用 Visual Studio for Mac 和 Visual Studio 中的[自訂執行參數](~/ios/watchos/get-started/installation.md#custommodes), 以一般應用程式模式啟動監看式應用程式。


您也可以使用命令列來控制 iOS 模擬器。 用來啟動監看式應用程式的命令列工具是**mtouch**。

以下是完整的範例 (在終端機中以一行執行):

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

您需要更新以反映您的應用程式的參數`launchsimwatch`為:

### <a name="--launchsimwatch"></a>--launchsimwatch

IOS 應用程式的主要應用程式套件組合的完整路徑 *, 其中包含監看式應用程式和延伸*模組。

> [!NOTE]
> 您需要提供的路徑適用于*iPhone 應用程式*檔案, 也就是將部署到 iOS 模擬器, 並同時包含 watch 擴充功能和監看式應用程式的檔。

範例：

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>通知模式

若要測試應用程式的[**通知**模式](~/ios/watchos/platform/notifications.md) `watchlaunchmode` , 請將參數`Notification`設定為, 並提供包含測試通知裝載之 JSON 檔案的路徑。

通知模式*需要*承載參數。

例如, 將下列引數新增至 mtouch 命令:

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>其他引數

其餘的引數如下所述:

### <a name="--sdkroot"></a>--sdkroot

必要項。 指定 Xcode (6.2 或更新版本) 的路徑。

範例：

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--裝置

要執行的模擬器裝置。 這可以透過兩種方式來指定: 使用特定裝置的 udid, 或使用執行時間和裝置類型的組合。

確切的值會因電腦而異, 而且可以使用 Apple 的**simctl**工具進行查詢:

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

範例：

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**執行時間和裝置類型**

範例：

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
