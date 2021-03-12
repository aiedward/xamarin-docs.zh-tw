---
title: 逐步解說-Xamarin 中的背景位置
description: 本檔提供如何在背景執行的 Xamarin iOS 應用程式中使用位置資訊的逐步解說。 它描述必要的設定、使用者介面和應用程式狀態。
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 6bd93fe50dc97c2349486513079768c473355e76
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602979"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>逐步解說-Xamarin 中的背景位置

在此範例中，我們將建立 iOS 位置應用程式，以將目前位置的相關資訊（緯度、經度和其他參數）列印到螢幕。 此應用程式將示範如何在應用程式為作用中或背景執行時，適當地執行位置更新。

本逐步解說將說明一些重要的背景處理概念，包括將應用程式註冊為背景必要的應用程式、在背景執行應用程式時暫停 UI 更新，以及使用 `WillEnterBackground` 和 `WillEnterForeground` `AppDelegate` 方法。

## <a name="application-set-up"></a>應用程式設定

1. 首先，建立新的 **iOS > 應用程式 > 單一視圖應用程式 (c # )**。 呼叫它的 _位置_ ，並確保已選取 IPad 和 iPhone。

1. 位置應用程式在 iOS 中符合背景必要的應用程式。 藉由編輯專案的 **plist** 檔案，將應用程式註冊為位置應用程式。

    在 [方案 Explorer] 下，按兩下 **plist** 檔案將它開啟，並將其向下移動至清單底部。 勾選 [ **啟用背景模式** ] 和 [ **位置更新** ] 核取方塊。

    在 Visual Studio for Mac 中，它看起來會像這樣：

    [![勾選 [啟用背景模式] 和 [位置更新] 核取方塊](location-walkthrough-images/image7.png)](location-walkthrough-images/image7.png#lightbox)

    在 Visual Studio 中， **plist** 必須藉由新增下列索引鍵/值組，以手動方式更新：

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. 現在已註冊應用程式，它可以從裝置取得位置資料。 在 iOS 中， `CLLocationManager` 類別是用來存取位置資訊，而且可以引發提供位置更新的事件。

1. 在程式碼中，建立名為的新類別 `LocationManager` ，以提供單一位置給各種畫面和程式碼，以訂閱位置更新。 在 `LocationManager` 類別中，將呼叫的實例設 `CLLocationManager` 為 `LocMgr` ：

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

    上述程式碼會設定 [CLLocationManager](xref:CoreLocation.CLLocationManager) 類別的一些屬性和許可權：

    - `PausesLocationUpdatesAutomatically` –這是一個布林值，可以根據系統是否允許暫停位置更新來設定。 在某些裝置上，它會預設為 `true` ，這可能會導致裝置在大約15分鐘後停止取得背景位置更新。
    - `RequestAlwaysAuthorization` -您應該傳遞這個方法，讓應用程式使用者選擇允許在背景中存取位置。 `RequestWhenInUseAuthorization` 如果您想要讓使用者能夠選擇只有在應用程式在前景時才能存取位置，也可以傳遞。
    - `AllowsBackgroundLocationUpdates` –這是在 iOS 9 中引進的布林值屬性，可設定為允許應用程式在暫停時接收位置更新。

    > [!IMPORTANT]
    > iOS 8 (和更高的) 也需要 **plist** 檔案中的專案，以將使用者顯示為授權要求的一部分。

1. 針對應用程式所需的許可權類型，新增 **plist** 的金鑰– `NSLocationAlwaysUsageDescription` 、 `NSLocationWhenInUseUsageDescription` 和/或 `NSLocationAlwaysAndWhenInUseUsageDescription` –以及將在要求位置資料存取的警示中顯示給使用者的字串。

1. 當使用 `AllowsBackgroundLocationUpdates` **plist** 包含 `UIBackgroundModes` 具有值的索引鍵時，iOS 9 會要求您 `location` 這樣做。 如果您已完成此逐步解說的步驟2，這應該已經在您的 plist 檔案中。

1. 在 `LocationManager` 類別內，使用下列程式碼建立呼叫的方法 `StartLocationUpdates` 。 此程式碼會示範如何開始接收位置更新   `CLLocationManager` ：

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

    這種方法有幾個重要的事情。 首先，我們會執行檢查，以查看應用程式是否有權存取裝置上的位置資料。 我們藉由在上呼叫來確認這一點 `LocationServicesEnabled` `CLLocationManager` 。 如果使用者已拒絕應用程式對位置資訊的存取，則此方法會傳回 **false** 。

1. 接下來，告訴定位管理員要更新的頻率。 `CLLocationManager` 提供許多選項來篩選和設定位置資料，包括更新的頻率。 在此範例中，請將設定 `DesiredAccuracy` 為在每次由計量變更時更新。 如需設定位置更新頻率和其他喜好設定的詳細資訊，請參閱 Apple 檔中的 [CLLocationManager 類別參考](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html) 。

1. 最後， `StartUpdatingLocation` 在實例上呼叫 `CLLocationManager` 。 這會指示位置管理員取得目前位置的初始修正，以及開始傳送更新

到目前為止，位置管理員已建立，並以我們想要接收的資料類型進行設定，並決定了初始位置。 現在程式碼需要將位置資料轉譯至使用者介面。 我們可以使用接受作為引數的自訂事件來執行此動作 `CLLocation` ：

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

下一步是訂閱的位置更新 `CLLocationManager` ，並在 `LocationUpdated` 新的位置資料可供使用時引發自訂事件，以引數形式傳入位置。 若要這樣做，請建立新的類別 **LocationUpdateEventArgs.cs**。 此程式碼可在主應用程式記憶體取，並會在引發事件時傳回裝置位置：

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

1. 使用 Xcode 介面產生器來建立將顯示位置資訊的畫面。 按兩下 **主要** 的分鏡腳本檔案以開始。

    在腳本中，將數個標籤拖曳到螢幕上，作為位置資訊的預留位置。 在此範例中，緯度、經度、海拔高度、課程和速度都有標籤。

    如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](~/ios/user-interface/storyboards/index.md)。

1. 在 Solution Pad 中，按兩下該檔案 `ViewController.cs` 並加以編輯，以建立 LocationManager 的新實例，並 `StartLocationUpdates` 對其進行呼叫。
  變更程式碼，如下所示：

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

    這將會啟動應用程式啟動時的位置更新，但不會顯示任何資料。

1. 現在已收到位置更新，請使用位置資訊來更新畫面。 下列方法會取得來自事件的位置 `LocationUpdated` ，並將其顯示在 UI 中：

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

我們仍然需要訂閱 `LocationUpdated` AppDelegate 中的事件，並呼叫新方法來更新 UI。 在呼叫之後，于右邊新增下列程式碼 `ViewDidLoad,` `StartLocationUpdates` ：

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

[![執行範例應用程式](location-walkthrough-images/image5.png)](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>處理作用中和背景狀態

1. 當應用程式在前景和作用中時，會將位置更新輸出。 為了示範當應用程式進入背景時會發生什麼事，請覆寫 `AppDelegate` 追蹤應用程式狀態變更的方法，讓應用程式在前景與背景之間轉換時，寫入主控台：

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

    在中加入下列程式碼 `LocationManager` ，以持續將更新的位置資料列印到應用程式輸出，以確認在背景中仍可使用位置資訊：

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

1. 程式碼有一個剩餘的問題：當應用程式背景執行時嘗試更新 UI 將會導致 iOS 終止。 當應用程式進入背景時，程式碼需要取消訂閱位置更新並停止更新 UI。

    iOS 會在應用程式即將轉換至不同的應用程式狀態時，提供通知給我們。 在此情況下，我們可以訂閱 `ObserveDidEnterBackground` 通知。

    下列程式碼片段示範如何使用通知，讓 view 知道何時要暫停 UI 更新。 這將會進入 `ViewDidLoad` ：

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    當應用程式正在執行時，輸出看起來會像這樣：

    ![主控台中的位置輸出範例](location-walkthrough-images/image6.png)

1. 應用程式會在前景中操作時，將位置更新列印到螢幕，並在背景操作時繼續將資料列印到應用程式輸出視窗。

只有一個未解決的問題會保持：當應用程式第一次載入時，畫面會啟動 UI 更新，但無法得知應用程式何時重新進入前景。 如果背景執行應用程式回到前景，UI 更新將不會繼續。

若要修正此問題，請在另一個通知中嵌套可啟動 UI 更新的呼叫，這會在應用程式進入作用中狀態時引發：

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

現在，當應用程式第一次啟動時，UI 會開始更新，並在應用程式重新進入前景時繼續更新。

在這個逐步解說中，我們建立了一個運作正常、背景感知的 iOS 應用程式，將位置資料列印到畫面和應用程式輸出視窗。

## <a name="related-links"></a>相關連結

- [位置 (第4部分)  (範例) ](/samples/xamarin/ios-samples/location)
- [核心位置架構參考](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)