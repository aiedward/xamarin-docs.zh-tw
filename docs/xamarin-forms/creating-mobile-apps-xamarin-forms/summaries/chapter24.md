---
title: 第24章摘要。 頁面巡覽
description: 使用 Xamarin.表單創建行動應用程式:第 24 章摘要。 頁面巡覽
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: fd8e4fc77917fcba9bc61e59ced714ac1cd6fbe9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292051"
---
# <a name="summary-of-chapter-24-page-navigation"></a>第24章摘要。 頁面巡覽

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

許多應用程式由多個頁面組成,使用者在其中導航。 應用程式始終具有*主頁*或*主頁*,用戶從那裡導航到其他頁面,這些頁面保存在堆疊中以進行回導航。 第 25 章介紹了其他導航選項[**。頁面品種**](chapter25.md).

## <a name="modal-pages-and-modeless-pages"></a>樣式頁面與無模式頁面

`VisualElement`定義[`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation)[`INavigation`](xref:Xamarin.Forms.INavigation)類型 屬性,其中包括導航到新頁面的以下兩種方法:

- [`PushAsync`](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page))
- [`PushModalAsync`](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page))

兩種方法都接受`Page`實例作為參數並`Task`返回 物件。 以下兩種方法將導航回上一頁:

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

如果用戶介面有自己的 **「後退」** 按鈕(如 Android 和 Windows 手機),則應用程式無需調用這些方法。

儘管這些方法可從任何`VisualElement`中獲取,但它們通常`Navigation`從`Page`當前 實例的屬性調用。

應用程式通常使用模態頁時,使用者需要提供頁面上的一些資訊,然後再返回到上一頁。 不是模態的頁面有時稱為無模式或*分層*。 頁面本身沒有將其區分為模態或無模式;它由用於導航到它的方法控制。 要跨所有平臺工作,模式頁必須提供自己的用戶介面,以便導航回上一頁。

"[**無模式和模態"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal)範例允許您探索無模式頁面和模態頁面之間的區別。 使用頁面導航的任何應用程式都必須將其主頁傳遞給[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)構造函數,通常是在程式`App`的 類中。 一個好處是,您不再需要在 iOS`Padding`的頁面上設定 。

您會發現,對於無模式頁面,將顯示頁面的屬性[`Title`](xref:Xamarin.Forms.Page.Title)。 iOS、Android、Windows 平板電腦和桌面平臺都提供了使用者介面元素,可以導航回上一頁。 當然,Android 和 Windows 手機設備有一個標準的 **「後退**」按鈕。

對於模態頁,不`Title`顯示該頁,並且不提供用戶介面元素以返回上一頁。 儘管您可以使用 Android 和 Windows 手機標準 **「後退」** 按鈕返回到上一頁,但其他平臺上的模式頁面必須提供自己的返回機制。

### <a name="animated-page-transitions"></a>動畫頁面轉換

如果希望頁面過渡包含動畫,則提供各種導航方法的替代版本,並設置第`true`二個布爾參數:

- [PushAsync](xref:Xamarin.Forms.INavigation.PushAsync(Xamarin.Forms.Page,System.Boolean))
- [推送模式同步](xref:Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page,System.Boolean))
- [波普阿同步](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [流行模式同步](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

但是,標準頁面導航方法預設包含動畫,因此這些操作僅在啟動時導航到特定頁面(如本章末尾討論)或提供您自己的入口動畫時(如[**第 22 章所述)。"動畫**](chapter22.md)。

### <a name="visual-and-functional-variations"></a>視覺和功能變化

`NavigationPage`包括兩個屬性,您可以在`App`方法中實體化類別時設定:

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage`還包括四個附加的可綁定屬性,這些屬性會影響設置它們的特定頁面:

- [`SetHasBackButton`](xref:Xamarin.Forms.NavigationPage.SetHasBackButton(Xamarin.Forms.Page,System.Boolean))和[`GetHasBackButton`](xref:Xamarin.Forms.NavigationPage.GetHasBackButton(Xamarin.Forms.Page))
- [`SetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.SetHasNavigationBar(Xamarin.Forms.BindableObject,System.Boolean))和[`GetHasNavigationBar`](xref:Xamarin.Forms.NavigationPage.GetHasNavigationBar(Xamarin.Forms.BindableObject))
- [`SetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.SetBackButtonTitle(Xamarin.Forms.BindableObject,System.String))只在[`GetBackButtonTitle`](xref:Xamarin.Forms.NavigationPage.GetBackButtonTitle(Xamarin.Forms.BindableObject))iOS 工作
- [`SetTitleIcon`](xref:Xamarin.Forms.NavigationPage.SetTitleIcon(Xamarin.Forms.BindableObject,Xamarin.Forms.FileImageSource))僅在[`GetTitleIcon`](xref:Xamarin.Forms.NavigationPage.GetTitleIcon(Xamarin.Forms.BindableObject))iOS 和安卓上工作

### <a name="exploring-the-mechanics"></a>探索機制

頁面導航方法都是非同步的,應與一起使用`await`。 完成並不表示頁面導航已完成,而只表示檢查頁面導航堆疊是安全的。

當一個頁面導航到另一個頁面時,第一頁通常會收到對其[`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing)方法的調用,第二個頁面將調用[`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing)其 方法。 同樣,當一個頁面返回到另一個頁面時,第一頁將`OnDisappearing`調用其方法,第二頁通常收到對其`OnAppearing`方法的調用。 這些調用的順序(以及調用導航的非同步方法的完成)與平台相關。 在前面的兩個語句中使用"一般"一詞是由於 Android 模式頁導航,其中這些方法調用不會發生。

此外,對和`OnAppearing``OnDisappearing`的調用不一定指示頁面導航。

該`INavigation`介面包括兩個集合屬性,允許您檢查導航堆疊:

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack)無模式`IReadOnlyList<Page>`堆疊的類型
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack)`IReadOnlyList<Page>`樣式堆疊型態

