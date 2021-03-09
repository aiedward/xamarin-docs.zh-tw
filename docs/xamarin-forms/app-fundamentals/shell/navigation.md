---
title: Xamarin.Forms Shell 導覽
description: Xamarin.Forms Shell 應用程式可以利用以 URI 為基礎的流覽體驗，允許流覽至應用程式中的任何頁面，而不需要遵循設定的導覽階層。
ms.prod: xamarin
ms.assetid: 57079D89-D1CB-48BD-9FEE-539CEC29EABB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/09/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d8c4337a09044756a1ea453cbf67afb50f13bb08
ms.sourcegitcommit: c153247dc069e5393e5bf492465cdb08530c8d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511536"
---
# <a name="xamarinforms-shell-navigation"></a>Xamarin.Forms Shell 導覽

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.Forms Shell 包含以 URI 為基礎的流覽體驗，可使用路由導覽至應用程式中的任何頁面，而不需要遵循設定的導覽階層。 此外，它還提供向後巡覽的能力，而不需瀏覽導覽堆疊上的所有頁面。

[`Shell`](xref:Xamarin.Forms.Shell)類別會定義下列導覽相關的屬性：

- [`BackButtonBehavior`](xref:Xamarin.Forms.Shell.BackButtonBehaviorProperty)型別為的 [`BackButtonBehavior`](xref:Xamarin.Forms.BackButtonBehavior) 附加屬性，定義 [上一頁] 按鈕的行為。
- [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem)，類型為 [`ShellItem`](xref:Xamarin.Forms.ShellItem) 目前選取的專案。
- [`CurrentPage`](xref:Xamarin.Forms.Shell.CurrentPage)，類型為 [`Page`](xref:Xamarin.Forms.Page) 目前顯示的頁面。
- [`CurrentState`](xref:Xamarin.Forms.Shell.CurrentState)的型別 [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) ，這是目前的流覽狀態 [`Shell`](xref:Xamarin.Forms.Shell) 。
- [`Current`](xref:Xamarin.Forms.Shell.Current)型別為的型別為 [`Shell`](xref:Xamarin.Forms.Shell) 轉換的別名 `Application.Current.MainPage` 。

[`BackButtonBehavior`](xref:Xamarin.Forms.Shell.BackButtonBehaviorProperty)、 [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) 和 [`CurrentState`](xref:Xamarin.Forms.Shell.CurrentState) 屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示這些屬性可以是資料系結的目標。

從類別叫用方法來執行導覽 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) [`Shell`](xref:Xamarin.Forms.Shell) 。 當導覽即將執行時， [`Navigating`](xref:Xamarin.Forms.Shell.Navigating) 就會引發事件，並在 [`Navigated`](xref:Xamarin.Forms.Shell.Navigated) 流覽完成時引發事件。

> [!NOTE]
> 您仍然可以使用 [導覽](xref:Xamarin.Forms.NavigableElement.Navigation) 屬性，在 Shell 應用程式的頁面之間進行導覽。 如需詳細資訊，請參閱[階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="routes"></a>路由

導覽是在 Shell 應用程式中，透過指定要導覽的 URI 執行的。 導覽 URI 可以有三個元件：

- *路由*：定義當作 Shell 視覺階層一部分存在之內容的路徑。
- *頁面*。 Shell 視覺階層中不存在的頁面可以從 Shell 應用程式中的任何位置推送到導覽堆疊上。 例如，不會在 Shell 視覺階層中定義詳細資料頁面，但可以視需要將其推送至導覽堆疊上。
- 一或多個 *查詢參數*。 查詢參數是導覽時可以傳遞至目的地頁面的參數。

當導覽 URI 包含全部三個元件時，結構是：//route/page?queryParameters

### <a name="register-routes"></a>註冊路由

您可以 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) 透過屬性，在、 [`TabBar`](xref:Xamarin.Forms.TabBar) 、 [`Tab`](xref:Xamarin.Forms.Tab) 和 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件上定義路由 `Route` 。

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
> Shell 階層中的所有項目有與其相關聯的路由。 如果您未設定路由，則會在執行時間產生一個路由。 但是，不保證產生的路由在不同的應用程式工作階段之間都一致。

