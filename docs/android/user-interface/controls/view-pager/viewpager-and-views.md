---
title: ViewPager 與檢視
description: ViewPager 是可讓您實作 gestural 巡覽配置管理員。 左邊和右邊來逐步執行的資料頁，gestural 瀏覽可讓使用者撥動。 本指南說明如何實作 swipeable UI ViewPager 與 PagerTabStrip，做為資料頁中使用檢視 （後續指南涵蓋如何使用片段頁面）。
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 39251f7cf6bc287b76b7921278853158bdb14d66
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772454"
---
# <a name="viewpager-with-views"></a>ViewPager 與檢視

_ViewPager 是可讓您實作 gestural 巡覽配置管理員。左邊和右邊來逐步執行的資料頁，gestural 瀏覽可讓使用者撥動。本指南說明如何實作 swipeable UI ViewPager 與 PagerTabStrip，做為資料頁中使用檢視 （後續指南涵蓋如何使用片段頁面）。_

 
## <a name="overview"></a>總覽

本指南會逐步解說中所提供的逐步示範如何使用`ViewPager`實作落葉和長青樹狀結構的映像庫。 在此應用程式中使用者 swipes left 和 right 透過 「 樹狀目錄 」 檢視樹狀目錄中的影像。 類別目錄的每一頁頂端樹狀目錄的名稱會列在`PagerTabStrip`，而且樹狀目錄中的影像會顯示在`ImageView`。 使用配接器介面`ViewPager`至基礎資料模型。 此應用程式會實作衍生自配接器`PagerAdapter`。 

雖然`ViewPager`-基礎應用程式通常會實作`Fragment`s，有幾個相當簡單的使用案例，額外的複雜性`Fragment`s 不是必要。 例如，本逐步解說中所述的基本映像庫應用程式不需要使用`Fragment`s。 因為內容是靜態的不同的映像之間來回的使用者只有 swipes，實作可以保持簡單使用 Android 的標準檢視和版面配置。 



## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android 專案，稱為**TreePager** (請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)如需有關建立新的 Android 專案)。 接下來，啟動 NuGet 套件管理員。 (如需安裝 NuGet 封裝的詳細資訊，請參閱[逐步解說： 在您的專案包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough))。 尋找並安裝**Android 支援程式庫 v4**: 

