---
title: 改善 Xamarin.Forms 應用程式效能
description: 有許多技術可提高應用程式的效能 Xamarin.Forms 。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 96b5939fd1f8448d45d1398fd56770f9032de083
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139109"
---
# <a name="improve-xamarinforms-app-performance"></a>改善 Xamarin.Forms 應用程式效能

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**演進2016：使用優化應用程式效能Xamarin.Forms**

不佳的應用程式效能會以許多方式表現。 它可能會讓應用程式看起來沒有回應、造成捲動緩慢，以及減少裝置電池壽命。 不過，最佳化效能不僅僅只牽涉到實作有效率的程式碼而已。 同時也必須考量使用者對於應用程式效能的體驗。 例如，確保作業能在不封鎖使用者執行其他活動的情況下執行，將可以協助改善使用者體驗。

有許多技巧可提高應用程式的效能和認知效能 Xamarin.Forms 。 這些技巧可共同大幅減少由 CPU 所執行的工作量，和由應用程式所耗用的記憶體數量。

> [!NOTE]
>  在閱讀本文之前，您應該先閱讀[跨平台效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)，其中討論了非平台的特定技術來改善記憶體使用量和使用 Xamarin 平台建置之應用程式的效能。

## <a name="enable-the-xaml-compiler"></a>啟用 XAML 編譯器

XAML 可選擇性地使用 XAML 編譯器 (XAMLC) 直接編譯成中繼語言 (IL)。 XAMLC 提供許多優點：

- 它會執行 XAML 的編譯時間檢查，以通知使用者是否有任何錯誤。
- 它能免去 XAML 元素一部分的載入和具現化時間。
- 它能透過不再包含 .xaml 檔案來協助減少最終組件的檔案大小。

新解決方案中的 XAMLC 預設為啟用 Xamarin.Forms 。 不過，可能需要在較舊的解決方案中啟用它。 如需詳細資訊，請參閱[編譯 XAML](~/xamarin-forms/xaml/xamlc.md)。

## <a name="use-compiled-bindings"></a>使用編譯的繫結

