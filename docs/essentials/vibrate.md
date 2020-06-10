---
title： " Xamarin.Essentials ：震動" 描述： "本檔描述中的震動類別 Xamarin.Essentials ，可讓您在所需的時間量內啟動和停止震動功能。"
assetid： 7E8B24C4-2625-4DAE-A129-383542D34F1E author： jamesmontemagno ms. custom： video ms. 作者： jamont ms. date： 11/04/2018 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials：震動

**震動**類別可讓您在所需的時間內啟動和停止震動功能。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取**震動**功能，需要下列平台特定設定。

# <a name="android"></a>[Android](#tab/android)

需要震動權限，而且必須在 Android 專案中設定。 能以下列方式新增：

開啟 [Properties]**** 資料夾下的 **AssemblyInfo.cs** 檔案並新增：

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

或更新 Android 資訊清單：

開啟 [ **Properties** ] 資料夾下的**AndroidManifest.xml**檔案，並在**資訊清單**節點內新增下列內容。

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

禍以滑鼠右鍵按一 Android 專案並開啟專案的屬性。 在 [Android 資訊清單]**** 下，尋找 [必要權限]**** 區域並選取 [震動]**** 權限。 這將會自動更新 **AndroidManifest.xml** 檔案。

# <a name="ios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

## <a name="using-vibration"></a>使用震動

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

可以要求震動功能一段時間或預設的 500 毫秒。

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

可以使用 `Cancel` 方法要求取消裝置震動：

```csharp
try
{
    Vibration.Cancel();
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="platform-differences"></a>平台差異

# <a name="android"></a>[Android](#tab/android)

無平台差異。

# <a name="ios"></a>[iOS](#tab/ios)

- 僅在裝置設定為「鈴響時震動」時震動。
- 一律震動 500 毫秒。
- 無法取消震動。

# <a name="uwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

## <a name="api"></a>API

- [震動原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [震動 API 文件](xref:Xamarin.Essentials.Vibration)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Vibration-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
