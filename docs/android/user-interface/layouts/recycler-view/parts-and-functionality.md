---
title: RecyclerView 組件和功能
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 44f042359c9363f8ec3008ee49d2f6a874983e12
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="recyclerview-parts-and-functionality"></a>RecyclerView 組件和功能


`RecyclerView` 部分工作內部 （如捲動和回收處理檢視），但它的控制代碼為基本上管理員協調來顯示集合的 helper 類別。 `RecyclerView` 委派下列的協助程式類別的工作：

-   **`Adapter`** &ndash; 「 充氣 」 項目配置 （具現化配置檔案的內容） 和資料繫結至檢視內顯示`RecyclerView`。 配接器也會報告項目按一下事件。

-   **`LayoutManager`** &ndash; 量值，並置於內的項目檢視`RecyclerView`及管理檢視循環使用原則。

-   **`ViewHolder`** &ndash; 查閱並儲存檢視的參考。 檢視擁有者也有助於偵測項目檢視按一下。

-   **`ItemDecoration`** &ndash; 可讓應用程式的特定檢視的繪製項目，會反白顯示和視覺化群組界限之間的分隔線加入特殊繪圖和配置的位移。

-   **`ItemAnimator`** &ndash; 定義項目動作期間進行，或變更時所建立的配接器的動畫。

之間的關聯性`RecyclerView`， `LayoutManager`，和`Adapter`類別會在下圖說明：

![若要存取資料集使用配接器，在含有 LayoutManager RecyclerView 的圖表](parts-and-functionality-images/01-recyclerview-diagram.png)

如本圖所示，`LayoutManager`可以視為之間的媒介`Adapter`和`RecyclerView`。 `LayoutManager`呼叫`Adapter`方法代表`RecyclerView`。 例如，`LayoutManager`呼叫`Adapter`方法來建立新檢視中的特定項目位置的時候`RecyclerView`。 `Adapter`擴大該項目的版面配置，並建立`ViewHolder`（未顯示） 的執行個體，該位置檢視的快取參考。 當`LayoutManager`呼叫`Adapter`若要將特定的項目繫結至資料集，`Adapter`找出該項目的的資料、 擷取資料集，並將它複製到相關聯的項目檢視。

當使用`RecyclerView`應用程式中建立下列類別的衍生型別是需要：

-   **`RecyclerView.Adapter`** &ndash; 提供從您的應用程式的資料集 （這是您應用程式特有的） 繫結至項目檢視內顯示`RecyclerView`。 配接器知道如何建立關聯中的每個項目檢視位置`RecyclerView`到資料來源中的特定位置。 此外，配接器處理每個個別項目 」 檢視中之內容的配置，並建立每個檢視的檢視持有者。 配接器也會報告項目檢視所偵測到的項目按一下事件。

-   **`RecyclerView.ViewHolder`** &ndash; 會快取項目配置檔中檢視的參考，這樣不會不必要地重複資源查閱。 檢視擁有者也排列項目按一下事件轉送至配接器，當使用者點選檢視持有人相關聯的項目 」 檢視。

-   **`RecyclerView.LayoutManager`** &ndash; 將項目內`RecyclerView`。 您可以使用數個預先定義的配置管理員的其中一個，或您可以實作您自己的自訂配置管理員。
    `RecyclerView` 委派配置原則以配置管理員，因此您可以插入在不同的版面配置管理員中，而不必大幅變更為您的應用程式。

此外，您可以選擇性地擴充下列類別，以變更的外觀與風格`RecyclerView`應用程式中：

-   **`RecyclerView.ItemDecoration`**
-   **`RecyclerView.ItemAnimator`**

如果不延伸`ItemDecoration`和`ItemAnimator`，`RecyclerView`會使用預設實作。 本指南不會說明如何建立自訂`ItemDecoration`和`ItemAnimator`類別; 如需有關這些類別的詳細資訊，請參閱[RecyclerView.ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html)和[RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).


