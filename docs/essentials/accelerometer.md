---
標題： ' ' Xamarin.Essentials ：加速計 ' ' 描述： ' 中的加速計類別 Xamarin.Essentials 可讓您監視裝置的加速計感應器，這表示裝置在三維空間中的加速度。
assetid： author： ms-chap： ms. date： ms. custom： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinessentials-accelerometer"></a>Xamarin.Essentials：加速計

**Accelerometer** 類別可讓您監視裝置的加速計感應器，此感應器指出裝置在三維空間中的加速度。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-accelerometer"></a>使用 Accelerometer

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

Accelerometer 功能的運作方式是呼叫 `Start` 和 `Stop` 方法，以觀察加速度的變化。 所有變化都會透過 `ReadingChanged` 事件傳回。 以下是範例使用方式：

```csharp

public class AccelerometerTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public AccelerometerTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Accelerometer.ReadingChanged += Accelerometer_ReadingChanged;
    }

    void Accelerometer_ReadingChanged(object sender, AccelerometerChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: X: {data.Acceleration.X}, Y: {data.Acceleration.Y}, Z: {data.Acceleration.Z}");
        // Process Acceleration X, Y, and Z
    }

    public void ToggleAccelerometer()
    {
        try
        {
            if (Accelerometer.IsMonitoring)
              Accelerometer.Stop();
            else
              Accelerometer.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

Accelerometer 讀數會以 G 為單位回報。G 是一個重力單位，等於地球重力場所作用的重力 (9.81 m/s^2)。

座標系統的定義，相對於手機螢幕的預設方向。 當裝置的螢幕方向變更時，軸不會換位。

X 軸是指向右方的水平軸，Y 軸是指向上方的垂直軸，Z 軸則指向螢幕正面的外側。 在這個系統中，螢幕背面的座標會有負的 Z 值。

範例：

- 當裝置平放在桌上，然後從裝置左側往右側推時，x 加速度值為正。

- 當裝置平放在桌上時，加速度值為 +1.00 G 或 (+9.81 m/s^2)，也就是裝置加速度 (0 m/s^2) 減掉重力 (-9.81 m/s^2)，然後以 G 標準化。

- 當裝置平放在桌上，然後您以 A m/s^2 的加速度將它垂直向上拿起時，加速度值等於 A+9.81，也就是裝置加速度 (+A m/s^2) 減掉重力 (-9.81 m/s^2)，然後以 G 標準化。

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="api"></a>API

- [Accelerometer 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Accelerometer)
- [Accelerometer API 文件](xref:Xamarin.Essentials.Accelerometer)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Accelerometer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
