---
標題：「 Xamarin.Forms 材質視覺效果」描述：「 Xamarin.Forms 材質視覺效果可用來建立 Xamarin.Forms 在 iOS 和 Android 上大致上相同的應用程式。」
assetid： B774F68C-EF9E-49E1-B738-CDC64879ADA2 ms. 技術： xamarin-表單作者： profexorgeek ms. author： jusjohns ms. 日期：11/25/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-material-visual"></a>Xamarin.Forms材質視覺效果

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

[材質設計](https://material.io)是 Google 所建立的固定設計系統，其中規定了視圖和版面配置外觀與行為的大小、色彩、間距和其他層面。

Xamarin.Forms材質視覺效果可用來將材質設計規則套用至 Xamarin.Forms 應用程式，建立在 iOS 和 Android 上看起來大致相同的應用程式。 啟用材質視覺效果時，支援的視圖會採用相同的設計跨平臺，並建立一致的外觀與風格。

[![材質視覺螢幕擷取畫面](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

Xamarin.Forms在您的應用程式中啟用材質視覺效果的流程如下：

1. 新增[ Xamarin.Forms 。](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/)適用于 iOS 和 Android 平臺專案的視覺材質 NuGet 套件。 此 NuGet 套件會在 iOS 和 Android 上提供優化的材質設計轉譯器。 在 iOS 上，套件會提供[MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents)的可轉移相依性，這是適用于 IOS 的 Google[材質元件](https://material.io/develop/ios/)的 c # 系結。 在 Android 上，套件會提供組建目標，以確保您的 TargetFramework 已正確設定。
1. 在每個平臺專案中初始化材質視覺效果。 如需詳細資訊，請參閱[初始化材質視覺效果](#initialize-material-visual)。
1. [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) `Material` 在應該採用材質設計規則的任何頁面上，將屬性設定為，以建立材質視覺控制項。 如需詳細資訊，請參閱[使用材質](#apply-material-visual)轉譯器。
1. 選擇性自訂材質控制項。 如需詳細資訊，請參閱[自訂材質控制項](#customize-material-visual)。

> [!IMPORTANT]
> 在 Android 上，材質視覺效果所需的最低版本為5.0 （API 21）或更高版本，以及9.0 版（API 28）的 TargetFramework。 此外，您的平臺專案需要28.0.0 或更高版本的 Android 支援程式庫，而且其主題必須繼承自材質元件主題，或繼續繼承自 AppCompat 主題。 如需詳細資訊，請參閱[開始使用適用于 Android 的材質元件](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md)。

材質視覺效果目前支援下列控制項：

- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Button`](xref:Xamarin.Forms.Button)
- [`CheckBox`](xref:Xamarin.Forms.CheckBox)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

材質控制項是透過材質轉譯器來實現，其適用于材質設計規則。 在功能上，材質轉譯器與預設轉譯器並無不同。 如需詳細資訊，請參閱[自訂材質視覺效果](#customize-material-visual)。

## <a name="initialize-material-visual"></a>初始化材質視覺效果

安裝之後[ Xamarin.Forms 。Visual. 材質](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/)NuGet 套件，材質轉譯器必須在每個平臺專案中初始化。

在 iOS 上，這應該會在**AppDelegate.cs**中，藉由在 `Xamarin.Forms.FormsMaterial.Init` 方法*之後*叫用方法來進行 `Xamarin.Forms.Forms.Init` ：

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

在 Android 上，這應該會在**MainActivity.cs**中，藉由在 `Xamarin.Forms.FormsMaterial.Init` 方法*之後*叫用方法來進行 `Xamarin.Forms.Forms.Init` ：

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="apply-material-visual"></a>套用材質視覺效果

應用程式可以藉由將 [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) 頁面、版面配置或視圖上的屬性設定為，來啟用材質視覺效果 `Material` ：

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

將 `VisualElement.Visual` 屬性設定為，會 `Material` 指示您的應用程式使用材質視覺化轉譯器，而不是預設轉譯器。 [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)屬性可以設定為任何執行的類型 `IVisual` ，並 [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) 提供下列屬性的類別 `IVisual` ：

- `Default`–表示此視圖應該使用預設轉譯器來呈現。
- `MatchParent`–表示此視圖應該使用與其直接父系相同的轉譯器。
- `Material`–表示此視圖應該使用材質轉譯器來呈現。

> [!IMPORTANT]
> [`Visual`](xref:Xamarin.Forms.VisualElement.Visual)屬性會定義在類別中 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，而 views 會 `Visual` 從其父代繼承屬性值。 因此， `Visual` 在上設定屬性 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 可確保頁面中任何支援的視圖都會使用該視覺效果。 此外，您 `Visual` 可以在視圖上覆寫屬性。

下列螢幕擷取畫面顯示使用預設轉譯器呈現的使用者介面：

[![IOS 和 Android 上預設轉譯器的螢幕擷取畫面](material-visual-images/default-renderers.png "使用預設轉譯器的視圖")](material-visual-images/default-renderers-large.png#lightbox)

下列螢幕擷取畫面顯示使用材質轉譯器呈現的相同使用者介面：

[![IOS 和 Android 上的材質轉譯器螢幕擷取畫面](material-visual-images/material-renderers.png "使用材質轉譯器的視圖")](material-visual-images/material-renderers-large.png#lightbox)

預設轉譯器和材質轉譯器之間的主要可見差異（如下所示），是材質轉譯器會以大寫顯示 [`Button`](xref:Xamarin.Forms.Button) 文字，並將框線的角落四捨五入 [`Frame`](xref:Xamarin.Forms.Frame) 。 不過，材質轉譯器會使用原生控制項，因此，字型、陰影、色彩和提高許可權等區域的平臺可能還是會有使用者介面的差異。

> [!NOTE]
> 材質設計元件會嚴格遵守 Google 的指導方針。 因此，材質設計轉譯器會偏向大小和行為。 當您需要對樣式或行為有更大的控制權時，您仍然可以建立自己的[效果](~/xamarin-forms/app-fundamentals/effects/index.md)、[行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)或[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器，以達到所需的詳細資料。

## <a name="customize-material-visual"></a>自訂材質視覺效果

材質視覺效果 NuGet 套件是可實現控制項的轉譯器集合 Xamarin.Forms 。 自訂材質視覺控制項與自訂預設控制項相同。

當目標是要自訂現有的控制項時，效果是建議的技巧。 如果有材質視覺效果轉譯器，則自訂控制項的效果比較少，而不是將轉譯器設為子類別。 如需效果的詳細資訊，請參閱[ Xamarin.Forms 效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

當材質轉譯器不存在時，自訂轉譯器是建議的技術。 下列轉譯程式類別隨附于材質視覺效果中：

- `MaterialButtonRenderer`
- `MaterialCheckBoxRenderer`
- `MaterialEntryRenderer`
- `MaterialFrameRenderer`
- `MaterialProgressBarRenderer`
- `MaterialDatePickerRenderer`
- `MaterialTimePickerRenderer`
- `MaterialPickerRenderer`
- `MaterialActivityIndicatorRenderer`
- `MaterialEditorRenderer`
- `MaterialSliderRenderer`
- `MaterialStepperRenderer`

將材質轉譯器子類別化與非材質轉譯器幾乎相同。 不過，匯出子類別化材質轉譯器的轉譯器時，您必須提供第三個引數給 `ExportRenderer` 指定 `VisualMarker.MaterialVisual` 類型的屬性：

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        //...
    }
}
```

在此範例中， `ExportRendererAttribute` `CustomMaterialProgressBarRenderer` 會指定將用來呈現視圖的類別 [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) ，並將 `IVisual` 類型註冊為第三個引數。

> [!NOTE]
> 指定 `IVisual` 類型做為其一部分的轉譯器 `ExportRendererAttribute` 將用來轉譯加入宣告的視圖，而不是預設轉譯器。 在轉譯器選取時間， `Visual` 會檢查視圖的屬性，並將其包含在轉譯器選取進程中。

如需自訂轉譯器的詳細資訊，請參閱[自訂](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)轉譯器。

## <a name="related-links"></a>相關連結

- [材質視覺效果（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [建立 Xamarin.Forms 視覺效果轉譯器](create.md)
- [Xamarin.Forms效應](~/xamarin-forms/app-fundamentals/effects/index.md)
- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
