---
title: 使用檢視的 ViewPager
description: ViewPager 是可讓您實作手勢導覽的佈局管理員。 手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。 本指南說明如何使用 ViewPager 和 PagerTabStrip 來執行 swipeable UI，並以 Views 作為資料頁（後續的指南會討論如何使用頁面的片段）。
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 8e9788b31bc397a45e4ac98a01bc788096bbd523
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762370"
---
# <a name="viewpager-with-views"></a>使用檢視的 ViewPager

_ViewPager 是可讓您實作手勢導覽的佈局管理員。手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。本指南說明如何使用 ViewPager 和 PagerTabStrip 來執行 swipeable UI，並以 Views 作為資料頁（後續的指南會討論如何使用頁面的片段）。_

## <a name="overview"></a>總覽

本指南是逐步解說，提供如何使用`ViewPager`來實作為落葉和長時間樹狀結構映射庫的逐步示範。 在此應用程式中，使用者會透過「樹狀目錄」向左和向右撥動以觀看樹狀結構影像。 在目錄的每一頁頂端，樹狀目錄的名稱會列在`PagerTabStrip`中，而且樹狀結構的影像會顯示`ImageView`在中。 介面卡是用來對基礎`ViewPager`資料模型進行介面。 此應用程式會實行衍生自`PagerAdapter`的介面卡。 

雖然`ViewPager`以為基礎的應用程式通常`Fragment`會使用來執行，但有一些相當簡單的`Fragment`使用案例，也就是不需要的額外複雜性。 例如，本逐步解說中所述的基本映射庫應用程式不需要使用`Fragment`s。 因為內容是靜態的，而且使用者只會在不同的影像之間來回撥動，所以使用標準的 Android 視圖和版面配置可讓您更輕鬆地執行此程式。 

## <a name="start-an-app-project"></a>啟動應用程式專案

建立名為**TreePager**的新 Android 專案（如需有關建立新 Android 專案的詳細資訊，請參閱[Hello，android](~/android/get-started/hello-android/hello-android-quickstart.md) ）。 接下來，啟動 NuGet 套件管理員。 （如需安裝 NuGet 套件的詳細資訊， [請參閱逐步解說：在您的專案](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)中包含 NuGet）。 尋找並安裝**Android 支援程式庫 v4**： 

