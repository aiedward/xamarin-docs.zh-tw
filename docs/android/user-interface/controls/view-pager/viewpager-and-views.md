---
title: 使用檢視的 ViewPager
description: ViewPager 是一種版面建構管理員，可讓您執行手勢導覽。 手勢流覽可讓使用者向左和向右滑動，以逐步執行資料頁面。 本指南說明如何使用 ViewPager 和 PagerTabStrip 來執行 swipeable UI，並使用 view 作為資料頁面 (後續的指南會說明如何針對頁面) 使用片段。
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: e4f4243c06d98eac6f3501c41b48508f260d4633
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456986"
---
# <a name="viewpager-with-views"></a>使用檢視的 ViewPager

_ViewPager 是一種版面建構管理員，可讓您執行手勢導覽。手勢流覽可讓使用者向左和向右滑動，以逐步執行資料頁面。本指南說明如何使用 ViewPager 和 PagerTabStrip 來執行 swipeable UI，並使用 view 作為資料頁面 (後續的指南會說明如何針對頁面) 使用片段。_

## <a name="overview"></a>概觀

本指南提供逐步示範如何使用 `ViewPager` 來實作為落葉和長時間樹狀結構映射庫的逐步解說。 在此應用程式中，使用者會透過「樹狀目錄」向左和向右撥動以查看樹狀結構影像。 在目錄的每個頁面頂端，樹狀結構的名稱會列在中 `PagerTabStrip` ，而樹狀結構的影像會顯示在中 `ImageView` 。 介面卡可用來將與 `ViewPager` 基礎資料模型進行介面。 此應用程式會實作為衍生自的介面卡 `PagerAdapter` 。 

雖然 `ViewPager` 應用程式通常會使用來執行 `Fragment` ，但是有一些相當簡單的使用案例，其中不需要額外的複雜性 `Fragment` 。 例如，在此逐步解說中說明的基本映射庫應用程式不需要使用 `Fragment` 。 因為內容是靜態的，而且使用者只會在不同的映射之間來回撥動，所以可以使用標準的 Android 視圖和版面配置來更輕鬆地執行此程式。 

## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android 專案，稱為 **TreePager** (如需建立新 android 專案的詳細資訊，請參閱 [Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)) 。 接著，啟動 NuGet 封裝管理員。  (如需安裝 NuGet 套件的詳細資訊，請參閱 [逐步解說：在您的專案中包含 NuGet](/visualstudio/mac/nuget-walkthrough)) 。 尋找並安裝 **Android 支援程式庫 v4**： 