<a name="recycling" />

### <a name="how-view-recycling-works"></a>檢視回收運作方式

`RecyclerView` 不會為資料來源中的每個項目配置的項目檢視。 相反地，配置在螢幕上的項目檢視的數目，它會重複使用捲動這些項目配置。 當檢視第一次捲動使時，它將經歷回收處理程序，如下圖所示：

[![圖表檢視回收的六個步驟的說明](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1.  當檢視使捲動，並不會再顯示時，它會變成*放棄檢視*。

2.  剪輯檢視放在集區內，而且會變成*回收檢視*。
    此集區是顯示相同的資料類型的檢視表的快取。

3.  若要顯示新的項目時，檢視是取自回收集區，以供重複使用。 因為此檢視必須重新繫結的配接器才會顯示，它就稱為*中途檢視*。

4.  已變更的檢視就會回收： 配接器找出下一個項目，要顯示的資料，並將此資料複製到這個項目的檢視。 從 [回收] 檢視相關聯的檢視持有者擷取這些檢視的參考。

5.  回收的檢視加入至清單中的項目`RecyclerView`，即將螢幕上移。

6.  回收的檢視螢幕上會捲動`RecyclerView`清單中下一個項目。 同時，另一個檢視使捲動，然後就會回收根據上述的步驟。

項目檢視重複使用，除了`RecyclerView`也會使用另一個效率最佳化： 檢視持有者。 A*檢視持有者*是簡單的快取檢視參考的類別。 配接器 「 充氣 」 項目配置的檔案，每次它也會建立對應的檢視擁有者。 檢視擁有者使用`FindViewById`擴大的項目配置檔案內取得檢視的參考。 這些參考用來檢視新資料載入，每次配置是可以回收，以顯示新的資料。
 


### <a name="the-layout-manager"></a>配置管理員

配置管理員會負責定位在項目`RecyclerView`顯示; 它會判斷簡報類型 （清單或方格）、 （是否項目會顯示垂直或水平） 的方向，以及應該顯示方向的項目（在正常的順序或以相反順序）。 配置管理員也會負責計算的大小和位置的每個項目**RecycleView**顯示。

配置管理員有額外的用途： 它會判斷何時回收不再向使用者顯示的項目檢視的原則。
因為配置管理員了解哪些檢視會顯示 （和並不是），則為決定當檢視可以回收的最佳位置。 若要回收的檢視，請配置管理員通常會呼叫配接器以回收檢視的內容取代為不同的資料，如先前所述[檢視回收的運作方式](#recycling)。

您可以擴充`RecyclerView.LayoutManager`建立自己版面配置管理員 中，或者您可以使用預先定義的配置管理員。 `RecyclerView` 提供下列預先定義的配置管理員：

-   **`LinearLayoutManager`** &ndash; 排列項目可以水平捲動的資料列或可垂直捲動的資料行中。

-   **`GridLayoutManager`** &ndash; 在方格中顯示項目。

-   **`StaggeredGridLayoutManager`** &ndash; 顯示項目在交錯的方格中，其中某些項目有不同的高度和寬度。

若要指定配置管理員，您所選擇的配置管理員具現化並將它傳遞給`SetLayoutManager`方法。 請注意，您*必須*指定配置管理員&ndash;`RecyclerView`不會選取預設的預先定義的配置管理員。

如需有關配置管理員的詳細資訊，請參閱[RecyclerView.LayoutManager 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html)。


### <a name="the-view-holder"></a>檢視持有者

檢視持有者是您針對快取檢視參考所定義的類別。 配接器會使用這些檢視的參考，將每個檢視繫結至其內容。 在每個項目`RecyclerView`具有相關聯的檢視擁有者執行個體來快取該項目的檢視的參考。 若要建立檢視的擁有者，請使用下列步驟來定義類別以包裝確實的檢視每個項目集：

1.  子類別`RecyclerView.ViewHolder`。
2.  實作，查閱，並將檢視參考的建構函式。
3.  實作屬性，配接器可以用來存取這些參考。

詳細的範例`ViewHolder`實作會以[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)。
如需有關`RecyclerView.ViewHolder`，請參閱[RecyclerView.ViewHolder 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。


### <a name="the-adapter"></a>配接器

大部分的"繁重工作 」 的`RecyclerView`整合程式碼發生在配接器。 `RecyclerView` 會要求您提供衍生自配接器`RecyclerView.Adapter`來存取資料來源，並填入每個項目，以從資料來源的內容。
因為資料來源特定的應用程式，您必須實作配接器功能以了解如何存取您的資料。 配接器會從資料來源擷取資訊，並將其載入至每個項目`RecyclerView`集合。

欞迶飹晱配接器如何將透過檢視持有者的資料來源中的內容對應到每個資料列項目內的個別檢視`RecyclerView`:

[![圖表說明 ViewHolders 來連接資料來源的配接器](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

配接器會載入每個`RecyclerView`與特定資料列項目的資料列。 資料列位置的*P*，比方說，配接器會找出相關聯的資料位置*P*內的資料來源和複製這項資料給資料列項目位置*P*中`RecyclerView`集合。
在上述的繪圖，例如，配接器使用檢視持有者查閱的參考，`ImageView`和`TextView`在該位置，因此不需要重複呼叫`FindViewById`這些檢視表，為使用者捲動集合和會重複使用的檢視。

當您實作配接器時，您必須覆寫下列`RecyclerView.Adapter`方法：

-   **`OnCreateViewHolder`** &ndash; 具現化項目配置檔案及 檢視擁有者。

-   **`OnBindViewHolder`** &ndash; 將位於指定位置的資料載入到其參考會儲存在指定的檢視擁有者的檢視。

-   **`ItemCount`** &ndash; 資料來源中傳回的項目數。

配置管理員呼叫這些方法，而它會定位項目內`RecyclerView`。 



### <a name="notifying-recyclerview-of-data-changes"></a>資料變更的通知 RecyclerView

`RecyclerView` 不會自動更新其顯示時其資料的內容來源的變更。配接器必須通知`RecyclerView`資料集變更時。 在許多方面，可以變更的資料集比方說，可以變更的項目內容，或可能會修改資料的整體結構。
`RecyclerView.Adapter` 提供許多您可以呼叫的方法，讓`RecyclerView`回應至資料變更，以最有效率的方式：

-  **`NotifyItemChanged`** &ndash; 指定位置處的項目已變更的訊號。

-  **`NotifyItemRangeChanged`** &ndash; 指定的範圍內的位置的項目已變更的訊號。

-  **`NotifyItemInserted`** &ndash; 指定的位置中的項目具有新插入的訊號。

-  **`NotifyItemRangeInserted`** &ndash; 指定的範圍內的位置的項目有新插入的訊號。

-  **`NotifyItemRemoved`** &ndash; 指定的位置中的項目已移除的訊號。

-  **`NotifyItemRangeRemoved`** &ndash; 指定的範圍內的位置的項目已移除的訊號。

-  **`NotifyDataSetChanged`** &ndash; 資料集已變更的訊號 （強制的完整更新）。

如果您知道如何您的資料集已變更的確切，您可以呼叫適當的方法來重新整理上述`RecyclerView`以最有效率的方式。 如果您不知道如何您的資料集已變更的確切，您可以呼叫`NotifyDataSetChanged`，這會遠低於有效率因為`RecyclerView`必須重新整理所有使用者都能看見的檢視。 如需有關這些方法的詳細資訊，請參閱[RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。

在下一個主題中，[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)，範例應用程式會實作以示範真實的程式碼範例的組件和以上所述的功能。


## <a name="related-links"></a>相關連結

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [基本的 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