[![選取 NuGet 套件管理員中的螢幕擷取畫面的支援 v4 Nuget](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

這也會安裝的任何其他封裝 reaquired **Android 支援程式庫 v4**。



## <a name="add-an-example-data-source"></a>將範例資料來源

在此範例中，樹狀目錄的資料來源 (由`TreeCatalog`類別) 提供`ViewPager`與項目內容。 
`TreeCatalog` 包含現成的樹狀目錄中的映像和配接器會使用建立的樹狀結構項目集合`View`s。 `TreeCatalog`建構函式不需要引數：

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

集合中的映像`TreeCatalog`的排序方式可依索引子存取每個映像。 例如，下列程式碼會擷取集合中的第三個影像的影像資源識別碼： 

```csharp
int imageId = treeCatalog[2].imageId;
```

因為的實作詳細資料`TreeCatalog`不了解相關`ViewPager`、`TreeCatalog`此處未列出程式碼。 原始程式碼`TreeCatalog`位於[TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs)。 下載這個原始程式檔 (或複製並貼到新的程式碼**TreeCatalog.cs**檔案) 並將它加入至您的專案。 此外，下載並解壓縮[影像檔](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true)到您**資源/drawable**資料夾並將它們包含在專案中。 



## <a name="create-a-viewpager-layout"></a>建立 ViewPager 版面配置

開啟**Resources/layout/Main.axml** ，並以下列 XML 取代其內容：

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

1.  設定從檢視**Main.axml**配置資源。

2.  擷取參考`ViewPager`與配置。

3.  具現化新`TreeCatalog`做為資料來源。

當您建置並執行此程式碼時，您應該會看到類似下列的螢幕擷取畫面顯示： 

[![顯示空白的 ViewPager 應用程式的螢幕擷取畫面](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

此時，`ViewPager`是空的因為它缺少配接器用於存取中的內容**TreeCatalog**。 下一節**PagerAdapter**是用來連接`ViewPager`至**TreeCatalog**。 


## <a name="create-the-adapter"></a>建立配接器

`ViewPager` 使用配接器的控制器物件位於之間`ViewPager`和資料來源 (請參閱中的圖例[配接器](~/android/user-interface/controls/view-pager/index.md#adapter))。 若要存取此資料，`ViewPager`會要求您提供自訂的配接器會衍生自`PagerAdapter`。 此配接器會填入每個`ViewPager`從資料來源的內容頁面。 因為此資料來源是應用程式專屬，自訂配接器是了解如何存取資料的程式碼。 為透過頁面使用者 swipes `ViewPager`，配接器會從資料來源擷取資訊，並將其載入至之頁面的`ViewPager`顯示。 

當您實作`PagerAdapter`，您必須覆寫下列：

-   **InstantiateItem** &ndash;所建立的網頁 (`View`) 指定的位置並將它加入`ViewPager`的檢視集合。 

-   **DestroyItem** &ndash;從指定位置移除的頁面。

-   **計數**&ndash;唯讀屬性，傳回可用的檢視 （頁） 的數目。 

-   **IsViewFromObject** &ndash;判斷頁面是否為特定的索引鍵物件相關聯。 (此物件由`InstantiateItem`方法。)在此範例中，索引鍵的物件是`TreeCatalog`資料物件。

加入新的檔案稱為**TreePagerAdapter.cs** ，並以下列程式碼取代它的內容： 

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

此程式碼會虛設出基本`PagerAdapter`實作。 在下列章節中，每一種方法會以運作的程式碼取代。 



### <a name="implement-the-constructor"></a>實作建構函式

應用程式具現化`TreePagerAdapter`，其提供的內容 ( `MainActivity`) 和具現化`TreeCatalog`。 將下列成員變數和建構函式加入至頂端`TreePagerAdapter`類別**TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

這個建構函式的目的是要儲存內容和`TreeCatalog`執行個體`TreePagerAdapter`將會使用。 



### <a name="implement-count"></a>實作計數

`Count`實作是相當簡單： 它會傳回樹狀目錄中的樹狀結構數目。 以下列程式碼取代 `Count`：

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

`NumTrees`屬性`TreeCatalog`資料集中傳回的樹狀結構 （頁數） 數目。



### <a name="implement-instantiateitem"></a>實作 InstantiateItem

`InstantiateItem`方法建立的指定位置的頁面。 它也必須加入至新建立之檢視`ViewPager`的檢視集合。 若要進行這項作業，`ViewPager`本身傳遞做為容器參數。 

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

1.  具現化新`ImageView`樹狀影像顯示在指定的位置。 應用程式的`MainActivity`的內容會傳遞至`ImageView`建構函式。

2.  設定`ImageView`資源`TreeCatalog`影像資源識別碼的指定位置。

3.  轉換傳入的容器`View`至`ViewPager`參考。
    請注意，您必須使用`JavaCast<ViewPager>()`正常執行這項轉換 （這樣做，使 Android 會進行執行階段檢查型別轉換）。

4.  將具現化`ImageView`至`ViewPager`並傳回`ImageView`給呼叫者。

當`ViewPager`顯示的影像`position`，它會顯示這個`ImageView`。 一開始，`InstantiateItem`呼叫兩次以填入與檢視的前兩個頁面。 當使用者捲動時，會再次呼叫它來維護檢視，只要後面，並晚於目前顯示的項目。 



### <a name="implement-destroyitem"></a>實作 DestroyItem

`DestroyItem`方法會將頁面移除給定的位置。 在應用程式可以在其中指定位置的檢視變更，`ViewPager`必須有移除過時的檢視，該位置之前它取代為新檢視的方式。 在`TreeCatalog`範例中，將檢視在每個位置不會變更，所以檢視移除由`DestroyItem`只會重新加入時`InstantiateItem`呼叫該位置。 (對於較好的效率，其中一個無法實作的集區回收`View`，它會重新顯示在相同的位置。) 

以下列程式碼取代 `DestroyItem` 方法： 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

此程式碼會執行下列動作：

1.  轉換傳入的容器`View`到`ViewPager`參考。

2.  將傳入的 Java 物件轉型 (`view`) 成 C# `View` (`view as View`);

3.  移除從檢視`ViewPager`。 



### <a name="implement-isviewfromobject"></a>實作 IsViewFromObject

為使用者投影片向左和右透過頁面內容，`ViewPager`呼叫`IsViewFromObject`可讓您確認子`View`給定的位置是與該相同位置的配接器的物件產生關聯 (因此，會呼叫配接器的物件*物件索引鍵*)。 相當簡單的應用程式的關聯是其中一個識別&ndash;配接器的物件索引鍵，該執行個體是先前回到檢視`ViewPager`透過`InstantiateItem`。 但是對於其他應用程式中，物件索引鍵可能是相關聯的某些其他配接器特定的類別執行個體 （但不是完全相同） 的子檢視，`ViewPager`會顯示在該位置。 配接器知道是相關聯的傳遞 檢視和物件識別碼。 

`IsViewFromObject` 必須實作`PagerAdapter`正常運作。 如果`IsViewFromObject`傳回`false`給定的位置，如`ViewPager`不會顯示在檢視該位置。 在`TreePager`應用程式，該物件所傳回金鑰`InstantiateItem`*是*頁面`View`的樹狀目錄，因此程式碼只會對檢查身分識別 （也就是，物件索引鍵和檢視是同一個）。 以下列程式碼取代 `IsViewFromObject`： 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```


## <a name="add-the-adapter-to-the-viewpager"></a>加入 ViewPager 配接器

既然`TreePagerAdapter`是實作之後，就可以將它加入至`ViewPager`。 在**Weatherapp**，加入下列程式碼行結尾`OnCreate`方法：

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

此程式碼會具現化`TreePagerAdapter`，並傳入`MainActivity`做為內容 (`this`)。 具現化`TreeCatalog`傳遞至建構函式的第二個引數。 `ViewPager`的`Adapter`屬性設定來具現化`TreePagerAdapter`物件; 此插頭`TreePagerAdapter`到`ViewPager`。 

現在已完成的核心實作&ndash;建置並執行應用程式。 您應該會看到的下一個螢幕擷取畫面所示，畫面上出現的第一個影像的樹狀目錄。 撥動以查看更多的樹狀結構檢視中，保留然後向右撥動以向後移動樹狀目錄： 

[![撥動透過樹狀目錄中的映像的螢幕擷取畫面的 TreePager 應用程式](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>加入頁面巡覽區指標

此最小`ViewPager`實作顯示影像的樹狀目錄中，但是它會提供使用者所在的目錄中任何指示。 下一個步驟是加入`PagerTabStrip`。 `PagerTabStrip`會通知的使用者，做為哪一頁會顯示，並且藉由顯示上一個和下一個頁面的提示提供瀏覽內容。 `PagerTabStrip` 要做的目前頁面指標`ViewPager`; 它捲動，並為每個頁面上使用者 swipes 更新。 

開啟**Resources/layout/Main.axml**並加入`PagerTabStrip`版面配置：

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

`ViewPager` 和`PagerTabStrip`為了共同運作。 當您宣告`PagerTabStrip`內`ViewPager`版面配置，`ViewPager`會自動尋找`PagerTabStrip`並將它連接至配接器。 當您建置並執行應用程式時，您應該會看到空白`PagerTabStrip`顯示在每個畫面最上方： 

[![空 PagerTabStrip 經過螢幕擷取畫面](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)



### <a name="display-a-title"></a>顯示標題

若要加入至每個頁面 索引標籤的標題，實作`GetPageTitleFormatted`方法中的`PagerAdapter`-衍生的類別。 `ViewPager` 呼叫`GetPageTitleFormatted`（如果實作） 以取得描述指定的位置 頁面的標題字串。 將下列方法加入`TreePagerAdapter`類別**TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

此程式碼樹狀結構的標題字串擷取樹狀目錄中指定的頁面 （位置）、 將它轉換為 Java `String`，並傳回其`ViewPager`。 當您執行應用程式與這個新方法時，每一頁會顯示在樹狀結構標題`PagerTabStrip`。 您應該會看到樹狀目錄名稱沒有底線螢幕的頂端： 

[![具有文字填入 PagerTabStrip 索引標籤頁面的螢幕擷取畫面](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

您可以滑來回到目錄中檢視每個標號樹狀目錄中的映像。 



### <a name="pagertitlestrip-variation"></a>PagerTitleStrip Variation

`PagerTitleStrip` 非常類似於`PagerTabStrip`不同之處在於`PagerTabStrip`加入目前選取的索引標籤的底線。您可以取代`PagerTabStrip`與`PagerTitleStrip`在上面的 配置 和 執行應用程式，再次以查看其外觀與`PagerTitleStrip`: 

[![以底線表示從文字移除 PagerTitleStrip](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

請注意，當您將轉換也會移除底線`PagerTitleStrip`。 


 
## <a name="summary"></a>總結

本逐步解說提供如何建立基本的逐步範例`ViewPager`為基礎的應用程式，而不使用`Fragment`s。 它會顯示包含影像和標題字串的範例資料來源`ViewPager`來顯示影像的配置和`PagerAdapter`連接的子類別`ViewPager`到資料來源。 為了幫助使用者瀏覽資料集，已包含指示，說明如何加入`PagerTabStrip`或`PagerTitleStrip`顯示每個頁面頂端的影像標題。 


## <a name="related-links"></a>相關連結

- [TreePager （範例）](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
