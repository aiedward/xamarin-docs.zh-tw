---
title: 逐步解說-Xamarin 中的背景位置
description: 本檔提供如何在背景執行的 Xamarin iOS 應用程式中使用位置資訊的逐步解說。 其中描述必要的設定、使用者介面和應用程式狀態。
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: e9cfdc32f5e679216781abda1cf718a1c1a186ae
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620708"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>逐步解說-Xamarin 中的背景位置

在此範例中, 我們將建立 iOS 位置應用程式, 以列印目前位置的相關資訊: 緯度、經度和螢幕的其他參數。 此應用程式將示範如何在應用程式為作用中或背景執行時, 適當地執行位置更新。

本逐步解說說明一些重要的背景處理概念, 包括將應用程式註冊為背景必要的應用程式、在背景執行應用程式時暫停 UI 更新, 以及`WillEnterBackground`使用`WillEnterForeground`和`AppDelegate`方法.

## <a name="application-set-up"></a>應用程式設定


1. 首先, 建立新的**iOS > 應用程式 > 單一視圖應用C#程式 ()** 。 呼叫 [_位置_], 並確定已選取 [iPad] 和 [iPhone]。

1. 位置應用程式會在 iOS 中限定為背景必要的應用程式。 藉由編輯專案的**plist**檔案, 將應用程式註冊為位置應用程式。

    在 [方案總管] 底下, 按兩下**plist**檔案以開啟它, 然後流覽至清單底部。 勾選 [**啟用背景模式**] 和 [**位置更新**] 核取方塊。

    在 Visual Studio for Mac 中, 它看起來會像這樣:

    [![](location-walkthrough-images/image7.png "勾選 [啟用背景模式] 和 [位置更新] 核取方塊")](location-walkthrough-images/image7.png#lightbox)

    在 Visual Studio 中, 您必須新增下列索引鍵/值組來手動更新**plist** :

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. 現在已註冊應用程式, 它可以從裝置取得位置資料。 在 iOS `CLLocationManager`中, 類別是用來存取位置資訊, 而且可以引發提供位置更新的事件。

1. 在程式碼中, 建立名`LocationManager`為的新類別, 針對各種畫面和程式碼提供單一位置來訂閱位置更新。 在類別中, 建立名`LocMgr`為的`CLLocationManager`實例: `LocationManager`

    ```csharp
    public class LocationManager
    {
        protected CLLocationManager locMgr;

        public LocationManager () {
            this.locMgr = new CLLocationManager();
            this.locMgr.PausesLocationUpdatesAutomatically = false;

            // iOS 8 has additional permissions requirements
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                locMgr.RequestAlwaysAuthorization (); // works in background
                //locMgr.RequestWhenInUseAuthorization (); // only in foreground
            }

            if (UIDevice.CurrentDevice.CheckSystemVersion (9, 0)) {
                locMgr.AllowsBackgroundLocationUpdates = true;
            }
        }

        public CLLocationManager LocMgr {
            get { return this.locMgr; }
        }
    }
    ```

    上述程式碼會在[CLLocationManager](xref:CoreLocation.CLLocationManager)類別上設定一些屬性和許可權:

    - `PausesLocationUpdatesAutomatically`–這是布林值, 可以根據系統是否允許暫停位置更新來設定。 在某些裝置上, 它`true`會預設為, 這可能會導致裝置在大約15分鐘後停止取得背景位置更新。
    - `RequestAlwaysAuthorization`-您應該傳遞這個方法, 為應用程式使用者提供允許在背景存取位置的選項。 `RequestWhenInUseAuthorization`如果您想要讓使用者選擇只在應用程式位於前景時才允許存取位置, 也可以傳遞。
    - `AllowsBackgroundLocationUpdates`–這是在 iOS 9 中引進的布林值屬性, 可設定為允許應用程式在暫停時接收位置更新。

    > [!IMPORTANT]
    > iOS 8 (和更新版本) 也需要**plist**檔案中的專案, 以在授權要求中顯示使用者。

1. 加入索引鍵`NSLocationAlwaysUsageDescription` , `NSLocationWhenInUseUsageDescription`或包含會向使用者顯示的字串, 該警示會要求位置資料存取。

1. iOS `AllowsBackgroundLocationUpdates` 9 會在使用資訊時要求 **。 plist**包含具有值`UIBackgroundModes` `location`的索引鍵。 如果您已完成本逐步解說的步驟 2, 這應該已經在您的 plist 檔案中。


1. 在類別內, 使用下列程式碼`StartLocationUpdates`建立名為的方法。 `LocationManager` 這段程式碼示範如何從`CLLocationManager`開始接收位置更新:

    ```csharp
    if (CLLocationManager.LocationServicesEnabled) {
        //set the desired accuracy, in meters
        LocMgr.DesiredAccuracy = 1;
        LocMgr.LocationsUpdated += (object sender, CLLocationsUpdatedEventArgs e) =>
        {
            // fire our custom Location Updated event
            LocationUpdated (this, new LocationUpdatedEventArgs (e.Locations [e.Locations.Length - 1]));
        };
        LocMgr.StartUpdatingLocation();
    }
    ```

    在此方法中, 有幾個重要的事會發生。 首先, 我們會執行檢查, 確認應用程式是否能夠存取裝置上的位置資料。 我們藉由`CLLocationManager`在上`LocationServicesEnabled`呼叫來驗證這一點。 如果使用者拒絕了存取位置資訊的應用程式, 這個方法就會傳回**false** 。

1. 接下來, 告訴位置管理員要更新的頻率。 `CLLocationManager`提供許多用於篩選和設定位置資料的選項, 包括更新的頻率。 在此範例中, 請`DesiredAccuracy`將設定為在每次由計量變更時進行更新。 如需設定位置更新頻率和其他喜好設定的詳細資訊, 請參閱 Apple 檔中的[CLLocationManager 類別參考](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html)。

1. 最後, `StartUpdatingLocation` `CLLocationManager`在實例上呼叫。 這會告訴位置管理員取得目前位置的初始修正, 並開始傳送更新

到目前為止, 已建立位置管理員, 並以我們想要接收的資料類型來設定, 並決定了初始位置。 現在程式碼必須將位置資料轉譯為使用者介面。 我們可以使用接受`CLLocation`做為引數的自訂事件來執行此動作:

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

下一個步驟是從`CLLocationManager`訂閱位置更新, 並在有新的位置資料可用時引發自訂事件, 並傳入位置做為自`LocationUpdated`變數。 若要這麼做, 請建立新的類別**LocationUpdateEventArgs.cs**。 此程式碼可在主要應用程式中存取, 並會在事件引發時傳回裝置位置:

```csharp
public class LocationUpdatedEventArgs : EventArgs
{
    CLLocation location;

    public LocationUpdatedEventArgs(CLLocation location)
    {
       this.location = location;
    }

    public CLLocation Location
    {
       get { return location; }
    }
}
```

## <a name="user-interface"></a>使用者介面

1. 使用 iOS 設計工具來建立會顯示位置資訊的畫面。 按兩下**主要**的分鏡腳本檔案以開始。

    在分鏡腳本上, 將數個標籤拖曳到螢幕上, 以作為位置資訊的預留位置。 在此範例中, 有緯度、經度、海拔高度、課程和速度的標籤。

    版面配置應如下所示:

    ![](location-walkthrough-images/image8.png "IOS 設計工具中的範例 UI 版面配置")

1. 在 Solution Pad 中, 按兩下`ViewController.cs`檔案並加以編輯, 以建立 LocationManager 的新實例, 並在其上呼叫。 `StartLocationUpdates`
  變更程式碼, 如下所示:

    ```csharp
    #region Computed Properties
    public static bool UserInterfaceIdiomIsPhone {
        get { return UIDevice.CurrentDevice.UserInterfaceIdiom == UIUserInterfaceIdiom.Phone; }
    }

    public static LocationManager Manager { get; set;}
    #endregion

    #region Constructors
    public ViewController (IntPtr handle) : base (handle)
    {
    // As soon as the app is done launching, begin generating location updates in the location manager
        Manager = new LocationManager();
        Manager.StartLocationUpdates();
    }

    #endregion
    ```

    這會在應用程式啟動時啟動位置更新, 但不會顯示任何資料。

1. 現在已收到位置更新, 請使用位置資訊更新畫面。 下列方法會取得`LocationUpdated`事件的位置, 並將其顯示在 UI 中:

    ```csharp
    #region Public Methods
    public void HandleLocationChanged (object sender, LocationUpdatedEventArgs e)
    {
        // Handle foreground updates
        CLLocation location = e.Location;

        LblAltitude.Text = location.Altitude + " meters";
        LblLongitude.Text = location.Coordinate.Longitude.ToString ();
        LblLatitude.Text = location.Coordinate.Latitude.ToString ();
        LblCourse.Text = location.Course.ToString ();
        LblSpeed.Text = location.Speed.ToString ();

        Console.WriteLine ("foreground updated");
    }
    #endregion
    ```

我們仍然需要在我們的`LocationUpdated` AppDelegate 中訂閱事件, 並呼叫新的方法來更新 UI。 在`ViewDidLoad,` 呼叫`StartLocationUpdates`之後的右側新增下列程式碼:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


現在, 當應用程式執行時, 它看起來應該像這樣:

[![](location-walkthrough-images/image5.png "範例應用程式執行")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>處理現用和背景狀態

1. 當應用程式處於前景和作用中狀態時, 就會輸出位置更新。 若要示範當應用程式進入背景時所發生的狀況`AppDelegate` , 請覆寫追蹤應用程式狀態變更的方法, 讓應用程式在前景和背景之間轉換時寫入主控台:

    ```csharp
    public override void DidEnterBackground (UIApplication application)
    {
        Console.WriteLine ("App entering background state.");
    }

    public override void WillEnterForeground (UIApplication application)
    {
        Console.WriteLine ("App will enter foreground");
    }
    ```

    在中`LocationManager`新增下列程式碼, 以持續將更新的位置資料列印至應用程式輸出, 以確認位置資訊仍然可在背景中使用:

    ```csharp
    public class LocationManager
    {
        public LocationManager ()
        {
        ...
        LocationUpdated += PrintLocation;
        }
        ...

        //This will keep going in the background and the foreground
        public void PrintLocation (object sender, LocationUpdatedEventArgs e) {
        CLLocation location = e.Location;
        Console.WriteLine ("Altitude: " + location.Altitude + " meters");
        Console.WriteLine ("Longitude: " + location.Coordinate.Longitude);
        Console.WriteLine ("Latitude: " + location.Coordinate.Latitude);
        Console.WriteLine ("Course: " + location.Course);
        Console.WriteLine ("Speed: " + location.Speed);
        }
    }
    ```

1. 程式碼還有一個剩餘的問題: 嘗試在背景執行應用程式時更新 UI 時, 將會導致 iOS 終止它。 當應用程式進入背景時, 程式碼必須取消訂閱位置更新, 並停止更新 UI。

    當應用程式即將轉換成不同的應用程式狀態時, iOS 會提供通知給我們。 在此情況下, 我們可以訂閱`ObserveDidEnterBackground`通知。

    下列程式碼片段顯示如何使用通知, 讓此視圖知道何時會停止 UI 更新。 這會進入`ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    當應用程式正在執行時, 輸出看起來會像這樣:

    ![](location-walkthrough-images/image6.png "主控台中位置輸出的範例")

1. 應用程式會在前景中操作時, 將位置更新列印至螢幕, 並在背景操作時繼續將資料列印至應用程式輸出視窗。

只有一個未解決的問題會維持: 畫面會在第一次載入應用程式時啟動 UI 更新, 但無法得知應用程式重新進入前景的時機。 如果背景執行應用程式重新進入前景, UI 更新將不會繼續。

若要修正此問題, 請在另一個通知中嵌套呼叫來啟動 UI 更新, 這會在應用程式進入作用中狀態時引發:

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

現在 UI 會在第一次啟動應用程式時開始更新, 並在應用程式回到前景時繼續更新。

在本逐步解說中, 我們建立了一個運作正常的背景感知 iOS 應用程式, 可將位置資料列印至螢幕和應用程式輸出視窗。


## <a name="related-links"></a>相關連結

- [位置 (第4部分) (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/location)
- [核心位置架構參考](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
