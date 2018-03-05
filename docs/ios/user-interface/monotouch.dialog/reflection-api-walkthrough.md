---
title: "逐步解說： 建立使用反映 API 的應用程式"
description: "除了項目 API，MonoTouch.Dialog （機動D） 也包含屬性為基礎的反映 API。 反映 API 可讓使用機動建立螢幕D 與裝飾類別與屬性一樣容易。 本文提供的逐步示範如何建立使用反映 API 的應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C0F923D2-300E-DB9D-F390-9FA71B22DFD6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 44df6fce4ec6d667c096da01cfc339ec2afdb077
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough-creating-an-application-using-the-reflection-api"></a>逐步解說： 建立使用反映 API 的應用程式

_除了項目 API，MonoTouch.Dialog （機動D） 也包含屬性為基礎的反映 API。反映 API 可讓使用機動建立螢幕D 與裝飾類別與屬性一樣容易。本文提供的逐步示範如何建立使用反映 API 的應用程式。_


周D 反映 API 可讓類別能夠以屬性裝飾的周D 會使用自動建立螢幕。 反映 API 提供這些類別，並在螢幕上顯示的內容之間的繫結。 這個 API 不會提供更細微的控制的應用程式開發介面的項目，雖然它可自動建置出根據類別裝飾項目階層架構來降低複雜性。

 <a name="Getting_Started_with_the_Reflection_API" />


## <a name="getting-started-with-the-reflection-api"></a>反映 API 使用者入門

使用反映 API，就像簡單：

1.  建立類別以機動裝飾D 屬性。
1.  建立`BindingContext`執行個體，請將上述類別的執行個體傳遞給它。 
1.  建立`DialogViewController`，將其傳遞`BindingContext’s` `RootElement` 。 


讓我們看看範例來說明如何使用反映 API。 在此範例中，我們將建立簡單的資料輸入螢幕如下所示：

 [ ![](reflection-api-walkthrough-images/01-expense-entry.png "在此範例中，我們將建立簡單的資料輸入螢幕如下所示")](reflection-api-walkthrough-images/01-expense-entry.png)

 <a name="Creating_a_Class_with_MT.D_Attributes" />


## <a name="creating-a-class-with-mtd-attributes"></a>使用機動建立類別D 屬性

首先我們要使用反映 API 是以屬性裝飾的類別。 這些屬性將會使用機動從項目 API，D 在內部用來建立物件。 例如，請考慮下列類別定義：

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

`SectionAttribute`區段將會導致`UITableView`正在建立的與用來填入該區段的標頭的字串引數。 一旦宣告區段，它後面的每個欄位會包含在該區段中，直到另一個區段宣告。
為欄位建立的使用者介面項目類型將視欄位的類型和機動而將其 D 屬性。

例如，`Name`欄位是`string`和其用來裝飾`EntryAttribute`。 這會導致資料列加入到指定的標題文字輸入欄位與資料表。 同樣地，`IsApproved`欄位是`bool`與`CheckboxAttribute`，產生的資料表資料列與資料表資料格右邊的核取方塊。 周D 會使用該欄位的名稱自動加入一個空格，在此情況下，建立標題，因為未指定的屬性。

 <a name="Adding_the_BindingContext" />


## <a name="adding-the-bindingcontext"></a>加入 Bindingparameters

若要使用`Expense`類別，建立所需的`BindingContext`。 A`BindingContext`是從屬性化類別以建立元素的階層會將資料繫結的類別。 若要建立一個，我們只是它執行個體化，並傳入建構函式的屬性化類別的執行個體。

例如，若要加入我們宣告使用的 UI 中的屬性`Expense`類別中，加入下列程式碼中的`FinishedLaunching`方法`AppDelegate`:

```csharp
var expense = new Expense ();
var bctx = new BindingContext (null, expense, "Create a task");
```

然後我們只需要建立 UI 是加入`BindingContext`至`DialogViewController`並將它做為設定`RootViewController`的視窗，如下所示：

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

執行應用程式現在會導致畫面顯示如上所示。

 <a name="Adding_a_UINavigationController" />


### <a name="adding-a-uinavigationcontroller"></a>加入 UINavigationController

不過請注意，標題 [建立工作]，我們傳遞至`BindingContext`不會顯示。 這是因為`DialogViewController`不是屬於`UINavigatonController`。 讓我們來變更程式碼以加入`UINavigationController`與視窗的`RootViewController,`並加入`DialogViewController`作為根`UINavigationController`如下所示：

```csharp
nav = new UINavigationController(dvc);
window.RootViewController = nav;
```

現在當我們執行應用程式時，標題會出現在`UINavigationController’s`導覽列做為螢幕擷取畫面所示：

 [ ![](reflection-api-walkthrough-images/02-create-task.png "現在當我們執行應用程式時，標題會出現在 UINavigationControllers 導覽列")](reflection-api-walkthrough-images/02-create-task.png)

藉由`UINavigationController`，我們現在能夠利用機動的其他功能的優點瀏覽是必要的 D。 比方說，我們可以將列舉型別`Expense`類別定義的費用和機動的類別目錄D 將會自動建立的選取畫面。 若要進行示範，請修改`Expense`類別，以包含`ExpenseCategory`欄位，如下所示：

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

執行應用程式現在會導致類別目錄資料表中新的資料列所示：

 [ ![](reflection-api-walkthrough-images/03-set-details.png "如所示，執行應用程式現在導致類別目錄資料表中新的資料列")](reflection-api-walkthrough-images/03-set-details.png)

選取的資料列會產生應用程式瀏覽至新螢幕的資料列對應至列舉型別，如下所示：

 [ ![](reflection-api-walkthrough-images/04-set-category.png "選取的資料列會導致應用程式瀏覽至新螢幕的資料列對應至列舉")](reflection-api-walkthrough-images/04-set-category.png)

 <a name="Summary" />


## <a name="summary"></a>總結

這篇文章呈現反映 API 的逐步解說。 我們也示範了如何將屬性加入至類別，來控制顯示的內容。 我們也將討論如何使用`BindingContext`類別的資料繫結至項目階層所建立，以及如何使用機動與 D `UINavigationController`。


## <a name="related-links"></a>相關連結

- [MTDReflectionWalkthrough （範例）](https://developer.xamarin.com/samples/MTDReflectionWalkthrough/)
- [錄影畫面-Miguel de Icaza iOS 登入畫面以建立 MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [錄影畫面-輕鬆地建立 iOS 使用者介面與 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introduction to MonoTouch 對話方塊](~/ios/user-interface/monotouch.dialog/index.md)
- [逐步解說中應用程式開發介面項目](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [逐步解說中 JSON 項目](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Github 上的 MonoTouch 對話方塊](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [TweetStation 應用程式](https://github.com/migueldeicaza/TweetStation)
- [UITableViewController 類別參考](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 類別參考](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
