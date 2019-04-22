---
title: 逐步解說-在 Xamarin.iOS 中的背景位置
description: 本文件提供如何使用位置資訊 backgrounded 的 Xamarin.iOS 應用程式中的逐步解說。 它會描述必要的安裝，使用者介面和應用程式狀態。
ms.prod: xamarin
ms.assetid: F8EEA0FD-5614-47FE-ADAC-80A5BCA6EB5F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: fa8a48e165764a449af4bc5414d2e66aecea8269
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870140"
---
# <a name="walkthrough---background-location-in-xamarinios"></a>逐步解說-在 Xamarin.iOS 中的背景位置

在此範例中，我們要建置 iOS 會列印目前位置的相關資訊的位置應用程式： 緯度、 經度和其他參數到畫面。 此應用程式將示範如何在應用程式使用中或 Backgrounded 時正確地執行位置更新。

這個逐步解說會說明背景概念，包括註冊為背景必要應用程式的應用程式、 暫止的 UI 更新，當應用程式在背景，以及使用某個索引鍵`WillEnterBackground`並`WillEnterForeground``AppDelegate`方法.

## <a name="application-set-up"></a>設定應用程式


1. 首先，建立新**iOS > 應用程式 > 單一檢視應用程式 (C#)**。 稱之為_位置_，並確保 iPad 和 iPhone 已被選取。

1. 位置的應用程式可視為在 iOS 中的背景必要應用程式。 做為位置的應用程式註冊的應用程式，藉由編輯**Info.plist**專案檔。

    在 [方案總管] 中，按兩下**Info.plist**檔案，以開啟它，然後捲動至清單底部。 勾選這兩**啟用背景模式**並**位置更新**核取方塊。

    在 Visual Studio for Mac 中，它看起來像如下：

    [![](location-walkthrough-images/image7.png "勾選 啟用背景模式和位置更新核取方塊")](location-walkthrough-images/image7.png#lightbox)

    在 Visual Studio 中， **Info.plist**必須手動更新，新增下列索引鍵/值組：

    ```xml
    <key>UIBackgroundModes</key>
    <array>
      <string>location</string>
    </array>
    ```

1. 現在，應用程式註冊時，它可以從裝置取得位置資料。 在 iOS 中，`CLLocationManager`類別用來存取位置資訊，並可以引發事件，提供位置更新。

1. 在程式碼中，建立新的類別，稱為`LocationManager`，提供單一的位置，讓某些畫面與訂閱位置更新的程式碼。 在 `LocationManager`類別中，執行個體的製作`CLLocationManager`稱為`LocMgr`:

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

    上述程式碼上設定一些屬性和權限[CLLocationManager](xref:CoreLocation.CLLocationManager)類別：

    - `PausesLocationUpdatesAutomatically` – 這是布林值，可以根據系統是否可以暫停位置更新設定。 在某些裝置上則會預設為`true`，這可能會導致裝置停止收到背景位置更新，大約 15 分鐘之後。
    - `RequestAlwaysAuthorization` -您應傳入這個方法，以提供應用程式使用者選項，以允許在背景中存取的位置。 `RequestWhenInUseAuthorization` 也可以傳遞如果您想要授與使用者選項，以允許要在應用程式位於前景時，才可存取的位置。
    - `AllowsBackgroundLocationUpdates` – 這是布林值屬性，可以設定為允許應用程式接收位置更新時暫止的 iOS 9 中導入。

    > [!IMPORTANT]
    > iOS 8 （和更新版本） 也會要求中的項目**Info.plist**檔案，以向使用者顯示授權要求的一部分。

1. 新增金鑰`NSLocationAlwaysUsageDescription`或`NSLocationWhenInUseUsageDescription`向使用者要求位置資料的存取權的警示中顯示的字串。

1. iOS 9 需要使用時`AllowsBackgroundLocationUpdates` **Info.plist**包含索引鍵`UIBackgroundModes`值`location`。 如果您已經完成本逐步解說的步驟 2，這應該已被 Info.plist 檔案中。


1. 內部`LocationManager`類別中，建立一個方法，叫做`StartLocationUpdates`為下列程式碼。 此程式碼示範如何以開始接收位置更新從`CLLocationManager`:

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

    有數個重要的事項，這個方法中發生的情況。 首先，我們會執行檢查，以查看應用程式是否有在裝置上的位置資料的存取權。 我們藉由呼叫，確認這`LocationServicesEnabled`上`CLLocationManager`。 這個方法會傳回**false**如果使用者已拒絕應用程式存取位置資訊。

1. 接下來，告訴位置管理員的頻率更新。 `CLLocationManager` 提供許多選項來篩選和設定的位置資料，包括更新的頻率。 在此範例中，設定`DesiredAccuracy`位置變更的計量，更新。 如需有關設定位置更新頻率和其他喜好設定的詳細資訊，請參閱[CLLocationManager 類別參考](https://developer.apple.com/library/ios/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html)Apple 文件中。

1. 最後，呼叫`StartUpdatingLocation`上`CLLocationManager`執行個體。 這會告訴位置有更多的管理員以取得目前的位置中，初始的修正程式，並開始傳送更新

到目前為止，位置管理員建立之後，我們想要接收的資料類型設定，並已判定的初始位置。 現在程式碼需要呈現使用者介面的位置資料。 我們可以這樣做會以自訂事件`CLLocation`做為引數：

```csharp
// event for the location changing
public event EventHandler<LocationUpdatedEventArgs>LocationUpdated = delegate { };
```

下一個步驟是從位置更新訂閱`CLLocationManager`，並引發自訂`LocationUpdated`事件位置的新資料可用時，傳遞做為引數的位置。 若要這樣做，請建立新的類別**LocationUpdateEventArgs.cs**。 此程式碼中主要的應用程式存取，並引發事件時，會傳回裝置位置：

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

1. 您可以使用 iOS 設計工具，建置會顯示位置資訊的畫面。 按兩下**Main.storyboard**檔案開始。

    在腳本中，將多個標籤拖曳到螢幕上以做為輸入位置資訊的預留位置。 在此範例中，有的緯度、 經度、 高度、 課程和速度的標籤。

    版面配置應如下所示：

    ![](location-walkthrough-images/image8.png "在 iOS 設計工具 UI 配置範例")

1. 在 Solution Pad 中，按兩下`ViewController.cs`檔案，並加以編輯以建立新的執行個體的呼叫與 LocationManager`StartLocationUpdates`在其上。
  變更程式碼如下所示：

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

    這會在應用程式啟動時，啟動位置更新，雖然會不顯示任何資料。

1. 現在，接收位置更新，請使用位置資訊更新畫面。 下列方法取得的位置，從我們`LocationUpdated`事件，並顯示在 UI 中：

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

我們仍需要訂閱`LocationUpdated`AppDelegate，以及呼叫新方法來更新 UI 的事件。 新增下列程式碼`ViewDidLoad,`後緊接著`StartLocationUpdates`呼叫：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // It is better to handle this with notifications, so that the UI updates
    // resume when the application re-enters the foreground!
    Manager.LocationUpdated += HandleLocationChanged;

}
```


現在，當應用程式執行時，它應該看起來像這樣：

[![](location-walkthrough-images/image5.png "執行範例應用程式")](location-walkthrough-images/image5.png#lightbox)

## <a name="handling-active-and-background-states"></a>處理作用中，背景狀態

1. 應用程式在前景中和作用中時，會輸出位置更新。 若要示範應用程式進入背景時，會發生什麼事，覆寫`AppDelegate`追蹤應用程式的方法狀態變更，以便應用程式在前景和背景之間轉換時，要寫入至主控台：

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

    新增下列程式碼中的`LocationManager`持續列印已更新的位置到應用程式輸出，以確認該位置資訊的資料是在背景中仍然可用：

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

1. 一個使用程式碼的其餘問題： 嘗試更新 UI，當應用程式在背景將原因 iOS 會終止。 當應用程式進入背景時，程式碼需要取消訂閱位置更新，並停止更新 UI。

    iOS 我們時提供通知應用程式即將轉換為不同的應用程式狀態。 在此情況下，我們可以訂閱`ObserveDidEnterBackground`通知。

    下列程式碼片段示範如何使用通知，讓知道何時要暫止的 UI 更新的檢視。 這就會進入`ViewDidLoad`:

    ```csharp
    UIApplication.Notifications.ObserveDidEnterBackground ((sender, args) => {
        Manager.LocationUpdated -= HandleLocationChanged;
    });
    ```

    當應用程式執行時，輸出會看起來像這樣：

    ![](location-walkthrough-images/image6.png "在主控台中的位置輸出範例")

1. 應用程式在前景，操作時，會列印至畫面的位置更新，並繼續來列印時在背景運作的應用程式的 [輸出] 視窗的資料。

仍然只有一個未處理的問題： 第一次載入應用程式，但它有沒有辦法知道當應用程式已重新進入前景時，畫面會啟動 UI 更新。 如果 backgrounded 應用程式帶回前景，不會繼續 UI 更新。

若要修正此問題，建立巢狀呼叫，以啟動另一個通知，應用程式處於作用中狀態時都會引發內的 UI 更新︰

```csharp
UIApplication.Notifications.ObserveDidBecomeActive ((sender, args) => {
  Manager.LocationUpdated += HandleLocationChanged;
});
```

現在 UI 會開始更新時第一次啟動應用程式，並繼續更新應用程式隨時回到前景。

在本逐步解說中，我們會建立會列印在螢幕和應用程式的 [輸出] 視窗的位置資料的行為良好的背景感知的 iOS 應用程式。


## <a name="related-links"></a>相關連結

- [位置 (第 4 部分) （範例）](https://developer.xamarin.com/samples/monotouch/Location/)
- [核心位置 Framework 參考](https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CoreLocation_Framework/_index.html)
