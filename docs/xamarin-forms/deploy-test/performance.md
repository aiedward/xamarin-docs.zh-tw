---
title: 改進 Xamarin.Forms 應用程式效能
description: 有許多技巧可增加 Xamarin.Forms 應用程式的效能。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2019
ms.openlocfilehash: 4427d347723284a2f8897612f10857270c9631bf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305210"
---
# <a name="improve-xamarinforms-app-performance"></a>改進 Xamarin.Forms 應用程式效能

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evolve 2016：使用 Xamarin.Forms 最佳化應用程式效能** \(英文\)

不佳的應用程式效能會以許多方式表現。 它可能會讓應用程式看起來沒有回應、造成捲動緩慢，以及減少裝置電池壽命。 不過，最佳化效能不僅僅只牽涉到實作有效率的程式碼而已。 同時也必須考量使用者對於應用程式效能的體驗。 例如，確保作業能在不封鎖使用者執行其他活動的情況下執行，將可以協助改善使用者體驗。

有許多技術可用來提升 Xamarin.Forms 應用程式的效能和認知效能。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。

> [!NOTE]
>  在閱讀本文之前，您應該先閱讀[跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)，其中討論了非平台的特定技術來改善記憶體使用量和使用 Xamarin 平台建置之應用程式的效能。

## <a name="enable-the-xaml-compiler"></a>啟用 XAML 編譯器

XAML 可選擇性地使用 XAML 編譯器 (XAMLC) 直接編譯成中繼語言 (IL)。 XAMLC 提供許多優點：

- 它會執行 XAML 的編譯時間檢查，以通知使用者是否有任何錯誤。
- 它能免去 XAML 元素一部分的載入和具現化時間。
- 它能透過不再包含 .xaml 檔案來協助減少最終組件的檔案大小。

預設會在新的 Xamarin Forms 解決方案中啟用 XAMLC。 不過，可能需要在較舊的解決方案中啟用它。 如需詳細資訊，請參閱[編譯 XAML](~/xamarin-forms/xaml/xamlc.md)。

## <a name="use-compiled-bindings"></a>使用編譯的繫結

