---
title: 建立 Xamarin.iOS 應用程式使用反映 API
description: 本文件描述 MonoTouch.Dialog 屬性型反映 API，其會建立以屬性裝飾的類別為基礎的 UI。
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: 9ea31d977352c5cc9609136c74099c99c08bdc30
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675173"
---
# <a name="creating-a-xamarinios-application-using-the-reflection-api"></a>建立 Xamarin.iOS 應用程式使用反映 API

Mt。D 反映 API 可讓類別能夠以屬性裝飾，MTD 會使用自動建立螢幕。 反映 API 會提供這些類別，並在畫面上顯示的內容之間的繫結。 雖然此 API 不提供精細的控制，項目 API，它便會降低複雜性，藉由自動建置出基礎類別裝飾項目階層。

## <a name="setting-up-mtd"></a>MT 的設定D

MTD Xamarin.iOS 一起散發。 若要使用它，以滑鼠右鍵按一下**參考**節點的 Xamarin.iOS 專案在 Visual Studio 2017 或 Visual Studio for Mac，並加入參考**MonoTouch.Dialog 1**組件。 然後，新增`using MonoTouch.Dialog`視您在來源中的陳述式的程式碼。

## <a name="getting-started-with-the-reflection-api"></a>開始使用反映 API

使用反映 API 很簡單，只要：

1.  建立類別以 MT 裝飾D 屬性。
1.  建立`BindingContext`執行個體，將上述類別的執行個體傳遞給它。 
1.  建立`DialogViewController`，將其傳遞`BindingContext’s` `RootElement` 。 


讓我們看看範例來說明如何使用反映 API。 在此範例中，我們將建置簡單的資料輸入螢幕，如下所示：

 [![](reflection-api-walkthrough-images/01-expense-entry.png "在此範例中，我們將建置簡單的資料輸入畫面如下所示")](reflection-api-walkthrough-images/01-expense-entry.png#lightbox)

## <a name="creating-a-class-with-mtd-attributes"></a>使用 MT 建立類別D 屬性

我們需要使用反映 API 的第一件事是使用屬性裝飾的類別。 這些屬性將供 MTD 在內部建立的物件項目 API。 例如，請考慮下列類別定義：

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

`SectionAttribute`的各節將會導致`UITableView`，使用建立用來填入該區段的標頭的字串引數。 區段宣告後，接在後面的每個欄位將包含在該區段中，，直到宣告另一個區段。
使用者介面項目建立之欄位的類型會視欄位的類型和 mt。D 屬性裝飾它。

例如，`Name`欄位是`string`而且它使用裝飾`EntryAttribute`。 這會新增至指定的標題文字輸入欄位與資料表的資料列。 同樣地，`IsApproved`欄位是`bool`使用`CheckboxAttribute`，產生的資料表資料列在表格儲存格右邊的核取方塊。 MTD 使用欄位名稱，自動加入一個空格，在此情況下，建立標題，因為未指定的屬性。

## <a name="adding-the-bindingcontext"></a>新增 BindingContext

若要使用`Expense`類別，我們需要建立`BindingContext`。 A`BindingContext`是從屬性化類別，以建立項目階層架構會將資料繫結的類別。 若要建立一個，我們只是它具現化，並傳入建構函式的屬性化類別的執行個體。

例如，若要將使用我們所宣告的 UI 中的屬性`Expense`類別中，包括中的下列程式碼`FinishedLaunching`方法`AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

然後我們要建立的 UI 所做的是新增`BindingContext`要`DialogViewController`並將它設為`RootViewController`的視窗，如下所示：

```csharp
UIWindow window;

public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
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

執行應用程式現在會導致顯示上方顯示的畫面。

### <a name="adding-a-uinavigationcontroller"></a>新增 UINavigationController

不過請注意，標題 [建立工作]，我們傳遞給`BindingContext`則不會顯示。 這是因為`DialogViewController`不是屬於`UINavigatonController`。 讓我們變更程式碼來新增`UINavigationController`成視窗`RootViewController,`並新增`DialogViewController`做為`UINavigationController`，如下所示：

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

現在當我們執行應用程式時，標題會出現在`UINavigationController’s`導覽列中的為螢幕擷取畫面如下所示：

 [![](reflection-api-walkthrough-images/02-create-task.png "現在當我們執行應用程式時，標題會出現在 UINavigationControllers 導覽列")](reflection-api-walkthrough-images/02-create-task.png#lightbox)

藉由包括`UINavigationController`，我們現在可以利用其他功能的 MT瀏覽是必要的 D。 比方說，我們可以將列舉型別`Expense`類別定義的費用，而 MT 類別D 將會自動建立的選取畫面。 為了示範，修改`Expense`類別，以包含`ExpenseCategory`欄位，如下所示：

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

執行應用程式現在會產生新的資料列，類別目錄資料表中所示：

 [![](reflection-api-walkthrough-images/03-set-details.png "如所示，執行應用程式現在導致新的資料列，資料表中的類別目錄")](reflection-api-walkthrough-images/03-set-details.png#lightbox)

選取的資料列會產生應用程式瀏覽至新的畫面與資料列對應至列舉型別，如下所示：

 [![](reflection-api-walkthrough-images/04-set-category.png "選取的資料列會導致應用程式瀏覽至新的畫面與資料列對應至列舉型別")](reflection-api-walkthrough-images/04-set-category.png#lightbox)

 <a name="Summary" />


## <a name="summary"></a>總結

這篇文章所呈現反映 API 逐步的解說。 我們示範了如何將屬性加入至類別，來控制顯示的內容。 我們也討論如何使用`BindingContext`類別的資料繫結至項目階層架構所建立，以及如何使用 MT使用 D `UINavigationController`。


## <a name="related-links"></a>相關連結

- [MTDReflectionWalkthrough （範例）](https://developer.xamarin.com/samples/MTDReflectionWalkthrough/)
- [螢幕錄製影片-Miguel de Icaza 建立 iOS 登入畫面 MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [螢幕錄製影片-輕鬆地建立 iOS 使用者介面與 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [簡介 MonoTouch 對話方塊](~/ios/user-interface/monotouch.dialog/index.md)
- [項目 API 逐步解說](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [JSON 元素的逐步解說](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [在 Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