上述範例會建立下列路由階層，可用於以程式設計方式流覽：

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

若要流覽至 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 路由的物件 `dogs` ，絕對路由 URI 為 `//animals/domestic/dogs` 。 同樣地，若要導覽至 `about` 路由的 `ShellContent` 物件，絕對路由 URI 為 `//about`。

> [!WARNING]
> 如果偵測到重複的路由，將會在應用程式啟動時擲回 `ArgumentException`。 如果階層中有兩個以上的同層級路由共用一個路由名稱，也會擲回此例外狀況。

### <a name="register-detail-page-routes"></a>註冊詳細資料頁面路由

在子類別的函式中 [`Shell`](xref:Xamarin.Forms.Shell) ，或在叫用路由之前執行的任何其他位置中，可以針對 Shell 視覺階層中未表示的任何詳細資料頁面，明確註冊其他路由。 這可透過方法來完成 [`Routing.RegisterRoute`](xref:Xamarin.Forms.Routing.RegisterRoute*) ：

```csharp
Routing.RegisterRoute("monkeydetails", typeof(MonkeyDetailPage));
Routing.RegisterRoute("beardetails", typeof(BearDetailPage));
Routing.RegisterRoute("catdetails", typeof(CatDetailPage));
Routing.RegisterRoute("dogdetails", typeof(DogDetailPage));
Routing.RegisterRoute("elephantdetails", typeof(ElephantDetailPage));
```

此範例會將子類別中未定義的詳細資料頁面註冊 [`Shell`](xref:Xamarin.Forms.Shell) 為路由。 然後，您可以從應用程式內的任何位置，使用以 URI 為基礎的導覽來流覽這些詳細資料頁面。 這類頁面的路由稱為 *全域路由*。

> [!WARNING]
> `ArgumentException`如果 [`Routing.RegisterRoute`](xref:Xamarin.Forms.Routing.RegisterRoute*) 方法嘗試註冊相同的路由至兩個或多個不同的類型，將會擲回。

或者，如果需要，可以在不同的路由階層註冊頁面：

```csharp
Routing.RegisterRoute("monkeys/details", typeof(MonkeyDetailPage));
Routing.RegisterRoute("bears/details", typeof(BearDetailPage));
Routing.RegisterRoute("cats/details", typeof(CatDetailPage));
Routing.RegisterRoute("dogs/details", typeof(DogDetailPage));
Routing.RegisterRoute("elephants/details", typeof(ElephantDetailPage));
```

此範例會啟用內容相關式頁面導覽，其中，從 `monkeys` 路由的頁面導覽至 `details` 路由會顯示 `MonkeyDetailPage`。 同樣地，從 `elephants` 路由的頁面導覽至 `details` 路由會顯示 `ElephantDetailPage`。 如需詳細資訊，請參閱關聯式 [導覽](#contextual-navigation)。

> [!NOTE]
> [`Routing.RegisterRoute`](xref:Xamarin.Forms.Routing.RegisterRoute*)如果有需要，可以使用方法來取消註冊其路由已向方法註冊的頁面 [`Routing.UnRegisterRoute`](xref:Xamarin.Forms.Routing.UnRegisterRoute*) 。

## <a name="perform-navigation"></a>執行導覽

若要執行導覽， [`Shell`](xref:Xamarin.Forms.Shell) 必須先取得子類別的參考。 您可以藉由將 `App.Current.MainPage` 屬性轉換為 `Shell` 物件，或透過屬性來取得此參考 [`Shell.Current`](xref:Xamarin.Forms.Shell.Current) 。 然後，您可以 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) 在物件上呼叫方法來執行導覽 `Shell` 。 這個方法會導覽至 [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) ，並傳回完成 `Task` 導覽動畫之後將完成的。 `ShellNavigationState` 物件是由 `GoToAsync` 方法，從 `string` 或 `Uri` 所建構，且其 `Location` 屬性設為 `string` 或 `Uri` 引數。

