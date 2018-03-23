---
title: 逐步解說-使用背景位置
ms.topic: article
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: efdabc5450b186c89785ee3cb19a3decdd6c2064
ms.sourcegitcommit: 7b76c3d761b3ffb49541e2e2bcf292de6587c4e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough---using-background-location"></a>逐步解說-使用背景位置

在此範例中，我們會建置 iOS 位置應用程式會列印我們目前位置的相關資訊： 緯度、 經度和螢幕其他參數。 此應用程式將示範如何在應用程式時作用中] 或 [Backgrounded 正確執行位置更新。

這個逐步解說說明 backgrounding 概念，包括做為背景必要應用程式中註冊應用程式、 暫止 UI 更新時 backgrounded 應用程式，以及使用某個索引鍵`WillEnterBackground`和`WillEnterForeground``AppDelegate`方法.

## <a name="application-set-up"></a>應用程式設定


1. 首先，建立新**iOS > 應用程式 > 單一檢視應用程式 (C#)**。 呼叫它_位置_，並確定 iPad 和 iPhone 已被選取。

1. 位置的應用程式限定為背景必要應用程式在 iOS 中。 做為位置的應用程式中註冊應用程式，藉由編輯**Info.plist**專案檔。

    在 [方案總管] 中，按兩下**Info.plist**檔案，以開啟它，然後捲動至清單的底部。 勾選這兩**啟用背景模式**和**位置更新**核取方塊。

    在 Visual Studio for Mac 它看起來像下面這樣：

    [![](location-walkthrough-images/image7.png "勾選 啟用背景模式和位置更新核取方塊")](location-walkthrough-images/image7.png#lightbox)

    在 Visual Studio **Info.plist**必須手動更新，可以將下列索引鍵/值組加入：

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. 既然註冊應用程式，它可以從裝置取得位置資料。 在 iOS 中，`CLLocationManager`類別用來存取位置資訊，並可以引發事件，提供位置更新。

1. 在程式碼中，建立新的類別稱為`LocationManager`，提供單一位置，以各種螢幕和位置更新訂閱的程式碼。 在`LocationManager`類別，請執行個體`CLLocationManager`呼叫`LocMgr`:

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

    上述程式碼上設定的屬性和權限的數字[CLLocationManager](https://developer.xamarin.com/api/type/CoreLocation.CLLocationManager/)類別：

    - `PausesLocationUpdatesAutomatically` – 這是布林值，您可以將根據系統是否允許暫停位置更新。 在某些裝置上，預設為`true`，而這可能導致裝置停止取得背景大約 15 分鐘之後的位置更新。
    - `RequestAlwaysAuthorization` -您應該傳遞此方法以提供應用程式使用者選項以允許在背景中存取的位置。 `RequestWhenInUseAuthorization` 也可以傳遞如果您想要授與使用者選項以允許應用程式位於前景時，才可存取的位置。
    - `AllowsBackgroundLocationUpdates` – 這是布林值屬性，可以設定為允許應用程式接收位置的更新時暫止的 iOS 9 中導入。

    > [!IMPORTANT]
    > iOS 8 （和大於） 也需要中的項目**Info.plist**檔案對使用者顯示的授權要求的一部分。

1. 加入的機碼`NSLocationAlwaysUsageDescription`或`NSLocationWhenInUseUsageDescription`加上字串將顯示給使用者的要求位置資料存取的警示。

1. iOS 9 需要使用時`AllowsBackgroundLocationUpdates` **Info.plist**包含索引鍵`UIBackgroundModes`值`location`。 如果您已經完成步驟 2，本逐步解說的這應該已被 Info.plist 檔案中。


1. 內部`LocationManager`類別，建立一個稱為方法`StartLocationUpdates`為下列程式碼。 此程式碼會示範如何開始接收來自位置更新`CLLocationManager`:

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

    有幾個重要的方法，這個方法中發生的情況。 首先，我們會執行檢查，以查看應用程式是否有在裝置上的位置資料的存取權。 我們驗證，請呼叫`LocationServicesEnabled`上`CLLocationManager`。 這個方法會傳回**false**如果使用者已拒絕應用程式存取的位置資訊。

1. 接下來，若要更新告訴位置管理員的頻率。 `CLLocationManager` 提供許多選項來篩選，以及設定位置資料，包括更新的頻率。 在此範例中，設定`DesiredAccuracy`的計量器的位置變更時，更新。 如需有關設定位置更新頻率和其他喜好設定的詳細資訊，請參閱[CLLocationManager 類別參考](http://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html)Apple 文件中。

1. 最後，呼叫`StartUpdatingLocation`上`CLLocationManager`執行個體。 這會告知在目前的位置上取得初始的修正程式，並開始將更新傳送位置管理員

目前為止，位置管理員建立之後，我們想要接收的資料類型設定並判定的初始位置。 現在程式碼需要呈現使用者介面則位置資料。 我們可以這樣做會採用一個自訂事件與`CLLocation`做為引數：

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

下一個步驟是從位置更新訂閱`CLLocationManager`，並引發自訂`LocationUpdated`新位置的資料可用時，傳遞做為引數位置中的事件。 若要這樣做，請建立新類別**LocationUpdateEventArgs.cs**。 這段程式碼中主應用程式存取和引發事件時傳回裝置位置：

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

1. 使用 iOS 設計工具建立的螢幕會顯示位置資訊。 按兩下**Main.storyboard**檔案開始。

    在腳本中，將多個標籤拖曳到螢幕做為預留位置的位置資訊。 在此範例中，有緯度、 經度、 海拔高度、 過程和速度的標籤。

    版面配置應該如下所示：

    ![](location-walkthrough-images/image8.png "在 iOS 設計工具中 UI 配置範例")

1. 在方案板中，按兩下`ViewController.cs`檔案，並編輯建立的新執行個體 LocationManager 以及呼叫`StartLocationUpdates`在其上。
  變更的程式碼如下所示：

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

    這會啟動位置更新應用程式啟動時，雖然會不顯示任何資料。

1. 現在，接收位置更新，以更新畫面位置資訊。 下列方法取得從位置我們`LocationUpdated`事件，並顯示在 UI 中：

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

我們仍需要訂閱`LocationUpdated`AppDelegate，以及呼叫新方法，更新 UI 的事件。 加入下列程式碼中的`ViewDidLoad,`之後`StartLocationUpdates`呼叫：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


現在，當應用程式執行時，看起來應該像這樣：

[![](location-walkthrough-images/image5.png "執行範例應用程式")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>處理作用中和背景的狀態

1. 在前景和作用中時，應用程式會輸出位置更新。 若要示範應用程式進入背景時，會發生什麼事，請覆寫`AppDelegate`狀態變更追蹤的應用程式的方法，讓應用程式寫入主控台的前景和背景之間轉換時：

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

    加入下列程式碼中的`LocationManager`持續列印更新的位置的應用程式輸出，以確認該位置資訊的資料是在背景中仍然可用：

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

1. 一個剩餘的問題的程式碼： 嘗試更新 UI，當應用程式 backgrounded 將原因 iOS 會終止。 當應用程式進入背景時，程式碼需要取消位置更新，以及停止更新 UI。

    iOS 我們時提供通知的應用程式即將轉換至不同的應用程式狀態。 在此情況下，我們可以訂閱`ObserveDidEnterBackground`通知。

    下列程式碼片段示範如何使用通知，告訴知道何時停止 UI 更新的檢視。 這就會進入`ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    當執行應用程式時，輸出看起來會像這樣：

    ![](location-walkthrough-images/image6.png "在主控台中的位置輸出範例")

1. 應用程式在前景中操作時，會列印至畫面的位置更新，並繼續在背景中操作時列印至應用程式的 [輸出] 視窗的資料。

只能有一個未處理的問題會維持： 螢幕啟動 UI 更新，在第一次載入應用程式，但它具有無從得知當應用程式已重新進入前景。 如果 backgrounded 應用程式會載入到前景，UI 更新將不會繼續。

若要修正此問題，建立巢狀呼叫，以啟動另一個通知，應用程式進入作用中狀態時，會引發在 UI 更新：

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

現在 UI 開始更新時第一次啟動應用程式，並繼續更新應用程式的任何時間恢復到前景。

在本逐步解說中，我們會建置列印至螢幕和應用程式的 [輸出] 視窗的位置資料的行為良好、 背景感知的 iOS 應用程式。


## <a name="related-links"></a>相關連結

- [位置 (第 4 部分) （範例）](https://developer.xamarin.com/samples/monotouch/Location/)
- [核心位置 Framework 參考](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