從`Navigation``NavigationPage`(應`App`是[`MainPage`](xref:Xamarin.Forms.Application.MainPage)類的屬性) 的屬性訪問這些堆疊是最安全的。 只有在異步頁面導航方法完成後,才能安全地檢查這些堆疊。 如果[`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage)當前頁面`NavigationPage`是 模態頁,則 屬性不指示當前頁面,而是指示最後一個無模式頁面。

[**以 SinglePage 瀏覽**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation)範例,您可以瀏覽頁面導航和堆疊以及網頁瀏覽的合法類型:

- 無模式頁面可以瀏覽到另一個無模式頁面或模式頁
- 模式頁只能瀏覽到另一個模式頁

### <a name="enforcing-modality"></a>執行方式

當需要從用戶獲取一些資訊時,應用程式使用模式頁。 在提供該資訊之前,應禁止使用者返回上一頁。 在 iOS 上,很容易提供 **「後退」** 按鈕,並且僅在使用者完成頁面後啟用它。 但對於 Android 和 Windows 手機[`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed)設備,應用程式應`true`重寫該方法, 如果程式已處理 **「後退**」按鈕本身,則應用程式應返回,如[**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement)範例所示。

[**Mvvm強制範例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement)示範如何在MVVM方案中工作。

## <a name="navigation-variations"></a>導覽變體

如果特定模式頁可以多次導航到,則應保留資訊,以便使用者可以編輯資訊,而不是再次鍵入所有資訊。 您可以通過保留模式頁的特定實例來處理這種情況,但更好的方法(尤其是在 iOS 上)是在視圖模型中保留資訊。

### <a name="making-a-navigation-menu"></a>製作瀏覽選單

[**"ViewGalleryType"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType)範例示範`TableView`使用 使用 使用使用選單項目的 。 每個專案都與特定頁面`Type`的對象相關聯。 選中該專案后,程式將實例化頁面並導航到該頁面。

