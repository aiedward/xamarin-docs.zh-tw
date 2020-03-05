---
title: 第24章的摘要。 頁面巡覽
description: 使用 Xamarin 建立 Mobile Apps：第24章的摘要。 頁面巡覽
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: fd8e4fc77917fcba9bc61e59ced714ac1cd6fbe9
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "78292051"
---
# <a name="summary-of-chapter-24-page-navigation"></a>第24章的摘要。 頁面巡覽

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

許多應用程式是由使用者流覽的多個頁面所組成。 應用程式一律會有*主*頁面或*首頁*，而使用者會在該處流覽至其他頁面，並在堆疊中維護以供流覽。 第25章會涵蓋其他導覽選項[ **。頁面的種類**](chapter25.md)。

## <a name="modal-pages-and-modeless-pages"></a>強制回應頁面和非模式頁面

`VisualElement` 會定義[`INavigation`](xref:Xamarin.Forms.INavigation)類型的[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)屬性，其中包含下列兩個流覽至新頁面的方法：

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

這兩種方法都接受 `Page` 實例做為引數，並傳回 `Task` 物件。 下列兩個方法會流覽回到上一頁：

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

如果使用者介面有自己的 [**上一頁**] 按鈕（如同 Android 和 Windows phone），則應用程式不需要呼叫這些方法。

雖然這些方法可從任何 `VisualElement`取得，但通常是從目前 `Page` 實例的 `Navigation` 屬性呼叫。

當使用者需要在頁面上提供某些資訊，然後再回到上一頁之前，應用程式通常會使用強制回應頁面。 不是強制回應的頁面有時稱為非模式或*階層*式。 頁面本身不會將它區別為強制回應或非模式;它會受到控管，而不是用來流覽至它的方法。 若要在所有平臺上工作，強制回應頁面必須提供自己的使用者介面，以供流覽回到前一頁。

[**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal)範例可讓您探索無模式和強制回應頁面之間的差異。 任何使用頁面導覽的應用程式都必須將其首頁傳遞至[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)的程式，通常是在程式的 `App` 類別中。 其中一個優點是您不再需要在 iOS 的頁面上設定 `Padding`。

您會發現，針對非模式頁面，會顯示頁面的 [ [`Title`](xref:Xamarin.Forms.Page.Title) ] 屬性。 IOS、Android 和 Windows 平板電腦和桌面平臺全都提供使用者介面元素，以流覽回上一頁。 當然，Android 和 Windows phone 裝置都有標準的 [**上一頁**] 按鈕可返回。

若為強制回應頁面，則不會顯示頁面 `Title`，而且不會提供任何使用者介面專案來回到上一頁。 雖然您可以使用 [Android] 和 [Windows phone 標準**上一頁**] 按鈕返回上一頁，但其他平臺上的強制回應頁面必須提供自己的機制來返回。

### <a name="animated-page-transitions"></a>動畫頁面轉換

如果您想要讓頁面轉換包含動畫，則會以您設定的第二個布林值引數來提供各種導覽方法的替代版本 `true`：

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [PushModalAsync](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

不過，標準頁面導覽方法預設包含動畫，因此只有在啟動時流覽至特定頁面（如本章節結尾所述），或提供您自己的進入動畫時（如 Chapter22 中所述），才有價值[ **。動畫**](chapter22.md)）。

### <a name="visual-and-functional-variations"></a>視覺效果和功能變化

`NavigationPage` 包含兩個屬性，您可以在 `App` 方法中具現化類別時加以設定：

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` 也包含四個附加的可系結屬性，會影響其設定所在的特定頁面：

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean)) 和 [`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean)) 和 [`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String))和[`GetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject))僅適用于 iOS
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource))和[`GetTitleIcon`](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject))僅適用于 iOS 和 Android

### <a name="exploring-the-mechanics"></a>探索機制

頁面導覽方法全都是非同步，而且應該與 `await`搭配使用。 完成並不表示頁面導覽已完成，但只是可以安全地檢查頁面導覽堆疊。

