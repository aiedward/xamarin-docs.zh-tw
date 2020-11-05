---
title: 第24章摘要。 頁面瀏覽
description: 建立 Mobile Apps，包含 Xamarin.Forms ：第24章的摘要。 頁面瀏覽
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: DDCDB49C-6008-4F72-B095-463EE21D7C23
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4ab09546bb2a1dcbc221f3819bc891ce096ce569
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373961"
---
# <a name="summary-of-chapter-24-page-navigation"></a>第24章摘要。 頁面瀏覽

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

許多應用程式都是由使用者流覽的多個頁面所組成。 應用程式一律會有 *主* 頁面或 *首頁* ，而使用者會流覽至其他頁面，這些頁面會在堆疊中維護，以供流覽回來。 第25章涵蓋其他導覽選項 [**。頁面種類**](chapter25.md)。

## <a name="modal-pages-and-modeless-pages"></a>強制回應頁面和非模式頁面

`VisualElement` 定義 [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) 類型的屬性 [`INavigation`](xref:Xamarin.Forms.INavigation) ，其中包含下列兩種流覽至新頁面的方法：

- [ `PushAsync` ] (x： Xamarin.Forms 。INavigation. PushAsync (Xamarin.Forms 。頁面) # A3
- [ `PushModalAsync` ] (x： Xamarin.Forms 。INavigation. PushModalAsync (Xamarin.Forms 。頁面) # A3

這兩種方法都會接受 `Page` 實例作為引數，並傳回 `Task` 物件。 下列兩個方法會流覽回前一頁：

- [`PopAsync`](xref:Xamarin.Forms.INavigation.PopAsync)
- [`PopModalAsync`](xref:Xamarin.Forms.INavigation.PopModalAsync)

如果使用者介面有自己的 [ **上一頁** ] 按鈕 (Android 和 Windows phone) 那麼應用程式就不需要呼叫這些方法。

雖然這些方法都可以從任何方法取得 `VisualElement` ，但是通常會從 `Navigation` 目前實例的屬性呼叫這些方法 `Page` 。

應用程式通常會使用強制回應頁面，而使用者必須先在頁面上提供某些資訊，然後再返回上一頁。 非強制回應的頁面有時稱為非模式或 *階層* 式。 頁面本身的任何內容都會將它區分為強制回應或非強制回應;它是由用來流覽至它的方法進行控管。 若要在所有平臺上運作，強制回應頁面必須提供自己的使用者介面，以供流覽回上一頁。

[**ModelessAndModal**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModelessAndModal)範例可讓您探索無模式和強制回應頁面之間的差異。 使用頁面流覽的任何應用程式都必須將其首頁傳遞至函式 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，通常是在程式的 `App` 類別中。 其中一個好處是您不再需要 `Padding` 在 iOS 的頁面上設定。

您將會發現，針對非模式頁面，會 [`Title`](xref:Xamarin.Forms.Page.Title) 顯示頁面的屬性。 IOS、Android 和 Windows 平板電腦和桌面平臺全都提供使用者介面元素，以流覽回上一頁。 當然，Android 和 Windows phone 裝置有標準的 [ **上一頁** ] 按鈕可返回。

若為強制回應頁面， `Title` 就不會顯示頁面，而且不會提供任何使用者介面元素來返回上一頁。 雖然您可以使用 Android 和 Windows phone 的 [ **上一頁** ] 按鈕返回上一頁，但其他平臺上的強制回應頁面必須提供它自己的機制，才能返回。

### <a name="animated-page-transitions"></a>動畫頁面轉換

`true`如果您想要讓頁面轉換包含動畫，則會以您設定的第二個布林值引數，提供各種不同流覽方法的替代版本：

- [PushAsync] (x： Xamarin.Forms 。INavigation. PushAsync (Xamarin.Forms 。Page、System.object) # A3
- [PushModalAsync] (x： Xamarin.Forms 。INavigation. PushModalAsync (Xamarin.Forms 。Page、System.object) # A3
- [PopAsync](xref:Xamarin.Forms.INavigation.PopAsync(System.Boolean))
- [PopModalAsync](xref:Xamarin.Forms.INavigation.PopModalAsync(System.Boolean))

不過，根據預設，標準頁面導覽方法會包含動畫，因此只有在啟動 (的特定頁面時，才有價值，如本章節結尾所述) 或提供您自己的進入動畫 (如 Chapter22 中所述 [**。動畫**](chapter22.md)) 。

### <a name="visual-and-functional-variations"></a>視覺效果和功能變化

`NavigationPage` 包含可在您的方法中具現化類別時設定的兩個屬性 `App` ：

- [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)
- [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor)

`NavigationPage` 也包含四個附加的可系結屬性，這些屬性會影響所設定的特定頁面：

- [ `SetHasBackButton` ] (x： Xamarin.Forms 。NavigationPage. SetHasBackButton (Xamarin.Forms 。Page、System.object) # A3 和 [ `GetHasBackButton` ] (x： Xamarin.Forms 。NavigationPage. GetHasBackButton (Xamarin.Forms 。頁面) # A7
- [ `SetHasNavigationBar` ] (x： Xamarin.Forms 。NavigationPage. SetHasNavigationBar (Xamarin.Forms 。BindableObject、System.object) # A3 和 [ `GetHasNavigationBar` ] (x： Xamarin.Forms 。NavigationPage. GetHasNavigationBar (Xamarin.Forms 。BindableObject) # A7
- [ `SetBackButtonTitle` ] (x： Xamarin.Forms 。NavigationPage. SetBackButtonTitle (Xamarin.Forms 。BindableObject，System.string) # A3 和 [ `GetBackButtonTitle` ] (x： Xamarin.Forms 。NavigationPage. GetBackButtonTitle (Xamarin.Forms 。BindableObject) # A7 僅適用于 iOS
- [ `SetTitleIcon` ] (x： Xamarin.Forms 。NavigationPage. SetTitleIcon (Xamarin.Forms 。BindableObject， Xamarin.Forms 。FileImageSource) # A3 和 [ `GetTitleIcon` ] (x： Xamarin.Forms 。NavigationPage. GetTitleIcon (Xamarin.Forms 。BindableObject) # A7 僅適用于 iOS 和 Android

### <a name="exploring-the-mechanics"></a>探索機制

頁面流覽方法都是非同步，而且應該搭配使用 `await` 。 完成並不表示頁面導覽已完成，但只是可以安全地檢查頁面導覽堆疊。

當某個頁面流覽至另一個頁面時，第一個頁面通常會取得其方法的呼叫 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) ，而第二個頁面則會取得其方法的呼叫 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 。 同樣地，當某個頁面返回另一個頁面時，第一頁會取得其方法的呼叫 `OnDisappearing` ，而第二個頁面通常會取得其方法的呼叫 `OnAppearing` 。 這些呼叫的順序 (以及叫用流覽) 的非同步方法的完成順序，視平臺而定。 在上述兩個語句中使用 "一般而言" 一字是因為 Android 強制回應頁面導覽，在此頁面中不會發生這些方法呼叫。

此外，呼叫 `OnAppearing` 和 `OnDisappearing` 方法不一定表示頁面流覽。

此 `INavigation` 介面包含兩個集合屬性，可讓您檢查流覽堆疊：

- [`NavigationStack`](xref:Xamarin.Forms.INavigation.NavigationStack)非模式堆疊的型別 `IReadOnlyList<Page>`
- [`ModalStack`](xref:Xamarin.Forms.INavigation.ModalStack)模式堆疊的型別 `IReadOnlyList<Page>`

從 `Navigation` (的屬性 `NavigationPage` （應該是 `App` 類別的 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 屬性) ）存取這些堆疊是最安全的做法。 在完成非同步頁面導覽方法之後，才可以安全地檢查這些堆疊。 [`CurrentPage`](xref:Xamarin.Forms.NavigationPage.CurrentPage) `NavigationPage` 如果目前的頁面是強制回應頁面，但改為指出最後一個非強制回應頁面，則的屬性不會指出目前的頁面。

[**SinglePageNavigation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SinglePageNavigation)範例可讓您探索頁面流覽和堆疊，以及網頁流覽的合法類型：

- 非強制回應頁面可以流覽至另一個非模式頁面或強制回應頁面
- 強制回應頁面只能流覽至另一個強制回應頁面

### <a name="enforcing-modality"></a>強制執行樣式

當需要從使用者取得某些資訊時，應用程式會使用強制回應頁面。 在提供該資訊之前，應該禁止使用者返回上一頁。 在 iOS 上，您可以輕鬆地提供 [ **上一頁** ] 按鈕，並只在使用者完成頁面時才啟用。 但是針對 Android 和 Windows phone 裝置，應用程式應該覆寫 [`OnBackButtonPressed`](xref:Xamarin.Forms.Page.OnBackButtonPressed) 方法，並在 `true` 程式已處理 [ **上一頁** ] 按鈕本身的情況下傳回（如 [**ModalEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ModalEnforcement) 範例所示）。

[**MvvmEnforcement**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/MvvmEnforcement)範例會示範如何在 MVVM 案例中運作。

## <a name="navigation-variations"></a>導覽變化

如果特定的強制回應頁面可以導覽到多次，則應該保留資訊，讓使用者可以編輯資訊，而不是再次輸入資訊。 您可以藉由保留強制回應頁面的特定實例來處理此情況，但更好的方法 (特別是在 iOS) 將資訊保留在視圖模型中。

### <a name="making-a-navigation-menu"></a>製作導覽功能表

[**ViewGalleryType**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryType)範例會示範如何使用 `TableView` 來列出功能表項目。 每個專案都與 `Type` 特定頁面的物件相關聯。 選取該專案時，程式會將頁面具現化，並流覽至該專案。

[![視圖庫類型的三重螢幕擷取畫面](images/ch24fg21-small.png "TableView 清單功能表項目")](images/ch24fg21-large.png#lightbox "TableView 清單功能表項目")

[**ViewGalleryInst**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/ViewGalleryInst)範例的方式稍有不同，因為功能表包含每個頁面的實例，而不是類型。 這有助於保留每一頁的資訊，但所有的頁面都必須在程式啟動時具現化。

### <a name="manipulating-the-navigation-stack"></a>操作導覽堆疊

[**StackManipulation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackManipulation) 示範數個由定義的函式 `INavigation` ，可讓您以結構化的方式操作流覽堆疊：

- [ `RemovePage` ] (x： Xamarin.Forms 。INavigation. RemovePage (Xamarin.Forms 。頁面) # A3
- [ `InsertPageBefore` ] (x： Xamarin.Forms 。INavigation. InsertPageBefore (Xamarin.Forms 。頁面 Xamarin.Forms 。頁面) # A3
- [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync) 和 [`PopToRootAsync`](xref:Xamarin.Forms.INavigation.PopToRootAsync(System.Boolean)) 選擇性動畫

### <a name="dynamic-page-generation"></a>動態頁面產生

[**BuildAPage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/BuildAPage)範例會示範如何在執行時間根據使用者輸入來建立頁面。

## <a name="patterns-of-data-transfer"></a>資料傳輸模式

通常需要在頁面之間共用資料 &mdash; ，以將資料傳送至流覽的頁面，以及讓頁面將資料傳回給叫用該頁面的頁面。 有幾種方法可以執行這項操作。

### <a name="constructor-arguments"></a>函式引數

流覽至新的頁面時，可以使用可讓頁面初始化本身的函式引數，來具現化頁面類別。 [**SchoolAndStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/SchoolAndStudents)範例會示範這一點。 導覽的頁面也有可能 `BindingContext` 由流覽至該頁面的頁面設定。

### <a name="properties-and-method-calls"></a>屬性和方法呼叫

其餘的資料傳輸範例將探索當某個頁面流覽至另一個頁面時，在頁面之間傳遞資訊的問題。 在這些討論中， *首頁* 會流覽至 [ *資訊* ] 頁面，而且必須將初始化的資訊傳送至 [ *資訊* ] 頁面。 *資訊* 頁面會取得使用者的其他資訊，並將資訊傳送至 *首頁* 。

當它具現化該頁面時， *首頁* 可以輕鬆地存取 *資訊* 頁面中的公用方法和屬性。 [ *資訊* ] 頁面也可以存取 [ *首頁* ] 中的公用方法和屬性，但為此選擇適當的時間可能有點棘手。 [**DateTransfer1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer1)範例會在其覆寫中執行這項工作 `OnDisappearing` 。 其中一個缺點是 *資訊* 頁面必須知道 *首頁* 的類型。

### <a name="messagingcenter"></a>MessagingCenter

Xamarin.Forms [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) 類別提供另一種方式，讓兩頁彼此通訊。 訊息是以文字字串來識別，並且可以伴隨任何物件。

想要從特定類型接收訊息的程式必須使用來訂閱這些訊息 [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) ，並指定回呼函數。 稍後它可以藉由呼叫來取消訂閱 [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) 。 回呼函式會使用透過方法傳送的指定名稱，接收從指定類型傳送的任何訊息 [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) 。

[**DateTransfer2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer2)程式示範如何使用訊息中心來傳送資料，但同樣地， *資訊* 頁面也需要知道 *首頁* 的類型。

### <a name="events"></a>事件

事件是一種接受時間的方法，可讓某個類別將資訊傳送至另一個類別，而不需要知道該類別的型別。 在 [**DateTransfer3**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer3) 範例中， *info* 類別會定義資訊就緒時所引發的事件。 但是， *首頁* 沒有任何便利的地方可以卸離事件處理常式。

### <a name="the-app-class-intermediary"></a>應用程式類別仲介

[**DateTransfer4**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer4)範例會示範如何透過首頁 `App` 和 *資訊* 頁面，存取在類別中 *home* 定義的屬性。 這是不錯的解決方案，但下一節將說明更好的方法。

### <a name="switching-to-a-viewmodel"></a>切換至 ViewModel

使用資訊的 ViewModel 可 *讓首頁和**資訊頁面共用* 資訊類別的實例。 [**DateTransfer5**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer5)範例會示範這一點。

### <a name="saving-and-restoring-page-state"></a>儲存和還原頁面狀態

當應用程式在 [ `App` *資訊* ] 頁面處於作用中狀態時，如果程式進入睡眠狀態，則類別媒介或 ViewModel 方法是理想的選擇。 [**DateTransfer6**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/DataTransfer6)範例會示範這一點。

## <a name="saving-and-restoring-the-navigation-stack"></a>儲存和還原導覽堆疊

在一般情況下，進入睡眠狀態的多頁程式應該會在還原時流覽至相同的頁面。 這表示這類程式應儲存導覽堆疊的內容。 本節說明如何在基於此目的而設計的類別中將此程式自動化。 此類別也會呼叫個別頁面，以允許它們儲存和還原其頁面狀態。

[**Xamarin.Forms Book**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) library 程式庫會定義名為 [`IPersistantPage`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IPersistentPage.cs) 的介面，該介面可讓類別用來儲存和還原字典中的專案 `Properties` 。

[`MultiPageRestorableApp`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MultiPageRestorableApp.cs) **Xamarin.Forms 書籍** 庫中的類別衍生自 `Application` 。 然後，您可以 `App` 從衍生類別 `MultiPageRestorableApp` ，並執行一些進行中的維護。

[**StackRestoreDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/StackRestoreDemo)示範使用 `MultiPageRestorableApp` 。

### <a name="something-like-a-real-life-app"></a>像是真實生活的應用程式

[**NoteTaker**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24/NoteTaker)範例也會使用 `MultiPageRestorableApp` ，並允許輸入和編輯儲存在字典中的附注 `Properties` 。

## <a name="related-links"></a>相關連結

- [第24章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch24-Apr2016.pdf)
- [第24章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter24)
- [階層式導覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)
