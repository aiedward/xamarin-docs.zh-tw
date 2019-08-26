---
title: Xamarin.Forms Shell 導覽
description: Xamarin.Forms Shell 應用程式可以利用 URI 式導覽體驗，允許導覽至應用程式中的任何頁面，而不需要遵循設定的導覽階層。
ms.prod: xamarin
ms.assetid: 57079D89-D1CB-48BD-9FEE-539CEC29EABB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: c65a1aed79199106d2a754329dd38d87feda66a2
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888961"
---
# <a name="xamarinforms-shell-navigation"></a>Xamarin.Forms Shell 導覽

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell 包含 URI 式導覽體驗，可使用路由導覽至應用程式中的任何頁面，而不需要遵循設定的導覽階層。 此外，它還提供向後巡覽的能力，而不需瀏覽導覽堆疊上的所有頁面。

`Shell` 會定義下列導覽相關的屬性：

- `BackButtonBehavior`，屬於 `BackButtonBehavior` 類型，可定義上一頁按鈕行為的一種附加屬性。
- `CurrentItem`，屬於 `FlyoutItem` 類型，是目前選取的 `FlyoutItem`。
- `CurrentState`，屬於 `ShellNavigationState` 類型，是 `Shell` 的目前導覽狀態。
- `Current`，屬於 `Shell` 類型，是 `Application.Current.MainPage` 類型轉換的別名。

`BackButtonBehavior`、`CurrentItem` 和 `CurrentState` 屬性都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標。

導覽是透過從 `Shell` 類別叫用 `GoToAsync` 方法執行的。 導覽即將執行時，會引發 `Navigating` 事件，而導覽完成時，則會引發 `Navigated` 事件。

> [!NOTE]
> 使用 [Navigation](xref:Xamarin.Forms.NavigableElement.Navigation) 屬性仍然可以在 Xamarin.Forms Shell 應用程式中執行導覽。 如需詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="routes"></a>路由

導覽是在 Shell 應用程式中，透過指定要導覽的 URI 執行的。 導覽 URI 可以有三個元件：

- *路由*：定義當作 Shell 視覺階層一部分存在之內容的路徑。
- *頁面*。 Shell 視覺階層中不存在的頁面可以從 Shell 應用程式中的任何位置推送到導覽堆疊上。 例如，在 Shell 視覺階層中不會定義項目詳細資料頁面，但是如有需要，可以推送到導覽堆疊上。
- 一或多個*查詢參數*。 查詢參數是導覽時可以傳遞至目的地頁面的參數。

當導覽 URI 包含全部三個元件時，結構是：//route/page?queryParameters

### <a name="register-routes"></a>註冊路由

路由可以在 `FlyoutItem`、`Tab` 和 `ShellContent` 物件上，透過其 `Route` 屬性定義：

```xaml
<Shell ...>
    <FlyoutItem ...
                Route="animals">
        <Tab ...
             Route="domestic">
            <ShellContent ...
                          Route="cats" />
            <ShellContent ...
                          Route="dogs" />
        </Tab>
        <ShellContent ...
                      Route="monkeys" />
        <ShellContent ...
                      Route="elephants" />  
        <ShellContent ...
                      Route="bears" />
    </FlyoutItem>
    <ShellContent ...
                  Route="about" />                  
    ...
</Shell>
```

> [!NOTE]
> Shell 階層中的所有項目有與其相關聯的路由。 如果開發人員未設定路由，則會執行階段產生路由。 但是，不保證產生的路由在不同的應用程式工作階段之間都一致。

此範例會從範例應用程式中建立下列路由階層，以用於程式設計的導覽：

```
animals
  domestic
    cats
    dogs
  monkeys
  elephants
  bears
about
```

若要導覽至 `dogs` 路由的 `ShellContent` 物件，絕對路由 URI 為 `//animals/domestic/dogs`。 同樣地，若要導覽至 `about` 路由的 `ShellContent` 物件，絕對路由 URI 為 `//about`。

> [!IMPORTANT]
> 如果偵測到重複的路由，將會在應用程式啟動時擲回 `ArgumentException`。 如果階層中有兩個以上的同層級路由共用一個路由名稱，也會擲回此例外狀況。

#### <a name="register-page-routes"></a>註冊頁面路由

在 Shell 子類別建構函式或在叫用路由之前執行的其他任何位置中，可以針對 Shell 視覺階層中未表示的任何頁面，明確地註冊其他路由：