編譯的繫結會在編譯時間而非執行階段使用反映來解析繫結運算式，藉以提升 Xamarin.Forms 應用程式中的資料繫結效能。 編譯繫結運算式會產生編譯的程式碼，通常會使用比傳統繫結快上 8-20 倍的速度來解析繫結。 如需詳細資訊，請參閱[編譯繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

## <a name="reduce-unnecessary-bindings"></a>減少不必要的繫結

請勿針對能輕易靜態設定的內容使用繫結。 將不需繫結的資料繫結並不會帶來任何好處，因為繫結本身並不符合成本效益。 例如,與綁定`Button.Text = "Accept"`[`Button.Text`](xref:Xamarin.Forms.Button.Text)到值"接受"的視圖模型`string`屬性相比,設置的開銷要小。

## <a name="use-fast-renderers"></a>使用快速轉譯器

快速轉譯器可透過壓平所產生的原生控制項階層，來降低 Xamarin.Forms 控制項在 Android 上的膨脹和轉譯成本。 這能透過建立較少的物件來降低視覺化樹狀結構的複雜度，以進一步降低記憶體使用量並提升效能。

從 Xamarin.Forms 4.0 開始，所有以 `FormsAppCompatActivity` 為目標的應用程式預設都會使用快速轉譯器。 如需詳細資訊，請參閱[快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)。

## <a name="enable-startup-tracing-on-android"></a>在 Android 上啟用啟動追蹤

Android 上的預先 (AOT) 編譯可將 Just in Time (JIT) 應用程式啟動額外負荷和記憶體使用量降到最低，代價則是建立更大的 APK。 替代方法是使用啟動追蹤，相較於傳統 AOT 編譯，這可在 Android APK 大小和啟動時間之間進行取捨。

與其盡可能將應用程式編譯為非受控程式碼，啟動追蹤只會編譯一組受控方法，這些方法代表空白 Xamarin Forms 應用程式中成本最高的應用程式啟動部分。 相較於傳統的 AOT 編譯，此方法會導致 APK 大小降低，同時仍會提供類似的啟動改進。

## <a name="enable-layout-compression"></a>啟用版面配置壓縮

版面配置壓縮會從視覺化樹狀結構中移除指定的版面配置，以試圖改善頁面轉譯效能。 這所提供的效能優勢，會根據頁面的複雜性、所使用的作業系統版本，以及執行應用程式的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。 如需詳細資訊，請參閱[版面配置壓縮](~/xamarin-forms/user-interface/layouts/layout-compression.md)。

## <a name="choose-the-correct-layout"></a>選擇正確的版面配置

能顯示多個子系但卻只有單一子系的版面配置，是很浪費資源的。 例如, 以下代碼範例顯示 有[`StackLayout`](xref:Xamarin.Forms.StackLayout)單個子項目的 :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

這是浪費,[`StackLayout`](xref:Xamarin.Forms.StackLayout)應刪除該元素,如以下代碼範例所示:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

此外，請勿嘗試使用其他版面配置的組合來重現特定版面配置的外觀，這會導致執行不必要的版面配置計算。 例如,不要嘗試使用實例的組合[`Grid`](xref:Xamarin.Forms.Grid)[`StackLayout`](xref:Xamarin.Forms.StackLayout)來重現佈局。 下列程式碼範例會示範此錯誤作法的範例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
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
</ContentPage>
```

這很浪費資源，因為會執行不必要的版面配置計算。 相反,可以使用 可以更好地使用 實現所需[`Grid`](xref:Xamarin.Forms.Grid)的佈局 ,如以下代碼範例所示:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
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
</ContentPage>
```

## <a name="optimize-layout-performance"></a>將版面配置效能最佳化

如果要獲得最佳的版面配置效能，請遵循下列指導方針：

- 通過指定[`Margin`](xref:Xamarin.Forms.View.Margin)屬性值來減小佈局層次結構的深度,從而允許創建具有較少環繞視圖的佈局。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。
- 使用[`Grid`](xref:Xamarin.Forms.Grid)時 ,請嘗試確保盡可能少的行和[`Auto`](xref:Xamarin.Forms.GridLength.Auto)列設置為 大小。 每個自動調整大小的資料列或資料行都會導致版面配置引擎執行額外的版面配置計算。 可能的話，請改用固定大小的資料列和資料行。 或者,設置行和列以佔用具有[`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star)枚舉值的比例空間量,前提是父樹遵循這些佈局準則。
- 除非需要,[`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions)否則不要[`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions)設置 佈局的和 屬性。 的[`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)預設值[`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand), 允許最佳化最佳佈局。 變更這些屬性會衍生成本，同時也會消耗記憶體，即使是將它們設為預設值也一樣。
- 盡可能避免[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)使用 。 它會導致 CPU 必須執行更多工作。
- 使用[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)時 ,請[`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize)盡可能避免 使用 屬性。
- 使用[`StackLayout`](xref:Xamarin.Forms.StackLayout)時 ,請確保只有一個子級[`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)設定為 。 此屬性可確保指定的子系會佔用 `StackLayout` 所能提供的最大空間，重複執行這些計算將會浪費資源。
- 避免調用[`Layout`](xref:Xamarin.Forms.Layout)類的任何方法,因為它們會導致執行昂貴的布局計算。 相反,可以通過[`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)設置[`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)和 屬性來獲取所需的布局行為。 或者,對類進行[`Layout<View>`](xref:Xamarin.Forms.Layout`1)子類,以實現所需的布局行為。
- 不要更新任何[`Label`](xref:Xamarin.Forms.Label)實例的頻率超過要求,因為標籤大小的更改可能會導致重新計算整個螢幕佈局。
- 除非需要,[`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment)否則不要設定該屬性。
- 可能將[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)任何[`Label`](xref:Xamarin.Forms.Label)實體的設定為[`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap)

## <a name="use-asynchronous-programming"></a>使用非同步程式設計

通過使用非同步程式設計,可以增強應用程式的總體回應能力,並經常避免性能瓶頸。 在 .NET 中,[基於任務的非同步模式 (TAP)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)是非同步操作的建議設計模式。 但是,使用 TAP 不正確可能會導致應用程式性能不執行。 因此,在使用 TAP 時,應遵循以下準則。

### <a name="fundamentals"></a>基礎

- 瞭解任務生命週期,該生命週期由`TaskStatus`枚舉表示。 有關詳細資訊,請參閱[任務狀態](https://devblogs.microsoft.com/pfxteam/the-meaning-of-taskstatus/)和[任務狀態](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap#task-status)的含義。
- 使用`Task.WhenAll`方法非同步等待多個非同步操作完成,而不是單獨執行`await`一系列非同步操作。 有關詳細資訊,請參閱[Task.When。](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall)
- 使用`Task.WhenAny`方法異步等待多個非同步操作之一完成。 有關詳細資訊,請參閱[Task.WhenAny。](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall)
- 使用`Task.Delay`方法生成在`Task`指定時間之後完成的物件。 這對於數據輪詢和延遲在預定時間內處理用戶輸入等方案非常有用。 有關詳細資訊,請參閱[任務.delay](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskdelay).
- 使用`Task.Run`方法線上程池上執行密集的同步 CPU 操作。 此方法是方法的快捷方式,`TaskFactory.StartNew`並設置了最佳參數。 有關詳細資訊,請參閱[Task.Run](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskrun)。
- 避免嘗試創建非同步構造函數。 相反,使用生命週期事件或單獨的初始化邏輯來正確`await`正確實現任何初始化。 有關詳細資訊,請參閱blog.stephencleary.com上的[非同步建構函數](https://blog.stephencleary.com/2013/01/async-oop-2-constructors.html)。
- 使用延遲任務模式以避免等待非同步操作在應用程式啟動期間完成。 有關詳細資訊,請參閱[非同步拉茲](https://devblogs.microsoft.com/pfxteam/asynclazyt/)。
- 通過創建`TaskCompletionSource<T>`物件,為不使用 TAP 的現有非同步操作創建任務包裝器。 這些物件獲得了可`Task`編程性的好處,並使您能夠控制關聯`Task`的存留期和完成。 有關詳細資訊,請參閱[工作完成來源的性質](https://devblogs.microsoft.com/pfxteam/the-nature-of-taskcompletionsourcetresult/)。
 
- 當不需要`Task`處理非同步操作的結果時`Task`, 傳回物件,而不是返回等待的物件。 由於執行的上下文切換較少,因此性能會更大。
- 在工作並行庫 (TPL) 資料流庫(例如在資料可用時處理資料)或有多個操作必須非同步通訊時,請使用任務並行庫 (TPL) 資料流庫。 有關詳細資訊,請參閱[數據流(任務並行庫)。](/dotnet/standard/parallel-programming/dataflow-task-parallel-library)

### <a name="ui"></a>UI

- 調用 API 的非同步版本(如果可用)。 這可讓 UI 執行緒保持在未遭封鎖的狀態，協助改善使用者使用應用程式的體驗。
- 使用UI線程上的非同步操作的數據更新 UI 元素,以避免引發異常。 但是,對`ListView.ItemsSource`屬性的更新將自動封送到 UI 線程。 有關確定代碼是否在 UI 線程上執行的資訊,請參閱[Xamarin.](~/essentials/main-thread.md?content=xamarin/xamarin-forms)

    > [!IMPORTANT]
    > 通過數據綁定更新的任何控制項屬性將自動封送到 UI 線程。

### <a name="error-handling"></a>錯誤處理

- 瞭解異步異常處理。 由非同步運行的代碼引發的未處理異常將傳播回調用線程,但在某些情況下除外。 有關詳細資訊,請參閱[錯誤(工作並行庫)](/dotnet/standard/parallel-programming/exception-handling-task-parallel-library)。
- 避免創建`async void`方法,而是建立`async Task`方法。 這些功能使錯誤處理、可組合性和可測試性更容易。 此準則的例外情況是非同步事件處理程式,它必須傳回`void`。 有關詳細資訊,請參閱[避免非同步虛空](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#avoid-async-void)。
- 不要通過調用`Task.Wait``Task.Result`、`GetAwaiter().GetResult`或 方法混合阻塞和非同步代碼,因為它們可能會導致發生死鎖。 但是,如果必須違反此準則,首選方法是調用 方法,`GetAwaiter().GetResult`因為它保留任務異常。 有關詳細資訊,請參閱[.NET 4.5 中的](https://devblogs.microsoft.com/pfxteam/task-exception-handling-in-net-4-5/)["一路同步"](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way)和"任務異常處理"。
- 盡可能`ConfigureAwait`使用方法創建無上下文代碼。 無上下文代碼對移動應用程式具有更好的性能,是使用部分非同步代碼庫時避免死鎖的有用技術。 有關詳細資訊,請參閱[設定上下文](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#configure-context)。
- 此功能(如處理前一個非同步動作引發的例外)與繼續啟動之前或執行時取消延續等功能使用*延續工作*。 有關詳細資訊,請參閱[使用連續工作連結工作](/dotnet/standard/parallel-programming/chaining-tasks-by-using-continuation-tasks)。
- 從 呼叫`ICommand`非同步作業時`ICommand`, 請使用非同步的實作。 這可確保非同步命令邏輯中的任何異常都可以處理。 有關詳細資訊,請參閱[非同步編程:非同步 MVVM 應用程式的模式:命令](/archive/msdn-magazine/2014/april/async-programming-patterns-for-asynchronous-mvvm-applications-commands)。

## <a name="choose-a-dependency-injection-container-carefully"></a>謹慎選擇相依性插入容器

相依性插入容器會對行動應用程式引進額外的效能限制式。 使用容器來登錄和解析類型具有效能成本，因為容器會使用反映來建立每種類型，特別是在針對應用程式中的每個頁面巡覽重建相依性時。 如果有許多或深度相依性，則建立的成本可能會大幅增加。 此外，根據使用的容器而定，類型註冊 (通常會在應用程式啟動期間進行) 可能會對啟動時間產生明顯的影響。

替代方法是使用 Factory 手動實作相依性插入，使其更具效能。

## <a name="create-shell-applications"></a>建立 Shell 應用程式

Xamarin.Forms Shell 應用程式會根據飛出視窗和索引標籤，提供固定的巡覽體驗。 如果您的應用程式使用者體驗可以透過 Shell 實作，那麼，這樣做是有助益的。 Shell 應用程式有助於避免產生不佳的啟動體驗，因為頁面會在需要時 (而非應用程式啟動時) 建立來回應巡覽，這會發生於使用 [`TabbedPage'](xref:Xamarin.Forms.TabbedPage) 的應用程式中。 有關詳細資訊,請參閱[Xamarin.Forms 外殼](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="use-collectionview-instead-of-listview"></a>使用 CollectionView 而非 ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView)是使用不同布局規範顯示數據列表的檢視。 它提供了一個更靈活,執行的替代方案[`ListView`](xref:Xamarin.Forms.ListView)。 如需詳細資訊，請參閱 [Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)。

## <a name="optimize-listview-performance"></a>將 ListView 效能最佳化

使用[`ListView`](xref:Xamarin.Forms.ListView)時,有許多用戶體驗需要優化:

- **初始化**：自控制項建立時開始，並於畫面上顯示項目時結束的時間間隔。
- **捲動**：能夠捲動清單並確保 UI 跟得上觸控手勢。
- **互動**：加入、刪除和選取項目的互動。

該[`ListView`](xref:Xamarin.Forms.ListView)控件需要應用程式提供數據和單元格範本。 實現此目標的方式將對控制項的效能產生很大的影響。 如需詳細資訊，請參閱 [ListView 效能](~/xamarin-forms/user-interface/listview/performance.md)。

## <a name="optimize-image-resources"></a>最佳化影像資源

顯示影像資源可能會大幅增加應用程式的磁碟使用量。 因此應該只有在必要時才建立它們，且應在應用程式不再需要它們時應立即釋出。 例如，如果某應用程式是藉由從資料流讀取影像資料來顯示影像，請務必只在必要時才建立資料流，且務必於不再需要資料流時將它釋出。 這可以通過在創建頁面時創建流[`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing)或 事件觸發時實現,[`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing)然後在 事件觸發時釋放流來實現。

下載使用[`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri))方法顯示的圖像時,請通過[`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled)確保 屬性`true`設置為 快取下載的影像。 如需詳細資訊，請參閱[使用影像](~/xamarin-forms/user-interface/images.md)。

如需詳細資訊，請參閱[最佳化影像資源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)。

## <a name="reduce-the-visual-tree-size"></a>減少視覺化樹狀結構大小

減少頁面上的元素數目，可讓頁面轉譯速度變得更快。 有兩個主要技巧可以實現此目標。 其一是隱藏看不見的元素。 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)每個元素的屬性確定元素是否應是可視化樹的一部分。 因此，如果某個元素因為隱藏在其他元素背後而看不見，請移除該元素或將其 `IsVisible` 屬性設為 `false`。

第二個技巧是移除不必要的元素。 例如,以下代碼範例顯示包含多個[`Label`](xref:Xamarin.Forms.Label)物件的頁面佈局:

```xaml
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
```

在減少元素數目的情況下，仍然能維持相同的頁面版面配置，如下列程式碼範例所示：

```xaml
<StackLayout Padding="20,35,20,20" Spacing="25">
  <Label Text="Hello" />
  <Label Text="Welcome to the App!" />
  <Label Text="Downloading Data..." />
</StackLayout>
```

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

不過，頁面特有的 XAML 不應包含於應用程式的資源字典中，因為資源接著將在應用程式啟動時 (而非在頁面要求時) 進行剖析。 如果某個資源是由某個非啟動頁面的頁面使用，則應將它放在該頁面的資源字典中，這有助於減少應用程式啟動時所剖析的 XAML 數目。 下列程式碼範例顯示 `HeadingLabelStyle` 資源，由於它僅用於單一頁面，因此我們將它定義於頁面的資源字典中：

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
    ...
</ContentPage>
```

如需應用程式資源的詳細資訊，請參閱 [XAML 樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

## <a name="use-the-custom-renderer-pattern"></a>使用自訂轉譯器模式

大部分的 Xamarin.Forms 轉譯器類別都會公開 `OnElementChanged` 方法，在建立 Xamarin.Forms 自訂控制項以轉譯對應的原生控制項時呼叫此方法。 在每個平台專案中自訂轉譯器類別，然後覆寫此方法以具現化並自訂原生控制項。 `SetNativeControl` 方法可用來具現化原生控制項，且這個方法也會將控制項參考指派給 `Control` 屬性。

但在某些情況下，可能會一再呼叫 `OnElementChanged` 方法。 因此，為避免因記憶體流失而影響效能，在具現化新的原生控制項時，請務必謹慎。 下列程式碼範例顯示在自訂轉譯器中具現化新的原生控制項時所使用的方法：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null)
  {
    if (Control == null)
    {
      // Instantiate the native control with the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

當 `Control` 屬性是 `null` 時，新的原生控制項只應具現化一次。 此外，應該只在自訂控制項附加於新的 Xamarin.Forms 元素時，才建立、設定控制項並訂閱事件處理常式。 同樣地，應該只在轉譯器附加到的元素變更時，才取消訂閱任何已訂閱的事件處理常式。 採用這個方法將有助於建立有效執行的自訂轉譯器，避免發生記憶體流失。

> [!IMPORTANT]
> 只有在 `e.NewElement` 屬性不是 `null`，且 `Control` 屬性為 `null` 時，才應叫用 `SetNativeControl` 方法。

如需自訂轉譯器的詳細資訊，請參閱[在每個平台上自訂控制項](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

## <a name="related-links"></a>相關連結

- [跨平臺效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [編譯 XAML](~/xamarin-forms/xaml/xamlc.md)
- [編譯的繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)
- [快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)
- [版面配置壓縮](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)
- [ListView 效能](~/xamarin-forms/user-interface/listview/performance.md)
- [最佳化影像資源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [XAML 樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [自訂每個平台上的控制項](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
