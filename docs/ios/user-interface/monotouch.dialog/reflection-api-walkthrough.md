---
title: 使用反映 API 建立 Xamarin iOS 應用程式
description: 本檔說明以 Monotouch.dll 屬性為基礎的反映 API，它會根據以屬性裝飾的類別來建立 UI。
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 2eef38791f420fd49b87b46fd131f1e6ce501892
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436576"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>使用反映 API 建立 Xamarin iOS 應用程式

MT。D 反映 API 可讓類別以 MT 的屬性裝飾。D 用來自動建立畫面。 反映 API 提供這些類別之間的系結，以及畫面上顯示的內容。 雖然此 API 未提供專案 API 所執行的細部控制項，但它會根據類別裝飾自動建立專案階層來降低複雜度。

## <a name="setting-up-mtd"></a>設定 MT。D

MT.D 與 Xamarin 一起散發。 若要使用它，請在 Visual Studio 2017 或 Visual Studio for Mac 中，以滑鼠右鍵按一下 Xamarin 專案的 [ **參考** ] 節點，然後加入 monotouch.dll 的參考 **。對話方塊-1** 元件。 然後， `using MonoTouch.Dialog` 視需要在您的原始程式碼中加入語句。

## <a name="getting-started-with-the-reflection-api"></a>開始使用反映 API

使用反映 API 很簡單，如下所示：

1. 建立以 MT 裝飾的類別。D 屬性。
1. 建立  `BindingContext` 實例，並將上述類別的實例傳遞給它。
1. 建立  `DialogViewController` ，並將它傳遞給  `BindingContext’s` `RootElement` 。

讓我們來看一個範例，說明如何使用反映 API。 在此範例中，我們將建立簡單的資料輸入畫面，如下所示：

 [![在此範例中，我們將建立簡單的資料輸入畫面，如下所示](reflection-api-walkthrough-images/01-expense-entry.png)](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>使用 MT 建立類別。D 屬性

我們需要使用反映 API 的第一件事，是以屬性裝飾的類別。 MT 將使用這些屬性。D 內部從 Elements API 建立物件。 例如，請考慮下列類別定義：

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

`SectionAttribute`會導致建立的區段 `UITableView` ，並使用字串引數來填入區段的標頭。 宣告區段之後，接下來的每個欄位都會包含在該區段中，直到宣告另一個區段為止。
針對此欄位所建立的使用者介面元素類型將取決於欄位的類型和 MT。D 屬性裝飾它。

例如，此 `Name` 欄位是， `string` 而且會以進行裝飾 `EntryAttribute` 。 這會導致將資料列加入資料表中，其中包含文字專案欄位和指定的標題。 同樣地，此 `IsApproved` 欄位是 `bool` 具有的 `CheckboxAttribute` ，會產生資料表資料列，其中包含資料表資料格右邊的核取方塊。 MT.D 會使用功能變數名稱，自動加入空格，在此案例中建立標題，因為它不是在屬性中指定。

## <a name="adding-the-bindingcontext"></a>新增 BindingCoNtext

若要使用 `Expense` 類別，我們必須建立 `BindingContext` 。 `BindingContext`是類別，會系結屬性化類別的資料，以建立專案的階層。 若要建立一個，我們只需將它具現化，並將屬性化類別的實例傳遞至函式。

例如，若要加入我們使用類別中的屬性所宣告的 UI `Expense` ，請在的方法中包含下列程式碼 `FinishedLaunching` `AppDelegate` ：

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

然後，我們只需要將新增 `BindingContext` 至， `DialogViewController` 然後將它設定為 `RootViewController` 視窗的，如下所示：

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

現在，執行應用程式會導致顯示上面所顯示的畫面。

### <a name="adding-a-uinavigationcontroller"></a>新增 UINavigationController

不過請注意，我們傳遞給的「建立工作」標題 `BindingContext` 不會顯示。 這是因為不 `DialogViewController` 是的一部分 `UINavigatonController` 。 讓我們變更程式碼，將加入 `UINavigationController` 做為視窗的 `RootViewController,` ，並將加入作為的根目錄，如下 `DialogViewController` `UINavigationController` 所示：

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

現在當我們執行應用程式時，標題會出現在導覽列中， `UINavigationController’s` 如下列螢幕擷取畫面所示：

 [![現在當我們執行應用程式時，標題會出現在 UINavigationControllers 巡覽列中。](reflection-api-walkthrough-images/02-create-task.png)](reflection-api-walkthrough-images/02-create-task.png#lightbox)

藉由加入 `UINavigationController` ，我們現在可以利用 MT 的其他功能。需要導覽的 D。 例如，我們可以將列舉新增至類別 `Expense` ，以定義費用和 MT 的分類。D 會自動建立選取畫面。 若要示範，請修改 `Expense` 類別以包含 `ExpenseCategory` 欄位，如下所示：

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

現在，執行應用程式會在資料表中產生新的資料列，如下所示：

 [![現在，執行應用程式會在資料表中產生新的資料列，如下所示](reflection-api-walkthrough-images/03-set-details.png)](reflection-api-walkthrough-images/03-set-details.png#lightbox)

選取資料列會導致應用程式流覽至具有對應至列舉之資料列的新畫面，如下所示：

 [![選取資料列會導致應用程式流覽至具有對應至列舉之資料列的新畫面](reflection-api-walkthrough-images/04-set-category.png)](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary"></a>

## <a name="summary"></a>摘要

本文提供反映 API 的逐步解說。 我們已示範如何將屬性新增至類別，以控制要顯示的內容。 我們也討論了如何使用將 `BindingContext` 資料從類別系結至所建立的元素階層，以及如何使用 MT。使用的 D `UINavigationController` 。

## <a name="related-links"></a>相關連結

- [MTDReflectionWalkthrough (範例) ](/samples/xamarin/ios-samples/mtdreflectionwalkthrough)
- [Monotouch.dll 對話方塊簡介](~/ios/user-interface/monotouch.dialog/index.md)
- [Elements API 逐步解說](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [JSON 元素逐步解說](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Github 上的 Monotouch.dll 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)