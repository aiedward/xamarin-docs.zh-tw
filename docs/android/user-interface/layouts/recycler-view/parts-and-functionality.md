---
title: RecyclerView 元件和功能
description: RecyclerView 版面建構管理員、介面卡及檢視器的總覽。
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 8d987fc91544cfb87a2da6adba9f178931fee563
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567790"
---
# <a name="recyclerview-parts-and-functionality"></a>RecyclerView 元件和功能

`RecyclerView`在內部處理某些工作（例如，視圖的滾動和回收），但它基本上是協調 helper 類別以顯示集合的管理員。 `RecyclerView`將工作委派給下列 helper 類別：

- **`Adapter`**&ndash;擴大專案版面配置（具現化配置檔案的內容），並將資料系結至中顯示的視圖 `RecyclerView` 。 介面卡也會報告按專案的事件。

- **`LayoutManager`**&ndash;測量並定位中的專案查看 `RecyclerView` ，並管理用於回收的原則。

- **`ViewHolder`**&ndash;查閱並儲存 view 參考。 視圖持有者也有助於偵測專案-視圖的點擊。

- **`ItemDecoration`**&ndash;允許應用程式將特殊繪圖和版面配置位移新增至特定的視圖，以繪製專案、醒目提示和視覺效果群組邊界之間的分隔線。

- **`ItemAnimator`**&ndash;定義在專案動作期間或對介面卡進行變更時所發生的動畫。

`RecyclerView` `LayoutManager` 下圖描述、和類別之間的關聯性 `Adapter` ：

![包含 LayoutManager 的 RecyclerView 圖，使用介面卡來存取資料集](parts-and-functionality-images/01-recyclerview-diagram.png)

如本圖所示，可以將視為與 `LayoutManager` 之間的媒介 `Adapter` `RecyclerView` 。 `LayoutManager`會 `Adapter` 代表呼叫方法 `RecyclerView` 。 例如，當您在 `LayoutManager` `Adapter` 中建立特定專案位置的新 view 時，會呼叫方法 `RecyclerView` 。 會 `Adapter` 擴大該專案的版面配置，並建立 `ViewHolder` 實例（未顯示），以快取該位置之 views 的參考。 當 `LayoutManager` 呼叫來將 `Adapter` 特定專案系結至資料集時，會找出 `Adapter` 該專案的資料、從資料集抓取它，然後將它複製到相關聯的專案視圖。

`RecyclerView`在您的應用程式中使用時，需要建立下列類別的衍生類型：

- **`RecyclerView.Adapter`**&ndash;提供從您應用程式的資料集（即您的應用程式特定）到中顯示之專案視圖的系結 `RecyclerView` 。 介面卡知道如何將中的每個專案視圖位置與 `RecyclerView` 資料來源中的特定位置產生關聯。 此外，介面卡會處理每個個別專案視圖中內容的版面配置，並建立每個視圖的視圖持有者。 介面卡也會報告專案視圖所偵測到的專案點擊事件。

- **`RecyclerView.ViewHolder`** 快取專案配置檔案 &ndash; 中 views 的參考，讓資源查閱不會不必要地重複。 當使用者按了視圖器的相關聯專案視圖時，視圖持有者也會針對要轉送到介面卡的專案點擊事件進行排列。

- **`RecyclerView.LayoutManager`**&ndash;在中放置專案 `RecyclerView` 。 您可以使用數個預先定義的建構管理員之一，也可以執行您自己的自訂版面建構管理員。
    `RecyclerView`將配置原則委派給版面建構管理員，讓您可以插入不同的版面建構管理員，而不需要對應用程式進行重大變更。

此外，您可以選擇性地擴充下列類別，以變更應用程式中的外觀與風格 `RecyclerView` ：

- **`RecyclerView.ItemDecoration`**
- **`RecyclerView.ItemAnimator`**

如果您沒有擴充 `ItemDecoration` 和 `ItemAnimator` ，會 `RecyclerView` 使用預設的執行。 本指南不會說明如何建立自訂 `ItemDecoration` 和 `ItemAnimator` 類別; 如需這些類別的詳細資訊，請參閱[RecyclerView. ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html)和[RecyclerView. ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html)。

