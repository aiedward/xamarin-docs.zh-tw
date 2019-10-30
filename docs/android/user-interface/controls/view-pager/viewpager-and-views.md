---
title: 使用檢視的 ViewPager
description: ViewPager 是可讓您執行 gestural 導覽的版面建構管理員。 Gestural 導覽可讓使用者向左和向右滑動以逐步執行資料頁面。 本指南說明如何使用 ViewPager 和 PagerTabStrip 來執行 swipeable UI，並以 Views 作為資料頁（後續的指南會討論如何使用頁面的片段）。
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 65a613f229f04a4ab01ca73a9c53c026add49f84
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029040"
---
# <a name="viewpager-with-views"></a>使用檢視的 ViewPager

_ViewPager 是可讓您執行 gestural 導覽的版面建構管理員。Gestural 導覽可讓使用者向左和向右滑動以逐步執行資料頁面。本指南說明如何使用 ViewPager 和 PagerTabStrip 來執行 swipeable UI，並以 Views 作為資料頁（後續的指南會討論如何使用頁面的片段）。_

## <a name="overview"></a>總覽

本指南提供逐步解說，說明如何使用 `ViewPager` 來實作為落葉和長時間樹狀結構的映射庫。 在此應用程式中，使用者會透過「樹狀目錄」向左和向右撥動以觀看樹狀結構影像。 在目錄的每一頁頂端，樹狀目錄的名稱會列在`PagerTabStrip`中，而樹狀結構的影像會顯示在 `ImageView`中。 介面卡是用來將 `ViewPager` 介面用於基礎資料模型。 此應用程式會實行衍生自 `PagerAdapter`的介面卡。 

雖然 `ViewPager`型應用程式通常是使用 `Fragment`來執行，但還是有一些相當簡單的使用案例，也就是 `Fragment`的額外複雜度不是必要的。 例如，本逐步解說中所述的基本映射庫應用程式不需要使用 `Fragment`s。 因為內容是靜態的，而且使用者只會在不同的影像之間來回撥動，所以使用標準的 Android 視圖和版面配置可讓您更輕鬆地執行此程式。 

## <a name="start-an-app-project"></a>啟動應用程式專案

建立名為**TreePager**的新 Android 專案（如需有關建立新 Android 專案的詳細資訊，請參閱[Hello，android](~/android/get-started/hello-android/hello-android-quickstart.md) ）。 接下來，啟動 NuGet 套件管理員。 （如需安裝 NuGet 套件的詳細資訊，請參閱[逐步解說：在您的專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)）。 尋找並安裝**Android 支援程式庫 v4**： 

