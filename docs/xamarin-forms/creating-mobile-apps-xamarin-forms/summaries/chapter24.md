---
title: 章 24 的摘要。 頁面巡覽
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 9d1a226a4532b745fddee28e943562fb51d34e65
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-24-page-navigation"></a>章 24 的摘要。 頁面巡覽

在這些使用者瀏覽的多個頁面包含許多應用程式。 應用程式一定有*主要*頁面或*家用* 頁面上，並從該處使用者巡覽至其他頁面，會在向後巡覽的堆疊中維護。 會說明其他導覽選項[**第 25 章。頁面上須**](chapter25.md)。

## <a name="modal-pages-and-modeless-pages"></a>強制回應頁面和非強制回應頁面

`VisualElement` 定義[ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/)型別的屬性[ `INavigation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INavigation/)，其中包含下列兩種方法，才能巡覽至新的頁面：

- [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/)
- [`PushModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync/p/Xamarin.Forms.Page/)

這兩種方法接受`Page`做為引數和傳回的執行個體`Task`物件。 回到上一頁，瀏覽下列兩種方法：

- [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopAsync()/)
- [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/)

如果使用者介面都有它自己**回**按鈕 （如執行 Android 和 Windows phone），則不需要呼叫這些方法的應用程式。

雖然這些方法可從任何`VisualElement`，通常從呼叫`Navigation`屬性的目前`Page`執行個體。

當使用者需要提供一些資訊頁面上的傳回前一頁之前，應用程式通常使用強制回應頁面。 不會強制回應的頁面有時也稱為非強制回應或*階層式*。 在頁面本身中為 nothing 區分為強制回應或非強制回應。它受到改為用來巡覽找到它的方法。 跨所有平台運作，強制回應頁面必須提供自己的使用者介面向後巡覽至前一個頁面。

[ **ModelessAndModal** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal)範例可讓您瀏覽非強制回應和強制回應頁面之間的差異。 使用網頁瀏覽任何應用程式必須傳遞至其首頁[ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/)建構函式，通常是在程式的`App`類別。 一個獎金是您不再需要設定`Padding`iOS 的頁面上。

您將會非強制回應頁面，頁面的發現[ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/)顯示屬性。 在 iOS、 Android 和 Windows 平板電腦和桌面平台所有提供的使用者介面項目，若要導覽回到前一個頁面。 課程、 Android 和 Windows phone 裝置有標準**回**返回 按鈕。

強制回應頁面，頁面`Title`未顯示，並沒有任何使用者介面項目提供回到前一個頁面。 雖然您可以使用 Android 和 Windows phone 標準**回**回到上一頁，強制回應的頁面上，在其他平台上必須提供自己的機制，以回到上一步 按鈕。

### <a name="animated-page-transitions"></a>動畫的畫面轉換

不同的巡覽方法的替代版本所提供的第二個布林引數，您將設定為`true`如果您想要包含動畫網頁切換：

- [PushAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushAsync/p/Xamarin.Forms.Page/System.Boolean/)
- [PushModalAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync/p/Xamarin.Forms.Page/System.Boolean/)
- [PopAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopAsync/p/System.Boolean/)
- [PopModalAsync](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync/p/System.Boolean/)

不過，標準的頁面巡覽方法包含動畫根據預設，或讓這些只是重要的 （如結束時的這個章節討論），瀏覽至特定的頁面上，在啟動時 （如所述，提供您自己進入動畫[ **Chapter22。動畫**](chapter22.md))。

### <a name="visual-and-functional-variations"></a>視覺及功能的變化

`NavigationPage` 包含兩個屬性，當您具現化類別中的，您可以設定您`App`方法：

- [`BarBackgroundColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/)
- [`BarTextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarTextColor/)

`NavigationPage` 也包含會影響特定設定所在的頁面上的四個附加可繫結屬性：

