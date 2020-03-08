---
title: Xamarin 中的字型
description: 本文說明如何在顯示 Xamarin. Forms 應用程式中文字的控制項上，指定字型資訊。
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/20/2020
ms.openlocfilehash: 3798e3612547d36905dd62e6314f158958782874
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78911329"
---
# <a name="fonts-in-xamarinforms"></a>Xamarin 中的字型

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfonts)

本文說明 Xamarin 如何讓您指定顯示文字之控制項的字型屬性（包括權數和大小）。 字型資訊可以[在程式碼中指定](#Setting_Font_in_Code)，或[在 XAML 中指定](#Setting_Font_in_Xaml)。 也可以使用[自訂字型](#Using_a_Custom_Font)，並[顯示字型圖示](#display-font-icons)。

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>在程式碼中設定字型

使用顯示文字的任何控制項的三個與字型相關屬性：

- **FontFamily** &ndash; `string` 字型名稱。
- **FontSize** &ndash; 字型大小 `double`。
- **FontAttributes** &ndash; 字串，指定樣式資訊，例如*斜體*和**粗體**（使用中C#的 `FontAttributes` 列舉）。

此程式碼示範如何建立標籤和指定的字型大小和加權來顯示：

```csharp
var about = new Label
{
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>字型大小

`FontSize` 屬性可以設定為 double 值，例如：

```csharp
label.FontSize = 24;
```

大小值是以與裝置無關的單位來測量。 如需詳細資訊，請參閱[測量單位](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)。

[Xamarin] 也會在[`NamedSize`](xref:Xamarin.Forms.NamedSize)列舉中定義代表特定字型大小的欄位。 如需有關命名的字型大小的詳細資訊，請參閱[命名字型大小](#named-font-sizes)。

<a name="FontAttributes" />

### <a name="font-attributes"></a>字型屬性

可以在 [`FontAttributes`] 屬性上設定字型樣式（例如**粗體**和*斜體*）。 目前支援下列值：

- **None**
- **粗體字**
- **斜體**

`FontAttribute` 列舉可用如下所示（您可以指定單一屬性或將其 `OR` 在一起）：

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>設定每個平臺的字型資訊

或者，`Device.RuntimePlatform` 屬性可以用來在每個平臺上設定不同的字型名稱，如下列程式碼所示：

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "MarkerFelt-Thin" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/ArimaMadurai-Black.ttf#Arima Madurai",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

適用于 iOS 的字型資訊來源是[iosfonts.com](http://iosfonts.com)。

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>在 XAML 中設定字型

所有顯示文字的表單控制項都有一個可在 XAML 中設定的 `FontSize` 屬性。 在 XAML 中設定字型最簡單的方式是使用已命名的大小列舉值，如下列範例所示：

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

`FontSize` 屬性有內建的轉換器，可將所有字型設定表示為 XAML 中的字串值。 此外，`FontAttributes` 屬性可以用來指定字型屬性：

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values)屬性也可以在 XAML 中用來在每個平臺上呈現不同的字型。 下列範例會在每個平臺上使用不同的字型：

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

## <a name="named-font-sizes"></a>命名字型大小

Xamarin 會定義[`NamedSize`](xref:Xamarin.Forms.NamedSize)列舉中的欄位，以代表特定字型大小。 下表顯示 `NamedSize` 成員，以及其在 iOS、Android 和通用 Windows 平臺（UWP）上的預設大小：

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

大小值是以與裝置無關的單位來測量。 如需詳細資訊，請參閱[測量單位](~/xamarin-forms/user-interface/controls/common-properties.md#units-of-measurement)。

您可以透過 XAML 和程式碼來設定命名的字型大小。 此外，您可以呼叫 `Device.GetNamedSize` 方法，以傳回代表已命名字型大小的 `double`：

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> 在 iOS 和 Android 上，命名的字型大小會根據作業系統協助工具選項自動調整。 您可以在 iOS 上使用平臺特定來停用此行為。 如需詳細資訊，請參閱[iOS 上命名的字型大小的協助工具調整](~/xamarin-forms/platform/ios/named-font-size-scaling.md)。

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>使用自訂字型

使用非內建的字樣的字型不需要某些平台特定程式碼。 此螢幕擷取畫面顯示從[Google 的開放原始](https://www.google.com/fonts)碼字型**Lobster** ，並使用 Xamarin 呈現的自訂字型。

 [![IOS 和 Android 上的自訂字型](fonts-images/custom-sml.png "自訂字型範例")](fonts-images/custom.png#lightbox "自訂字型範例")

以下概述每個平臺所需的步驟。 包含應用程式的自訂字型檔案時，請務必確認字型的授權允許散發。

### <a name="ios"></a>iOS

若要顯示自訂字型，您可以先確定它已載入，然後使用 Xamarin `Font` 方法依名稱參考它。
遵循[此 blog 文章](https://devblogs.microsoft.com/xamarin/custom-fonts-in-ios/)中的指示：

1. 新增具有組建動作的字型檔案 **： BundleResource**，以及
2. 更新**plist**檔案（**應用程式所提供的**字型，或 `UIAppFonts`金鑰），然後
3. 在您于 [Xamarin. 表單] 中定義字型的位置，依名稱參考它。

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

適用於 Android 的 Xamarin.Forms 可以參考自訂字型已加入至專案遵循特定的命名標準。 首先，將字型檔案新增至應用程式專案中的 [**資產**] 資料夾，並設定 [*組建動作： AndroidAsset*]。 然後使用以雜湊（#）分隔的完整路徑和*字型名稱*，做為 Xamarin 中的字型名稱，如下列程式碼片段所示：

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

適用于 Windows 平臺的 Xamarin 可以參考已依照特定命名標準新增至專案的自訂字型。 首先，將字型檔案新增至應用程式專案中的 **/Assets/Fonts/** 資料夾，並設定 [**建立動作：內容**]。 然後使用完整路徑和字型檔案名，後面接著雜湊（#）和**字型名稱**，如下列程式碼片段所示：

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> 請注意，字型檔案名和字型名稱可能不同。 若要在 Windows 上探索字型名稱，請以滑鼠右鍵按一下 .ttf 檔案，然後選取 [**預覽**]。 然後可以從 [預覽] 視窗判斷字型名稱。

### <a name="xaml"></a>XAML

您也可以使用 XAML 中的[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads)來呈現自訂字型：

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="use-a-custom-font-preview"></a>使用自訂字型（預覽）

自訂字型可以加入至您的 Xamarin. Forms 共用專案，並由平臺專案取用，而不需要任何額外的工作。 完成此動作的程序如下所示：

1. 將字型加入至您的 Xamarin. 表單共用專案做為內嵌資源（**組建動作： EmbeddedResource**）。
1. 使用 `ExportFont` 屬性，在檔案（例如**AssemblyInfo.cs**）中，向元件註冊字型檔案。

下列範例顯示向元件註冊的 Lobster 一般字型：

```csharp
using Xamarin.Forms;

[assembly: ExportFont("Lobster-Regular.ttf")]
```

> [!NOTE]
> 字型可以位於共用專案的任何資料夾中，而不需要在向元件註冊字型時指定資料夾名稱。

然後，可以在每個平臺上使用此字型，方法是參考其名稱，而不需要副檔名：

```xaml
<Label Text="Hello Xamarin.Forms"
       FontFamily="Lobster-Regular" />
```

對等的 C# 程式碼為：

```csharp
Label label = new Label
{
    Text = "Hello Xamarin.Forms!",
    FontFamily = "Lobster-Regular"
};
```

下列螢幕擷取畫面顯示自訂字型：

[![IOS 和 Android 上的自訂字型](fonts-images/custom-sml.png "自訂字型範例")](fonts-images/custom.png#lightbox "自訂字型範例")

## <a name="display-font-icons"></a>顯示字型圖示

Xamarin. Forms 應用程式可以藉由在 `FontImageSource` 物件中指定字型圖示資料來顯示字型圖示。 這個類別衍生自[`ImageSource`](xref:Xamarin.Forms.ImageSource)類別，具有下列屬性：

- `Glyph` –字型圖示的 unicode 字元值，指定為 `string`。
- `Size` –表示呈現的字型圖示之大小（以與裝置無關的單位）的 `double` 值。 預設值是 30。 此外，這個屬性也可以設定為已命名的字型大小。
- `FontFamily` –代表字型圖示所屬之字型系列的 `string`。
- `Color` –顯示字型圖示時要使用的選擇性[`Color`](xref:Xamarin.Forms.Color)值。

這項資料是用來建立 PNG，可以顯示 `ImageSource`的任何視圖顯示。 這種方法允許多個視圖顯示字型圖示（例如 emoji），而不是將字型圖示顯示限制為單一文字呈現視圖，例如[`Label`](xref:Xamarin.Forms.Label)。

> [!IMPORTANT]
> 字型圖示目前只能以 unicode 字元標記法來指定。

下列 XAML 範例中， [`Image`](xref:Xamarin.Forms.Image)視圖會顯示單一字型圖示：

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

此程式碼會在[`Image`](xref:Xamarin.Forms.Image)視圖中顯示 XBox 圖示，從 Ionicons 字型系列。 請注意，雖然這個圖示的 unicode 字元是 `\uf30c`，但它必須在 XAML 中以轉義，因此會變成 `&#xf30c;`。 對等的 C# 程式碼為：

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
