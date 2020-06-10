---
title： " Xamarin.Forms 實驗性旗標" 描述： " Xamarin.Forms 實驗性旗標可讓工程小組更快速地將新功能交付給使用者，同時仍然能夠在他們移至穩定版本之前，先變更功能 api。」
assetid： AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：04/14/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-experimental-flags"></a>Xamarin.Forms實驗性旗標

當新 Xamarin.Forms 功能執行時，有時會放在實驗旗標後方。 這可讓工程小組更快速地為您提供新功能，同時仍可在將功能 Api 移至穩定版本之前加以變更。 一旦將功能移至穩定版本之後，就會移除實驗旗標。

Xamarin.Forms包含下列實驗旗標：

- `AppTheme_Experimental`
- `CarouselView_Experimental`
- `Expander_Experimental`
- `IndicatorView_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `RadioButton_Experimental`
- `Shell_UWP_Experimental`
- `StateTriggers_Experimental`
- `SwipeView_Experimental`

使用實驗旗標後方的功能時，您必須在應用程式中啟用旗標或旗標。 有兩種方法可以啟用實驗旗標：

- 在您的平臺專案中啟用實驗旗標或旗標。
- 在您的類別中啟用實驗旗標或旗標 `App` 。

> [!WARNING]
> 使用實驗性旗標後方的功能，而不啟用旗標，會導致應用程式擲回例外狀況，指出必須啟用的旗標。

## <a name="enable-flags-in-platform-projects"></a>啟用平臺專案中的旗標

`Xamarin.Forms.Forms.SetFlags`方法可以用來在您的平臺專案中啟用實驗旗標：

```csharp
Xamarin.Forms.Forms.SetFlags("CarouselView_Experimental");
```

在 `SetFlags` `AppDelegate` IOS、Android 上的 `MainActivity` 類別中，以及您在 UWP 的類別中，應在您的類別中叫用方法 `App` 。

> [!IMPORTANT]
> 在您的平臺專案中啟用實驗旗標必須在叫用方法之前發生 `Forms.Init` 。

`Xamarin.Forms.Forms.SetFlags`方法 `string` 會接受陣列引數，讓您可以在單一方法呼叫中啟用多個實驗旗標：

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "IndicatorView_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> 請勿多次呼叫 `SetFlags` 方法，因為後續呼叫將會覆寫先前呼叫的結果。

## <a name="enable-flags-in-your-app-class"></a>啟用應用程式類別中的旗標

`Device.SetFlags`方法可以用來在 `App` 您的共用程式碼專案中啟用類別中的實驗旗標：

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

`Device.SetFlags`方法 `IReadOnlyList<string>` 會接受引數，讓您可以在單一方法呼叫中啟用多個實驗旗標：

```csharp
Device.SetFlags(new string[]{ "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> 請勿多次呼叫 `SetFlags` 方法，因為後續呼叫將會覆寫先前呼叫的結果。
