---
title: 第 24 章的摘要。 頁面巡覽
description: 使用 Xamarin.Forms 建立行動應用程式：第 24 章的摘要。 頁面巡覽
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 7210cffb3cd348a6bdbf6cee80c4b6cd55553c58
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61333537"
---
# <a name="summary-of-chapter-24-page-navigation"></a>第 24 章的摘要。 頁面巡覽

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

許多應用程式是由使用者所巡覽之多個頁面所組成。 應用程式一定有*主要*頁面或*家用* 頁面上，並從該處使用者巡覽至其他頁面，會保存在向後巡覽堆疊中。 其他瀏覽選項所述[**第 25 章。頁面上各種**](chapter25.md)。

## <a name="modal-pages-and-modeless-pages"></a>強制回應頁面和非強制回應頁面

`VisualElement` 定義[ `Navigation` ](xref:Xamarin.Forms.NavigableElement.Navigation)屬性的型別[ `INavigation` ](xref:Xamarin.Forms.INavigation)，其中包含下列兩種方法，以巡覽至新的頁面：

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

這兩個方法都接受`Page`執行個體做為引數和傳回`Task`物件。 下列兩種方法瀏覽回到前一個頁面：

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

如果使用者介面都有它自己**回**按鈕 （如執行 Android 和 Windows phone），則不需要應用程式呼叫這些方法。

雖然這些方法可從任何`VisualElement`，從的呼叫通常`Navigation`屬性與目前`Page`執行個體。

使用者必須提供頁面上的某些資訊，然後再回到前一個頁面時，應用程式通常會使用強制回應頁面。 不是強制回應頁面有時也稱為非強制回應或*階層式*。 在網頁中為 nothing 區分為強制回應或非強制回應;它受到改為用來瀏覽至它的方法。 若要跨所有平台，強制回應頁面必須提供它自己的使用者介面向後巡覽至上一頁。

[ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal)範例可讓您瀏覽非強制回應和強制回應頁面之間的差異。 使用網頁瀏覽任何應用程式必須通過其首頁[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)建構函式，通常是在程式的`App`類別。 一個好處是讓您不再需要設定`Padding`頁面上的 iOS。

您將會發現非強制回應頁面，頁面的[ `Title` ](xref:Xamarin.Forms.Page.Title)屬性會顯示。 IOS、 Android 和 Windows 平板電腦和桌上型電腦平台所有提供的使用者介面項目，若要瀏覽回到前一個頁面。 課程、 Android 和 Windows phone 裝置有一種標準**回**返回 按鈕。

強制回應頁面，頁面`Title`未顯示，且沒有任何使用者介面項目提供回到前一個頁面。 雖然您可以使用 Android 和 Windows phone 標準**回**按鈕以返回先前的頁面上，強制回應頁面，在其他平台上的必須提供它自己的機制，以返回。

### <a name="animated-page-transitions"></a>動畫的頁面轉換

第二個的布林值引數，來提供各種巡覽方法的替代版本`true`如果您想要包含動畫的頁面轉換：

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

不過，標準的頁面巡覽方法包括動畫預設情況下，或使這些都只是重要的 （如本章節的結尾），瀏覽至特定的頁面上，在啟動時 （如所述，提供您自己的開場動畫[ **Chapter22。動畫**](chapter22.md))。

### <a name="visual-and-functional-variations"></a>視覺和功能的變化