<a name="recycling"></a>

## <a name="how-view-recycling-works"></a>視圖回收的運作方式

`RecyclerView`不會為數據源中的每個專案設定項目視圖。 相反地，它只會配置適合螢幕的專案數，並在使用者滾動時重複使用這些專案版面配置。 當此視圖首次向外滾動時，會經歷下圖所示的回收程式：

[![說明視圖回收六個步驟的圖表](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1. 當視圖向外滾動，且不再顯示時，它會變成*廢料視圖*。

2. [廢料] 視圖會放在集區中，並成為*回收視圖*。
    此集區是顯示相同資料類型之視圖的快取。

3. 當要顯示新的專案時，會從回收集區取得一個視圖以供重複使用。 因為介面卡必須在顯示之前重新系結此視圖，所以它稱為「中途*查看*」。

4. 中途查看已回收：介面卡會找出要顯示的下一個專案的資料，並將此資料複製到這個專案的 views。 這些視圖的參考會從與回收視圖相關聯的視圖持有者中抓取。

5. [回收] 視圖會加入至 [即將進入] 畫面中的專案清單 `RecyclerView` 。

6. 當使用者將滾動 `RecyclerView` 到清單中的下一個專案時，回收視圖就會進入螢幕上。 同時，另一個 view 會向外滾動，並根據上述步驟進行回收。

除了專案視圖的重複使用之外， `RecyclerView` 也會使用另一個效率優化： view 持有者。 *視圖預留位置*是快取視圖參考的簡單類別。 每次介面卡擴大專案配置檔案時，它也會建立對應的視圖持有者。 視圖持有者會使用 `FindViewById` 來取得放大專案配置檔案內的視圖參考。 這些參考是用來在每次回收配置以顯示新資料時，將新資料載入至 views。

## <a name="the-layout-manager"></a>版面建構管理員

版面建構管理員會負責定位顯示中的專案 `RecyclerView` ; 它會決定呈現類型（清單或方格）、方向（專案是否以垂直或水準方式顯示），以及應該顯示的方向專案（以一般順序或相反順序）。 版面建構管理員也會負責計算**RecycleView**顯示中每個專案的大小和位置。

版面建構管理員有額外的用途：它會決定何時回收使用者不再可見之專案視圖的原則。
由於版面建構管理員知道哪些視圖是可見的（而不是），因此它在決定何時可以回收視圖的最佳位置。 為了回收視圖，版面建構管理員通常會呼叫介面卡，以不同的資料來取代回收視圖的內容，如先前在[查看回收運作方式](#recycling)中所述。

您可以擴充 `RecyclerView.LayoutManager` 以建立您自己的建構管理員，或者可以使用預先定義的建構管理員。 `RecyclerView`提供下列預先定義的版面建構管理員：

- **`LinearLayoutManager`** 在可以 &ndash; 垂直捲動的資料行中，或在可水準滾動的資料列中排列專案。

- **`GridLayoutManager`**&ndash;在方格中顯示專案。

- **`StaggeredGridLayoutManager`**&ndash;在交錯的方格中顯示專案，其中有些專案具有不同的高度和寬度。

若要指定建構管理員，請將您選擇的建構管理員具現化，並將其傳遞給 `SetLayoutManager` 方法。 請注意，您*必須*指定 [版面建構管理員] &ndash; `RecyclerView` 預設不會選取預先定義的版面建構管理員。

如需版面建構管理員的詳細資訊，請參閱[RecyclerView. LayoutManager 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html)。

## <a name="the-view-holder"></a>視圖持有者

視圖預留位置是您針對快取視圖參考所定義的類別。 介面卡會使用這些視圖參考，將每個視圖系結至其內容。 中的每個專案 `RecyclerView` 都有相關聯的「視圖預留位置」實例，可快取該專案的視圖參考。 若要建立視圖持有者，請使用下列步驟來定義類別，以保存每個專案的確切視圖集：

1. 子類別 `RecyclerView.ViewHolder` 。
2. 執行查詢並儲存 view 參考的函式。
3. 執行介面卡可用於存取這些參考的屬性。

`ViewHolder`[基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)中會顯示執行的詳細範例。
如需的詳細資訊 `RecyclerView.ViewHolder` ，請參閱[RecyclerView. ViewHolder 類別參考](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。

## <a name="the-adapter"></a>介面卡

大部分的整合程式碼「繁重」都是 `RecyclerView` 在介面卡中進行。 `RecyclerView`要求您提供衍生自的介面卡 `RecyclerView.Adapter` 來存取資料來源，並在每個專案中填入資料來源的內容。
因為資料來源是應用程式特定的，所以您必須執行瞭解如何存取資料的介面卡功能。 介面卡會從資料來源中提取資訊，並將其載入集合中的每個專案 `RecyclerView` 。

下圖說明介面卡如何透過 view 持有者，將資料來源中的內容對應至中每個資料列專案內的個別視圖 `RecyclerView` ：

[![說明介面卡將資料來源連接到 ViewHolders 的圖表](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

介面卡會載入每個 `RecyclerView` 資料列，其中包含特定資料列專案的資料。 例如，針對資料列位置*p*，介面卡會在資料來源中的位置*p*找出相關聯的資料，並將此資料複製到集合中位置*p*的資料列專案 `RecyclerView` 。
例如，在上圖中，介面卡會使用視圖預留位置來查閱和位置的參考， `ImageView` `TextView` 因此 `FindViewById` 當使用者在集合中滾動並重複使用 views 時，不需要重複呼叫這些視圖。

當您執行介面卡時，必須覆寫下列 `RecyclerView.Adapter` 方法：

- **`OnCreateViewHolder`** 具現 &ndash; 化專案配置檔案和視圖預留位置。

- **`OnBindViewHolder`** 將 &ndash; 位於指定位置的資料載入至其參考儲存在指定之視圖持有者的視圖中。

- **`ItemCount`** 傳回 &ndash; 資料來源中的專案數。

當版面建構管理員在中定位專案時，會呼叫這些方法 `RecyclerView` 。

## <a name="notifying-recyclerview-of-data-changes"></a>通知 RecyclerView 資料變更

`RecyclerView`當其資料來源的內容變更時，不會自動更新其顯示;當資料集發生變更時，介面卡必須通知 `RecyclerView` 。 資料集可以透過許多方式變更;例如，專案內的內容可能會變更，或資料的整體結構可能會改變。
`RecyclerView.Adapter`提供一些您可以呼叫的方法，以便以 `RecyclerView` 最有效率的方式回應資料變更：

- **`NotifyItemChanged`**&ndash;通知指定位置的專案已變更。

- **`NotifyItemRangeChanged`**&ndash;表示指定位置範圍內的專案已變更。

- **`NotifyItemInserted`**&ndash;表示已新插入指定位置的專案。

- **`NotifyItemRangeInserted`**&ndash;表示已新插入指定範圍內的專案。

- **`NotifyItemRemoved`**&ndash;表示已移除指定位置中的專案。

- **`NotifyItemRangeRemoved`**&ndash;表示已移除指定位置範圍內的專案。

- **`NotifyDataSetChanged`** 指示 &ndash; 資料集已變更（強制執行完整更新）。

如果您確切知道資料集的變更方式，您可以呼叫上述適當的方法，以 `RecyclerView` 最有效率的方式重新整理。 如果您不知道資料集變更的確切方式，可以呼叫 `NotifyDataSetChanged` ，因為必須重新整理 `RecyclerView` 使用者看得見的所有視圖，所以效率較低。 如需這些方法的詳細資訊，請參閱[RecyclerView。](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)

在下一個主題中，[基本的 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)中，範例應用程式會實作為示範上述元件和功能的實際程式碼範例。

## <a name="related-links"></a>相關連結

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [基本 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [擴充 RecyclerView 範例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