```csharp
Routing.RegisterRoute("monkeydetails", typeof(MonkeyDetailPage));
Routing.RegisterRoute("beardetails", typeof(BearDetailPage));
Routing.RegisterRoute("catdetails", typeof(CatDetailPage));
Routing.RegisterRoute("dogdetails", typeof(DogDetailPage));
Routing.RegisterRoute("elephantdetails", typeof(ElephantDetailPage));
```

此範例會註冊項目詳細資料頁面，Shell 子類別中未將此頁面定義為路由。 接著，可以使用 URI 式導覽，從應用程式中的任何位置導覽這些頁面。 這類頁面的路由稱為*全域路由*。

> [!NOTE]
> 如果需要，已使用 `Routing.RegisterRoute` 方法註冊其路由的頁面可使用 `Routing.UnRegisterRoute` 方法取消註冊。

或者，如果需要，可以在不同的路由階層註冊頁面：

```csharp
Routing.RegisterRoute("monkeys/details", typeof(MonkeyDetailPage));
Routing.RegisterRoute("bears/details", typeof(BearDetailPage));
Routing.RegisterRoute("cats/details", typeof(CatDetailPage));
Routing.RegisterRoute("dogs/details", typeof(DogDetailPage));
Routing.RegisterRoute("elephants/details", typeof(ElephantDetailPage));
```

此範例會啟用內容相關式頁面導覽，其中，從 `monkeys` 路由的頁面導覽至 `details` 路由會顯示 `MonkeyDetailPage`。 同樣地，從 `elephants` 路由的頁面導覽至 `details` 路由會顯示 `ElephantDetailPage`。

> [!IMPORTANT]
> 如果 `Routing.RegisterRoute` 方法嘗試向兩個以上的不同類型註冊相同的路由，則會擲回 `ArgumentException`。

## <a name="perform-navigation"></a>執行導覽

若要執行導覽，必須先取得 `Shell` 子類別的參考。 將 `App.Current.MainPage` 屬性轉換為 `Shell` 物件，或透 過`Shell.Current` 屬性可以取得這個參考。 接著，可以對 `Shell` 物件呼叫 `GoToAsync` 方法來執行導覽。 此方法會導覽至 `ShellNavigationState`，並在導覽動畫完成後傳回將會完成的 `Task`。 `ShellNavigationState` 物件是由 `GoToAsync` 方法，從 `string` 或 `Uri` 所建構，且其 `Location` 屬性設為 `string` 或 `Uri` 引數。

從 Shell 視覺階層導覽至路由時，不會建立導覽堆疊。 但是，導覽至不在 Shell 視覺階層中的頁面時，則會建立導覽堆疊。

> [!NOTE]
> `Shell` 目前的導覽狀態可以透過 `Shell.Current.CurrentState` 屬性擷取，其中包含 `Location` 屬性中所顯示路由的 URI。

### <a name="absolute-routes"></a>絕對路由

將有效的絕對 URI 指定為 `GoToAsync` 方法的引數可執行導覽：

```csharp
await Shell.Current.GoToAsync("//animals/monkeys");
```

此範例會導覽至 `monkeys` 路由的頁面，並在 `ShellContent` 物件上定義路由。 表示 `monkeys` 路由的 `ShellContent` 物件為 `FlyoutItem` 物件子系，其路由是 `animals`。

### <a name="relative-routes"></a>相對路由

將有效的相對 URI 指定為 `GoToAsync` 方法的引數也可以執行導覽。 路由系統將會嘗試比對 `ShellContent` 物件的 URI。 因此，如果應用程式中的所有路由都是唯一的，則僅能透過將唯一的路由名稱指定為相對 URI 來執行導覽：

```csharp
await Shell.Current.GoToAsync("monkeydetails");
```

此範例會導覽至 `monkeydetails` 路由的頁面。

此外，支援下列相對路徑格式：

| 格式 | 說明 |
| --- | --- |
| //*route* | 將會從目前顯示的路由向上搜尋路由階層中指定的路由。 |
| ///*route* | 將會從目前顯示的路由向下搜尋路由階層中指定的路由。 |

#### <a name="contextual-navigation"></a>關聯式導覽

相對路由可啟用關聯式導覽。 例如，請考慮下列路由階層：

```
monkeys
  details
bears
  details
```