`NavigationPage` 包含兩個屬性，當您具現化中的類別，您可以設定您`App`方法：

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` 也包含四個附加的可繫結屬性，會影響在其設定的特定頁面：

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) 和 [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) 和 [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String)) 並[ `GetBackButtonTitle` ](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject))僅在 iOS 上的工作
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource)) 並[ `GetTitleIcon` ](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject)) iOS 和 Android 只上工作

### <a name="exploring-the-mechanics"></a>探索機制

頁面瀏覽方法全都是非同步，而且應該搭配`await`。 完成並不表示頁面瀏覽已完成，但僅，安全地檢查頁面瀏覽堆疊。

當一頁巡覽到另一個時，第一個頁面通常會取得呼叫其[ `OnDisappearing` ](xref:Xamarin.Forms.Page.OnDisappearing)方法和第二個頁面取得呼叫其[ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing)方法。 同樣地，當一個頁面傳回至另一個時，第一頁會取得呼叫其`OnDisappearing`方法和第二個頁面通常會取得呼叫其`OnAppearing`方法。 這些呼叫 （和叫用的瀏覽完成的非同步方法） 的順序取決於平台。 使用兩個先前的陳述式中的 「 通常 」 這個字是因為 Android 的強制回應頁面導覽，這些方法呼叫不會發生。

此外，呼叫`OnAppearing`和`OnDisappearing`方法不一定表示頁面導覽。

`INavigation`介面包含可讓您檢查導覽堆疊上的兩個集合屬性：

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack) 型別的`IReadOnlyList<Page>`適用於非強制回應堆疊
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack) 型別的`IReadOnlyList<Page>`的強制回應堆疊

它是最安全的方式來存取從這些堆疊`Navigation`的屬性`NavigationPage`(應該`App`類別的[ `MainPage` ](xref:Xamarin.Forms.Application.MainPage)屬性)。 只是安全的非同步頁面巡覽方法完成之後，請檢查這些堆疊。 [ `CurrentPage` ](xref:Xamarin.Forms.NavigationPage.CurrentPage)屬性`NavigationPage`不會指出目前的頁面目前的頁面是否為強制回應頁面，但表示改為最後一個非強制回應頁面。

[ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation)範例可讓您瀏覽頁面導覽和堆疊和頁面巡覽的合法的類型：

- 非強制回應頁面可以巡覽至另一個非強制回應頁面 」 或 「 強制回應頁面
- 強制回應頁面只能巡覽使用另一個強制回應頁面

### <a name="enforcing-modality"></a>強制執行強制回應性

需要向使用者取得某些資訊時，應用程式會使用強制回應頁面。 使用者應該禁止回到前一頁，提供該資訊之前。 在 iOS 上，就可以輕鬆地提供**回**按鈕，然後在使用者與頁面完成時才加以啟用。 適用於 Android 和 Windows phone 裝置，應用程式應該覆寫，但[ `OnBackButtonPressed` ](xref:Xamarin.Forms.Page.OnBackButtonPressed)方法，並傳回`true`如果計畫已處理**回**按鈕本身，而如所示[ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement)範例。

[ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement)範例會示範如何將在 MVVM 案例中。

## <a name="navigation-variations"></a>瀏覽變化

如果特定的強制回應頁面可以巡覽至多次，以便使用者可以編輯資訊，而不是輸入，它應該保留資訊全都在一次。 您可以處理這藉由保留特定的執行個體的強制回應頁面中，但更好的方法 （特別是在 iOS 上） 在保存的檢視模型中的資訊。

### <a name="making-a-navigation-menu"></a>建立導覽功能表

[ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType)範例示範如何使用`TableView`列出功能表項目。 每個項目相關聯`Type`特定頁面的物件。 選取該項目時，程式會具現化的頁面，並巡覽至它。

[![檢視組件庫類型的三個螢幕擷取畫面](images/ch24fg21-small.png "TableView 列出的功能表項目")](images/ch24fg21-large.png#lightbox "TableView 列出的功能表項目")

[ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst)範例是有點不同，因為該功能表包含每個頁面，而不是類型的執行個體。 這可協助保留資訊從每個頁面上，但所有頁面必須在程式啟動時具現都化。

### <a name="manipulating-the-navigation-stack"></a>操作在巡覽堆疊

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation)示範數個函式所定義`INavigation`，可讓您操作巡覽堆疊中的結構化的方式：

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) 並[ `PopToRootAsync` ](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean))選擇性的動畫

### <a name="dynamic-page-generation"></a>產生動態頁面

[ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage)範例示範如何建構在執行階段根據使用者輸入的頁面。

## <a name="patterns-of-data-transfer"></a>資料傳輸的模式

它通常是為了頁面之間共用資料&mdash;頁面叫用它來傳回資料頁面巡覽的頁面上，來回傳輸資料。 有數種方法來執行此動作。

### <a name="constructor-arguments"></a>建構函式引數

當瀏覽至新的頁面，就可以具現化網頁類別，建構函式引數，可讓頁面以初始化其本身。 [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents)為範例。 此外，也可以巡覽頁面有其`BindingContext`頁面瀏覽至它所設定。

### <a name="properties-and-method-calls"></a>屬性和方法呼叫

其餘的資料傳輸範例中，瀏覽之間單頁瀏覽至另一個頁面的頁面之間的來回傳遞資訊的問題。 在這些討論中，*家用*頁面會巡覽至*info*頁面，然後必須初始化的資訊傳輸到*資訊*頁面。 *Info*頁面會從使用者取得其他資訊，以及傳輸的資訊*家用*頁面。

*家用*中公用方法和屬性，可以輕鬆地存取頁面*資訊*頁面，它會具現化該頁面。 *Info*中公用方法和屬性，也可以存取頁面*家用* 頁面上，但選擇的好時機，這可以麻煩。 [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1)範例會執行其`OnDisappearing`覆寫。 其中一個缺點是， *info*網頁必須知道的型別*家用*頁面。

### <a name="messagingcenter"></a>MessagingCenter

Xamarin.Forms [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)類別提供兩個頁面與彼此進行通訊的另一種方法。 訊息文字字串所識別，並伴隨的任何物件。

訂閱這些事件的程式，想要接收特定類型的訊息必須使用[ `MessagingCenter.Subscribe` ](xref:Xamarin.Forms.MessagingCenter.Subscribe*)和指定的回呼函式。 稍後可以藉由呼叫中取消[ `MessagingCenter.Unsubscribe` ](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)。 回呼函式會接收從指定的型別傳送具有指定的名稱，透過傳送任何訊息[ `Send` ](xref:Xamarin.Forms.MessagingCenter.Send*)方法。

[ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2)程式會示範如何使用訊息中心，資料傳輸，但需要再次*資訊*頁面知道類型*家用*頁面。

### <a name="events"></a>事件

事件是由來已久的方法，將資訊傳送至另一個類別，而不需要知道該類別類型的一個類別。 在  [ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3)範例*資訊*類別會定義它時所引發事件的資訊已就緒。 不過，沒有方便的地方*家用*中斷連結事件處理常式的頁面。

### <a name="the-app-class-intermediary"></a>應用程式類別媒介

[ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4)範例示範如何存取中定義的屬性`App`類別同時*家用*頁面和*資訊*頁面。 這是很好的解決方案，但是下一節描述更佳的項目。

### <a name="switching-to-a-viewmodel"></a>切換到 ViewModel

資訊可讓使用 ViewModel*家用*頁面並*資訊*頁面，即可共用資訊類別的執行個體。 這示範於[ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5)範例。

### <a name="saving-and-restoring-page-state"></a>儲存和還原的頁面狀態

`App`類別的媒介或 ViewModel 方法非常適合用來應用程式必須將儲存的資訊，如果程式進入睡眠狀態時*資訊*頁面是使用中。 [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6)為範例。

## <a name="saving-and-restoring-the-navigation-stack"></a>儲存和還原在巡覽堆疊

在一般情況下進入睡眠狀態的多頁程式應該瀏覽至相同的頁面還原時，會。 這表示這類程式應該儲存在巡覽堆疊的內容。 本節說明如何自動執行此程序的類別，針對此用途而設計。 這個類別也會呼叫個別的頁面，以允許他們儲存和還原其頁面狀態。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫會定義名為的介面[ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs)類別可以實作儲存和還原中的項目`Properties`字典。

[ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs)類別**Xamarin.FormsBook.Toolkit**文件庫衍生自`Application`。 您可以接著衍生您`App`類別從`MultiPageRestorableApp`並執行一些內部管理。

[ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo)示範如何使用`MultiPageRestorableApp`。

### <a name="something-like-a-real-life-app"></a>類似的實際應用程式

[**便箋**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker)範例也會利用`MultiPageRestorableApp`，並允許輸入和編輯儲存在備忘稿`Properties`字典。



## <a name="related-links"></a>相關連結

- [第 24 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [第 24 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)
