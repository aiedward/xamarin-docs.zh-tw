---
title： " Xamarin.Forms Shell 頁面設定" 描述： "shell 類別定義附加屬性，可用於設定 Shell 應用程式中頁面的外觀 Xamarin.Forms 。 這包括設定頁面色彩、停用巡覽列、停用索引標籤列，以及在導覽列中顯示 views。」
assetid： 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：01/29/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-shell-page-configuration"></a>Xamarin.FormsShell 頁面設定

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

`Shell`類別會定義附加屬性，以用來設定 Shell 應用程式中頁面的外觀 Xamarin.Forms 。 這包括設定頁面色彩、設定頁面呈現模式、停用巡覽列、停用索引標籤列，以及在導覽列中顯示 views。

## <a name="set-page-colors"></a>設定頁面色彩

`Shell` 類別定義下列附加屬性，可用來在 Shell 應用程式中設定頁面色彩：

- 型別為 `Color` 的 `BackgroundColor` 定義 Shell 色彩中的背景色彩。 色彩將不會填入 Shell 內容的背景。
- 型別為 `Color` 的 `DisabledColor` 定義用來為遭停用文字和圖示上色之色彩。
- 型別為 `Color` 的 `ForegroundColor` 定義用來為文字和圖示上色之色彩。
- 型別為 `Color` 的 `TitleColor` 定義用於目前頁面標題之色彩。
- 型別為 `Color` 的 `UnselectedColor` 定義用於 Shell 色彩中未選取文字和圖示的色彩。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標，並使用 XAML 樣式來設計樣式。 此外，這些屬性可以使用階層式樣式表 (CSS) 來設定。 如需詳細資訊，請參閱[ Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

> [!NOTE]
> 也有可定義索引標籤色彩的屬性。 如需詳細資訊，請參閱[索引標籤外觀](tabs.md#tab-appearance)。

下列 XAML 顯示在子類別化 `Shell` 類別中設定色彩屬性：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell"
       BackgroundColor="#455A64"
       ForegroundColor="White"
       TitleColor="White"
       DisabledColor="#B4FFFFFF"
       UnselectedColor="#95FFFFFF">

</Shell>
```

在此範例中，除非在頁面層級覆寫色彩值，否則色彩值會套用至 Shell 應用程式中的所有頁面。

因為色彩屬性是附加屬性，所以可以在個別頁面上設定它們，若要在該頁面上設定色彩：

```xaml
<ContentPage ...
             Shell.BackgroundColor="Gray"
             Shell.ForegroundColor="White"
             Shell.TitleColor="Blue"
             Shell.DisabledColor="#95FFFFFF"
             Shell.UnselectedColor="#B4FFFFFF">

</ContentPage>
```

或者，可以使用 XAML 樣式來設定色彩屬性：

```xaml
<Style x:Key="DomesticShell"
       TargetType="Element" >
    <Setter Property="Shell.BackgroundColor"
            Value="#039BE6" />
    <Setter Property="Shell.ForegroundColor"
            Value="White" />
    <Setter Property="Shell.TitleColor"
            Value="White" />
    <Setter Property="Shell.DisabledColor"
            Value="#B4FFFFFF" />
    <Setter Property="Shell.UnselectedColor"
            Value="#95FFFFFF" />
</Style>
```

如需 XAML 樣式的詳細資訊，請參閱[ Xamarin.Forms 使用 xaml 樣式設定應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

## <a name="set-page-presentation-mode"></a>設定頁面簡報模式

根據預設，當頁面流覽至方法時，會發生小型導覽動畫 `GoToAsync` 。 不過，將 `Shell.PresentationMode` 上的附加屬性設定 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 為其中一個列舉成員，即可變更此行為 `PresentationMode` ：

- `NotAnimated`表示在沒有導覽動畫的情況下，會顯示頁面。
- `Animated`指出頁面會以導覽動畫顯示。 這是附加屬性的預設值 `Shell.PresentationMode` 。
- `Modal`表示頁面將會顯示為強制回應頁面。
- `ModalAnimated`表示頁面將會顯示為具有導覽動畫的模式頁面。
- `ModalNotAnimated`表示頁面將會顯示為強制回應頁面，而不需要導覽動畫。

> [!IMPORTANT]
> `PresentationMode`類型是旗標列舉。 這表示可以在程式碼中套用列舉成員的組合。 不過，為了方便在 XAML 中使用， `ModalAnimated` 成員是 `Animated` 和成員的組合 `Modal` ，而 `ModalNotAnimated` 成員則是 `NotAnimated` 和成員的組合 `Modal` 。 如需旗標列舉的詳細資訊，請參閱[列舉類型做為位旗標](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags)。

下列 XAML 範例會 `Shell.PresentationMode` 在上設定附加屬性 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

在此範例中， [`ContentPage`](xref:Xamarin.Forms.ContentPage) 當頁面流覽至方法時，會設定為顯示為強制回應頁面 `GoToAsync` 。

## <a name="enable-navigation-bar-shadow"></a>啟用導覽列陰影

`Shell`類別 `NavBarHasShadow` 會定義類型的附加屬性， `bool` 以控制導覽列是否有陰影。 根據預設，屬性的值為 `false` iOS 和 Android 上的 `true` 。

雖然可以在子類別化物件上設定這個屬性 `Shell` ，但它也可以在想要啟用導覽列陰影的任何頁面上設定。 例如，下列 XAML 會顯示如何啟用的導覽列陰影 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

這會導致流覽列陰影被啟用。

## <a name="disable-the-navigation-bar"></a>停用瀏覽列

`Shell` 類別定義 `NavBarIsVisible` 附加屬性 (`bool` 型別)，它會控制呈現頁面時是否應該顯示導覽列。 根據預設，該屬性的值為 `true`。

雖然此屬性可以設定在子類別化 `Shell` 物件上，但通常會將它設定在想要隱藏導覽列的任何頁面上。 例如，下列 XAML 會顯示從中停用巡覽列 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

這會導致當該頁面呈現時，導覽列變成是隱藏的：

![在 iOS 和 Android 上具有不可見巡覽列的 Shell 頁面螢幕擷取畫面](configuration-images/navigationbar-invisible.png "具有隱藏導覽列的 Shell 頁面")

## <a name="disable-the-tab-bar"></a>停用索引標籤列

`Shell` 類別定義 `TabBarIsVisible` 附加屬性 (`bool` 型別)，它會控制呈現頁面時是否應該顯示索引標籤列。 根據預設，該屬性的值為 `true`。

雖然這個屬性可以設定在子類別化 `Shell` 物件上，但通常會將它設定在想要隱藏索引標籤列的任何頁面上。 例如，下列 XAML 會顯示停用的索引標籤列 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

這會導致當該頁面呈現時，索引標籤列變成是隱藏的：

![在 iOS 和 Android 上具有隱藏索引標籤列的 Shell 頁面螢幕擷取畫面](configuration-images/tabbar-invisible.png "具有隱藏索引標籤列的 Shell 頁面")

## <a name="display-views-in-the-navigation-bar"></a>在導覽列中顯示檢視

`Shell`類別會定義類型為的 `TitleView` 附加屬性 `View` ，可讓任何 Xamarin.Forms [`View`](xref:Xamarin.Forms.View) 顯示在導覽列中。

雖然這個屬性可以設定在子類別化 `Shell` 物件上，但也可以將它設定在想要在導覽列中顯示檢視的任何頁面上。 例如，下列 XAML 會顯示 [`Image`](xref:Xamarin.Forms.Image) 在的巡覽列中顯示 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<ContentPage ...>
    <Shell.TitleView>
        <Image Source="xamarin_logo.png"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Shell.TitleView>
    ...
</ContentPage>
```

這會導致影像顯示在頁面上的導覽列中：

![在 iOS 和 Android 上具有標題視圖的 Shell 頁面螢幕擷取畫面](configuration-images/titleview.png "具有標題視圖的 Shell 頁面")

> [!IMPORTANT]
> 如果已經將導覽列設定為隱藏 (使用 `NavBarIsVisible` 附加屬性)，就不會顯示標題檢視。

除非您使用和屬性來指定視圖的大小 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) ，或使用和屬性指定視圖的位置，否則不會在導覽列中顯示許多視圖 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。

由於 [`Layout`](xref:Xamarin.Forms.Layout) 類別衍生自 [`View`](xref:Xamarin.Forms.View) 類別，因此 `TitleView` 可以設定附加屬性，以顯示包含多個視圖的版面配置類別。 同樣地，因為 [`ContentView`](xref:Xamarin.Forms.ContentView) 類別最終衍生自 [`View`](xref:Xamarin.Forms.View) 類別，所以 `TitleView` 可以設定附加屬性，以顯示 `ContentView` 包含單一視圖的。

## <a name="page-visibility"></a>頁面可見度

Shell 會遵循頁面可見度，並使用 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 屬性來設定。 因此，當頁面的 `IsVisible` 屬性設定為時， `false` 不會顯示在 Shell 應用程式中，因此無法流覽至它。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms使用 XAML 樣式設定應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.FormsCSS Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
