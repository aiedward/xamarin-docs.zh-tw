---
title: "字型"
description: "Xamarin.Forms 中的設定字型"
ms.topic: article
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 771e1607bc4e6be8f0991e159b5d34f6d4ea9c02
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="fonts"></a>字型

本文說明如何 Xamarin.Forms 可讓您指定的字型屬性 （包括權數和大小） 上顯示文字的控制項。 字型資訊可以是[程式碼中指定](#Setting_Font_in_Code)或[Xaml 中指定](#Setting_Font_in_Xaml)。
它也可使用[自訂字型](#Using_a_Custom_Font)。

<a name="Setting_Font_in_Code" />

## <a name="setting-font-in-code"></a>程式碼中設定字型

使用任何控制項的顯示文字的三個字型相關的屬性：

- **FontFamily** &ndash; `string`字型名稱。
- **FontSize** &ndash;字型大小為`double`。
- **FontAttributes** &ndash;字串，指定的樣式資訊，例如*斜體*和**粗體**(使用`FontAttributes`列舉型別在 C# 中的)。

此程式碼會示範如何建立標籤和指定的字型大小和加權來顯示：

```csharp
var about = new Label {
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>字型大小

`FontSize`屬性可以設定為雙精度浮點數值，例如：

```csharp
label.FontSize = 24;
```

您也可以使用`NamedSize`列舉具有四個內建的選項。Xamarin.Forms 會選擇每個平台的最佳大小。

-  **Micro**
-  **小**
-  **媒體**
-  **Large**


`NamedSize`列舉型別可以是任一處使用`FontSize`您可以使用指定`Device.GetNamedSize`方法將值轉換成`double`:

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

<a name="FontAttributes" />

### <a name="font-attributes"></a>字型屬性

字型樣式例如**粗體**和*斜體*可以設定上`FontAttributes`屬性。 目前支援下列值：

-  **無**
-  **粗體**
-  **Italic**

`FontAttribute`列舉可用，如下所示 (您可以指定單一屬性或`OR`一起):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="formattedstring"></a>FormattedString

某些 Xamarin.Forms 控制項 (例如`Label`) 也支援在字串使用不同的字型屬性`FormattedString`類別。 A`FormattedString`組成一或多個`Span`s，其中每一個都可以有自己的格式化屬性。

`Span`類別具有下列屬性：

* **文字**&ndash;顯示的值
* **FontFamily** &ndash;字型名稱
* **FontSize** &ndash;字型大小
* **FontAttributes** &ndash;樣式資訊，例如*斜體*和**粗體**
* **ForegroundColor** &ndash;文字色彩
* **BackgroundColor** &ndash;背景色彩

建立和顯示的範例`FormattedString`如下所示&ndash;請注意，將它指派給標籤的`FormattedText`屬性而非`Text`屬性。

```csharp
var labelFormatted = new Label ();
var fs = new FormattedString ();
fs.Spans.Add (new Span { Text="Red, ", ForegroundColor = Color.Red, FontSize = 20, FontAttributes = FontAttributes.Italic });
fs.Spans.Add (new Span { Text=" blue, ", ForegroundColor = Color.Blue, FontSize = 32 });
fs.Spans.Add (new Span { Text=" and green!", ForegroundColor = Color.Green, FontSize = 12 });
labelFormatted.FormattedText = fs;
```


### <a name="setting-font-info-per-platform"></a>設定每個平台的字型資訊

或者，`Device.RuntimePlatform`屬性可用來設定不同的字型名稱，每個平台上，在此程式碼所示：

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

理想字型資訊適用於 iOS 的來源是[iosfonts.com](http://iosfonts.com)。

<a name="Setting_Font_in_Xaml" />

## <a name="setting-the-font-in-xaml"></a>在 Xaml 中設定字型

Xamarin.Forms 控制顯示文字全都有`Font`可以在 Xaml 中設定的屬性。 在 Xaml 中設定字型最簡單的方式是使用具名的大小的列舉值，這個範例中所示：

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

沒有內建的轉換器`Font`屬性，可讓所有字型設定，以在 Xaml 中的字串值。 下列範例顯示如何指定的字型屬性和大小在 Xaml 中：

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

若要指定多個`Font`設定，結合成單一的字型屬性字串所需的設定。 字型屬性字串的格式應為`"[font-face],[attributes],[size]"`。 參數的順序很重要，所有參數都是選擇性的與多個`attributes`可以指定，例如：

```xaml
<Label Text="Small bold text" FontAttributes="Bold" FontSize="Micro" />
<Label Text="Really big italic text" FontAttributes="Italic" FontSize="72" />
```

`FormattedString`類別也可用在 XAML 中，如下所示：

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <FormattedString.Spans>
                <Span Text="Red, " ForegroundColor="Red" FontAttributes="Italic" FontSize="20" />
                <Span Text=" blue, " ForegroundColor="Blue" FontSize="32" />
                <Span Text=" and green! " ForegroundColor="Green" FontSize="12"/>
            </FormattedString.Spans>
        </FormattedString>
    </Label.FormattedText>
</Label>
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-values) 也可用在 XAML 中呈現每個平台上不同的字型。 以下範例在 iOS 上使用自訂的字型 (<span style="font-family:MarkerFelt-Thin">MarkerFelt 精簡</span>)，並指定其他平台上只大小/屬性：

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP, WinRT, WinPhone" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

指定自訂的字型，時，一律最好使用`OnPlatform`，因為它容易找到適用於所有平台的字型。

<a name="Using_a_Custom_Font" />

## <a name="using-a-custom-font"></a>使用自訂的字型

使用內建的字體以外字型需要某些平台專屬程式碼撰寫。 這個螢幕擷取畫面顯示自訂字型**洛貝斯特**從[Google 的開放原始碼字型](https://www.google.com/fonts)呈現在 iOS、 Android 和 Windows Phone 使用 Xamarin.Forms。

 [![IOS 和 Android 上的自訂字型](fonts-images/custom-sml.png "自訂字型範例")](fonts-images/custom.png#lightbox "自訂字型範例")

每個平台所需的步驟如下所述。 包含自訂字型檔案與應用程式時，請務必確認字型的授權可允許發佈。

### <a name="ios"></a>iOS

可自訂的字型顯示先確保，會載入它，然後使用 Xamarin.Forms 來依據名稱參考它`Font`方法。
請依照下列中的指示[此部落格文章](http://blog.xamarin.com/custom-fonts-in-ios/):

1. 將字型檔案與**建置動作： BundleResource**，和
2. 更新**Info.plist**檔案 (**應用程式所提供的字型**，或`UIAppFonts`、 索引鍵)，然後
3. 它依名稱參考只要您定義 Xamarin.Forms 中的字型 ！

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

適用於 Android 的 Xamarin.Forms 可以參考自訂字型已加入至專案依照特定的命名標準。 第一次加入字型檔案**資產**資料夾中的應用程式專案和組*建置動作： AndroidAsset*。 然後使用完整路徑和*字型名稱*隔開的雜湊 （#） 的字型名稱，在 Xamarin.Forms，如下列程式碼片段所示：

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

Xamarin.Forms，針對 Windows 平台可以參考自訂字型已加入至專案依照特定的命名標準。 第一次加入字型檔案**/Assets/字型/**資料夾中的應用程式專案和組<span class="UIItem">建置動作： Content</span>。 然後使用完整路徑和字型檔名，後面接著雜湊 （#） 和<span class="UIItem">字型名稱</span>，如下列程式碼片段所示：

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> 請注意，字型檔名和字型名稱可能不同。 若要探索 Windows 上的字型名稱，以滑鼠右鍵按一下.ttf 檔案，然後選取**預覽**。 然後您可以從預覽視窗判斷字型名稱。

現已完成應用程式的通用程式碼。 平台專屬的電話撥號員程式碼現在會當作 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 實作。

### <a name="xaml"></a>XAML

您也可以使用[ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#providing-platform-values)來呈現自訂字型的 XAML 中：

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP, WinRT, WinPhone" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

<a name="Summary" />

## <a name="summary"></a>總結

Xamarin.Forms 提供簡單的預設設定，讓您輕鬆地針對所有支援平台的文字的大小。 也可讓您指定的字體和大小&ndash;甚至不同的每個平台&ndash;需要更細部的掌控時。 `FormattedString`類別可以用來建構字串，包含使用不同的字型規格`Span`類別。

您也可以在 Xaml 中使用的格式正確的字型屬性指定字型資訊或`FormattedString`具有項目`Span`子系。


## <a name="related-links"></a>相關連結

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [文字 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