當一頁流覽至另一個頁面時，第一個頁面通常會呼叫其[`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing)方法，而第二個頁面則會取得其[`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing)方法的呼叫。 同樣地，當某一頁傳回另一個頁面時，第一頁會取得其 `OnDisappearing` 方法的呼叫，而第二頁通常會取得其 `OnAppearing` 方法的呼叫。 這些呼叫的順序（以及叫用導覽的非同步方法完成）與平臺相依。 在上述兩個語句中使用「一般」一詞，是因為 Android 強制回應頁面導覽，在此情況下不會呼叫這些方法。

此外，對 `OnAppearing` 和 `OnDisappearing` 方法的呼叫不一定表示頁面導覽。

`INavigation` 介面包含兩個集合屬性，可讓您檢查流覽堆疊：

- 非模式堆疊的類型 `IReadOnlyList<Page>` [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack)
- 強制回應堆疊類型 `IReadOnlyList<Page>` 的[`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack)

從 `NavigationPage` 的 `Navigation` 屬性（應該是 `App` 類別的[`MainPage`](xref:Xamarin.Forms.Application.MainPage)屬性）存取這些堆疊，是最安全的做法。 完成非同步頁面流覽方法之後，才可以安全地檢查這些堆疊。 如果目前的頁面是強制回應頁面，則 `NavigationPage` 的[`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage)屬性不會指出目前的頁面，而是表示最後一個非模式頁面。

[**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation)範例可讓您探索頁面導覽和堆疊，以及頁面流覽的法律類型：

- 非模式頁面可以導覽至另一個非模式頁面或模式頁面
- 模式頁面只能流覽至另一個強制回應頁面

### <a name="enforcing-modality"></a>強制執行樣式