> [!IMPORTANT]
> 從 Shell 視覺階層導覽至路由時，不會建立導覽堆疊。 但是，導覽至不在 Shell 視覺階層中的頁面時，則會建立導覽堆疊。

您可以透過屬性來抓取物件的目前導覽狀態 [`Shell`](xref:Xamarin.Forms.Shell) [`Shell.Current.CurrentState`](xref:Xamarin.Forms.Shell.CurrentState) ，其中包含屬性中顯示之路徑的 URI `Location` 。

### <a name="absolute-routes"></a>絕對路由

您可以藉由指定有效的絕對 URI 做為方法的引數來執行導覽 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) ：

```csharp
await Shell.Current.GoToAsync("//animals/monkeys");
```

此範例會導覽至路由的頁面 `monkeys` ，並在物件上定義路由 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 。 `ShellContent`表示路由的物件 `monkeys` 是物件的子系 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) ，其路由為 `animals` 。

### <a name="relative-routes"></a>相對路由

您也可以藉由指定有效的相對 URI 做為方法的引數來執行導覽 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) 。 路由系統會嘗試比對 URI 與 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件。 因此，如果應用程式中的所有路由都是唯一的，則只能將唯一的路由名稱指定為相對 URI，藉以執行導覽。

以下是支援的相對路由格式：

