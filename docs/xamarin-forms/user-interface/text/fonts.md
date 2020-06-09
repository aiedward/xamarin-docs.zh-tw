---
標題：「中的字型」 Xamarin.Forms 描述：「本文將說明如何在顯示應用程式文字的控制項上指定字型資訊」 Xamarin.Forms 。
assetid： 49DD2249-C575-41AE-AE06-08F890FD6031 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：04/01/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="fonts-in-xamarinforms"></a>中的字型Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

本文說明如何 Xamarin.Forms 讓您在顯示文字的控制項上指定字型屬性（包括權數和大小）。 字型資訊可以[在程式碼中指定](#set-the-font-in-code)，或[在 XAML 中指定](#set-the-font-in-xaml)。 也可以使用[自訂字型](#use-a-custom-font)，並[顯示字型圖示](#display-font-icons)。

## <a name="set-the-font-in-code"></a>在程式碼中設定字型

使用顯示文字之任何控制項的三個字型相關屬性：

- **FontFamily** &ndash;`string`字型名稱。
- **FontSize** &ndash;當做的字型大小 `double` 。
- **FontAttributes** &ndash;字串，指定如*斜體*和**粗體**的樣式資訊（使用 `FontAttributes` c # 中的列舉）。

這段程式碼會示範如何建立標籤，並指定要顯示的字型大小和粗細：

```csharp
var about = new Label
{
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

### <a name="font-size"></a>字型大小

`FontSize`屬性可以設定為 double 值，例如：

```csharp
label.FontSize = 24;
```

大小值是以與裝置無關的單位來測量。 如需詳細資訊，請參閱[測量單位](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)。

Xamarin.Forms也會定義列舉中的欄位 [`NamedSize`](xref:Xamarin.Forms.NamedSize) ，以代表特定字型大小。 如需有關命名的字型大小的詳細資訊，請參閱[命名字型大小](#named-font-sizes)。

### <a name="font-attributes"></a>字型屬性

您可以在屬性上設定字型樣式（例如**粗體**和*斜體*） `FontAttributes` 。 目前支援下列值：

- **無**
- **黑**
- **斜體**

`FontAttribute`列舉可用如下所示（您可以指定單一屬性或 `OR` 一起使用）：

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>設定每個平臺的字型資訊

或者，您 `Device.RuntimePlatform` 可以使用屬性，在每個平臺上設定不同的字型名稱，如下列程式碼所示：

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

適用于 iOS 的字型資訊來源是[iosfonts.com](http://iosfonts.com)。

## <a name="set-the-font-in-xaml"></a>在 XAML 中設定字型

Xamarin.Forms顯示文字的控制項都具有 `FontSize` 可在 XAML 中設定的屬性。 在 XAML 中設定字型最簡單的方式是使用已命名的大小列舉值，如下列範例所示：

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

屬性有內建的轉換器，可 `FontSize` 讓所有字型設定以 XAML 中的字串值表示。 此外， `FontAttributes` 屬性也可以用來指定字型屬性：

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#provide-platform-specific-values)屬性也可以在 XAML 中用來在每個平臺上呈現不同的字型。 下列範例會在每個平臺上使用不同的字型：

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="named-font-sizes"></a>具名字型大小

Xamarin.Forms定義列舉中的欄位 [`NamedSize`](xref:Xamarin.Forms.NamedSize) ，以代表特定字型大小。 下表顯示 `NamedSize` 成員及其在 iOS、Android 和通用 Windows 平臺（UWP）上的預設大小：

| 成員 | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15.667 |
| `Small` | 13 | 14 | 18.667 |
| `Medium` | 16 | 17 | 22.667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

大小值是以與裝置無關的單位來測量。 如需詳細資訊，請參閱[測量單位](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)。

您可以透過 XAML 和程式碼來設定命名的字型大小。 此外，您 `Device.GetNamedSize` 可以呼叫方法，以傳回 `double` 代表已命名字型大小的：

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> 在 iOS 和 Android 上，命名的字型大小會根據作業系統協助工具選項自動調整。 您可以在 iOS 上使用平臺特定來停用此行為。 如需詳細資訊，請參閱[iOS 上命名的字型大小的協助工具調整](~/xamarin-forms/platform/ios/named-font-size-scaling.md)。

## <a name="use-a-custom-font"></a>使用自訂字型

自訂字型可以新增至您 Xamarin.Forms 的共用專案，並由平臺專案取用，而不需要任何額外的工作。 完成此動作的程序如下所示：

1. 將字型加入至您 Xamarin.Forms 的共用專案做為內嵌資源（**組建動作： EmbeddedResource**）。
1. 使用屬性，在檔案（例如**AssemblyInfo.cs**）中，向元件註冊字型檔案 `ExportFont` 。 也可以指定選擇性的別名。

> [!IMPORTANT]
> 內嵌字型需要使用 Xamarin.Forms 4.5.0.530 或更高版本。

下列範例顯示在元件中註冊的 Lobster 一般字型，以及一個別名：

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> 字型可以位於共用專案的任何資料夾中，而不需要在向元件註冊字型時指定資料夾名稱。

然後，可以在每個平臺上使用此字型，方法是參考其名稱，而不需要副檔名：

```xaml
<!-- Use font name -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

或者，您也可以藉由參考其別名，在每個平臺上使用它：

```xaml
<!-- Use font alias -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster" />
```

對等的 C# 程式碼為：

```csharp
// Use font name
Label label1 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster-Regular"
};

// Use font alias
Label label2 = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster"
};
```

下列螢幕擷取畫面顯示自訂字型：

[![IOS 和 Android 上的自訂字型](fonts-images/custom-sml.png "自訂字型範例")](fonts-images/custom.png#lightbox "自訂字型範例")

> [!IMPORTANT]
> 在 Windows 上，字型檔案名和字型名稱可能不同。 若要在 Windows 上探索字型名稱，請以滑鼠右鍵按一下 .ttf 檔案，然後選取 [**預覽**]。 然後可以從 [預覽] 視窗判斷字型名稱。

## <a name="display-font-icons"></a>顯示字型圖示

Xamarin.Forms應用程式可以藉由指定物件中的字型圖示資料來顯示字型圖示 `FontImageSource` 。 這個類別衍生自 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 類別，具有下列屬性：

- `Glyph`–字型圖示的 unicode 字元值，指定為 `string` 。
- `Size`– `double` 值，指出轉譯字型圖示的大小（以與裝置無關的單位）。 預設值是 30。 此外，這個屬性也可以設定為已命名的字型大小。
- `FontFamily`– `string` 代表字型圖示所屬的字型系列。
- `Color`– [`Color`](xref:Xamarin.Forms.Color) 顯示字型圖示時要使用的選擇性值。

這項資料是用來建立 PNG，可以顯示的任何視圖顯示 `ImageSource` 。 這種方法允許多個視圖顯示字型圖示（例如 emoji），而不是將字型圖示顯示限制為單一文字呈現視圖，例如 [`Label`](xref:Xamarin.Forms.Label) 。

> [!IMPORTANT]
> 字型圖示目前只能以 unicode 字元標記法來指定。

下列 XAML 範例有一個由視圖顯示的單一字型圖示 [`Image`](xref:Xamarin.Forms.Image) ：

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

此程式碼會在視圖中顯示 XBox 圖示，從 Ionicons 字型系列 [`Image`](xref:Xamarin.Forms.Image) 。 請注意，雖然這個圖示的 unicode 字元是 `\uf30c` ，但它必須在 XAML 中以轉義，因此會變成 `&#xf30c;` 。 對等的 C# 程式碼為：

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

下列螢幕擷取畫面是可系結[的版面配置範例，](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)顯示由可系結的配置顯示的幾個字型圖示：

![在 iOS 和 Android 上顯示的字型圖示螢幕擷取畫面](fonts-images/font-image-source.png "影像視圖中顯示的字型圖示")

## <a name="related-links"></a>相關連結

- [FontsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [文字（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [可系結版面配置（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [可繫結的版面配置](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
