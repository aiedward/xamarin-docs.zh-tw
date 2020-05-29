---
title： " Xamarin.Essentials ：電池" 描述： "本檔描述中的電池類別 Xamarin.Essentials ，可讓您檢查裝置的電池資訊並監視變更。"
assetid： 47EB26D8-8C62-477B-A13C-6977F74E6E43 author： jamesmontemagno ms-chap： jamont ms. date： 01/22/2019 ms. custom： video no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinessentials-battery"></a>Xamarin.Essentials：電池

**Battery** 類別可讓您檢查裝置的電池資訊和監視變更，並提供裝置的省電狀態資訊，這說明裝置是否正在低耗電模式中執行。 若裝置已開啟低電源狀態，應用程式應該避免背景處理。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取**電池**功能，需要下列平台特定設定。

# <a name="android"></a>[Android](#tab/android)

需要 `Battery` 權限，而且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties]**** 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

或更新 Android 資訊清單：

開啟**Properties**資料夾底下的**androidmanifest.xml** ，並在**資訊清單**節點內新增下列內容。

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [Android 資訊清單]**** 下，尋找 [必要權限]**** 區域並選取 [電池]**** 權限。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="ios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwp"></a>[UWP](#tab/uwp)

不需要進行額外設定。

-----

## <a name="using-battery"></a>使用電池

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

檢查目前的電池資訊：

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or 1.0 when on AC or no battery.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.AC:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

每當任一電池的屬性變更時，會觸發事件：

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryInfoChanged += Battery_BatteryInfoChanged;
    }

    void Battery_BatteryInfoChanged(object sender, BatteryInfoChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

以電池電力運作的裝置可能會進入低電源省電狀態。 有時候裝置會自動切換到此狀態，例如當電池電力低於總容量的 20% 時。 針對省電模式，作業系統會減少可能會耗電的活動。 當進入省電模式時，應用程式也可以透過避免進行背景處理或執行其他耗電量大的活動，以降低耗電量。

您也可以使用靜態 `Battery.EnergySaverStatus` 屬性取得裝置目前的省電狀態：

```csharp
// Get energy saver status
var status = Battery.EnergySaverStatus;
```

此屬性會傳回 `EnergySaverStatus` 列舉的成員，亦即 `On`, `Off` 或 `Unknown`。 若屬性傳回 `On`，應用程式應該避免進行背景處理或執行其他耗電量大的活動。

應用程式也應該安裝事件處理常式。 **Power** 類別會公開當省電狀態變更時所觸發的事件：

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Battery.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

若省電狀態變更為 `On`，應用程式應該停止執行背景處理。 若狀態變更為 `Unknown` 或 `Off`，應用程式可以繼續進行背景處理。

## <a name="platform-differences"></a>平台差異

# <a name="android"></a>[Android](#tab/android)

無平台差異。

# <a name="ios"></a>[iOS](#tab/ios)

- 裝置必須用來測試 API。
- 針對 `PowerSource`，只會傳回 `AC` 或 `Battery`。

# <a name="uwp"></a>[UWP](#tab/uwp)

- 針對 `PowerSource`，只會傳回 `AC` 或 `Battery`。

-----

## <a name="api"></a>API

- [電池原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [電池 API 文件](xref:Xamarin.Essentials.Battery)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Battery-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