[![檢視庫類型的三重螢幕截圖](images/ch24fg21-small.png "表檢視清單選單項目")](images/ch24fg21-large.png#lightbox "表檢視清單選單項目")

[**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst)示例略有不同,因為功能表包含每個頁面的實例,而不是類型。 這有助於保留每個頁面中的資訊,但必須在程式啟動時實例化所有頁面。

### <a name="manipulating-the-navigation-stack"></a>動作堆疊

[**堆疊操作**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation)示範了幾個函`INavigation`數 ,這些函數允許您以結構化方式操作導航堆疊:

- [`RemovePage`](xref:Xamarin.Forms.INavigation.RemovePage(Xamarin.Forms.Page))
- [`InsertPageBefore`](xref:Xamarin.Forms.INavigation.InsertPageBefore(Xamarin.Forms.Page,Xamarin.Forms.Page))
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync)選擇[`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean))選動畫

### <a name="dynamic-page-generation"></a>動態頁面產生

[**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage)範例展示在執行時根據使用者輸入構造頁面。

## <a name="patterns-of-data-transfer"></a>資料傳輸模式

通常需要在頁面&mdash;之間共享數據以將數據傳輸到導航頁,以及頁面將數據返回到調用數據的頁面。 有幾種技術可以做到這一點。

### <a name="constructor-arguments"></a>建構函數參數

導航到新頁面時,可以使用允許頁面初始化自己的構造函數參數實例化頁面類。 [**學校和學生**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents)示例演示了這一點。 導航頁面也可以由導航到它的頁面`BindingContext`設置。

### <a name="properties-and-method-calls"></a>屬性與方法呼叫

其餘的數據傳輸示例探討了當一個頁面導航到另一個頁面並返回時在頁面之間傳遞資訊的問題。 在這些討論中,*主頁*導航到*資訊*頁,並且必須將初始化的資訊傳輸到*資訊*頁。 *資訊*頁從使用者處取得其他資訊,並將資訊傳輸到*主頁*。

*主頁*在實例化該頁面后,可以輕鬆地訪問*信息*頁中的公共方法和屬性。 *信息*頁還可以訪問*主頁*中的公共方法和屬性,但為此選擇好時機可能比較棘手。 [**DateTrans1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1)範例`OnDisappearing`在其重寫中執行此功能。 一個缺點是*信息*頁需要知道*主頁*的類型。

### <a name="messagingcenter"></a>MessagingCenter

Xamarin.Forms[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)類為兩個頁面提供了另一種相互通信的方式。 消息由文本字串標識,並可以隨附任何物件。

希望接收來自特定類型的消息的程式必須使用[`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*)並指定回調函數訂閱它們。 稍後,它可以通過調[`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*)用 取消訂閱。 回檔函數接收從指定類型發送的任何消息,指定名稱[`Send`](xref:Xamarin.Forms.MessagingCenter.Send*)通過 方法發送。

[**DateTrans2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2)程式展示如何使用消息傳遞中心傳輸數據,但同樣,這要求*資訊*頁知道*主頁*的類型。

### <a name="events"></a>事件

該事件是一個類在不知道該類的類型的情況下將資訊發送到另一個類的久經久不近的方法。 在[**DateTrans3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3)範例中,*資訊*類定義資訊準備就緒時觸發的事件。 但是,*主頁*沒有用於分離事件處理程式的方便位置。

### <a name="the-app-class-intermediary"></a>應用類仲介

[**DateTrans4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4)範例展示如何`App`存取 主頁*和資訊頁在*類中定義*home*的屬性。 這是一個很好的解決方案,但下一節將介紹更好的內容。

### <a name="switching-to-a-viewmodel"></a>切換到檢視模型

對資訊使用 ViewModel 允許*主頁**和資訊*頁共享資訊類的實例。 這在[**Date 傳輸5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5)示例中演示。

### <a name="saving-and-restoring-page-state"></a>儲存與復原頁面狀態

`App`當程式在*資訊*頁處於活動狀態時進入睡眠狀態時,應用程式必須保存資訊時,類仲介或 ViewModel 方法是理想的。 [**Date轉移6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6)示例演示了這一點。

## <a name="saving-and-restoring-the-navigation-stack"></a>儲存及修復導覽堆疊

在一般情況下,進入睡眠狀態的多頁程式在還原時應導航到同一頁。 這意味著此類程式應保存導航堆疊的內容。 本節演示如何在為此目的設計的類中自動執行此過程。 此類還會調用各個頁面以允許它們保存和恢復其頁面狀態。

[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫定義了一個名為[`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs)的 介面,類可以實現該介面來保存和還原`Properties`字典 中的項。

**Xamarin.FormsBook.Toolkit**庫`Application`[`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs)中的 類別的類別 。 然後,你可以從`App``MultiPageRestorableApp`中派生您的課程並執行一些內務。

[**堆疊還原演示**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo)了`MultiPageRestorableApp`的 使用方式。

### <a name="something-like-a-real-life-app"></a>類似真實應用的東西

[**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker)示例還`MultiPageRestorableApp`使用 並允許輸入和編輯保存在字`Properties`典中的註釋。

## <a name="related-links"></a>相關連結

- [第24章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [第24章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)