[![在 NuGet 套件管理員中選取的支援 v4 Nuget 的螢幕擷取畫面](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

這也會安裝**Android 支援程式庫 v4**所 reaquired 的任何其他套件。

## <a name="add-an-example-data-source"></a>新增範例資料來源

在此範例中，樹狀目錄資料源（由 `TreeCatalog` 類別表示）會提供具有專案內容的 `ViewPager`。 
`TreeCatalog` 包含現成的樹狀映射和樹狀目錄標題集合，介面卡將用來建立 `View`s。 `TreeCatalog` 的構造函式不需要任何引數：

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

`TreeCatalog` 中的影像集合會進行組織，讓索引子可以存取每個影像。 例如，下列程式程式碼會針對集合中的第三個影像抓取映射資源識別碼： 

```csharp
int imageId = treeCatalog[2].imageId;
```

因為 `TreeCatalog` 的執行詳細資料與瞭解 `ViewPager`無關，所以此處不會列出 `TreeCatalog` 程式碼。 `TreeCatalog` 的原始程式碼可在[TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs)取得。 下載此原始程式檔（或將程式碼複製並貼到新的**TreeCatalog.cs**檔案），並將它新增至您的專案。 此外，將[影像檔](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true)案下載並解壓縮至您的**資源/可繪製**資料夾，並將它們包含在專案中。 

## <a name="create-a-viewpager-layout"></a>建立 ViewPager 版面配置

開啟**Resources/layout/axml** ，並將其內容取代為下列 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>

```csharp
This XML defines a `ViewPager` that occupies the entire screen. Note that
you must use the fully-qualified name **android.support.v4.view.ViewPager**
because `ViewPager` is packaged in a support library. `ViewPager` is
available only from 
[Android Support Library v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);
it is not available in the Android SDK. 

## Set up ViewPager

Edit **MainActivity.cs** and add the following `using` statement:

```csharp
using Android.Support.V4.View;
```

以下列程式碼取代 `OnCreate` 方法：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    TreeCatalog treeCatalog = new TreeCatalog();
}
```

此程式碼會執行下列動作：

1. 設定**axml**版面配置資源的視圖。

2. 從版面配置中抓取 `ViewPager` 的參考。

3. 具現化新的 `TreeCatalog` 做為資料來源。

當您建立並執行此程式碼時，您應該會看到類似下列螢幕擷取畫面的顯示： 

[![顯示空白 ViewPager 之應用程式的螢幕擷取畫面](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

此時，`ViewPager` 是空的，因為它缺少介面卡來存取**TreeCatalog**中的內容。 在下一節中，會建立**PagerAdapter**以將 `ViewPager` 連接到**TreeCatalog**。 

## <a name="create-the-adapter"></a>建立介面卡

`ViewPager` 使用位於 `ViewPager` 和資料來源之間的介面卡控制器物件（請參閱[介面卡](~/android/user-interface/controls/view-pager/index.md#adapter)中的圖例）。 若要存取此資料，`ViewPager` 需要提供衍生自 `PagerAdapter`的自訂介面卡。 此介面卡會在每個 `ViewPager` 頁面中填入資料來源的內容。 因為此資料來源是應用程式特定的，所以自訂介面卡是瞭解如何存取資料的程式碼。 當使用者透過 `ViewPager`的頁面撥動時，介面卡會從資料來源中提取資訊，並將其載入頁面中，供 `ViewPager` 顯示。 

當您執行 `PagerAdapter`時，必須覆寫下列各項：

- **InstantiateItem** &ndash; 會建立指定位置的頁面（`View`），並將其新增至 `ViewPager`的視圖集合。 

- **DestroyItem** &ndash; 會從指定的位置移除頁面。

- **計數**&ndash; 唯讀屬性，可傳回可用的視圖（頁面）數目。 

- **IsViewFromObject** &ndash; 會判斷頁面是否與特定的索引鍵物件相關聯。 （此物件是由 `InstantiateItem` 方法所建立）。在此範例中，索引鍵物件是 `TreeCatalog` 資料物件。

新增名為**TreePagerAdapter.cs**的新檔案，並將其內容取代為下列程式碼： 

```csharp
using System;
using Android.App;
using Android.Runtime;
using Android.Content;
using Android.Views;
using Android.Widget;
using Android.Support.V4.View;
using Java.Lang;

namespace TreePager
{
    class TreePagerAdapter : PagerAdapter
    {
        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override bool IsViewFromObject(View view, Java.Lang.Object obj)
        {
            throw new NotImplementedException();
        }

        public override Java.Lang.Object InstantiateItem (View container, int position)
        {
            throw new NotImplementedException();
        }

        public override void DestroyItem(View container, int position, Java.Lang.Object view)
        {
            throw new NotImplementedException();
        }
    }
}
```

這段程式碼會將必要的 `PagerAdapter` 實作為存根。 在下列各節中，每個方法都會取代為工作程式碼。 

### <a name="implement-the-constructor"></a>執行此函式

當應用程式具現化 `TreePagerAdapter`時，它會提供內容（`MainActivity`）和具現化的 `TreeCatalog`。 在**TreePagerAdapter.cs**中，將下列成員變數和函式新增至 `TreePagerAdapter` 類別的頂端： 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

此函式的目的是要儲存 `TreePagerAdapter` 將使用的內容和 `TreeCatalog` 實例。 

### <a name="implement-count"></a>執行計數

`Count` 的執行相當簡單：它會傳回樹狀目錄中的樹狀結構數目。 以下列程式碼取代 `Count`：

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

`TreeCatalog` 的 `NumTrees` 屬性會傳回資料集內的樹狀結構（頁數）數目。

### <a name="implement-instantiateitem"></a>執行 InstantiateItem

`InstantiateItem` 方法會建立指定位置的頁面。 它也必須將新建立的視圖加入至 `ViewPager`的 view 集合。 若要這麼做，`ViewPager` 會將本身當做容器參數傳遞。 

以下列程式碼取代 `InstantiateItem` 方法：

```csharp
public override Java.Lang.Object InstantiateItem (View container, int position)
{
    var imageView = new ImageView (context);
    imageView.SetImageResource (treeCatalog[position].imageId);
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.AddView (imageView);
    return imageView;
}
```

此程式碼會執行下列動作：

1. 具現化新的 `ImageView`，以在指定的位置顯示樹狀結構影像。 應用程式的 `MainActivity` 是將傳遞至 `ImageView` 的函式的內容。

2. 將 `ImageView` 資源設定為指定位置的 `TreeCatalog` 映射資源識別碼。

3. 將傳遞的容器 `View` 轉換成 `ViewPager` 參考。
    請注意，您必須使用 `JavaCast<ViewPager>()` 來正確地執行此轉型（這是必要的，如此 Android 才會執行執行時間檢查類型轉換）。

4. 將具現化的 `ImageView` 新增至 `ViewPager`，並將 `ImageView` 傳回給呼叫者。

當 `ViewPager` 在 `position`顯示影像時，它會顯示此 `ImageView`。 一開始，會呼叫 `InstantiateItem` 兩次，將具有 views 的前兩個頁面填入其中。 當使用者進行滾動時，會再次呼叫它，以維護目前所顯示專案的後方和前方的視圖。 

### <a name="implement-destroyitem"></a>執行 DestroyItem

`DestroyItem` 方法會從指定的位置移除頁面。 在任何指定位置的視圖可以變更的應用程式中，`ViewPager` 必須先移除該位置的過時視圖，再以新的視圖取代。 在 `TreeCatalog` 範例中，每個位置的 view 不會變更，因此 `DestroyItem` 所移除的視圖只會在針對該位置呼叫 `InstantiateItem` 時重新加入。 （為了提高效率，您可以執行集區來回收會重新顯示在相同位置的 `View`。） 

以下列程式碼取代 `DestroyItem` 方法： 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

此程式碼會執行下列動作：

1. 將傳遞的容器 `View` 轉換成 `ViewPager` 參考。

2. 將傳遞的 JAVA 物件（`view`）轉換成C#`View`（`view as View`）;

3. 從 `ViewPager`中移除此視圖。 

### <a name="implement-isviewfromobject"></a>執行 IsViewFromObject

當使用者在內容頁面中向左和向右滑動時，`ViewPager` 會呼叫 `IsViewFromObject` 以確認指定位置的子 `View` 與該相同位置的介面卡物件相關聯（因此，介面卡的物件稱為*物件金鑰*）。). 對於相對簡單的應用程式，此關聯是識別的其中一個 &ndash; 介面卡的物件金鑰是先前透過 `InstantiateItem`傳回 `ViewPager` 的視圖。 不過，對於其他應用程式，物件索引鍵可能是與 `ViewPager` 在該位置顯示的子視圖相關聯的其他介面卡特定類別實例（但不是相同的）。 只有介面卡知道傳遞的視圖和物件金鑰是否相關聯。 

必須執行 `IsViewFromObject`，`PagerAdapter` 才能正常運作。 如果 `IsViewFromObject` 傳回指定位置 `false`，`ViewPager` 將不會在該位置顯示視圖。 在 `TreePager` 應用程式中，由 `InstantiateItem` 傳回的物件索引鍵*是*樹狀結構的頁面 `View`，因此程式碼只需要檢查身分識別（亦即，物件索引鍵和視圖都是相同的）。 以下列程式碼取代 `IsViewFromObject`： 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```

## <a name="add-the-adapter-to-the-viewpager"></a>將介面卡新增至 ViewPager

既然 `TreePagerAdapter` 已經完成，現在可以將它新增至 `ViewPager`。 在**MainActivity.cs**中，將下列程式程式碼新增至 `OnCreate` 方法的結尾：

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

此程式碼會具現化 `TreePagerAdapter`，傳入 `MainActivity` 做為內容（`this`）。 具現化的 `TreeCatalog` 會傳遞至函式的第二個引數。 `ViewPager`的 `Adapter` 屬性會設定為具現化的 `TreePagerAdapter` 物件。這會將 `TreePagerAdapter` 插入 `ViewPager`。 

核心執行現在已完成，&ndash; 建立並執行應用程式。 您應該會看到樹狀目錄的第一個影像顯示在畫面上，如下一個螢幕擷取畫面所示。 向左滑動以查看更多樹狀檢視，然後向右滑動以移回樹狀目錄： 

[透過樹狀結構影像![TreePager 應用程式的螢幕擷取畫面](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>新增分頁指標

此最小 `ViewPager` 執行會顯示樹狀目錄的映射，但不提供使用者在目錄中的任何指示。 下一個步驟是新增 `PagerTabStrip`。 `PagerTabStrip` 會通知使用者要顯示哪一個頁面，並顯示 [上一頁] 和 [下一頁] 的提示，以提供流覽內容。 `PagerTabStrip` 是用來做為 `ViewPager`目前頁面的指標;它會在使用者撥動每個頁面時進行滾動和更新。 

開啟**Resources/layout/axml** ，並將 `PagerTabStrip` 新增至版面配置：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.v4.view.PagerTabStrip
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:layout_gravity="top"
          android:paddingBottom="10dp"
          android:paddingTop="10dp"
          android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

`ViewPager` 和 `PagerTabStrip` 設計成搭配使用。 當您在 `ViewPager` 配置內宣告 `PagerTabStrip` 時，`ViewPager` 會自動尋找 `PagerTabStrip`，並將其連接到介面卡。 當您建立並執行應用程式時，您應該會看到顯示在每個畫面頂端的空白 `PagerTabStrip`： 

[![特寫空白 PagerTabStrip 的螢幕擷取畫面](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)

### <a name="display-a-title"></a>顯示標題

若要將標題加入至每個頁面索引標籤，請在 `PagerAdapter`衍生類別中執行 `GetPageTitleFormatted` 方法。 `ViewPager` 會呼叫 `GetPageTitleFormatted` （若已實作為），以取得描述指定位置之頁面的標題字串。 將下列方法新增至**TreePagerAdapter.cs**中的 `TreePagerAdapter` 類別： 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

此程式碼會從樹狀目錄中的指定頁面（位置）抓取樹狀標題字串，將它轉換成 JAVA `String`，並將它傳回給 `ViewPager`。 當您使用這個新的方法執行應用程式時，每個頁面都會在 `PagerTabStrip`中顯示樹狀標題。 您應該會在畫面頂端看到樹狀結構名稱，但不加底線： 

[![具有文字填滿 PagerTabStrip 索引標籤的頁面螢幕擷取畫面](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

您可以來回滑動以查看目錄中的每個標題樹影像。 

### <a name="pagertitlestrip-variation"></a>PagerTitleStrip 變化

`PagerTitleStrip` 與 `PagerTabStrip` 非常類似，不同之處在于 `PagerTabStrip` 會為目前選取的索引標籤加上底線。您可以使用上述版面配置中的 `PagerTitleStrip` 來取代 `PagerTabStrip`，然後再次執行應用程式，以查看其 `PagerTitleStrip`的外觀： 

[已從文字中移除底線的![PagerTitleStrip](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

請注意，當您轉換成 `PagerTitleStrip`時，會移除底線。 

## <a name="summary"></a>總結

本逐步解說提供如何建立基本 `ViewPager`型應用程式，而不需要使用 `Fragment`的逐步範例。 它會呈現包含影像和標題字串的範例資料來源、用來顯示影像的 `ViewPager` 配置，以及將 `ViewPager` 連接至資料來源的 `PagerAdapter` 子類別。 為了協助使用者流覽資料集，其中包含說明如何新增 `PagerTabStrip` 或 `PagerTitleStrip`，以在每個頁面頂端顯示影像標題的指示。 

## <a name="related-links"></a>相關連結

- [TreePager （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
