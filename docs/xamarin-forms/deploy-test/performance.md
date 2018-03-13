---
title: "Xamarin.Forms 效能"
description: "有許多技巧可增加 Xamarin.Forms 應用程式的效能。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。 本文將描述並討論這些技巧。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: a750167cb9e6bde3a4a9abe11c5386497f9a12ae
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="xamarinforms-performance"></a>Xamarin.Forms 效能

_有許多技巧可增加 Xamarin.Forms 應用程式的效能。這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。本文將描述並討論這些技巧。_

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evolve 2016：使用 Xamarin.Forms 最佳化應用程式效能** \(英文\)

## <a name="overview"></a>總覽

不佳的應用程式效能會以許多方式表現。 它可能會讓應用程式看起來沒有回應、造成捲動緩慢，以及減少電池壽命。 不過，最佳化效能不僅僅只牽涉到實作有效率的程式碼而已。 同時也必須考量使用者對於應用程式效能的體驗。 例如，確保作業能在不封鎖使用者執行其他活動的情況下執行，將可以協助改善使用者體驗。

有一些技巧可以用來增加 Xamarin.Forms 應用程式的效能及使用者能體驗到的效能。 包括：

- [啟用 XAML 編譯器](#xamlc)
- [選擇正確的版面配置](#correctlayout)
- [啟用版面配置壓縮](#layoutcompression)
- [使用快速轉譯器](#fastrenderers)
- [減少不必要的繫結](#databinding)
- [最佳化版面配置效能](#optimizelayout)
- [最佳化 ListView 效能](#optimizelistview)
- [最佳化影像資源](#optimizeimages)
- [減少視覺化樹狀結構](#visualtree)
- [減少應用程式資源字典大小](#resourcedictionary)
- [使用自訂轉譯器模式](#rendererpattern)

> [!NOTE]
>  在閱讀本文之前，您應該先閱讀[跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)，其中探討可改善記憶體使用情況的非平台專用技術，以及使用 Xamarin 平台建置之應用程式的效能。

<a name="xamlc" />

## <a name="enable-the-xaml-compiler"></a>啟用 XAML 編譯器

XAML 可選擇性地使用 XAML 編譯器 (XAMLC) 直接編譯成中繼語言 (IL)。 XAMLC 提供許多優點：

- 它會執行 XAML 的編譯時間檢查，以通知使用者是否有任何錯誤。
- 它能免去 XAML 元素一部分的載入和具現化時間。
- 它能透過不再包含 .xaml 檔案來協助減少最終組件的檔案大小。

為確保回溯相容性，預設會停用 XAMLC。 不過，它可以在組件和類別層級啟用。 如需詳細資訊，請參閱[編譯 XAML](~/xamarin-forms/xaml/xamlc.md)。

<a name="correctlayout" />

## <a name="choose-the-correct-layout"></a>選擇正確的版面配置

能顯示多個子系但卻只有單一子系的版面配置，是很浪費資源的。 例如，下列程式碼範例會示範具有單一子系的 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <ContentPage.Content>
        <StackLayout>
            <Image Source="waterfront.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

這很浪費資源，因此應將 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 元素移除，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <ContentPage.Content>
        <Image Source="waterfront.jpg" />
    </ContentPage.Content>
</ContentPage>
```

此外，請勿嘗試使用其他版面配置的組合來重現特定版面配置的外觀，這會導致執行不必要的版面配置計算。 例如，請勿嘗試使用 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 執行個體的組合，重現 [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) 版面配置。 下列程式碼範例會示範此錯誤作法的範例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" />
                <Entry Placeholder="Enter your name" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Age:" />
                <Entry Placeholder="Enter your age" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Occupation:" />
                <Entry Placeholder="Enter your occupation" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Address:" />
                <Entry Placeholder="Enter your address" />
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

這很浪費資源，因為會執行不必要的版面配置計算。 改用 [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) 可以更恰當地實現所需的版面配置，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="100" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
            </Grid.RowDefinitions>
            <Label Text="Name:" />
            <Entry Grid.Column="1" Placeholder="Enter your name" />
            <Label Grid.Row="1" Text="Age:" />
            <Entry Grid.Row="1" Grid.Column="1" Placeholder="Enter your age" />
            <Label Grid.Row="2" Text="Occupation:" />
            <Entry Grid.Row="2" Grid.Column="1" Placeholder="Enter your occupation" />
            <Label Grid.Row="3" Text="Address:" />
            <Entry Grid.Row="3" Grid.Column="1" Placeholder="Enter your address" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

<a name="layoutcompression" />

## <a name="enable-layout-compression"></a>啟用版面配置壓縮

版面配置壓縮會從視覺化樹狀結構中移除指定的版面配置，以試圖改善頁面轉譯效能。 這所提供的效能優勢，會根據頁面的複雜性、所使用的作業系統版本，以及執行應用程式的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。 如需詳細資訊，請參閱[版面配置壓縮](~/xamarin-forms/user-interface/layouts/layout-compression.md)。

<a name="fastrenderers" />

## <a name="use-fast-renderers"></a>使用快速轉譯器

快速轉譯器可透過壓平所產生的原生控制項階層，來降低 Xamarin.Forms 控制項在 Android 上的膨脹和轉譯成本。 這能透過建立較少的物件來降低視覺化樹狀結構的複雜度，以進一步降低記憶體使用量並提升效能。 如需詳細資訊，請參閱[快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)。

<a name="databinding" />

## <a name="reduce-unnecessary-bindings"></a>減少不必要的繫結

請勿針對能輕易靜態設定的內容使用繫結。 將不需繫結的資料繫結並不會帶來任何好處，因為繫結本身並不符合成本效益。 例如，比起將 [`Button.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Text/) 繫結到含有 "Accept" 值的 ViewModel `string` 屬性，設定 `Button.Text = "Accept"` 的額外負荷將會較少。

<a name="optimizelayout" />

## <a name="optimize-layout-performance"></a>最佳化版面配置效能

Xamarin.Forms 2 導入會影響版面配置更新的最佳化版面配置引擎。 如果要獲得最佳的版面配置效能，請遵循下列指導方針：

- 指定 [`Margin`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) 屬性值，允許建立換行檢視數目較少的版面配置，以減少版面配置階層的深度。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。
- 使用 [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) 時，請盡可能不要將資料列和資料行設為 [`Auto`](https://developer.xamarin.com/api/property/Xamarin.Forms.GridLength.Auto/) 大小。 每個自動調整大小的資料列或資料行都會導致版面配置引擎執行額外的版面配置計算。 可能的話，請改用固定大小的資料列和資料行。 或是使用 [`GridUnitType.Star`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Star/) 列舉值將資料列和資料行設定為佔用成比例的空間量，前提是父樹狀結構也遵循這些版面配置指導方針。
- 除非必要，請勿設定版面配置的 [`VerticalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) 和 [`HorizontalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) 屬性。 [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) 和 [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/) 的預設值可提供最優異的版面配置最佳化。 變更這些屬性會衍生成本，同時也會消耗記憶體，即使是將它們設為預設值也一樣。
- 盡量避免使用 [`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/)。 它會導致 CPU 必須執行更多工作。
- 使用 [`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) 時，請盡可能避免使用 [`AbsoluteLayout.AutoSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.AbsoluteLayout.AutoSize/) 屬性。
- 使用 [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) 時，請確定只將一個子系設為 [`LayoutOptions.Expands`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/)。 此屬性可確保指定的子系會佔用 `StackLayout` 所能提供的最大空間，重複執行這些計算將會浪費資源。
- 請勿呼叫 [`Layout`](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) 類別的任一方法，因為它們會導致執行高成本的版面配置計算。 所需的版面配置行為通常可以透過改為設定 [`TranslationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) 和 [`TranslationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) 屬性來取得。 或是將 [`Layout<View>`](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) 類別子類別化，以實現所需的版面配置行為。
- 請勿過度頻繁地更新任何 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 執行個體，因為變更標籤大小會使系統需重新計算整個畫面的版面配置。
- 除非必要，請勿設定 [`Label.VerticalTextAlignment`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/) 屬性。
- 盡可能將任一 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 執行個體的 [`LineBreakMode`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.LineBreakMode/) 設為 [`NoWrap`](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.NoWrap/)。

<a name="optimizelistview" />

## <a name="optimize-listview-performance"></a>最佳化 ListView 效能

使用 [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控制項時，有許多使用者體驗應進行最佳化：

- **初始化**：自控制項建立時開始，並於畫面上顯示項目時結束的時間間隔。
- **捲動**：能夠捲動清單並確保 UI 跟得上觸控手勢。
- **互動**：加入、刪除和選取項目的互動。

[`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) 控制項需要應用程式提供資料和儲存格範本。 實現此目標的方式將對控制項的效能產生很大的影響。 如需詳細資訊，請參閱 [ListView 效能](~/xamarin-forms/user-interface/listview/performance.md)。

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>最佳化影像資源

顯示影像資源可能會大幅增加應用程式的記憶體使用量。 因此應該只有在必要時才建立它們，且應在應用程式不再需要它們時應立即釋出。 例如，如果某應用程式是藉由從資料流讀取影像資料來顯示影像，請務必只在必要時才建立資料流，且務必於不再需要資料流時將它釋出。 若要實現此目標，可藉由在頁面建立時或 [`Page.Appearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) 事件觸發時建立資料流，然後在 [`Page.Disappearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Disappearing/) 事件觸發時處置資料流。

使用 [`ImageSource.FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/) 方法下載要顯示的影像時，務必將 [`UriImageSource.CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) 屬性設為 `true` 以快取下載的影像。 如需詳細資訊，請參閱[使用影像](~/xamarin-forms/user-interface/images.md)。

如需詳細資訊，請參閱[最佳化影像資源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)。

<a name="visualtree" />

## <a name="reduce-the-visual-tree-size"></a>減少視覺化樹狀結構

減少頁面上的元素數目，可讓頁面轉譯速度變得更快。 有兩個主要技巧可以實現此目標。 其一是隱藏看不見的元素。 每個元素的 [`IsVisible`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) 屬性會決定該元素是否應成為視覺化樹狀結構的一部分。 因此，如果某個元素因為隱藏在其他元素背後而看不見，請移除該元素或將其 `IsVisible` 屬性設為 `false`。

第二個技巧是移除不必要的元素。 例如，下列程式碼範例顯示的頁面版面配置，會顯示一系列的 [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) 元素：

```xaml
<ContentPage.Content>
    <StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Hello" />
        </StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Welcome to the App!" />
        </StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Downloading Data..." />
        </StackLayout>
    </StackLayout>
</ContentPage.Content>
```

在減少元素數目的情況下，仍然能維持相同的頁面版面配置，如下列程式碼範例所示：

```xaml
<ContentPage.Content>
  <StackLayout Padding="20,20,0,0" Spacing="25">
    <Label Text="Hello" />
    <Label Text="Welcome to the App!" />
    <Label Text="Downloading Data..." />
  </StackLayout>
</ContentPage.Content>
```

<a name="resourcedictionary" />

## <a name="reduce-the-application-resource-dictionary-size"></a>減少應用程式資源字典大小

應用程式中所使用的任何資源，都應儲存在應用程式的資源字典中以避免重複。 這有助於減少整個應用程式中必須剖析的 XAML 數目。 下列程式碼範例顯示 `HeadingLabelStyle` 資源，由於它是用於整個應用程式，因此會定義於應用程式的資源字典中：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Resources.App">
     <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
     </Application.Resources>
</Application>
```

不過，某個頁面特有的 XAML 則不應包含在應用程式的資源字典中，因為這會使系統在應用程式啟動時剖析該資源，而非視頁面需求進行剖析。 如果某個資源是由某個非啟動頁面的頁面使用，則應將它放在該頁面的資源字典中，這有助於減少應用程式啟動時所剖析的 XAML 數目。 下列程式碼範例顯示 `HeadingLabelStyle` 資源，由於它僅用於單一頁面，因此我們將它定義於頁面的資源字典中：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Test.HomePage"
             Padding="0,20,0,0">
     <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
     </ContentPage.Resources>
    <ContentPage.Content>
      ...
    </ContentPage.Content>
</ContentPage>

```

如需應用程式資源的詳細資訊，請參閱[`Working with Styles`](~/xamarin-forms/user-interface/styles/index.md)。

<a name="rendererpattern" />

## <a name="use-the-custom-renderer-pattern"></a>使用自訂轉譯器模式

大部分的轉譯器類別會公開 `OnElementChanged` 方法。在建立 Xamarin.Forms 自訂控制項以呈現對應的原生控制項時，便會呼叫此方法。 在每個平台特定的轉譯器類別中自訂轉譯器類別，然後覆寫此方法以具現化並自訂原生控制項。 `SetNativeControl` 方法可用來具現化原生控制項，且這個方法也會將控制項參考指派給 `Control` 屬性。

但在某些情況下，可能會一再呼叫 `OnElementChanged` 方法。 因此，為避免因記憶體流失而影響效能，在具現化新的原生控制項時，請務必謹慎。 下列程式碼範例顯示在自訂轉譯器中具現化新的原生控制項時所使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

當 `Control` 屬性是 `null` 時，新的原生控制項只應具現化一次。 應該只在自訂控制項附加於新的 Xamarin.Forms 元素時，才設定控制項並訂閱事件處理常式。 同樣地，應該只在轉譯器附加到的元素變更時，才取消訂閱任何已訂閱的事件處理常式。 採用這個方法將有助於建立有效執行的自訂轉譯器，避免發生記憶體流失。

如需自訂轉譯器的詳細資訊，請參閱[在每個平台上自訂控制項](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

## <a name="summary"></a>總結

本文章已說明與討論用來增加 Xamarin.Forms 應用程式效能的技巧。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。


## <a name="related-links"></a>相關連結

- [跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [ListView 效能](~/xamarin-forms/user-interface/listview/performance.md)
- [快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)
- [版面配置壓縮](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Xamarin.Forms 影像調整器範例](https://developer.xamarin.com/samples/xamarin-forms/XamFormsImageResize/)
- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilation/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