- [`SetHasBackButton`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetHasBackButton/p/Xamarin.Forms.Page/System.Boolean/) 和 [`GetHasBackButton`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetHasBackButton/p/Xamarin.Forms.Page/)
- [`SetHasNavigationBar`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetHasNavigationBar/p/Xamarin.Forms.BindableObject/System.Boolean/) 和 [`GetHasNavigationBar`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetHasNavigationBar/p/Xamarin.Forms.BindableObject/)
- [`SetBackButtonTitle`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetBackButtonTitle/p/Xamarin.Forms.BindableObject/System.String/) 和[ `GetBackButtonTitle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetBackButtonTitle/p/Xamarin.Forms.BindableObject/)工作在僅限 iOS
- [`SetTitleIcon`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.SetTitleIcon/p/Xamarin.Forms.BindableObject/Xamarin.Forms.FileImageSource/) 和[ `GetTitleIcon` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.GetTitleIcon/p/Xamarin.Forms.BindableObject/)在 iOS 和 Android 只工作

### <a name="exploring-the-mechanics"></a>瀏覽機制

頁面巡覽方法都是完全非同步和應搭配`await`。 完成並不表示頁面瀏覽已完成，但僅，安全地檢查頁面巡覽堆疊。

當一個頁面巡覽到另一個時，第一頁通常取得呼叫其[ `OnDisappearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnDisappearing()/)方法，而且第二個頁面取得呼叫其[ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/)方法。 同樣地，當一頁傳回至另一個時，第一頁會取得呼叫其`OnDisappearing`方法，並在第二頁通常取得呼叫其`OnAppearing`方法。 這些呼叫 （與叫用的瀏覽完成的非同步方法） 的順序取決於平台。 使用兩個先前的陳述式中的 「 一般 」 這個字是因為 Android 強制回應頁面巡覽，這些方法呼叫不會發生。

此外，呼叫`OnAppearing`和`OnDisappearing`方法不一定表示頁面巡覽。

`INavigation`介面包含兩個集合屬性，可讓您檢查在巡覽堆疊：

- [`NavigationStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.NavigationStack/) 型別的`IReadOnlyList<Page>`非強制回應堆疊
- [`ModalStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) 型別的`IReadOnlyList<Page>`強制回應堆疊

