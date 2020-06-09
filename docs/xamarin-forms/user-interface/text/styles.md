---
title： " Xamarin.Forms 文字樣式" 描述： "本文說明如何設定應用程式中文字的樣式 Xamarin.Forms 。 樣式可以定義一次，並供許多視圖使用，但樣式只能搭配一個類型的視圖使用。」
assetid： 57C0CFD6-A568-46B8-ADA1-BF25681893CF ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：05/22/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-text-styles"></a>Xamarin.Forms文字樣式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_在 Xamarin 中設定文本的樣式_

樣式可以用來調整標籤、專案和編輯器的外觀。 樣式可以定義一次，並供許多視圖使用，但樣式只能搭配一種類型的視圖使用。
您可以指定樣式 `Key` ，並選擇性地使用特定控制項的屬性加以套用 `Style` 。

## <a name="built-in-styles"></a>內建樣式

Xamarin.Forms包含數個適用于常見案例的[內建](xref:Xamarin.Forms.Device.Styles)樣式：

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

若要套用其中一個內建樣式，請使用 `DynamicResource` 標記延伸來指定樣式：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 c # 中，已從選取內建樣式 `Device.Styles` ：

```csharp
label.Style = Device.Styles.TitleStyle;
```

![裝置樣式範例](styles-images/builtinstyles.png)

## <a name="custom-styles"></a>自訂樣式

樣式是由 setter 和 setter 所組成，其中包含屬性和屬性將設定為的值。

在 c # 中，具有大小為30之紅色文字之標籤的自訂樣式會定義如下：

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

在 XAML 中：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style x:Key="LabelStyle" TargetType="Label">
            <Setter Property="TextColor" Value="Red"/>
            <Setter Property="FontSize" Value="30"/>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>

<ContentPage.Content>
    <StackLayout>
        <Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
    </StackLayout>
</ContentPage.Content>
```

請注意，資源（包括所有樣式）定義于內 `ContentPage.Resources` ，這是比較熟悉元素的同級 `ContentPage.Content` 。

![自訂樣式範例](styles-images/customstyle.png)

## <a name="applying-styles"></a>套用樣式

建立樣式之後，即可將它套用至任何符合其的視圖 `TargetType` 。

在 XAML 中，會將自訂樣式套用至 views，方法是提供其 `Style` 屬性和 `StaticResource` 參考所需樣式的標記延伸：

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

在 c # 中，樣式可以直接套用至視圖，或從頁面的加入和取出 `ResourceDictionary` 。 若要直接新增：

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

若要從頁面中新增和取出 `ResourceDictionary` ：

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

內建樣式的套用方式不同，因為它們必須回應協助工具設定。 若要在 XAML 中套用內建樣式， `DynamicResource` 會使用標記延伸：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 c # 中，已從選取內建樣式 `Device.Styles` ：

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>協助工具選項

有內建樣式可讓您更輕鬆地遵循協助工具喜好設定。 使用任何內建樣式時，如果使用者據以設定其協助工具喜好設定，字型大小會自動增加。

請考慮下列範例，其中包含已啟用和停用協助工具設定的內建樣式樣式的相同網頁檢視：

已停用：

![已停用協助工具的裝置樣式](styles-images/pre-access.png)

已啟用：

![啟用協助工具的裝置樣式](styles-images/post-access.png)

為確保可存取性，請確定內建樣式是用來作為應用程式中任何文字相關樣式的基礎，而且您使用的樣式一致。 如需擴充和使用一般樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="related-links"></a>相關連結

- [建立 Mobile Apps Xamarin.Forms ，第12章](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [文字（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Style](xref:Xamarin.Forms.Style)
