---
title: 中的字型 Xamarin.Forms
description: 本文說明如何在應用程式中顯示文字的控制項上指定字型資訊 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fb32d3248b1dbbe633a99afd8de14b4fb4f0ba09
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562193"
---
# <a name="fonts-in-no-locxamarinforms"></a>中的字型 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

本文描述如何 Xamarin.Forms 讓您指定字型屬性 (包括顯示文字之控制項的權數和大小) 。 您可以在程式 [代碼中指定](#set-the-font-in-code) 字型資訊，或 [在 XAML 中指定](#set-the-font-in-xaml)。 也可以使用 [自訂字型](#use-a-custom-font)，並 [顯示字型圖示](#display-font-icons)。

## <a name="set-the-font-in-code"></a>在程式碼中設定字型

使用顯示文字之任何控制項的三個字型相關屬性：

- **FontFamily** &ndash;`string`字型名稱。
- **FontSize** &ndash; 的字型大小 `double` 。
- **FontAttributes** &ndash; 使用 c # ) 中的列舉來指定樣式資訊（例如 *斜體* 和 **粗體字** (）的字串 `FontAttributes` 。

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

您 `FontSize` 可以將屬性設定為雙精度值，例如：

```csharp
label.FontSize = 24;
```

大小值是以與裝置無關的單位來測量。 如需詳細資訊，請參閱 [測量單位](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)。

Xamarin.Forms 也會在列舉中定義 [`NamedSize`](xref:Xamarin.Forms.NamedSize) 表示特定字型大小的欄位。 如需有關命名字型大小的詳細資訊，請參閱 [命名字型大小](#named-font-sizes)。

### <a name="font-attributes"></a>字型屬性

可以在屬性上設定 **粗體** 和 *斜體* 等字型樣式 `FontAttributes` 。 目前支援下列值：

- **None**
- **粗體**
- **斜體**

`FontAttribute`列舉可如下所示使用 (您可以指定單一屬性或將 `OR` 其設定) ：

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>設定每個平臺的字型資訊

或者，您也 `Device.RuntimePlatform` 可以在每個平臺上使用屬性來設定不同的字型名稱，如下列程式碼所示：

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

適用于 iOS 的良好字型資訊來源為 [iosfonts.com](http://iosfonts.com)。

## <a name="set-the-font-in-xaml"></a>在 XAML 中設定字型

Xamarin.Forms 顯示文字的控制項都具有 `FontSize` 可在 XAML 中設定的屬性。 在 XAML 中設定字型最簡單的方式是使用命名的大小列舉值，如下列範例所示：

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

屬性有內建的轉換器，可 `FontSize` 讓所有字型設定以 XAML 中的字串值表示。 此外， `FontAttributes` 也可以使用屬性來指定字型屬性：

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

Xamarin.Forms 定義 [`NamedSize`](xref:Xamarin.Forms.NamedSize) 列舉中表示特定字型大小的欄位。 下表顯示成員及其 `NamedSize` 在 iOS、Android 和通用 Windows 平臺 (UWP) 的預設大小：

| member | iOS | Android | UWP |
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

大小值是以與裝置無關的單位來測量。 如需詳細資訊，請參閱 [測量單位](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)。

您可以透過 XAML 和程式碼來設定命名字型大小。 此外，您 `Device.GetNamedSize` 可以呼叫方法，以傳回 `double` 代表命名字型大小的。

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> 在 iOS 和 Android 上，命名字型的大小會根據作業系統協助工具選項自動調整。 您可以在 iOS 上使用平臺特定來停用此行為。 如需詳細資訊，請參閱 [iOS 上的命名字型大小的協助工具調整](~/xamarin-forms/platform/ios/named-font-size-scaling.md)。

## <a name="use-a-custom-font"></a>使用自訂字型

您可以將自訂字型加入至您 Xamarin.Forms 的共用專案，並由平臺專案取用，而不需要任何額外的工作。 完成此動作的程序如下所示：

1. 將字型新增到您 Xamarin.Forms 的共用專案，做為內嵌資源 (**組建動作： EmbeddedResource**) 。
1. 使用屬性，在檔案（例如 **AssemblyInfo.cs**）中向元件註冊字型檔案 `ExportFont` 。 也可以指定選擇性的別名。

> [!IMPORTANT]
> 內嵌字型需要使用 Xamarin.Forms 4.5.0.530 或更高版本。

下列範例顯示在元件中註冊的 Lobster 一般字型，以及別名：

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> 字型可以位於共用專案的任何資料夾中，而不需要在向元件註冊字型時指定資料夾名稱。

然後您可以在每個平臺上使用該字型，方法是參考其名稱，而不需要副檔名：

```xaml
<!-- Use font name -->
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

或者，您可以參考其別名，以在每個平臺上使用它：

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
> 在 Windows 上，字型檔案名和字型名稱可能會不同。 若要在 Windows 上探索字型名稱，請在 ttf 檔案上按一下滑鼠右鍵，然後選取 [ **預覽**]。 然後您可以從 [預覽] 視窗判斷字型名稱。

## <a name="display-font-icons"></a>顯示字型圖示

Xamarin.Forms應用程式可以藉由在物件中指定字型圖示資料來顯示字型圖示 `FontImageSource` 。 此類別衍生自 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 類別，具有下列屬性：

- `Glyph` –字型圖示的 unicode 字元值，指定為 `string` 。
- `Size` – `double` 值，表示轉譯字型圖示的大小（以與裝置無關的單位）。 預設值是 30。 此外，這個屬性也可以設定為命名的字型大小。
- `FontFamily` – `string` 代表字型圖示所屬字型家族的。
- `Color` – [`Color`](xref:Xamarin.Forms.Color) 顯示字型圖示時要使用的選擇性值。

這項資料是用來建立 PNG，可顯示的任何視圖都可以顯示 `ImageSource` 。 這種方法可讓多個視圖顯示字型圖示（例如 emoji），而不是將字型圖示顯示限制為單一文字呈現視圖，例如 [`Label`](xref:Xamarin.Forms.Label) 。

> [!IMPORTANT]
> 字型圖示目前只能由其 unicode 字元標記法指定。

下列 XAML 範例有一個視圖顯示的單一字型圖示 [`Image`](xref:Xamarin.Forms.Image) ：

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

此程式碼會顯示 Ionicons 字型系列中的 XBox 圖示 [`Image`](xref:Xamarin.Forms.Image) 。 請注意，雖然這個圖示的 unicode 字元是 `\uf30c` ，但它必須在 XAML 中進行轉義，因此會變成 `&#xf30c;` 。 對等的 C# 程式碼為：

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

下列螢幕擷取畫面，從可系結 [的版面配置範例中](/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts) ，顯示可系結配置所顯示的數個字型圖示：

![螢幕擷取畫面： iOS 和 Android 上顯示的字型圖示](fonts-images/font-image-source.png "影像視圖中顯示的字型圖示")

## <a name="related-links"></a>相關連結

- [FontsSample](/samples/xamarin/xamarin-forms-samples/workingwithfonts)
- [Text (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [ (範例) 的可系結版面配置 ](/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [可繫結的版面配置](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)