方式是先存取從這些堆疊`Navigation`屬性`NavigationPage`(應該`App`類別的[ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/)屬性)。 它只可以安全地完成非同步的頁面巡覽方法之後，請檢查這些堆疊。 [ `CurrentPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.CurrentPage/)屬性`NavigationPage`不會指出目前的頁面是否目前的頁面是強制回應的頁面上，但表示改為強制回應的最後一頁。

[ **SinglePageNavigation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation)範例可讓您瀏覽頁面導覽和堆疊和頁面巡覽的合法的類型：

- 非強制回應頁面可以瀏覽至另一個非強制回應頁面或強制回應頁面
- 強制回應頁面只能夠巡覽到另一個強制回應頁面

### <a name="enforcing-modality"></a>強制執行樣式

需要向使用者取得一些資訊時，應用程式會使用強制回應頁面。 使用者應該被禁止返回前一個頁面，直到提供該資訊。 在 iOS 上，所以可以輕鬆地提供**回**按鈕，並在使用者與頁面完成時才加以啟用。 Android 和 Windows phone 裝置，應用程式應該覆寫，但[ `OnBackButtonPressed` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnBackButtonPressed()/)方法，並傳回`true`如果程式已經處理過**回**按鈕，如下所示[ **ModalEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement)範例。

[ **MvvmEnforcement** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement)範例會示範如何將 MVVM 案例中。

## <a name="navigation-variations"></a>瀏覽變化

如果特定的強制回應頁面可以導覽至多次，它應該會保留資訊，讓使用者可以編輯資訊，而不是輸入中一次。 您可以處理這藉由保留特定的執行個體的強制回應 頁面上，但更好的方法 （特別是在 iOS 上） 在保存的檢視模型中的資訊。

### <a name="making-a-navigation-menu"></a>建立導覽功能表

[ **ViewGalleryType** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType)範例將示範如何使用`TableView`列出功能表項目。 每個項目相關聯`Type`特定的頁面上的物件。 選取該項目時，程式會具現化 頁面，並瀏覽至它。

[![檢視組件庫類型的三個螢幕擷取畫面](images/ch24fg21-small.png "TableView 列出功能表項目")](images/ch24fg21-large.png#lightbox "TableView 列出功能表項目")

[ **ViewGalleryInst** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst)範例是稍有不同，該功能表包含的每個頁面，而不是類型執行個體。 這可協助保留資訊，從每個頁面上，但所有頁面必須在程式啟動時具現都化。

### <a name="manipulating-the-navigation-stack"></a>操作在巡覽堆疊

[**StackManipulation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation)示範幾個函式所定義`INavigation`，可讓您操作巡覽堆疊中的結構化的方式：

- [`RemovePage`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.RemovePage/p/Xamarin.Forms.Page/)
- [`InsertPageBefore`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.InsertPageBefore/p/Xamarin.Forms.Page/Xamarin.Forms.Page/)
- [`PopToRootAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopToRootAsync()/) 和[ `PopToRootAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopToRootAsync/p/System.Boolean/)選擇性動畫

### <a name="dynamic-page-generation"></a>產生動態頁面

[ **BuildAPage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage)範例將示範如何建構在執行階段根據使用者輸入的頁面。

## <a name="patterns-of-data-transfer"></a>資料傳輸的模式

通常會需要頁面之間共用資料&mdash;將資料傳送到巡覽的頁面上，以及頁面，即可將資料傳回給叫用它的網頁。 有數種技巧可執行此動作。

### <a name="constructor-arguments"></a>建構函式引數

當巡覽至新頁面，便可具現化網頁類別，可讓頁面以自行初始化，建構函式引數。 [ **SchoolAndStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents)範例為其示範。 您也可用於巡覽頁面其`BindingContext`頁面瀏覽至它所設定。

### <a name="properties-and-method-calls"></a>屬性和方法呼叫

其餘的資料傳輸範例中，瀏覽之間來回單頁瀏覽至另一個頁面的頁面中傳遞資訊的問題。 在這些討論中，*家用*頁面巡覽至*資訊*頁面上，而且必須初始化的資訊轉移至*資訊*頁面。 *資訊*頁面會從使用者取得其他資訊，並會傳送資訊至*家用*頁面。

*家用*中公用方法和屬性，可以輕易地存取頁面*資訊*頁面上，因為它會具現化該頁面。 *資訊*中公用方法和屬性，也可以存取頁面*家用* 頁面上，但是這可以很難解釋選擇的好時機。 [ **DateTransfer1** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1)範例會執行其`OnDisappearing`覆寫。 一個缺點是，*資訊*頁面需要知道的型別*家用*頁面。

### <a name="messagingcenter"></a>MessagingCenter

Xamarin.Forms [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)類別會提供另一種方法來與對方進行通訊的兩個頁面。 訊息所識別的文字字串，而且可以伴隨著任何物件。

想要接收訊息，從特定類型的程式必須訂閱使用[ `MessagingCenter.Subscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Subscribe%7BTSender,TArgs%7D/p/System.Object/System.String/System.Action%7BTSender,TArgs%7D/TSender/)並指定回呼函式。 稍後就可以取消訂閱，藉由呼叫[ `MessagingCenter.Unsubscribe` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Unsubscribe%7BTSender,TArgs%7D/p/System.Object/System.String/)。 回呼函式會接收從指定的型別傳送具有指定的名稱，透過傳送任何訊息[ `Send` ](https://developer.xamarin.com/api/member/Xamarin.Forms.MessagingCenter.Send%7BTSender,TArgs%7D/p/TSender/System.String/TArgs/)方法。

[ **DateTransfer2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2)程式會示範如何使用訊息中心，資料傳輸，但需要再次*資訊*頁面知道類型*家用*頁面。

### <a name="events"></a>事件

此事件為一個類別，將資訊傳送至另一個類別，而不需要知道該類別類型的傳統方法。 在[ **DateTransfer3** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3)範例*資訊*類別會定義資訊就緒時引發的事件。 不過，沒有任何方便的位置，如*家用*頁面，即可中斷連結事件處理常式。

### <a name="the-app-class-intermediary"></a>應用程式類別媒介

[ **DateTransfer4** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4)範例示範如何存取中定義的屬性`App`類別兩者*家用*頁面和*資訊*頁面。 這是很好的解決方案，但是下一節描述更佳的項目。

### <a name="switching-to-a-viewmodel"></a>切換到 ViewModel

使用此資訊可讓 ViewModel*家用*頁面和*資訊*頁面，即可共用資訊類別的執行個體。 這示範於[ **DateTransfer5** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5)範例。

### <a name="saving-and-restoring-page-state"></a>儲存和還原的頁面狀態

`App`類別中繼 」 或 「 ViewModel 方法時，理想的應用程式必須儲存資訊，如果程式進入睡眠時*資訊*頁面為作用中。 [ **DateTransfer6** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6)範例為其示範。

## <a name="saving-and-restoring-the-navigation-stack"></a>儲存和還原在巡覽堆疊

在一般情況下，進入睡眠的多頁程式應該瀏覽至相同的頁面還原時。 這表示此種程式應該儲存在巡覽堆疊的內容。 本節說明如何針對此用途所設計的類別中的此程序自動化。 這個類別也會呼叫個別的頁面，以允許他們儲存和還原的頁面狀態。

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫會定義名為介面[ `IPersistantPage` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs)類別可以實作以儲存及還原中的項目`Properties`字典。

[ `MultiPageRestorableApp` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs)類別**Xamarin.FormsBook.Toolkit**程式庫是衍生自`Application`。 您可以再衍生您`App`類別從`MultiPageRestorableApp`並執行某些維護。

[ **StackRestoreDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo)示範如何使用`MultiPageRestorableApp`。

### <a name="something-like-a-real-life-app"></a>像是現實生活應用程式

[**便箋**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker)範例也利用`MultiPageRestorableApp`並允許用於輸入及編輯儲存在備忘稿`Properties`字典。



## <a name="related-links"></a>相關連結

- [章 24 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [章 24 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)
