---
title: Xamarin.Forms Shell 頁面
description: Shell 類別會定義可用來設定 Shell 應用程式中頁面外觀的附加屬性 Xamarin.Forms 。 這包括設定頁面色彩、停用導覽列、停用索引標籤列，以及在導覽列中顯示檢視。
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c008ffb6d58a86301f41fdaa54b23eb2d9869618
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101760260"
---
# <a name="xamarinforms-shell-pages"></a>Xamarin.Forms Shell 頁面

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

[`ShellContent`](xref:Xamarin.Forms.ShellContent)物件代表 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 每個或的物件 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) [`Tab`](xref:Xamarin.Forms.Tab) 。 當多個 `ShellContent` 物件出現在 `Tab` 物件中時，`ContentPage` 物件將可透過頂端索引標籤導覽。 在頁面中， `ContentPage` 可以流覽其他稱為詳細資料頁面的物件。

此外，此 [`Shell`](xref:Xamarin.Forms.Shell) 類別會定義可用來設定 Xamarin.Forms Shell 應用程式中頁面外觀的附加屬性。 這包括設定頁面色彩、設定頁面簡報模式、停用導覽列、停用索引標籤列，以及在導覽列中顯示視圖。

## <a name="display-pages"></a>顯示頁面

在 Xamarin.Forms Shell 應用程式中，頁面通常會視需要建立以回應導覽。 您可以使用 [`DataTemplate`](xref:Xamarin.Forms.Xaml.DataTemplateExtension) 標記延伸，將 [`ContentTemplate`](xref:Xamarin.Forms.ShellContent.ContentTemplate) 每個物件的屬性設定 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 為物件，以達成此目的 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
       <ShellContent Title="Cats"
                     Icon="cat.png"
                     ContentTemplate="{DataTemplate views:CatsPage}" />
       <ShellContent Title="Dogs"
                     Icon="dog.png"
                     ContentTemplate="{DataTemplate views:DogsPage}" />
       <ShellContent Title="Monkeys"
                     Icon="monkey.png"
                     ContentTemplate="{DataTemplate views:MonkeysPage}" />
    </TabBar>
</Shell>
```

在此範例中，會使用 Shell 的隱含轉換運算子來移除 [`Tab`](xref:Xamarin.Forms.Tab) 視覺階層中的物件。 不過，每個物件都會轉譯成一個索引標籤 [`ShellContent`](xref:Xamarin.Forms.ShellContent) ：

[![螢幕擷取畫面：在 iOS 和 Android 上有三個頁面的 Shell 應用程式](pages-images/three-pages.png)](pages-images/three-pages-large.png#lightbox)

> [!NOTE]
> [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)每個 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件的都會繼承自父 [`Tab`](xref:Xamarin.Forms.Tab) 物件。

在每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件中， `ContentPage` 都可以導覽至其他物件。 如需導覽的詳細資訊，請參閱[ Xamarin.Forms Shell 導覽](navigation.md)。

## <a name="load-pages-at-application-startup"></a>在應用程式啟動時載入頁面

在 Shell 應用程式中，每個 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 物件通常會視需要建立，以回應導覽。 不過，您也可以 `ContentPage` 在應用程式啟動時建立物件。

> [!WARNING]
> [`ContentPage`](xref:Xamarin.Forms.ContentPage) 在應用程式啟動時建立的物件可能會導致啟動經驗不佳。

[`ContentPage`](xref:Xamarin.Forms.ContentPage) 藉由將屬性設定為物件，可以在應用程式啟動時建立物件 [`ShellContent.Content`](xref:Xamarin.Forms.ShellContent.Content) `ContentPage` ：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
     <ShellContent Title="Cats"
                   Icon="cat.png">
         <views:CatsPage />
     </ShellContent>
     <ShellContent Title="Dogs"
                   Icon="dog.png">
         <views:DogsPage />
     </ShellContent>
     <ShellContent Title="Monkeys"
                   Icon="monkey.png">
         <views:MonkeysPage />
     </ShellContent>
    </TabBar>
</Shell>
```