顯示 `monkeys` 路由已註冊的頁面時，導覽至 `details` 路由將會顯示 `monkeys/details` 路由已註冊的頁面。 同樣地，顯示 `bears` 路由已註冊的頁面時，導覽至 `details` 路由將會顯示 `bears/details` 路由已註冊的頁面。 如需如何在此範例中註冊路由的相關資訊，請參閱[註冊頁面路由](#register-page-routes)。

### <a name="invalid-routes"></a>無效的路由

下列路由格式無效：

| 格式 | 說明 |
| --- | --- |
| *route* 或 /*route* | 視覺階層中的路由無法推送到導覽堆疊上。 |
| //*page* 或 ///*page* | 全域路由目前不得為導覽堆疊上的唯一頁面。 因此，不支援以絕對路由傳送至全域路由。 |

使用下列任何一種路由格式都會導致擲回 `Exception`。

> [!IMPORTANT]
> 嘗試導覽至不存在的路由會導致擲回 `ArgumentException` 例外狀況。

### <a name="debugging-navigation"></a>為導覽偵錯

部分 Shell 類別是以 `DebuggerDisplayAttribute` 裝飾，以指定偵錯工具如何顯示類別或欄位。 顯示與導覽要求相關的資料有助於為導覽要求偵錯。 例如，下列螢幕擷取畫面顯示 `Shell.Current` 物件的 `CurrentItem` 和 `CurrentState` 屬性：

![偵錯工具的螢幕擷取畫面](navigation-images/debugger.png "偵錯工具")

在此範例中，`CurrentItem` 屬性屬於 `FlyoutItem` 類型，可顯示 `FlyoutItem` 物件的標題和路由。 同樣地，`CurrentState` 屬性屬於 `ShellNavigationState` 類型，可顯示 Shell 應用程式內顯示之路由的 URI。

### <a name="tab-class"></a>Tab 類別

`Tab` 類別會定義 `IReadOnlyList<Page>` 類型的 `Stack` 屬性，這表示 `Tab` 中目前已推送的導覽堆疊。 此類別也會提供下列可覆寫的導覽方法：

- `GetNavigationStack`，傳回 `IReadOnlyList<Page`>，也就是目前的導覽堆疊。
- `OnInsertPageBefore`，會在呼叫 `INavigation.InsertPageBefore` 時呼叫。
- `OnPopAsync`，傳回 `Task<Page>`，會在呼叫 `INavigation.PopAsync` 時呼叫。
- `OnPopToRootAsync`，傳回 `Task`，會在呼叫 `INavigation.OnPopToRootAsync` 時呼叫。
- `OnPushAsync`，傳回 `Task`，會在呼叫 `INavigation.PushAsync` 時呼叫。
- `OnRemovePage`，會在呼叫 `INavigation.RemovePage` 時呼叫。

## <a name="navigation-events"></a>導覽事件

`Shell` 類別會定義 `Navigating` 事件，當導覽即將執行時，可能會因為程式設計導覽或使用者互動而引發此事件。 隨附 `Navigating` 事件的 `ShellNavigatingEventArgs` 物件會提供下列屬性：

| 屬性 | 類型 | 說明 |
|---|---|---|
| `Current` | `ShellNavigationState` | 目前頁面的 URI。 |
| `Source` | `ShellNavigationSource` | 發生導覽的類型。 |
| `Target` | `ShellNavigationState`  | 代表導覽目的地的 URI。 |
| `CanCancel`  | `bool` | 指出它是否可以取消導覽的值。 |
| `Cancelled`  | `bool` | 指出是否已取消導覽的值。 |

此外，`ShellNavigatingEventArgs` 類別會提供可用來取消導覽的 `Cancel` 方法。

> [!NOTE]
> `Shell` 類別中可覆寫的 `OnNavigating` 方法會引發 `Navigated` 事件。

`Shell` 類別也會定義 `Navigated` 事件，導覽完成時會引發此事件。 隨附 `Navigating` 事件的 `ShellNavigatedEventArgs` 物件會提供下列屬性：

| 屬性 | 類型 | 說明 |
|---|---|---|
| `Current` | `ShellNavigationState` | 目前頁面的 URI。 |
| `Previous`| `ShellNavigationState` | 上一頁的 URI。 |
| `Source`  | `ShellNavigationSource` | 發生導覽的類型。 |

> [!NOTE]
> `Shell` 類別中可覆寫的 `OnNavigated` 方法會引發 `Navigating` 事件。

例如，`ShellNavigatedEventArgs` 及 `ShellNavigatingEventArgs` 類別都擁有 `ShellNavigationSource` 類型的 `Source` 屬性。 此列舉提供下列值：

- `Unknown`
- `Push`
- `Pop`
- `PopToRoot`
- `Insert`
- `Remove`
- `ShellItemChanged`
- `ShellSectionChanged`
- `ShellContentChanged`

因此，在 `Navigating` 事件的處理常式中，可以攔截導覽，並根據導覽來源執行動作。 例如，下列的程式碼會示範如何在未儲存頁面上的資料時，取消向後導覽：

```csharp
void OnNavigating(object sender, ShellNavigatingEventArgs e)
{
    // Cancel back navigation if data is unsaved
    if (e.Source == ShellNavigationSource.Pop && !dataSaved)
    {
        e.Cancel();
    }
}
```

## <a name="pass-data"></a>傳遞資料

執行程式設計導覽時，可以將資料當作查詢參數傳遞。 例如，當使用者在 `ElephantsPage` 上選取大象時，範例應用程式中會執行下列程式碼：

```csharp
async void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string elephantName = (e.CurrentSelection.FirstOrDefault() as Animal).Name;
    await Shell.Current.GoToAsync($"//animals/elephants/elephantdetails?name={elephantName}");
}
```

此程式碼範例會在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中擷取目前選取的大象，並導覽至 `elephantdetails` 路由，將 `elephantName` 作為查詢參數傳遞。 請注意，查詢參數將會是針對導覽編碼的 URL，因此 "Indian Elephant" 將會變成 "Indian%20Elephant"。

若要接收資料，表示導覽至頁面的類別或頁面 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 的類別必須針對每個查詢參數，使用 `QueryPropertyAttribute` 裝飾：

```csharp
[QueryProperty("Name", "name")]
public partial class ElephantDetailPage : ContentPage
{
    public string Name
    {
        set
        {
            BindingContext = ElephantData.Elephants.FirstOrDefault(m => m.Name == Uri.UnescapeDataString(value));
        }
    }
    ...
}
```

`QueryPropertyAttribute` 的第一個引數會指定將接收資料之屬性的名稱，而第二個引數則指定查詢參數 ID。因此，上述範例中的 `QueryPropertyAttribute` 會指定 `Name` 屬性將從 `GoToAsync` 方法呼叫中的 URI，接收 `name` 查詢參數中所傳入的資料。 接著，`Name` 屬性會以 URL 解碼查詢參數值，並使用該值，將頁面的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 設定為將顯示的物件。

> [!NOTE]
> 類別可以使用多個 `QueryPropertyAttribute` 物件裝飾。

## <a name="back-button-behavior"></a>上一頁按鈕行為

`BackButtonBehavior` 類別會定義可控制上一頁按鈕外觀和行為的下列屬性：

- `Command`，屬於 `ICommand` 類型，會在按 [上一頁] 按鈕時執行。
- `CommandParameter`，屬於 `object` 類型，這是傳遞至 `Command` 的參數。
- `IconOveride`，屬於 [`ImageSource`](xref:Xamarin.Forms.ImageSource) 類型，這是用於上一頁按鈕的圖示。
- `IsEnabled`，屬於 `boolean` 類型，可指出是否啟用上一頁按鈕。 預設值為 `true`。
- `TextOverride`，屬於 `string` 類型，這是用於上一頁按鈕的文字。

所有這些屬性都以 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 物件為後盾，也就是說，這些屬性可以是資料繫結的目標。

將 `Shell.BackButtonBehavior` 附加屬性設為 `BackButtonBehavior` 物件可以取用 `BackButtonBehavior` 類別：

```xaml
<ContentPage ...>    
    <Shell.BackButtonBehavior>
        <BackButtonBehavior Command="{Binding BackCommand}"
                            IconOverride="back.png" />   
    </Shell.BackButtonBehavior>
    ...
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
Shell.SetBackButtonBehavior(this, new BackButtonBehavior
{
    Command = new Command(() =>
    {
        ...
    }),
    IconOverride = "back.png"
});
```

`Command` 屬性會設定要按 [上一頁] 按鈕時執行的 `ICommand`，而 `IconOverride` 屬性則會設定為用於 [上一頁] 按鈕的圖示：

[![Shell 上一頁按鈕圖示覆寫在 iOS 和 Android 上的螢幕擷取畫面](navigation-images/back-button.png "Shell 上一頁按鈕圖示覆寫")](navigation-images/back-button-large.png#lightbox "Shell 上一頁按鈕圖示覆寫")

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