[![NuGet 封裝管理員中選取的支援 v4 NuGet 螢幕擷取畫面](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

這也會安裝 **Android 支援程式庫 v4**所 reaquired 的任何其他套件。

## <a name="add-an-example-data-source"></a>新增範例資料來源

在此範例中，樹狀目錄資料源 (由類別表示 `TreeCatalog`) 提供 `ViewPager` 包含專案內容。 
`TreeCatalog` 包含一組現成的樹狀映射，以及介面卡將用來建立的樹狀目錄標題 `View` 。 此 `TreeCatalog` 函數不需要任何引數：

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

中的影像集合 `TreeCatalog` 會組織，讓索引子可以存取每個影像。 例如，下面這行程式碼會針對集合中的第三個影像抓取影像資源識別碼： 

```csharp
int imageId = treeCatalog[2].imageId;
```

由於的執行詳細資料與 `TreeCatalog` 理解無關，因此 `ViewPager` 此 `TreeCatalog` 程式碼不會列在此處。 您可以 `TreeCatalog` 在 [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs)取得的原始程式碼。 下載此原始程式檔 (，或將程式碼複製並貼到新的 **TreeCatalog.cs** 檔案) 並將其新增至您的專案。 此外，請將 [影像檔](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) 案下載並解壓縮至您的 **資源/可繪製** 資料夾中，並將它們包含在專案中。 

## <a name="create-a-viewpager-layout"></a>建立 ViewPager 版面配置

開啟 **Resources/layout/Main. .axml** ，並將其內容取代為下列 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>
```

這個 XML 會定義一個 `ViewPager` 佔用整個畫面的。 請注意，您必須使用完整名稱 **ViewPager** ，因為 `ViewPager` 封裝在支援程式庫中。 `ViewPager` 僅適用于 [Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);Android SDK 中無法使用。 

## <a name="set-up-viewpager"></a>設定 ViewPager

編輯 **MainActivity.cs** 並加入下列 `using` 語句：

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

此程式碼會執行以下動作：

1. 設定 **.axml** 版面配置資源的視圖。

2. 從配置中抓取的參考 `ViewPager` 。

3. 具現化新的 `TreeCatalog` 作為資料來源。

當您建立並執行此程式碼時，應該會看到類似下列螢幕擷取畫面的顯示： 

[![顯示空白 ViewPager 的應用程式螢幕擷取畫面](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

此時， `ViewPager` 會是空的，因為缺少可存取 **TreeCatalog**內容的介面卡。 在下一節中，會建立 **PagerAdapter** 以將連線 `ViewPager` 到 **TreeCatalog**。 

## <a name="create-the-adapter"></a>建立介面卡

`ViewPager` 使用位於與資料來源之間的介面卡控制器物件 `ViewPager` (查看 [介面卡](~/android/user-interface/controls/view-pager/index.md#adapter)) 中的圖例。 若要存取此資料， `ViewPager` 您需要提供衍生自的自訂介面卡 `PagerAdapter` 。 此介面卡會在每個 `ViewPager` 頁面中填入資料來源的內容。 因為此資料來源是應用程式特有的，所以自訂介面卡是瞭解如何存取資料的程式碼。 當使用者透過的頁面撥動時 `ViewPager` ，介面卡會將資料來源中的資訊解壓縮，並將其載入至頁面，以便 `ViewPager` 顯示。 

當您執行時 `PagerAdapter` ，必須覆寫下列各項：

- **InstantiateItem** &ndash; 為指定的位置建立 () 的頁面 `View` ，並將它加入至 [視圖] 的 `ViewPager` 集合。 

- **DestroyItem** &ndash; 從指定的位置移除頁面。

- **計數** &ndash; 唯讀屬性，可傳回 (頁面) 可用的視圖數目。 

- **IsViewFromObject** &ndash; 判斷頁面是否與特定的索引鍵物件相關聯。  (此物件是由方法建立的 `InstantiateItem` 。在此範例中，) 的索引鍵物件是 `TreeCatalog` 資料物件。

加入名為 **TreePagerAdapter.cs** 的新檔案，並將其內容取代為下列程式碼： 

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

此程式碼會將基本的執行程式程式碼端 `PagerAdapter` 。 在下列各節中，每個方法都會以工作程式碼取代。 

### <a name="implement-the-constructor"></a>執行函式

當應用程式具現化時 `TreePagerAdapter` ，它會提供 (`MainActivity`) 和具現化的內容 `TreeCatalog` 。 `TreePagerAdapter`在**TreePagerAdapter.cs**中，將下列成員變數和函式新增至類別的頂端： 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

此函式的目的是要儲存將使用的內容和 `TreeCatalog` 實例 `TreePagerAdapter` 。 

### <a name="implement-count"></a>執行計數

此 `Count` 實值相當簡單：它會傳回樹狀目錄中的樹狀目錄數目。 以下列程式碼取代 `Count`：

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

的 `NumTrees` 屬性會傳回 `TreeCatalog` 資料集中)  (數目的樹狀目錄數目。

### <a name="implement-instantiateitem"></a>執行 InstantiateItem

`InstantiateItem`方法會建立指定位置的頁面。 它也必須將新建立的視圖新增至的 `ViewPager` view 集合。 若要這麼做，請將 `ViewPager` 本身傳遞為容器參數。 

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

此程式碼會執行以下動作：

1. 具現化新的 `ImageView` ，以顯示位於指定位置的樹狀結構影像。 應用程式 `MainActivity` 是將傳遞給函式的內容 `ImageView` 。

2. 將 `ImageView` 資源設定為 `TreeCatalog` 指定位置的影像資源識別碼。

3. 將傳遞的容器轉換 `View` 為 `ViewPager` 參考。
    請注意，您必須使用 `JavaCast<ViewPager>()` 來適當地執行這項轉換 (這是必要的，因此 Android 會) 執行執行時間檢查的類型轉換。

4. 將具現化的加入 `ImageView` 至，並將傳回到 `ViewPager` `ImageView` 呼叫端。

當 `ViewPager` 顯示影像時 `position` ，會顯示此影像 `ImageView` 。 一開始 `InstantiateItem` 會呼叫兩次，以將視圖填入前兩頁。 當使用者滾動時，就會再次呼叫它，以維護目前顯示專案的後方和前方的視圖。 

### <a name="implement-destroyitem"></a>執行 DestroyItem

`DestroyItem`方法會從指定的位置移除頁面。 在任何指定位置的視圖可能變更的應用程式中， `ViewPager` 必須先移除該位置的過時視圖，才能將它取代為新的視圖。 在此 `TreeCatalog` 範例中，每個位置的視圖都不會變更，因此在 `DestroyItem` `InstantiateItem` 針對該位置呼叫時，會直接重新加入移除的視圖。  (為了獲得更好的效率，您可以將集區執行為回收，以在 `View` 相同位置重新顯示。 )  

以下列程式碼取代 `DestroyItem` 方法： 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

此程式碼會執行以下動作：

1. 將傳遞的容器轉換 `View` 成 `ViewPager` 參考。

2. 將傳遞的 JAVA 物件 (`view`) 轉換成 c # `View` (`view as View`) ;

3. 從移除視圖 `ViewPager` 。 

### <a name="implement-isviewfromobject"></a>執行 IsViewFromObject

當使用者在內容頁面中向左和向右滑動時， `ViewPager` `IsViewFromObject` 會呼叫以確認 `View` 指定位置的子系與介面卡的物件有關聯， (因此，該介面卡的物件稱為 *物件索引鍵*) 。 針對相對較簡單的應用程式，關聯是在 &ndash; 該實例上的介面卡物件索引鍵的識別之一 `ViewPager` `InstantiateItem` 。 不過，對於其他應用程式，物件索引鍵可能是與 (相關聯的其他特定介面卡類別實例，但與 `ViewPager` 顯示于該位置的子視圖) 不同。 只有介面卡知道傳遞的視圖和物件索引鍵是否相關聯。 

`IsViewFromObject` 必須執行 `PagerAdapter` 才能正常運作。 如果 `IsViewFromObject` `false` 傳回指定的位置， `ViewPager` 將不會在該位置顯示視圖。 在 `TreePager` 應用程式中，所傳回的物件索引鍵 `InstantiateItem` *是* 樹狀的頁面 `View` ，因此程式碼只需要檢查身分識別 (亦即，物件索引鍵和視圖都是一個和相同的) 。 以下列程式碼取代 `IsViewFromObject`： 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```

## <a name="add-the-adapter-to-the-viewpager"></a>將介面卡新增至 ViewPager

現在已完成 `TreePagerAdapter` ，現在可以將它新增至 `ViewPager` 。 在 **MainActivity.cs**中，將下面這行程式碼加入至方法的結尾 `OnCreate` ：

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

此程式碼會具現化 `TreePagerAdapter` ，並傳入 `MainActivity` 做為內容 (`this`) 。 具 `TreeCatalog` 現化會傳遞至函式的第二個引數。 的 `ViewPager` `Adapter` 屬性會設定為具現化的 `TreePagerAdapter` 物件; 這會將 `TreePagerAdapter` 插入至 `ViewPager` 。 

核心執行現在已完成 &ndash; 組建並執行應用程式。 您應該會看到樹狀目錄的第一個影像顯示在畫面上，如下一個螢幕擷取畫面所示。 向左滑動以查看更多樹狀檢視，然後向右滑動以移回樹狀目錄： 

[![透過樹狀映射 TreePager 應用程式輕量的螢幕擷取畫面](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>新增呼機指標

這種最基本的 `ViewPager` 執行會顯示樹狀目錄的影像，但不會指出使用者在目錄中的位置。 下一步是加入 `PagerTabStrip` 。 `PagerTabStrip`會通知使用者所顯示的頁面，並顯示上一頁和下一頁的提示，以提供導覽內容。 `PagerTabStrip` 的目的是用來做為目前頁面的指標 `ViewPager` ; 它會在使用者撥動每個頁面時進行滾動和更新。 

開啟 **Resources/layout/.axml** ，然後將加入 `PagerTabStrip` 至配置：

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

`ViewPager` 和 `PagerTabStrip` 都是設計來一起使用。 當您在版面配置內宣告時 `PagerTabStrip` `ViewPager` ， `ViewPager` 會自動尋找， `PagerTabStrip` 並將其連接至介面卡。 當您建立並執行應用程式時，您應該會看到 `PagerTabStrip` 顯示在每個畫面頂端的空白： 

[![特寫空白 PagerTabStrip 的螢幕擷取畫面](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)

### <a name="display-a-title"></a>顯示標題

若要將標題加入至每個頁面索引標籤，請 `GetPageTitleFormatted` 在衍生類別中執行方法 `PagerAdapter` 。 `ViewPager``GetPageTitleFormatted`如果實) 取得可在指定位置描述頁面的標題字串，則會呼叫 (。 將下列方法新增至 `TreePagerAdapter` **TreePagerAdapter.cs**中的類別： 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

這段程式碼會從指定的頁面抓取樹狀目錄標題字串， (位置) 在樹狀目錄中、將它轉換成 JAVA `String` ，然後將它傳回 `ViewPager` 。 當您使用這個新的方法來執行應用程式時，每個頁面都會在中顯示樹狀標題 `PagerTabStrip` 。 您應該會在畫面頂端看到不含底線的樹狀結構名稱： 

[![具有文字填滿 PagerTabStrip 索引標籤之頁面的螢幕擷取畫面](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

您可以來回滑動以查看目錄中的每個標題樹影像。 

### <a name="pagertitlestrip-variation"></a>PagerTitleStrip 變化

`PagerTitleStrip` 非常類似 `PagerTabStrip` ，但會 `PagerTabStrip` 加入目前所選索引標籤的底線。您可以 `PagerTabStrip` 使用 `PagerTitleStrip` 上述版面配置來取代，並再次執行應用程式以查看其外觀 `PagerTitleStrip` ： 

[![從文字移除底線的 PagerTitleStrip](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

請注意，當您轉換為時，會移除底線 `PagerTitleStrip` 。 

## <a name="summary"></a>摘要

本逐步解說提供如何在不使用的情況下建立基本架構應用程式的逐步範例 `ViewPager` `Fragment` 。 它會顯示包含影像和標題字串的範例資料來源、 `ViewPager` 顯示影像的版面配置，以及將 `PagerAdapter` 連接 `ViewPager` 到資料來源的子類別。 為了協助使用者流覽資料集，其中包含說明如何新增 `PagerTabStrip` 或 `PagerTitleStrip` 在每個頁面頂端顯示影像標題的指示。 

## <a name="related-links"></a>相關連結

- [TreePager (範例) ](/samples/xamarin/monodroid-samples/userinterface-treepager)