| 格式 | 描述 |
| --- | --- |
| *route* | 系統會從目前的位置向上搜尋路由階層中指定的路由。 相符的頁面將會推送至導覽堆疊。 |
| /*路線* | 路由階層將從指定的路由搜尋，從目前的位置往下搜尋。 相符的頁面將會推送至導覽堆疊。 |
| //*路線* | 系統會從目前的位置向上搜尋路由階層中指定的路由。 相符的頁面將會取代流覽堆疊。 |
| ///*路線* | 路由階層將會從目前的位置向下搜尋指定的路由。 相符的頁面將會取代流覽堆疊。 |

下列範例會流覽至路由的頁面 `monkeydetails` ：

```csharp
await Shell.Current.GoToAsync("monkeydetails");
```

在此範例中， `monkeyDetails` 會搜尋階層中的路由，直到找到相符的頁面為止。 找到頁面時，會將其推送至導覽堆疊。

#### <a name="contextual-navigation"></a>關聯式導覽

相對路由可啟用關聯式導覽。 例如，請考慮下列路由階層：

```
monkeys
  details
bears
  details
```

顯示 `monkeys` 路由已註冊的頁面時，導覽至 `details` 路由將會顯示 `monkeys/details` 路由已註冊的頁面。 同樣地，顯示 `bears` 路由已註冊的頁面時，導覽至 `details` 路由將會顯示 `bears/details` 路由已註冊的頁面。 如需如何在此範例中註冊路由的相關資訊，請參閱[註冊頁面路由](#register-detail-page-routes)。

### <a name="backwards-navigation"></a>向後瀏覽

您可以指定 "..." 做為方法的引數來執行回溯導覽 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) ：

```csharp
await Shell.Current.GoToAsync("..");
```

具有 ".." 的回溯導覽也可以與路由結合：

```csharp
await Shell.Current.GoToAsync("../route");
```

在此範例中，會執行向後導覽，然後流覽至指定的路由。

> [!IMPORTANT]
> 只有在向後導覽會將您放在路由階層中的目前位置，以流覽至指定的路由時，才可以向後導覽至指定的路由。

同樣地，您可以向前流覽多次，然後流覽至指定的路由：

```csharp
await Shell.Current.GoToAsync("../../route");
```

在此範例中，向後流覽會執行兩次，然後流覽至指定的路由。

此外，您可以在向後導覽時，透過查詢屬性傳遞資料：

```csharp
await Shell.Current.GoToAsync($"..?parameterToPassBack={parameterValueToPassBack}");
```

在此範例中，會執行向後導覽，並將查詢參數值傳遞至上一頁的查詢參數。

> [!NOTE]
> 查詢參數可以附加至任何回溯導覽要求。

如需在流覽時傳遞資料的詳細資訊，請參閱 [傳遞資料](#pass-data)。

### <a name="invalid-routes"></a>無效的路由

下列路由格式無效：

| 格式 | 說明 |
| --- | --- |
| //*page* 或 ///*page* | 全域路由目前不得為導覽堆疊上的唯一頁面。 因此，不支援以絕對路由傳送至全域路由。 |

使用這些路由格式會導致擲回 `Exception` 。

> [!WARNING]
> 嘗試導覽至不存在的路由會導致擲回 `ArgumentException` 例外狀況。

### <a name="debugging-navigation"></a>為導覽偵錯

部分 Shell 類別是以 `DebuggerDisplayAttribute` 裝飾，以指定偵錯工具如何顯示類別或欄位。 顯示與導覽要求相關的資料有助於為導覽要求偵錯。 例如，下列螢幕擷取畫面顯示物件的 [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) 和 [`CurrentState`](xref:Xamarin.Forms.Shell.CurrentState) 屬性 [`Shell.Current`](xref:Xamarin.Forms.Shell.Current) ：

![偵錯工具的螢幕擷取畫面](navigation-images/debugger.png "偵錯工具")

在此範例中，的 [`CurrentItem`](xref:Xamarin.Forms.Shell.CurrentItem) 屬性（類型為 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) ）會顯示物件的標題和路徑 `FlyoutItem` 。 同樣地， [`CurrentState`](xref:Xamarin.Forms.Shell.CurrentState) 型別的屬性（property）會 [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) 顯示在 Shell 應用程式內顯示之路由的 URI。

### <a name="navigation-stack"></a>導覽堆疊

[`Tab`](xref:Xamarin.Forms.Tab)類別會定義型別為 [`Stack`](xref:Xamarin.Forms.ShellSection.Stack) 的屬性 `IReadOnlyList<Page>` ，代表中目前的導覽堆疊 `Tab` 。 此類別也會提供下列可覆寫的導覽方法：

- [`GetNavigationStack`](xref:Xamarin.Forms.ShellSection.GetNavigationStack*)，傳回 `IReadOnlyList<Page` 目前的導覽堆疊>。
- [`OnInsertPageBefore`](xref:Xamarin.Forms.ShellSection.OnInsertPageBefore*)，呼叫時呼叫 `INavigation.InsertPageBefore` 。
- [`OnPopAsync`](xref:Xamarin.Forms.ShellSection.OnPopAsync*)、會傳回 `Task<Page>` ，而且會在 `INavigation.PopAsync` 呼叫時呼叫。
- [`OnPopToRootAsync`](xref:Xamarin.Forms.ShellSection.OnPopToRootAsync*)、會傳回 `Task` ，而且會在 `INavigation.OnPopToRootAsync` 呼叫時呼叫。
- [`OnPushAsync`](xref:Xamarin.Forms.ShellSection.OnPushAsync*)、會傳回 `Task` ，而且會在 `INavigation.PushAsync` 呼叫時呼叫。
- [`OnRemovePage`](xref:Xamarin.Forms.ShellSection.OnRemovePage*)，呼叫時呼叫 `INavigation.RemovePage` 。

下列範例顯示如何覆寫 [`OnRemovePage`](xref:Xamarin.Forms.ShellSection.OnRemovePage*) 方法：

```csharp
public class MyTab : Tab
{
    protected override void OnRemovePage(Page page)
    {
        base.OnRemovePage(page);

        // Custom logic
    }
}
```

在此範例中， `MyTab` 物件應該在 Shell 視覺階層中使用，而不是 [`Tab`](xref:Xamarin.Forms.Tab) 物件。

## <a name="navigation-events"></a>導覽事件

[`Shell`](xref:Xamarin.Forms.Shell)類別 [`Navigating`](xref:Xamarin.Forms.Shell.Navigating) 會定義事件，此事件會在導覽即將執行時引發，這可能是因為程式設計流覽或使用者互動所造成。 [`ShellNavigatingEventArgs`](xref:Xamarin.Forms.ShellNavigatingEventArgs)事件隨附的物件 `Navigating` 提供下列屬性：

| 屬性 | 類型 | Description |
|---|---|---|
| [`Current`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Current) | [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) | 目前頁面的 URI。 |
| [`Source`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Source) | [`ShellNavigationSource`](xref:Xamarin.Forms.ShellNavigationSource) | 發生導覽的類型。 |
| [`Target`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Target) | [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) | 代表導覽目的地的 URI。 |
| [`CanCancel`](xref:Xamarin.Forms.ShellNavigatingEventArgs.CanCancel)  | `bool` | 指出它是否可以取消導覽的值。 |
| [`Cancelled`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Cancelled)  | `bool` | 指出是否已取消導覽的值。 |

此外， [`ShellNavigatingEventArgs`](xref:Xamarin.Forms.ShellNavigatingEventArgs) 類別還提供 [`Cancel`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Cancel*) 可用來取消導覽的方法，以及傳回 [`GetDeferral`](xref:Xamarin.Forms.ShellNavigatingEventArgs.GetDeferral*) [`ShellNavigatingDeferral`](xref:Xamarin.Forms.ShellNavigatingDeferral) 可用來完成導覽之權杖的方法。 如需流覽延遲的詳細資訊，請參閱 [流覽延遲](#navigation-deferral)。

[`Shell`](xref:Xamarin.Forms.Shell)類別也會定義在 [`Navigated`](xref:Xamarin.Forms.Shell.Navigated) 導覽完成時引發的事件。 [`ShellNavigatedEventArgs`](xref:Xamarin.Forms.ShellNavigatedEventArgs)事件隨附的物件 `Navigated` 提供下列屬性：

| 屬性 | 類型 | Description |
|---|---|---|
| [`Current`](xref:Xamarin.Forms.ShellNavigatedEventArgs.Current)  | [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) | 目前頁面的 URI。 |
| [`Previous`](xref:Xamarin.Forms.ShellNavigatedEventArgs.Previous) | [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) | 上一頁的 URI。 |
| [`Source`](xref:Xamarin.Forms.ShellNavigatedEventArgs.Source) | [`ShellNavigationState`](xref:Xamarin.Forms.ShellNavigationState) | 發生導覽的類型。 |

> [!IMPORTANT]
> `OnNavigating`當事件引發時，會呼叫方法 [`Navigating`](xref:Xamarin.Forms.Shell.Navigating) 。 同樣地， `OnNavigated` 當引發事件時，會呼叫方法 [`Navigated`](xref:Xamarin.Forms.Shell.Navigated) 。 這兩種方法都可以在您的子類別中覆寫 [`Shell`](xref:Xamarin.Forms.Shell) 來攔截導覽要求。

[`ShellNavigatedEventArgs`](xref:Xamarin.Forms.ShellNavigatedEventArgs)和 [`ShellNavigatingEventArgs`](xref:Xamarin.Forms.ShellNavigatingEventArgs) 類別都具有 `Source` 類型的屬性 [`ShellNavigationSource`](xref:Xamarin.Forms.ShellNavigationSource) 。 此列舉提供下列值：

- `Unknown`
- `Push`
- `Pop`
- `PopToRoot`
- `Insert`
- `Remove`
- `ShellItemChanged`
- `ShellSectionChanged`
- `ShellContentChanged`

因此，可以在覆寫中攔截導覽， `OnNavigating` 並根據導覽來源執行動作。 例如，下列的程式碼會示範如何在未儲存頁面上的資料時，取消向後導覽：

```csharp
protected override void OnNavigating(ShellNavigatingEventArgs args)
{
    base.OnNavigating(args);

    // Cancel any back navigation.
    if (args.Source == ShellNavigationSource.Pop)
    {
        args.Cancel();
    }
// }
```

## <a name="navigation-deferral"></a>流覽延遲

您可以根據使用者的選擇來攔截、完成或取消 Shell 導覽。 這可以藉由覆寫子 `OnNavigating` 類別中的方法 [`Shell`](xref:Xamarin.Forms.Shell) ，以及在 [`GetDeferral`](xref:Xamarin.Forms.ShellNavigatingEventArgs.GetDeferral*) 物件上呼叫方法來達成 [`ShellNavigatingEventArgs`](xref:Xamarin.Forms.ShellNavigatingEventArgs) 。 這個方法會傳回 [`ShellNavigatingDeferral`](xref:Xamarin.Forms.ShellNavigatingDeferral) 具有方法的權杖 [`Complete`](xref:Xamarin.Forms.ShellNavigatingDeferral.Complete*) ，可用來完成導覽要求：

```csharp
public MyShell : Shell
{
    // ...
    protected override async void OnNavigating(ShellNavigatingEventArgs args)
    {
        base.OnNavigating(args);

        ShellNavigatingDeferral token = args.GetDeferral();

        var result = await DisplayActionSheet("Navigate?", "Cancel", "Yes", "No");
        if (result != "Yes")
        {
            args.Cancel();
        }
        token.Complete();
    }    
}
```

在此範例中，會顯示會邀請使用者完成導覽要求或將其取消的動作表。 藉由叫用物件上的方法來取消導覽 [`Cancel`](xref:Xamarin.Forms.ShellNavigatingEventArgs.Cancel*) [`ShellNavigatingEventArgs`](xref:Xamarin.Forms.ShellNavigatingEventArgs) 。 透過在 [`Complete`](xref:Xamarin.Forms.ShellNavigatingDeferral.Complete*) [`ShellNavigatingDeferral`](xref:Xamarin.Forms.ShellNavigatingDeferral) `GetDeferral` 物件上方法所抓取的 token 上叫用方法，即可完成導覽 `ShellNavigatingEventArgs` 。

> [!WARNING]
> [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) `InvalidOperationException` 如果使用者嘗試在有暫止的流覽延遲的情況下流覽時，方法會擲回。

## <a name="pass-data"></a>傳遞資料

執行以 URI 為基礎的程式設計導覽時，可以將資料當作查詢參數傳遞。 這是藉由在 `?` 路由之後附加，後面接著查詢參數識別碼、 `=` 和值來達成。 例如，當使用者在 `ElephantsPage` 上選取大象時，範例應用程式中會執行下列程式碼：

```csharp
async void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string elephantName = (e.CurrentSelection.FirstOrDefault() as Animal).Name;
    await Shell.Current.GoToAsync($"elephantdetails?name={elephantName}");
}
```

這個程式碼範例會在中取出目前選取的大象 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，並流覽至 `elephantdetails` 路由，並以 `elephantName` 查詢參數的形式傳遞。

有兩種方式可以接收導覽資料：

1. 代表要流覽之頁面的類別，或頁面的類別 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) ，可以使用 [`QueryPropertyAttribute`](xref:Xamarin.Forms.QueryPropertyAttribute) for each query 參數裝飾。 如需詳細資訊，請參閱 [使用查詢屬性屬性處理導覽資料](#process-navigation-data-using-query-property-attributes)。
1. 代表正在流覽之頁面的類別，或頁面的類別 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 可以實作為 [`IQueryAttributable`](xref:Xamarin.Forms.IQueryAttributable) 介面。 如需詳細資訊，請參閱 [使用單一方法處理流覽資料](#process-navigation-data-using-a-single-method)。

### <a name="process-navigation-data-using-query-property-attributes"></a>使用查詢屬性屬性處理導覽資料

您可以使用 [`QueryPropertyAttribute`](xref:Xamarin.Forms.QueryPropertyAttribute) for each query 參數裝飾接收類別，來接收導覽資料：

```csharp
[QueryProperty(nameof(Name), "name")]
public partial class ElephantDetailPage : ContentPage
{
    public string Name
    {
        set
        {
            LoadAnimal(value);
        }
    }
    ...

    void LoadAnimal(string name)
    {
        try
        {
            Animal animal = ElephantData.Elephants.FirstOrDefault(a => a.Name == name);
            BindingContext = animal;
        }
        catch (Exception)
        {
            Console.WriteLine("Failed to load animal.");
        }
    }    
}
```

的第一個引數 [`QueryPropertyAttribute`](xref:Xamarin.Forms.QueryPropertyAttribute) 會指定將接收資料之屬性的名稱，而第二個引數則指定查詢參數識別碼。因此， `QueryPropertyAttribute` 上述範例中的 `Name` 會指定屬性將 `name` 從方法呼叫中的 URI 接收查詢參數中傳遞的資料 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) 。 `Name`屬性 setter `LoadAnimal` 會呼叫方法來取出 `Animal` 的物件 `name` ，並將它設定為 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 頁面的。

> [!NOTE]
> 透過接收的查詢參數值會 [`QueryPropertyAttribute`](xref:Xamarin.Forms.QueryPropertyAttribute) 自動進行 URL 解碼。

### <a name="process-navigation-data-using-a-single-method"></a>使用單一方法處理導覽資料

您可以藉由在接收類別上執行介面來接收導覽資料 [`IQueryAttributable`](xref:Xamarin.Forms.IQueryAttributable) 。 [`IQueryAttributable`](xref:Xamarin.Forms.IQueryAttributable)介面會指定實類別必須執行 [`ApplyQueryAttributes`](xref:Xamarin.Forms.IQueryAttributable.ApplyQueryAttributes*) 方法。 這個方法具有 `query` 類型的引數， `IDictionary<string, string>` 其中包含導覽期間傳遞的任何資料。 字典中的每個索引鍵都是查詢參數識別碼，其值為查詢參數值。 使用這種方法的優點是可以使用單一方法來處理導覽資料，當您有多個需要整個處理的導覽資料項目目時，這會很有用。

下列範例顯示的視圖模型類別會實 [`IQueryAttributable`](xref:Xamarin.Forms.IQueryAttributable) 介面：

```csharp
public class MonkeyDetailViewModel : IQueryAttributable, INotifyPropertyChanged
{
    public Animal Monkey { get; private set; }

    public void ApplyQueryAttributes(IDictionary<string, string> query)
    {
        // The query parameter requires URL decoding.
        string name = HttpUtility.UrlDecode(query["name"]);
        LoadAnimal(name);
    }

    void LoadAnimal(string name)
    {
        try
        {
            Monkey = MonkeyData.Monkeys.FirstOrDefault(a => a.Name == name);
            OnPropertyChanged("Monkey");
        }
        catch (Exception)
        {
            Console.WriteLine("Failed to load animal.");
        }
    }
    ...
}
```

在此範例中， [`ApplyQueryAttributes`](xref:Xamarin.Forms.IQueryAttributable.ApplyQueryAttributes*) 方法會 `name` 從方法呼叫中的 URI 抓取查詢參數的值 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) 。 然後， `LoadAnimal` 呼叫方法以抓取 `Animal` 物件，其設定為數據所系結之屬性的值 `Monkey` 。

> [!IMPORTANT]
> 透過介面接收的查詢參數值 [`IQueryAttributable`](xref:Xamarin.Forms.IQueryAttributable) 不會自動進行 URL 解碼。

#### <a name="pass-and-process-multiple-query-parameters"></a>傳遞和處理多個查詢參數

您可以透過連接多個查詢參數來傳遞這些參數 `&` 。 例如，下列程式碼會傳遞兩個數據項：

```csharp
async void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string elephantName = (e.CurrentSelection.FirstOrDefault() as Animal).Name;
    string elephantLocation = (e.CurrentSelection.FirstOrDefault() as Animal).Location;
    await Shell.Current.GoToAsync($"elephantdetails?name={elephantName}&location={elephantLocation}");
}
```

這個程式碼範例會取出中目前選取的大象 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，並流覽至 `elephantdetails` 路由， `elephantName` 並傳遞和 `elephantLocation` 作為查詢參數。

若要接收多個資料項目目，代表要導覽之頁面的類別，或頁面的類別 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) ，可以使用 [`QueryPropertyAttribute`](xref:Xamarin.Forms.QueryPropertyAttribute) for each 查詢參數裝飾：

```csharp
[QueryProperty(nameof(Name), "name")]
[QueryProperty(nameof(Location), "location")]
public partial class ElephantDetailPage : ContentPage
{
    public string Name
    {
        set
        {
            // Custom logic
        }
    }

    public string Location
    {
        set
        {
            // Custom logic
        }
    }
    ...    
}
```

在此範例中，類別是以 [`QueryPropertyAttribute`](xref:Xamarin.Forms.QueryPropertyAttribute) for each query 參數的來裝飾。 第一個 `QueryPropertyAttribute` `Name` 會指定屬性將接收查詢參數中所傳遞的資料 `name` ，而第二個指定屬性 `QueryPropertyAttribute` `Location` 將接收 `location` 查詢參數中傳遞的資料。 在這兩種情況下，查詢參數值都是在方法呼叫的 URI 中指定 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) 。

或者，您可以藉由在 [`IQueryAttributable`](xref:Xamarin.Forms.IQueryAttributable) 代表所要流覽之頁面的類別上執行介面，或在頁面的類別中，藉由單一方法來處理導覽資料 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) ：

```csharp
public class ElephantDetailViewModel : IQueryAttributable, INotifyPropertyChanged
{
    public Animal Elephant { get; private set; }

    public void ApplyQueryAttributes(IDictionary<string, string> query)
    {
        string name = HttpUtility.UrlDecode(query["name"]);
        string location = HttpUtility.UrlDecode(query["location"]);
        ...        
    }
    ...
}
```

在此範例中， [`ApplyQueryAttributes`](xref:Xamarin.Forms.IQueryAttributable.ApplyQueryAttributes*) 方法會 `name` `location` 從方法呼叫中的 URI 抓取和查詢參數的值 [`GoToAsync`](xref:Xamarin.Forms.Shell.GoToAsync*) 。

## <a name="back-button-behavior"></a>上一頁按鈕行為

您可以藉由將 [`BackButtonBehavior`](xref:Xamarin.Forms.Shell.BackButtonBehaviorProperty) 附加屬性設定為物件，來重新定義 [上一頁] 按鈕外觀和行為 [`BackButtonBehavior`](xref:Xamarin.Forms.BackButtonBehavior) 。 [`BackButtonBehavior`](xref:Xamarin.Forms.BackButtonBehavior)類別會定義下列屬性：

- [`Command`](xref:Xamarin.Forms.BackButtonBehavior.Command)，類型為 `ICommand` ，在按下 [上一頁] 按鈕時執行。
- [`CommandParameter`](xref:Xamarin.Forms.BackButtonBehavior.CommandParameter)，屬於類型 `object` ，也就是傳遞給的參數 `Command` 。
- [`IconOverride`](xref:Xamarin.Forms.BackButtonBehavior.IconOverride)，類型為 [`ImageSource`](xref:Xamarin.Forms.ImageSource) ，用於 [上一頁] 按鈕的圖示。
- [`IsEnabled`](xref:Xamarin.Forms.BackButtonBehavior.IsEnabled)型別為的 `boolean` ，指出是否已啟用 [上一頁] 按鈕。 預設值是 `true`。
- [`TextOverride`](xref:Xamarin.Forms.BackButtonBehavior.TextOverride)，類型為 `string` ，用於 [上一頁] 按鈕的文字。

所有這些屬性都是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

下列程式碼顯示重新定義按鈕外觀和行為的範例：

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

[`Command`](xref:Xamarin.Forms.BackButtonBehavior.Command) `ICommand` 當按下 [上一頁] 按鈕時，屬性會設定為，並將 `IconOverride` 屬性設定為 [上一頁] 按鈕所用的圖示：

[![螢幕擷取畫面： iOS 和 Android 上的 Shell 上一頁按鈕圖示覆寫](navigation-images/back-button.png)](navigation-images/back-button-large.png#lightbox)

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
