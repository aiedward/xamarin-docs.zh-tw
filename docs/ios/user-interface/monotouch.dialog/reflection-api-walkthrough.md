---
title: 使用反映 API 建立 Xamarin iOS 應用程式
description: 本檔描述 MonoTouch 屬性型反映 API，它會根據以屬性裝飾的類別來建立 UI。
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 323b92190dc3ea18bc78871f5c19e51d0a6ea94e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002209"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>使用反映 API 建立 Xamarin iOS 應用程式

MT。D 反映 API 可讓類別以 MT 的屬性裝飾。D 會使用來自動建立畫面。 反映 API 會提供這些類別之間的系結，以及螢幕上顯示的內容。 雖然此 API 不會提供元素 API 所執行的細微控制，但它會根據類別裝飾自動建立元素階層，藉此降低複雜性。

## <a name="setting-up-mtd"></a>設定 MT。D

MT.D 與 Xamarin 一起散發。 若要使用它，請以滑鼠右鍵按一下 Visual Studio 2017 或 Visual Studio for Mac 中之 Xamarin 專案的 [**參考**] 節點，然後新增**MonoTouch**元件的參考。 然後，視需要在您的原始程式碼中加入 `using MonoTouch.Dialog` 語句。

## <a name="getting-started-with-the-reflection-api"></a>開始使用反映 API

使用反映 API 的方式很簡單：

1. 建立以 MT 裝飾的類別。D 屬性。
1. 建立 `BindingContext` 實例，並將上述類別的實例傳遞給它。 
1. 建立 `DialogViewController`，並將 `BindingContext’s` `RootElement` 傳遞給它。 

讓我們來看一個範例，以說明如何使用反映 API。 在此範例中，我們將建立簡單的資料輸入畫面，如下所示：

 [![](reflection-api-walkthrough-images/01-expense-entry.png "In this example, we'll build a simple data entry screen as shown here")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>建立具有 MT 的類別。D 屬性

我們需要使用反映 API 的第一件事是以屬性裝飾的類別。 MT 會使用這些屬性。D 內部，從 Elements API 建立物件。 例如，請考慮下列類別定義：

```csharp
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;
        
    [Section("Expense Details")]
  
    [Caption("Description")]
    [Entry]
    public string Details;
        
    [Checkbox]
    public bool IsApproved = true;
}
```

`SectionAttribute` 會導致建立 `UITableView` 的區段，並使用字串引數來填入區段的標頭。 一旦宣告區段之後，該區段後面的每個欄位都會包含在該區段中，直到宣告另一個區段為止。
針對欄位所建立的使用者介面元素類型，將取決於該欄位的類型和 MT。裝飾它的屬性。

例如，[`Name`] 欄位是一個 `string`，它會以 `EntryAttribute`裝飾。 這會導致在資料表中加入一個資料列，其中包含文字輸入欄位和指定的標題。 同樣地，[`IsApproved`] 欄位是具有 `CheckboxAttribute`的 `bool`，因此會產生資料表資料列，並在資料表資料格右邊加上核取方塊。 MT.D 會使用功能變數名稱，自動加入空格，在此案例中建立標題，因為它不是在屬性中指定。

## <a name="adding-the-bindingcontext"></a>新增 BindingCoNtext

若要使用 `Expense` 類別，我們需要建立 `BindingContext`。 `BindingContext` 是一個類別，它會系結屬性化類別的資料，以建立元素的階層架構。 若要建立一個，我們只需要將它具現化，並將屬性化類別的實例傳入至該函式。

例如，若要加入我們在 `Expense` 類別中使用屬性所宣告的 UI，請在 `AppDelegate`的 `FinishedLaunching` 方法中包含下列程式碼：

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

接下來，我們只需要將 `BindingContext` 新增至 `DialogViewController`，並將它設定為視窗的 `RootViewController`，如下所示：

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{   
    window = new UIWindow (UIScreen.MainScreen.Bounds);
            
    var expense = new Expense ();
    var bctx = new BindingContext (null, expense, "Create a task");
    var dvc = new DialogViewController (bctx.Root);
            
    window.RootViewController = dvc;
    window.MakeKeyAndVisible ();
            
    return true;
}
```

現在執行應用程式會導致顯示上面顯示的畫面。

### <a name="adding-a-uinavigationcontroller"></a>新增 UINavigationController

但請注意，我們傳遞給 `BindingContext` 的標題「建立工作」並不會顯示。 這是因為 `DialogViewController` 不是 `UINavigatonController`的一部分。 讓我們變更程式碼，將 `UINavigationController` 新增為視窗的 `RootViewController,`，並將 `DialogViewController` 新增為 `UINavigationController` 的根，如下所示：

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

現在當我們執行應用程式時，標題會出現在 [`UINavigationController’s`] 導覽列中，如下圖所示：

 [![](reflection-api-walkthrough-images/02-create-task.png "Now when we run the application, the title appears in the UINavigationControllers navigation bar")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

藉由包含 `UINavigationController`，我們現在可以利用 MT 的其他功能。需要導覽的 D。 例如，我們可以將列舉新增至 `Expense` 類別，以定義費用和 MT 的分類。D 會自動建立選取畫面。 若要示範，請修改 `Expense` 類別以包含 `ExpenseCategory` 欄位，如下所示：

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
public class Expense
{
    …

    [Caption("Category")]
    public Category ExpenseCategory;
}
```

現在執行應用程式會在資料表中產生新的資料列，如下所示：

 [![](reflection-api-walkthrough-images/03-set-details.png "Running the application now results in a new row in the table for the category as shown")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

選取資料列會導致應用程式流覽至新的畫面，其中包含對應至列舉的資料列，如下所示：

 [![](reflection-api-walkthrough-images/04-set-category.png "Selecting the row results in the application navigating to a new screen with rows corresponding to the enumeration")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />

## <a name="summary"></a>總結

本文提供反映 API 的逐步解說。 我們示範了如何將屬性加入至類別，以控制要顯示的內容。 我們也討論了如何使用 `BindingContext` 將類別中的資料系結至所建立的元素階層，以及如何使用 MT。具有 `UINavigationController`的 D。

## <a name="related-links"></a>相關連結

- [MTDReflectionWalkthrough （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdreflectionwalkthrough)
- [MonoTouch 對話方塊簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [Elements API 逐步解說](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [JSON 元素逐步解說](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