當應用程式需要向使用者取得某些資訊時，會使用強制回應頁面。 除非提供該資訊，否則應該禁止使用者回到前一頁。 在 iOS 上，您可以輕鬆地提供 [**上一頁**] 按鈕，並只在使用者完成頁面時才啟用。 但是對於 Android 和 Windows phone 裝置，應用程式應該覆寫[`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed)方法，並在程式已處理 [**上一頁**] 按鈕時傳回 `true` （如[**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement)範例中所示）。

[**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement)範例會示範其在 MVVM 案例中的運作方式。

## <a name="navigation-variations"></a>導覽變化

如果可以多次流覽特定的強制回應頁面，它應該會保留資訊，讓使用者可以編輯資訊，而不是再次輸入。 您可以保留特定的強制回應頁面實例來處理此情況，但更好的方法（尤其是在 iOS 上）會將資訊保留在視圖模型中。

### <a name="making-a-navigation-menu"></a>建立導覽功能表

[**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType)範例會示範如何使用 `TableView` 來列出功能表項目。 每個專案都會與特定頁面的 `Type` 物件相關聯。 選取該專案時，程式會具現化該頁面，並流覽至該網頁。

[![視圖庫類型的三重螢幕擷取畫面](images/ch24fg21-small.png "TableView 清單功能表項目")](images/ch24fg21-large.png#lightbox "TableView 清單功能表項目")

[**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst)範例稍有不同，因為功能表包含每個頁面的實例，而不是類型。 這有助於保留每個頁面中的資訊，但所有頁面都必須在程式啟動時具現化。

### <a name="manipulating-the-navigation-stack"></a>操作導覽堆疊

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation)會示範數個 `INavigation` 所定義的函式，可讓您以結構化的方式操作導覽堆疊：

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- 使用選擇性動畫[`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync)和[`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean))

### <a name="dynamic-page-generation"></a>動態頁面產生

[**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage)範例會示範如何在執行時間根據使用者輸入來建立頁面。

## <a name="patterns-of-data-transfer"></a>資料傳輸模式

通常必須在頁面之間共用資料，&mdash; 將資料傳送至流覽的頁面，並讓頁面將資料傳回給叫用它的頁面。 執行此作業的方法有好幾種。

### <a name="constructor-arguments"></a>函數引數

導覽至新的頁面時，可以使用可讓頁面初始化的函式引數，具現化頁面類別。 [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents)範例會示範這種情況。 導覽的頁面也可以讓流覽的頁面設定其 `BindingContext`。

### <a name="properties-and-method-calls"></a>屬性和方法呼叫

其餘的資料傳輸範例會探索當一個頁面流覽至另一個頁面並返回時，在頁面之間傳遞資訊的問題。 在這些討論中，*首頁*會流覽至 [*資訊*] 頁面，而且必須將已初始化的資訊傳送至 [*資訊*] 頁面。 [*資訊*] 頁面會從使用者取得其他資訊，並將資訊傳送*至首頁。*

當它具現化該頁面時 *，首頁可以*輕鬆地存取 [*資訊*] 頁面中的公用方法和屬性。 [*資訊*] 頁面也可以存取 [*首頁*] 中的公用方法和屬性，但選擇良好的時間可能會很棘手。 [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1)範例會在其 `OnDisappearing` 覆寫中執行此工作。 其中一個缺點是 [*資訊*] 頁面需要知道*首頁*的類型。

### <a name="messagingcenter"></a>MessagingCenter

Xamarin [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)類別提供另一種方式，讓兩個頁面彼此通訊。 訊息是以文字字串來識別，而且可以伴隨任何物件。

想要接收來自特定類型之訊息的程式，必須使用[`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*)來訂閱它們，並指定回呼函數。 稍後可以藉由呼叫[`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)來取消訂閱。 回呼函式會接收從指定的型別傳送的任何訊息，且指定的名稱會透過[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)方法傳送。

[**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2)程式會示範如何使用「訊息中心」來傳送資料，但同樣地，*資訊*頁面也需要知道*首頁*的類型。

### <a name="events"></a>事件

事件是一種接受時間的方法，可讓一個類別將資訊傳送至另一個類別，而不需要知道該類別的類型。 在[**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3)範例中， *info*類別會定義當資訊就緒時所引發的事件。 不過，*首頁*沒有方便的地方可以卸離事件處理常式。

### <a name="the-app-class-intermediary"></a>應用程式類別媒介

[**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4)範例會示範如何透過*首頁*和*資訊*頁面，存取在 `App` 類別中定義的屬性。 這是不錯的解決方案，但下一節會有更好的說明。

### <a name="switching-to-a-viewmodel"></a>切換至 ViewModel

使用資訊的 ViewModel 可讓 [*首頁*] 頁面和 [*資訊*] 頁面共用資訊類別的實例。 這會在[**DateTransfer5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5)範例中示範。

### <a name="saving-and-restoring-page-state"></a>儲存和還原頁面狀態

當應用程式在 [*資訊*] 頁面作用中時進入睡眠狀態時，`App` 類別媒介或 ViewModel 方法非常理想。 [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6)範例會示範這種情況。

## <a name="saving-and-restoring-the-navigation-stack"></a>儲存和還原導覽堆疊

在一般情況下，進入睡眠狀態的多頁程式應該在還原時流覽至相同的頁面。 這表示這類程式應該儲存流覽堆疊的內容。 本節說明如何在針對此目的而設計的類別中，自動執行此程式。 這個類別也會呼叫個別頁面，讓它們可以儲存和還原其頁面狀態。

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫會定義名為[`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs)的介面，類別可以在 `Properties` 字典中用來儲存和還原專案。

**FormsBook 工具**庫中的[`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs)類別衍生自 `Application`。 然後，您可以從 `MultiPageRestorableApp` 衍生 `App` 類別，並執行一些維護。

[**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo)示範 `MultiPageRestorableApp`的使用。

### <a name="something-like-a-real-life-app"></a>類似于實際應用程式

[**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker)範例也會使用 `MultiPageRestorableApp`，並允許輸入和編輯儲存在 `Properties` 字典中的附注。

## <a name="related-links"></a>相關連結

- [第24章的全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [第24章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)