在此範例中，、 `CatsPage` `DogsPage` 和 `MonkeysPage` 都是在應用程式啟動時建立，而不是依需求來回應導覽。

> [!NOTE]
> [`Content`](xref:Xamarin.Forms.ShellContent.Content)屬性是類別的 content 屬性 [`ShellContent`](xref:Xamarin.Forms.ShellContent) ，因此不需要明確設定。

## <a name="set-page-colors"></a>設定頁面色彩

[`Shell`](xref:Xamarin.Forms.Shell)類別會定義下列可用於在 Shell 應用程式中設定頁面色彩的附加屬性：

- [`BackgroundColor`](xref:Xamarin.Forms.Shell.BackgroundColorProperty)型別為的， [`Color`](xref:Xamarin.Forms.Color) 定義 Shell chrome 中的背景色彩。 色彩將不會填入 Shell 內容的背景。
- [`DisabledColor`](xref:Xamarin.Forms.Shell.DisabledColorProperty)型別為的， [`Color`](xref:Xamarin.Forms.Color) 定義停用文字和圖示的色彩。
- [`ForegroundColor`](xref:Xamarin.Forms.Shell.ForegroundColorProperty)型別為的， [`Color`](xref:Xamarin.Forms.Color) 定義用來為文字和圖示加上網底的色彩。
- [`TitleColor`](xref:Xamarin.Forms.Shell.TitleColorProperty)型別為的， [`Color`](xref:Xamarin.Forms.Color) 定義用於目前頁面標題的色彩。
- [`UnselectedColor`](xref:Xamarin.Forms.Shell.UnselectedColorProperty)型別為的， [`Color`](xref:Xamarin.Forms.Color) 定義用於 Shell chrome 中未選取文字和圖示的色彩。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標，並使用 XAML 樣式來設計樣式。 此外，這些屬性可以使用階層式樣式表 (CSS) 來設定。 如需詳細資訊，請參閱[ Xamarin.Forms Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)。

