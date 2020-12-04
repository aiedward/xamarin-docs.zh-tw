---
title: 中的字型 Xamarin.Forms
description: 本文說明如何在應用程式中顯示文字的控制項上指定字型資訊 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2020
ms.custom: contperfq2
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 271c6c5e510a892919b5d87c4dbc38ad8e9d657d
ms.sourcegitcommit: 342cfbd2502ad92cadada4fa9aec669b99d7830a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96604569"
---
# <a name="fonts-in-no-locxamarinforms"></a>中的字型 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/workingwithfonts)

依預設，會 Xamarin.Forms 使用每個平臺所定義的系統字型。 但是，顯示文字的控制項會定義您可以用來變更這個字型的屬性：

- `FontAttributes`，類型 `FontAttributes` 為，其為具有三個成員的列舉： `None` 、 `Bold` 和 `Italic` 。 此屬性的預設值為 `None`。
- 類型為 `double` 的 `FontSize`。
- 類型為 `string` 的 `FontFamily`。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

## <a name="set-font-attributes"></a>設定字型屬性

顯示文字的控制項可以設定 `FontAttributes` 屬性來指定字型屬性：

```xaml
<Label Text="Italics"
       FontAttributes="Italic" />
<Label Text="Bold and italics"
       FontAttributes="Bold, Italic" />
```

對等的 C# 程式碼為：

```csharp
Label label1 = new Label
{
    Text = "Italics",
    FontAttributes = FontAttributes.Italic
};

Label label2 = new Label
{
    Text = "Bold and italics",
    FontAttributes = FontAttributes.Bold | FontAttributes.Italic
};    
```

## <a name="set-the-font-size"></a>設定字型大小

顯示文字的控制項可以設定 `FontSize` 屬性來指定字型大小。 `FontSize`屬性可以直接設定為 `double` 值，或是由 [`NamedSize`](xref:Xamarin.Forms.NamedSize) 列舉值設定：

```xaml
<Label Text="Font size 24"
       FontSize="24" />
<Label Text="Large font size"
       FontSize="Large" />
```

對等的 C# 程式碼為：

```csharp
Label label1 = new Label
{
    Text = "Font size 24",
    FontSize = 24
};

Label label2 = new Label
{
    Text = "Large font size",
    FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label))
};
```

或者， `Device.GetNamedSize` 方法有一個覆寫，將第二個引數指定為 [`Element`](xref:Xamarin.Forms.Element) ：

```csharp
Label myLabel = new Label
{
    Text = "Large font size",
};
myLabel.FontSize = Device.GetNamedSize(NamedSize.Large, myLabel);
```

> [!NOTE]
> `FontSize`當指定為時 `double` ，值會以與裝置無關的單位來測量。 如需詳細資訊，請參閱 [測量單位](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)。

如需有關命名字型大小的詳細資訊，請參閱 [瞭解已命名的字型大小](#understand-named-font-sizes)。

## <a name="set-the-font-family"></a>設定字型系列

顯示文字的控制項可以將 `FontFamily` 屬性設定為字型家族名稱，例如「Times Roman」。 不過，只有在特定平臺上支援該字型家族時，才能使用此功能。

有一些技術可以用來嘗試衍生平臺上可用的字型。 不過，TTF (True 類型格式) 字型檔案不一定代表字型家族，而且通常會包含不適合在應用程式中使用的 TTFs。 此外，平臺上安裝的字型也可以隨著平臺版本而變更。 因此，指定字型家族最可靠的方法是使用自訂字型。

您可以將自訂字型加入至您 Xamarin.Forms 的共用專案，並由平臺專案取用，而不需要任何額外的工作。 完成此動作的程序如下所示：

1. 將字型新增到您 Xamarin.Forms 的共用專案，做為內嵌資源 (**組建動作： EmbeddedResource**) 。
1. 使用屬性，在檔案（例如 **AssemblyInfo.cs**）中向元件註冊字型檔案 `ExportFont` 。 也可以指定選擇性的別名。

下列範例顯示在元件中註冊的 Lobster-Regular 字型，以及別名：

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf", Alias = "Lobster")]
```

> [!NOTE]
> 字型可以位於共用專案的任何資料夾中，而不需要在向元件註冊字型時指定資料夾名稱。
>
> 在 Windows 上，字型檔案名和字型名稱可能會不同。 若要在 Windows 上探索字型名稱，請在 ttf 檔案上按一下滑鼠右鍵，然後選取 [ **預覽**]。 然後您可以從 [預覽] 視窗判斷字型名稱。

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
> 針對 Windows 上的發行組建，請確定包含自訂字型的元件是以方法呼叫中的引數形式傳遞 `Forms.Init` 。 如需詳細資訊，請參閱[疑難排解](~/xamarin-forms/platform/windows/installation/index.md#troubleshooting)。

## <a name="set-font-properties-per-platform"></a>設定每個平臺的字型屬性

[`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1)和 [`On`](xref:Xamarin.Forms.On) 類別可以在 XAML 中用來設定每個平臺的字型屬性。 下列範例會在每個平臺上設定不同的字型系列和大小：

```xaml
<Label Text="Different font properties on different platforms"
       FontSize="{OnPlatform iOS=20, Android=Medium, UWP=24}">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
            <On Platform="iOS" Value="MarkerFelt-Thin" />
            <On Platform="Android" Value="Lobster-Regular" />
            <On Platform="UWP" Value="ArimaMadurai-Black" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#provide-platform-specific-values)屬性可以在程式碼中用來設定每個平臺的字型屬性

```csharp
Label label = new Label
{
    Text = "Different font properties on different platforms"
};

label.FontSize = Device.RuntimePlatform == Device.iOS ? 20 :
    Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : 24;
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular" : "ArimaMadurai-Black";
```

如需有關提供平臺特定值的詳細資訊，請參閱 [提供平臺特定值](~/xamarin-forms/platform/device.md#provide-platform-specific-values)。 如需標記延伸的詳細資訊 `OnPlatform` ，請參閱 [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="understand-named-font-sizes"></a>瞭解命名字型大小

Xamarin.Forms 定義 [`NamedSize`](xref:Xamarin.Forms.NamedSize) 列舉中表示特定字型大小的欄位。 下表顯示成員及其 `NamedSize` 在 iOS、Android 和通用 Windows 平臺 (UWP) 的預設大小：

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

大小值是以與裝置無關的單位來測量。 如需詳細資訊，請參閱 [測量單位](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)。

> [!NOTE]
> 在 iOS 和 Android 上，命名字型的大小會根據作業系統協助工具選項自動調整。 您可以在 iOS 上使用平臺特定來停用此行為。 如需詳細資訊，請參閱 [iOS 上的命名字型大小的協助工具調整](~/xamarin-forms/platform/ios/named-font-size-scaling.md)。

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
- [提供平臺特定值](~/xamarin-forms/platform/device.md#provide-platform-specific-values)
- [OnPlatform 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)
- [可繫結的版面配置](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