編譯的系結會 Xamarin.Forms 在編譯時期解析系結運算式，而不是在執行時間使用反映，以改善應用程式中的資料系結效能。 編譯繫結運算式會產生編譯的程式碼，通常會使用比傳統繫結快上 8-20 倍的速度來解析繫結。 如需詳細資訊，請參閱[編譯繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

## <a name="reduce-unnecessary-bindings"></a>減少不必要的繫結

請勿針對能輕易靜態設定的內容使用繫結。 將不需繫結的資料繫結並不會帶來任何好處，因為繫結本身並不符合成本效益。 例如，設定的 `Button.Text = "Accept"` 額外負荷比系結 [`Button.Text`](xref:Xamarin.Forms.Button.Text) 至值為 `string` "Accept" 的 viewmodel 屬性少。

## <a name="use-fast-renderers"></a>使用快速轉譯器

快速轉譯器藉由簡維 Xamarin.Forms 產生的原生控制項階層，減少 Android 上控制項的擴大和呈現成本。 這能透過建立較少的物件來降低視覺化樹狀結構的複雜度，以進一步降低記憶體使用量並提升效能。

從 Xamarin.Forms 4.0 開始，所有以為目標的應用程式 `FormsAppCompatActivity` 預設都會使用快速轉譯器。 如需詳細資訊，請參閱[快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)。

## <a name="enable-startup-tracing-on-android"></a>在 Android 上啟用啟動追蹤

Android 上的預先 (AOT) 編譯可將 Just in Time (JIT) 應用程式啟動額外負荷和記憶體使用量降到最低，代價則是建立更大的 APK。 替代方法是使用啟動追蹤，相較於傳統 AOT 編譯，這可在 Android APK 大小和啟動時間之間進行取捨。

啟動追蹤只會編譯一組受管理的方法，而不會將大部分的應用程式編譯成未受管理的程式碼，而是只會在空白應用程式中，將代表應用程式啟動成本最高之元件的 Xamarin.Forms 相較於傳統的 AOT 編譯，此方法會導致 APK 大小降低，同時仍會提供類似的啟動改進。

## <a name="enable-layout-compression"></a>啟用版面配置壓縮

版面配置壓縮會從視覺化樹狀結構中移除指定的版面配置，以試圖改善頁面轉譯效能。 這所提供的效能優勢，會根據頁面的複雜性、所使用的作業系統版本，以及執行應用程式的裝置而有所不同。 然而，較舊裝置將能獲得較大的效能提升。 如需詳細資訊，請參閱[版面配置壓縮](~/xamarin-forms/user-interface/layouts/layout-compression.md)。

## <a name="choose-the-correct-layout"></a>選擇正確的版面配置

能顯示多個子系但卻只有單一子系的版面配置，是很浪費資源的。 例如，下列程式碼範例顯示 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 具有單一子系的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

這會浪費工作，而且 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 應該移除元素，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

此外，請勿嘗試使用其他版面配置的組合來重現特定版面配置的外觀，這會導致執行不必要的版面配置計算。 例如，請勿嘗試 [`Grid`](xref:Xamarin.Forms.Grid) 使用實例的組合來重現版面配置 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 下列程式碼範例會示範此錯誤作法的範例：

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

這很浪費資源，因為會執行不必要的版面配置計算。 相反地，所需的配置可以使用來獲得更好的配置 [`Grid`](xref:Xamarin.Forms.Grid) ，如下列程式碼範例所示：

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

- 藉由指定屬性值來減少版面配置階層的深度 [`Margin`](xref:Xamarin.Forms.View.Margin) ，讓您能夠以較少的包裝視圖建立版面配置。 如需詳細資訊，請參閱[邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。
- 當使用時 [`Grid`](xref:Xamarin.Forms.Grid) ，請嘗試確保最少的資料列和資料行設定為 [ [`Auto`](xref:Xamarin.Forms.GridLength.Auto) 大小]。 每個自動調整大小的資料列或資料行都會導致版面配置引擎執行額外的版面配置計算。 可能的話，請改用固定大小的資料列和資料行。 或者，您也可以將資料列和資料行設定為使用列舉值來佔用比例的空間量 [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) ，前提是父樹狀結構會遵循這些版面配置指導方針。
- [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions)除非必要，否則請勿設定版面配置的和 [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性。 和的預設值 [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 允許最佳的版面配置優化。 變更這些屬性會衍生成本，同時也會消耗記憶體，即使是將它們設為預設值也一樣。
- 盡可能避免使用 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 。 它會導致 CPU 必須執行更多工作。
- 使用時 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) ，請盡可能避免使用 [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) 屬性。
- 使用時 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，請確定只有一個子系設定為 [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) 。 此屬性可確保指定的子系會佔用 `StackLayout` 所能提供的最大空間，重複執行這些計算將會浪費資源。
- 請避免呼叫類別的任何方法 [`Layout`](xref:Xamarin.Forms.Layout) ，因為它們會導致執行昂貴的版面配置計算。 相反地，您可能會藉由設定和屬性來取得所需的版面配置行為 [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) 。 或者，將類別子類別化， [`Layout<View>`](xref:Xamarin.Forms.Layout`1) 以達到所需的版面配置行為。
- 請不要將任何 [`Label`](xref:Xamarin.Forms.Label) 實例更新為所需的頻率，因為標籤大小的變更可能會導致重新計算整個螢幕版面配置。
- [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment)除非必要，否則請勿設定屬性。
- 盡可能將 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 任何實例的設定 [`Label`](xref:Xamarin.Forms.Label) 為 [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) 。

## <a name="use-asynchronous-programming"></a>使用非同步程式設計

藉由使用非同步程式設計，您的應用程式的整體回應性可以增強，而且通常會避免效能瓶頸。 在 .NET 中，以工作為[基礎的非同步模式（點路）](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)是非同步作業的建議設計模式。 不過，不正確使用點的方式可能會導致 unperformant 應用程式。 因此，使用點按時應遵循下列指導方針。

### <a name="fundamentals"></a>基礎

- 瞭解工作生命週期，這是由列舉所表示 `TaskStatus` 。 如需詳細資訊，請參閱 TaskStatus 和工作[狀態](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap#task-status)的[意義](https://devblogs.microsoft.com/pfxteam/the-meaning-of-taskstatus/)。
- 使用 `Task.WhenAll` 方法，以非同步方式等候多個非同步作業完成，而不是個別進行 `await` 一系列的非同步作業。 如需詳細資訊，請參閱[system.threading.tasks.task.whenall](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall)。
- 使用 `Task.WhenAny` 方法，以非同步方式等候多個非同步作業的其中一個完成。 如需詳細資訊，請參閱[system.threading.tasks.task.whenany](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall)。
- 使用 `Task.Delay` 方法，即可產生在 `Task` 指定時間之後完成的物件。 這適用于資料輪詢之類的案例，並延遲處理使用者輸入的預定時間。 如需詳細資訊，請參閱[Delay](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskdelay)。
- 使用方法，線上程集區上執行密集的同步 CPU 作業 `Task.Run` 。 這個方法是方法的快捷方式 `TaskFactory.StartNew` ，其中已設定最最佳的引數。 如需詳細資訊，請參閱[執行](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskrun)。
- 請避免嘗試建立非同步函式。 請改為使用生命週期事件或分隔初始化邏輯，以正確地進行 `await` 任何初始化。 如需詳細資訊，請參閱 blog.stephencleary.com 上的[非同步構造](https://blog.stephencleary.com/2013/01/async-oop-2-constructors.html)函式。
- 使用 [延遲工作模式]，以避免在應用程式啟動期間等候非同步作業完成。 如需詳細資訊，請參閱[asynclazy<t>](https://devblogs.microsoft.com/pfxteam/asynclazyt/)。
- 藉由建立物件，建立現有非同步作業的工作包裝函式，而不使用點一下 `TaskCompletionSource<T>` 。 這些物件可獲得程式設計的優點 `Task` ，並可讓您控制相關聯的存留期和完成 `Task` 。 如需詳細資訊，請參閱[TaskCompletionSource 的本質](https://devblogs.microsoft.com/pfxteam/the-nature-of-taskcompletionsourcetresult/)。
 
- `Task` `Task` 當不需要處理非同步作業的結果時，傳回物件，而不是傳回等待的物件。 由於執行的內容切換較少，這會更有效率。
- 使用工作平行程式庫（TPL）資料流程程式庫，例如在資料可用時進行處理，或當您有多個必須以非同步方式彼此通訊的作業時。 如需詳細資訊，請參閱[資料流程（工作平行程式庫）](/dotnet/standard/parallel-programming/dataflow-task-parallel-library)。

### <a name="ui"></a>UI

- 呼叫 API 的非同步版本（如果有的話）。 這可讓 UI 執行緒保持在未遭封鎖的狀態，協助改善使用者使用應用程式的體驗。
- 使用 UI 執行緒上非同步作業的資料來更新 UI 元素，以避免擲回例外狀況。 不過，屬性的更新 `ListView.ItemsSource` 會自動封送處理至 UI 執行緒。 如需判斷程式碼是否正在 UI 執行緒上執行的相關資訊，請參閱[ Xamarin.Essentials ： MainThread](~/essentials/main-thread.md?content=xamarin/xamarin-forms)。

    > [!IMPORTANT]
    > 透過資料系結更新的任何控制項屬性都會自動封送處理至 UI 執行緒。

### <a name="error-handling"></a>錯誤處理

- 瞭解非同步例外狀況處理。 以非同步方式執行的程式碼所擲回的未處理例外狀況，會傳播回到呼叫執行緒，但在某些情況下除外。 如需詳細資訊，請參閱[例外狀況處理（工作平行程式庫）](/dotnet/standard/parallel-programming/exception-handling-task-parallel-library)。
- 請避免建立 `async void` 方法，並改為建立 `async Task` 方法。 這些可讓您更輕鬆地進行錯誤處理、複合性和可測試性。 此指導方針的例外狀況是非同步事件處理常式，必須傳回 `void` 。 如需詳細資訊，請參閱[避免 Async Void](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#avoid-async-void)。
- 請不要藉由呼叫、或方法來混合封鎖和非同步程式碼 `Task.Wait` `Task.Result` `GetAwaiter().GetResult` ，因為它們可能會導致發生鎖死。 不過，如果必須違反這項指導方針，慣用的方法是呼叫 `GetAwaiter().GetResult` 方法，因為它會保留工作例外狀況。 如需詳細資訊，請參閱[.net 4.5 中](https://devblogs.microsoft.com/pfxteam/task-exception-handling-in-net-4-5/)[的非同步方式和工作](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way)例外狀況處理。
- 盡可能使用 `ConfigureAwait` 方法，以建立無內容的程式碼。 無內容的程式碼對於行動應用程式具有較佳的效能，而且在使用部分非同步程式碼基底時，是避免鎖死的實用技巧。 如需詳細資訊，請參閱[設定內容](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#configure-context)。
- 使用*接續*工作來執行功能，例如處理先前非同步作業所擲回的例外狀況，以及在啟動或正在執行時取消接續。 如需詳細資訊，請參閱[使用連續工作來連結](/dotnet/standard/parallel-programming/chaining-tasks-by-using-continuation-tasks)工作。
- 從叫用 `ICommand` 非同步作業時，請使用非同步執行 `ICommand` 。 這可確保非同步命令邏輯中的任何例外狀況都可以處理。 如需詳細資訊，請參閱[Async 程式設計：非同步 MVVM 應用程式的模式：命令](/archive/msdn-magazine/2014/april/async-programming-patterns-for-asynchronous-mvvm-applications-commands)。

## <a name="choose-a-dependency-injection-container-carefully"></a>謹慎選擇相依性插入容器

相依性插入容器會對行動應用程式引進額外的效能限制式。 使用容器來登錄和解析類型具有效能成本，因為容器會使用反映來建立每種類型，特別是在針對應用程式中的每個頁面巡覽重建相依性時。 如果有許多或深度相依性，則建立的成本可能會大幅增加。 此外，根據使用的容器而定，類型註冊 (通常會在應用程式啟動期間進行) 可能會對啟動時間產生明顯的影響。

替代方法是使用 Factory 手動實作相依性插入，使其更具效能。

## <a name="create-shell-applications"></a>建立 Shell 應用程式

Xamarin.FormsShell 應用程式會根據 flyouts 和索引標籤提供固定的導覽體驗。 如果您的應用程式使用者體驗可以透過 Shell 實作，那麼，這樣做是有助益的。 Shell 應用程式有助於避免產生不佳的啟動體驗，因為頁面會在需要時 (而非應用程式啟動時) 建立來回應巡覽，這會發生於使用 [`TabbedPage'](xref:Xamarin.Forms.TabbedPage) 的應用程式中。 如需詳細資訊，請參閱[ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md)。

## <a name="use-collectionview-instead-of-listview"></a>使用 CollectionView 而非 ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView)這是使用不同的版面配置規格來呈現資料清單的視圖。 它提供更有彈性且更具效能的替代方法 [`ListView`](xref:Xamarin.Forms.ListView) 。 如需詳細資訊，請參閱[ Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)。

## <a name="optimize-listview-performance"></a>將 ListView 效能最佳化

使用時 [`ListView`](xref:Xamarin.Forms.ListView) ，有許多使用者體驗應該進行優化：

- **初始化**：自控制項建立時開始，並於畫面上顯示項目時結束的時間間隔。
- **捲動**：能夠捲動清單並確保 UI 跟得上觸控手勢。
- **互動**：加入、刪除和選取項目的互動。

[`ListView`](xref:Xamarin.Forms.ListView)控制項需要應用程式來提供資料和儲存格範本。 實現此目標的方式將對控制項的效能產生很大的影響。 如需詳細資訊，請參閱 [ListView 效能](~/xamarin-forms/user-interface/listview/performance.md)。

## <a name="optimize-image-resources"></a>最佳化影像資源

顯示影像資源可能會大幅增加應用程式的磁碟使用量。 因此應該只有在必要時才建立它們，且應在應用程式不再需要它們時應立即釋出。 例如，如果某應用程式是藉由從資料流讀取影像資料來顯示影像，請務必只在必要時才建立資料流，且務必於不再需要資料流時將它釋出。 這可以藉由在建立頁面時建立資料流程，或在事件引發時建立 [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) ，然後在事件引發時處置資料流程 [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) 。

使用方法下載要顯示的影像時 [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) ，請確定 [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) 屬性已設定為，以快取下載的影像 `true` 。 如需詳細資訊，請參閱[使用影像](~/xamarin-forms/user-interface/images.md)。

如需詳細資訊，請參閱[最佳化影像資源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)。

## <a name="reduce-the-visual-tree-size"></a>減少視覺化樹狀結構大小

減少頁面上的元素數目，可讓頁面轉譯速度變得更快。 有兩個主要技巧可以實現此目標。 其一是隱藏看不見的元素。 [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible)每個專案的屬性會決定元素是否應該是視覺化樹狀結構的一部分。 因此，如果某個元素因為隱藏在其他元素背後而看不見，請移除該元素或將其 `IsVisible` 屬性設為 `false`。

第二個技巧是移除不必要的元素。 例如，下列程式碼範例顯示包含多個物件的頁面配置 [`Label`](xref:Xamarin.Forms.Label) ：

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

大部分 Xamarin.Forms 的轉譯器類別 `OnElementChanged` 都會公開方法，這會在 Xamarin.Forms 建立自訂控制項以轉譯對應的原生控制項時呼叫。 在每個平台專案中自訂轉譯器類別，然後覆寫此方法以具現化並自訂原生控制項。 `SetNativeControl` 方法可用來具現化原生控制項，且這個方法也會將控制項參考指派給 `Control` 屬性。

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

當 `Control` 屬性是 `null` 時，新的原生控制項只應具現化一次。 此外，您應該只在自訂轉譯器附加至新專案時，建立、設定及訂閱控制項的事件處理常式 Xamarin.Forms 。 同樣地，應該只在轉譯器附加到的元素變更時，才取消訂閱任何已訂閱的事件處理常式。 採用這個方法將有助於建立有效執行的自訂轉譯器，避免發生記憶體流失。

> [!IMPORTANT]
> 只有在 `e.NewElement` 屬性不是 `null`，且 `Control` 屬性為 `null` 時，才應叫用 `SetNativeControl` 方法。

如需自訂轉譯器的詳細資訊，請參閱[在每個平台上自訂控制項](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

## <a name="related-links"></a>相關連結

- [跨平臺效能](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [編譯 XAML](~/xamarin-forms/xaml/xamlc.md)
- [編譯的繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)
- [快速轉譯器](~/xamarin-forms/internals/fast-renderers.md)
- [版面配置壓縮](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Xamarin.Forms外層](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.FormsCollectionView](~/xamarin-forms/user-interface/collectionview/index.md)
- [ListView 效能](~/xamarin-forms/user-interface/listview/performance.md)
- [最佳化影像資源](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [XAML 樣式](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [自訂每個平台上的控制項](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