> [!NOTE]
> 也有可定義索引標籤色彩的屬性。 如需詳細資訊，請參閱[索引標籤外觀](tabs.md#tab-appearance)。

下列 XAML 會顯示子類別化類別中的色彩屬性設定 [`Shell`](xref:Xamarin.Forms.Shell) ：

```xaml
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

如需 XAML 樣式的詳細資訊，請參閱 [ Xamarin.Forms 使用 xaml 樣式設定應用程式](~/xamarin-forms/user-interface/styles/xaml/index.md)的樣式。

## <a name="set-page-presentation-mode"></a>設定頁面簡報模式

根據預設，當使用方法流覽至頁面時，會發生小型的流覽動畫 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) 。 不過，您可以藉由將 [`Shell.PresentationMode`](xref:Xamarin.Forms.Shell.PresentationModeProperty) 中的附加屬性設定 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 為其中一個列舉成員，來變更此行為 [`PresentationMode`](xref:Xamarin.Forms.PresentationMode) ：

- `NotAnimated` 表示頁面將會顯示，而不會有導覽動畫。
- `Animated` 表示頁面將會顯示流覽動畫。 這是附加屬性的預設值 `Shell.PresentationMode` 。
- `Modal` 表示頁面將會顯示為強制回應頁面。
- `ModalAnimated` 表示頁面將會顯示為具有導覽動畫的強制回應頁面。
- `ModalNotAnimated` 表示頁面將會顯示為強制回應頁面，而不需要流覽動畫。

> [!IMPORTANT]
> 此 `PresentationMode` 類型為旗標列舉。 這表示可以在程式碼中套用列舉成員的組合。 不過，為了方便在 XAML 中使用，此 `ModalAnimated` 成員是和成員的組合 `Animated` `Modal` ，而 `ModalNotAnimated` 成員是 `NotAnimated` 和成員的組合 `Modal` 。 如需旗標列舉的詳細資訊，請參閱 [以位旗標列舉類型](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags)。

下列 XAML 範例會 [`Shell.PresentationMode`](xref:Xamarin.Forms.Shell.PresentationModeProperty) 在上設定附加屬性 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

在此範例中， [`ContentPage`](xref:Xamarin.Forms.ContentPage) 當使用方法流覽至頁面時，會設定為顯示為強制回應頁面 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) 。

## <a name="enable-navigation-bar-shadow"></a>啟用導覽列陰影

[`Shell.NavBarHasShadow`](xref:Xamarin.Forms.Shell.NavBarHasShadowProperty)附加的屬性（類型為 `bool` ）會控制導覽列是否有陰影。 根據預設，屬性的值是 `false` 在 iOS 和 `true` Android 上。

雖然可以在子類別化物件上設定此屬性 [`Shell`](xref:Xamarin.Forms.Shell) ，但也可以在想要啟用導覽列陰影的任何頁面上設定此屬性。 例如，下列 XAML 顯示如何從啟用巡覽列陰影 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

這會導致啟用巡覽列陰影。

## <a name="disable-the-navigation-bar"></a>停用瀏覽列

[`Shell.NavBarIsVisible`](xref:Xamarin.Forms.Shell.NavBarIsVisibleProperty)如果顯示頁面，則為類型的附加屬性 `bool` （property），可控制導覽列。 根據預設，該屬性的值為 `true`。

雖然您可以在子類別化物件上設定此屬性 [`Shell`](xref:Xamarin.Forms.Shell) ，但通常會將它設定在想要隱藏巡覽列的任何頁面上。 例如，下列 XAML 會顯示停用的導覽列 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

這會導致當該頁面呈現時，導覽列變成是隱藏的：

![IOS 和 Android 上具有隱藏巡覽列之 Shell 頁面的螢幕擷取畫面](pages-images/navigationbar-invisible.png)

## <a name="display-views-in-the-navigation-bar"></a>在導覽列中顯示檢視

[`Shell.TitleView`](xref:Xamarin.Forms.Shell.TitleViewProperty)型別的附加屬性（property [`View`](xref:Xamarin.Forms.View) ）可讓任何 `View` 顯示在導覽列中。

雖然可以在子類別化物件上設定此屬性 [`Shell`](xref:Xamarin.Forms.Shell) ，但也可以在想要在導覽列中顯示視圖的任何頁面上設定此屬性。 例如，下列 XAML 顯示 [`Image`](xref:Xamarin.Forms.Image) 在的巡覽列中顯示 [`ContentPage`](xref:Xamarin.Forms.ContentPage) ：

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

![IOS 和 Android 上具有標題視圖之 Shell 頁面的螢幕擷取畫面](pages-images/titleview.png "具有標題視圖的 Shell 頁面")

> [!IMPORTANT]
> 如果已使用附加屬性將巡覽列設為隱藏， [`NavBarIsVisible`](xref:Xamarin.Forms.Shell.NavBarIsVisibleProperty) 則不會顯示標題視圖。

除非使用和屬性指定視圖的大小 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) ，或使用和屬性指定視圖的位置，否則許多視圖都不會出現在巡覽列中 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。

因為 [`Layout`](xref:Xamarin.Forms.Layout) 類別衍生自 [`View`](xref:Xamarin.Forms.View) 類別，所以 [`TitleView`](xref:Xamarin.Forms.Shell.TitleViewProperty) 可以將附加屬性設定為顯示包含多個視圖的版面配置類別。 同樣地，因為 [`ContentView`](xref:Xamarin.Forms.ContentView) 類別最終衍生自 [`View`](xref:Xamarin.Forms.View) 類別，所以 `TitleView` 附加的屬性可以設定為顯示 `ContentView` 包含單一視圖的。

## <a name="page-visibility"></a>頁面可見度

Shell 會遵循頁面可見度，以屬性進行設定 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) 。 因此，當頁面的 `IsVisible` 屬性設定為時， `false` 不會顯示在 Shell 應用程式中，也無法流覽至該頁面。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms Shell 導覽](navigation.md)
- [Xamarin.Forms使用 XAML 樣式設定應用程式的樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.Forms CSS Shell 特定屬性](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