[![在 NuGet 套件管理員中選取的支援 v4 Nuget 的螢幕擷取畫面](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

這也會安裝**Android 支援程式庫 v4**所 reaquired 的任何其他套件。

## <a name="add-an-example-data-source"></a>新增範例資料來源

在此範例中，樹狀目錄資料源（由`TreeCatalog`類別表示）會提供具有專案內容的。 `ViewPager` 
`TreeCatalog`包含現成的樹狀映射和樹狀目錄標題集合，介面卡將用來建立`View`。 `TreeCatalog` 建構函式不需要引數：

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

中`TreeCatalog`的影像集合會進行組織，讓索引子可以存取每個影像。 例如，下列程式程式碼會針對集合中的第三個影像抓取映射資源識別碼： 

```csharp
int imageId = treeCatalog[2].imageId;
```

因為的執行詳細資料`TreeCatalog`與瞭解`ViewPager`無關， `TreeCatalog`所以此處不會列出程式碼。 的原始程式碼`TreeCatalog`可在[TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs)取得。 下載此原始程式檔（或將程式碼複製並貼到新的**TreeCatalog.cs**檔案），並將它新增至您的專案。 此外，將[影像檔](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true)案下載並解壓縮至您的**資源/可繪製**資料夾，並將它們包含在專案中。 

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

2. 從版面配置抓取的`ViewPager`參考。

3. 實例化新`TreeCatalog`做為資料來源。

當您建立並執行此程式碼時，您應該會看到類似下列螢幕擷取畫面的顯示： 

[![顯示空白 ViewPager 之應用程式的螢幕擷取畫面](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

此時，是空的`ViewPager` ，因為缺少介面卡來存取**TreeCatalog**中的內容。 在下一節中，會建立**PagerAdapter**以將連接`ViewPager`到**TreeCatalog**。 

## <a name="create-the-adapter"></a>建立介面卡

`ViewPager`使用位於`ViewPager`和資料來源之間的介面卡控制器物件（請參閱[介面卡](~/android/user-interface/controls/view-pager/index.md#adapter)中的圖例）。 若要存取此資料， `ViewPager`您需要提供`PagerAdapter`衍生自的自訂介面卡。 此介面卡會`ViewPager`在每個頁面中填入資料來源的內容。 因為此資料來源是應用程式特定的，所以自訂介面卡是瞭解如何存取資料的程式碼。 當使用者透過的`ViewPager`頁面撥動時，介面卡會從資料來源中提取資訊，並將其載入頁面中， `ViewPager`以供顯示。 

當您執行`PagerAdapter`時，必須覆寫下列各項：

- **InstantiateItem**建立指定位置的`View`頁面（） `ViewPager`，並將其加入至的視圖集合。 &ndash; 

- **DestroyItem**&ndash;從指定的位置移除頁面。

- **計數**&ndash;唯讀屬性，可傳回可用的視圖（頁面）數目。 

- **IsViewFromObject**&ndash;判斷頁面是否與特定索引鍵物件相關聯。 （此物件是由`InstantiateItem`方法所建立）。在此範例中，索引鍵物件是`TreeCatalog`資料物件。

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

這段程式碼會將`PagerAdapter`基本的執行工作。 在下列各節中，每個方法都會取代為工作程式碼。 

### <a name="implement-the-constructor"></a>執行此函式

當應用程式具現`TreePagerAdapter`化時，它會提供內容`MainActivity`（）和具`TreeCatalog`現化的。 在 TreePagerAdapter.cs 中，將下列成員變數和函式新增`TreePagerAdapter`至類別的頂端： 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

此函式的目的是要儲存`TreeCatalog` `TreePagerAdapter`將使用的內容和實例。 

### <a name="implement-count"></a>執行計數

此`Count`實作為相當簡單：它會傳回樹狀目錄中的樹狀結構數目。 以下列程式碼取代 `Count`：

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

`NumTrees` 的`TreeCatalog`屬性會傳回資料集內的樹狀結構（頁數）數目。

### <a name="implement-instantiateitem"></a>執行 InstantiateItem

`InstantiateItem`方法會建立指定位置的頁面。 它也必須將新建立的視圖加入至`ViewPager`的 view 集合。 若要這麼做，會`ViewPager`將本身當做容器參數來傳遞。 

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

1. 具現化`ImageView`新的，以在指定的位置顯示樹狀結構影像。 應用程式`MainActivity`的是將傳遞至此`ImageView`函式的內容。

2. 將資源設定為位於`TreeCatalog`指定位置的映射資源識別碼。 `ImageView`

3. 將傳遞的容器`View`轉換`ViewPager`為參考。
    請注意，您必須`JavaCast<ViewPager>()`使用正確地執行此轉換（這是必要的，以便 Android 執行執行時間檢查的類型轉換）。

4. 將具現`ImageView`化的`ViewPager`加入至， `ImageView`並將傳回給呼叫端。

當顯示影像時，它會顯示此`ImageView`畫面。 `position` `ViewPager` 一開始`InstantiateItem`會呼叫兩次，以使用 views 填入前兩個頁面。 當使用者進行滾動時，會再次呼叫它，以維護目前所顯示專案的後方和前方的視圖。 

### <a name="implement-destroyitem"></a>執行 DestroyItem

`DestroyItem`方法會從指定的位置移除頁面。 在任何指定位置的視圖可以變更的應用程式中`ViewPager` ，必須先移除該位置的過時視圖，再以新的視圖取代。 在範例中，每個位置的 view 不會變更，因此當針對該位置`DestroyItem`呼叫時`InstantiateItem` ，所移除的視圖只會重新加入。 `TreeCatalog` （為了獲得更好的效率，您可以執行一個`View`集區來回收會重新顯示在相同位置的）。 

以下列程式碼取代 `DestroyItem` 方法： 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

此程式碼會執行下列動作：

1. 將傳遞的容器`View`轉換`ViewPager`為參考。

2. 將傳遞的 JAVA 物件（`view`）轉換C# `View`成（`view as View`）;

3. 從中`ViewPager`移除此視圖。 

### <a name="implement-isviewfromobject"></a>執行 IsViewFromObject

當使用者在內容頁面中向左和向右滑動`ViewPager`時`IsViewFromObject` ，會呼叫以確認`View`指定位置的子系與該相同位置的介面卡物件相關聯（因此，介面卡的物件稱為*物件索引鍵*）。 對於相對簡單的應用程式，此關聯是其中&ndash;一個身分識別，該實例上的介面卡物件金鑰是先前透過傳回`InstantiateItem`的`ViewPager`視圖。 不過，對於其他應用程式，物件索引鍵可能是其他與在該位置`ViewPager`顯示的子視圖相關聯的其他介面卡特定類別實例（但不是相同的）。 只有介面卡知道傳遞的視圖和物件金鑰是否相關聯。 

`IsViewFromObject`必須執行， `PagerAdapter`才能正常運作。 如果`IsViewFromObject`針對指定的位置傳回， `ViewPager`將不會在該位置顯示視圖。 `false` 在應用程式中，所`InstantiateItem`傳回的物件索引鍵*是*樹狀結構的頁面`View` ，因此程式碼只需要檢查身分識別（也就是，物件索引鍵和視圖都是同一個）。 `TreePager` 以下列程式碼取代 `IsViewFromObject`： 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```

## <a name="add-the-adapter-to-the-viewpager"></a>將介面卡新增至 ViewPager

現在已實作為，接下來可以將它新增`ViewPager`至。 `TreePagerAdapter` 在**MainActivity.cs**中，將下列程式程式碼新增至`OnCreate`方法的結尾：

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

此程式碼會`TreePagerAdapter`具現化， `MainActivity`並傳入做為`this`內容（）。 具現`TreeCatalog`化的會傳遞至函式的第二個引數。 `TreePagerAdapter` `TreePagerAdapter` `ViewPager`的屬性會設定為具現化的物件; 這會將插入。 `ViewPager` `Adapter` 

核心執行現在已完成&ndash;組建並執行應用程式。 您應該會看到樹狀目錄的第一個影像顯示在畫面上，如下一個螢幕擷取畫面所示。 向左滑動以查看更多樹狀檢視，然後向右滑動以移回樹狀目錄： 

[![透過樹狀映射進行 TreePager 應用程式輕量的螢幕擷取畫面](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>新增分頁指標

這個最`ViewPager`小的執行會顯示樹狀目錄的映射，但不提供使用者在類別目錄中的指示。 下一個步驟是加入 `PagerTabStrip`。 會`PagerTabStrip`告知使用者要顯示哪一個頁面，並藉由顯示上一頁和下一頁的提示來提供導覽內容。 `PagerTabStrip`是用來做為目前頁面的`ViewPager`指標，它會在使用者撥動每個頁面時進行滾動和更新。 

開啟**Resources/layout/axml** ，並將新增`PagerTabStrip`至版面配置：

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

`ViewPager`和`PagerTabStrip`設計成搭配使用。 當您`PagerTabStrip`在`ViewPager`版面配置內宣告時， `ViewPager`會自動尋找`PagerTabStrip`並將它連接到介面卡。 當您建立並執行應用程式時，您應該會看到`PagerTabStrip`顯示在每個畫面頂端的空白： 

[![特寫空白 PagerTabStrip 的螢幕擷取畫面](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)

### <a name="display-a-title"></a>顯示標題

若要將標題加入至每個頁面索引卷`GetPageTitleFormatted`標，請`PagerAdapter`在衍生類別中執行方法。 `ViewPager`呼叫`GetPageTitleFormatted` （如果已實作為），以取得描述指定位置之頁面的標題字串。 將下列方法新增至`TreePagerAdapter` **TreePagerAdapter.cs**中的類別： 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

此程式碼會從樹狀目錄中指定的頁面（位置）抓取樹狀標題字串，將它轉換成 JAVA `String`，然後將它`ViewPager`傳回。 當您使用這個新的方法執行應用程式時，每個頁面都會在中`PagerTabStrip`顯示樹狀標題。 您應該會在畫面頂端看到樹狀結構名稱，但不加底線： 

[![具有文字填滿 PagerTabStrip 索引標籤的頁面螢幕擷取畫面](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

您可以來回滑動以查看目錄中的每個標題樹影像。 

### <a name="pagertitlestrip-variation"></a>PagerTitleStrip 變化

`PagerTitleStrip`非常類似`PagerTabStrip` ， `PagerTabStrip`但會為目前選取的索引標籤新增底線。您可以使用`PagerTabStrip` `PagerTitleStrip`上述版面配置中的取代，並再次執行應用程式，以`PagerTitleStrip`查看其外觀： 

[![已從文字中移除底線的 PagerTitleStrip](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

請注意，當您轉換為`PagerTitleStrip`時，會移除底線。 

## <a name="summary"></a>總結

本逐步解說提供如何在不使用`ViewPager` `Fragment`的情況下建立基本架構應用程式的逐步範例。 它會呈現包含影像和標題字串的範例資料來源、 `ViewPager`顯示影像的版面配置，以及將`PagerAdapter`連接`ViewPager`至資料來源的子類別。 為了協助使用者流覽資料集，其中包含說明如何新增`PagerTabStrip`或`PagerTitleStrip`以在每個頁面頂端顯示影像標題的指示。 

## <a name="related-links"></a>相關連結

- [TreePager （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
