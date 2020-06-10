---
標題：「 Xamarin.Essentials 平臺延伸模組」描述：」 Xamarin.Essentials 提供數種平臺擴充方法，以使用像是矩形、大小和點等平臺類型。」
assetid： AB4D198A-4FD7-479E-8627-01F887A6D056 author： jamesmontemagno ms-chap： jamont ms. date： 03/13/2019 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials：平臺擴充功能

Xamarin.Essentials當必須使用像是矩形、大小和點等平臺類型時，提供數種平臺擴充方法。 這代表您可在 `System` 版的這些類型 (其 iOS、Android 及 UWP 特定類型) 間轉換。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>使用平台擴充

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

全部的平台擴充模組皆僅可從 iOS、Android 或 UWP 專案呼叫。

## <a name="android-extensions"></a>Android 擴充功能

這些延伸模組只能從 Android 專案存取。

### <a name="application-context--activity"></a>應用程式內容 & 活動

您可以使用類別中的平臺擴充功能， `Platform` 取得目前 `Context` 或 `Activity` 執行中應用程式的存取權。

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

如果有需要的情況 `Activity` ，但應用程式尚未完全啟動，則 `WaitForActivityAsync` 應使用方法。

```csharp
var activity = await Platform.WaitForActivityAsync();
```

### <a name="activity-lifecycle"></a>活動開發週期

除了取得目前的活動之外，您也可以註冊生命週期事件。

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);

    Xamarin.Essentials.Platform.Init(this, bundle);

    Xamarin.Essentials.Platform.ActivityStateChanged += Platform_ActivityStateChanged;
}

protected override void OnDestroy()
{
    base.OnDestroy();
    Xamarin.Essentials.Platform.ActivityStateChanged -= Platform_ActivityStateChanged;
}

void Platform_ActivityStateChanged(object sender, Xamarin.Essentials.ActivityStateChangedEventArgs e) =>
    Toast.MakeText(this, e.State.ToString(), ToastLength.Short).Show();
```

活動狀態如下：

* 建立時間
* 已繼續
* 已暫停
* 終結
* SaveInstanceState
* 已開始
* 已停止

若要深入瞭解，請閱讀[活動生命週期](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/)檔。

## <a name="ios-extensions"></a>iOS 延伸模組

這些延伸模組只能從 iOS 專案存取。

### <a name="current-uiviewcontroller"></a>目前的 UIViewController

取得目前可見的存取權 `UIViewController` ：

```csharp
var vc = Platform.GetCurrentUIViewController();
```

如果無法偵測到，這個方法會傳回 `null` `UIViewController` 。

## <a name="cross-platform-extensions"></a>跨平臺擴充功能

這些擴充功能存在於每個平臺中。

### <a name="point"></a>Point

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformPoint();

// Back to System.Drawing.Point
var system2 = platform.ToSystemPoint();
```

### <a name="size"></a>大小

```csharp
var system = new System.Drawing.Size(width, height);

// Convert to CoreGraphics.CGSize, Android.Util.Size, and Windows.Foundation.Size
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="rectangle"></a>矩形

```csharp
var system = new System.Drawing.Rectangle(x, y, width, height);

// Convert to CoreGraphics.CGRect, Android.Graphics.Rect, and Windows.Foundation.Rect
var platform = system.ToPlatformRectangle();

// Back to System.Drawing.Rectangle
var system2 = platform.ToSystemRectangle();
```

## <a name="api"></a>API

- [轉換器原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [點轉換器 API 文件](xref:Xamarin.Essentials.PointExtensions)
- [矩形轉換器 API 文件](xref:Xamarin.Essentials.RectangleExtensions)
- [大小轉換器 API 文件](xref:Xamarin.Essentials.SizeExtensions)
