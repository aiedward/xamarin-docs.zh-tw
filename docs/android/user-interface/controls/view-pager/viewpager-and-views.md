---
title: 使用檢視的 ViewPager
description: ViewPager 是可讓您實作手勢導覽的佈局管理員。 手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。 本指南說明如何實作使用 ViewPager 和 PagerTabStrip，做為資料頁面中使用檢視 swipeable UI （後續的快速入門會說明如何使用片段頁面）。
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a8b7fa53d3384821d028e4a88ba22071a17e5bd9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113920"
---
# <a name="viewpager-with-views"></a>使用檢視的 ViewPager

_ViewPager 是可讓您實作手勢導覽的佈局管理員。手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。本指南說明如何實作使用 ViewPager 和 PagerTabStrip，做為資料頁面中使用檢視 swipeable UI （後續的快速入門會說明如何使用片段頁面）。_

 
## <a name="overview"></a>總覽

本指南會逐步解說中所提供的逐步示範如何使用`ViewPager`實作落葉和長青型樹狀結構的映像庫。 在此應用程式，使用者會 swipes left 和 right 透過 「 樹狀目錄 」 檢視樹狀目錄中的影像。 樹狀結構的名稱會列在目錄的每個頁面頂端`PagerTabStrip`，而樹狀結構的映像會顯示在`ImageView`。 使用配接器介面`ViewPager`至基礎資料模型。 此應用程式會實作衍生自配接器`PagerAdapter`。 

雖然`ViewPager`-根據應用程式通常會實作與`Fragment`s，有一些相當簡單的使用案例，額外的複雜性`Fragment`s 不是必要。 例如，本逐步解說中的基本映像資源庫應用程式不需要使用`Fragment`s。 因為內容是靜態，而且使用者只 swipes 之間不同的映像中，實作可以保持簡單使用標準的 Android 檢視和版面配置。 



## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android 專案，稱為**TreePager** (請參閱[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)如需有關建立新的 Android 專案)。 接下來，啟動 [NuGet 套件管理員]。 (如需有關如何安裝 NuGet 套件的詳細資訊，請參閱 <<c0> [ 逐步解說： 在您的專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough))。 尋找並安裝**Android 支援程式庫 v4**: 

[![在 NuGet 套件管理員中選取的螢幕擷取畫面的支援 v4 Nuget](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

這也會安裝的任何其他套件 reaquired **Android 支援程式庫 v4**。



## <a name="add-an-example-data-source"></a>新增範例資料來源

在此範例中，樹狀目錄的資料來源 (由`TreeCatalog`類別) 提供`ViewPager`與項目內容。 
`TreeCatalog` 包含現成的樹狀目錄中的映像和樹狀目錄中的項目，配接器要用來建立集合`View`s。 `TreeCatalog` 建構函式不需要引數：

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

集合中的映像`TreeCatalog`經過組織，使得索引子可以存取每個映像。 例如，下列一行程式碼會擷取集合中的第三個映像的映像資源識別碼： 

```csharp
int imageId = treeCatalog[2].imageId;
```

因為的實作詳細資料`TreeCatalog`了解無關`ViewPager`，則`TreeCatalog`此處未列出的程式碼。 原始程式碼`TreeCatalog`將會位於[TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs)。 下載這個原始程式檔 (或複製並貼到新的程式碼**TreeCatalog.cs**檔案) 並將它新增至您的專案。 此外，下載並解壓縮[影像檔](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true)到您**可繪製資源/** 資料夾並將它們包含在專案中。 



## <a name="create-a-viewpager-layout"></a>建立的 ViewPager 版面配置

開啟**Resources/layout/Main.axml**並以下列 XML 取代其內容：

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

1.  設定從檢視**Main.axml**版面配置資源。

2.  擷取參考`ViewPager`與配置。

3.  實例化新`TreeCatalog`做為資料來源。

當您建置並執行此程式碼時，您應該會看到類似下列螢幕擷取畫面顯示： 

[![顯示空白的 ViewPager 的應用程式的螢幕擷取畫面](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

在此時`ViewPager`是空的因為它缺乏配接器用於存取中的內容**TreeCatalog**。 下一節**PagerAdapter**建立連線`ViewPager`要**TreeCatalog**。 


## <a name="create-the-adapter"></a>建立配接器

`ViewPager` 使用配接器控制站物件，位於之間`ViewPager`和 資料來源 (請參閱上的圖[配接器](~/android/user-interface/controls/view-pager/index.md#adapter))。 若要存取此資料，請`ViewPager`會要求您提供自訂的配接器會衍生自`PagerAdapter`。 此配接器會填入每個`ViewPager`從資料來源的內容頁面。 因為此資料來源是應用程式專屬，自訂的配接器就會是了解如何存取資料的程式碼。 為透過頁面的使用者 swipes `ViewPager`，配接器會從資料來源擷取資訊，並將其載入至頁面以供`ViewPager`來顯示。 

當您實作`PagerAdapter`，您必須覆寫下列：

-   **InstantiateItem** &ndash;所建立的網頁 (`View`) 指定的位置並將它加入`ViewPager`的檢視集合。 

-   **DestroyItem** &ndash;頁面移除指定的位置。

-   **計數**&ndash;唯讀屬性，傳回可用的檢視 （頁） 的數目。 

-   **IsViewFromObject** &ndash;判斷頁面是否為特定的索引鍵物件相關聯。 (此物件由`InstantiateItem`方法。)在此範例中，索引鍵的物件是`TreeCatalog`資料物件。

加入新的檔名**TreePagerAdapter.cs**和其內容取代為下列程式碼： 

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

此程式碼虛設出基本`PagerAdapter`實作。 在下列章節中，每一種方法會取代工作程式碼。 



### <a name="implement-the-constructor"></a>實作建構函式

當應用程式具現化`TreePagerAdapter`，它提供的內容 ( `MainActivity`) 和具現化`TreeCatalog`。 將下列成員變數和建構函式加入至頂端`TreePagerAdapter`類別內**TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

這個建構函式的目的是以儲存內容及`TreeCatalog`執行個體`TreePagerAdapter`會使用。 



### <a name="implement-count"></a>實作計數

`Count`實作是相當簡單： 它會傳回在樹狀目錄中的樹狀結構數目。 以下列程式碼取代 `Count`：

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

`NumTrees`屬性`TreeCatalog`資料集中傳回樹狀結構 （頁數） 數目。



### <a name="implement-instantiateitem"></a>實作 InstantiateItem

`InstantiateItem`方法會建立指定位置的頁面。 它也必須新增新建立的檢視，以`ViewPager`的檢視集合。 若要執行這項操作，`ViewPager`傳遞本身做為容器參數。 

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

1.  具現化新`ImageView`位於指定位置顯示的樹狀目錄中的映像。 應用程式的`MainActivity`是將傳遞給內容`ImageView`建構函式。

2.  設定組`ImageView`資源以`TreeCatalog`映像位於指定位置的資源識別碼。

3.  將轉換傳入的容器`View`至`ViewPager`參考。
    請注意，您必須使用`JavaCast<ViewPager>()`正確地執行這項轉換 （這需要使 Android 執行執行階段檢查的型別轉換）。

4.  將具現化`ImageView`要`ViewPager`，並傳回`ImageView`給呼叫者。

當`ViewPager`會顯示在影像`position`，它會顯示這`ImageView`。 一開始，`InstantiateItem`呼叫兩次以填入與檢視的前兩個頁面。 當使用者捲動時，會再次呼叫它來維護檢視只是後置和目前顯示的項目之前。 



### <a name="implement-destroyitem"></a>實作 DestroyItem

`DestroyItem`方法會移除指定的位置中的頁面。 在 應用程式可以在其中將檢視在任何給定的位置變更，`ViewPager`必須有某個之前它取代為新的檢視中移除過時的檢視，在該位置的方式。 在`TreeCatalog`範例中，將檢視在每個位置不會變更，因此檢視移除`DestroyItem`就會重新新增時`InstantiateItem`呼叫該位置。 (更好的效率，其中可以實作的集區回收`View`，它將會重新顯示在相同的位置。) 

以下列程式碼取代 `DestroyItem` 方法： 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

此程式碼會執行下列動作：

1.  將轉換傳入的容器`View`成`ViewPager`參考。

2.  將轉換傳入的 Java 物件 (`view`) 到C# `View` (`view as View`);

3.  移除從檢視`ViewPager`。 



### <a name="implement-isviewfromobject"></a>實作 IsViewFromObject

為使用者滑向左和右經歷頁面的內容，`ViewPager`呼叫`IsViewFromObject`若要確認子`View`中指定的位置是 配接器的物件，該相同的位置相關聯 (因此，呼叫配接器的物件*物件索引鍵*)。 對於相對簡單的應用程式，關聯是其中一個身分識別&ndash;配接器的物件索引鍵，在該執行個體是先前已傳回的檢視`ViewPager`透過`InstantiateItem`。 不過對於其他應用程式中，物件索引鍵可能相關聯的一些其他配接器特定類別執行個體 （但不是完全相同） 的子檢視，`ViewPager`顯示該位置。 配接器知道傳遞的檢視和物件索引鍵會產生關聯。 

`IsViewFromObject` 必須針對實作`PagerAdapter`才能正確運作。 如果`IsViewFromObject`會傳回`false`指定的位置，`ViewPager`不會顯示在該位置檢視。 在 `TreePager`應用程式，該物件所傳回索引鍵`InstantiateItem`*是*頁面`View`樹狀結構，讓程式碼只會檢查身分識別的 （也就是，物件索引鍵與檢視是同一個）。 以下列程式碼取代 `IsViewFromObject`： 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```


## <a name="add-the-adapter-to-the-viewpager"></a>加入配接器的 ViewPager

既然`TreePagerAdapter`是實作，就可以開始將它新增至`ViewPager`。 在  **MainActivity.cs**，將下列程式碼行新增至結尾`OnCreate`方法：

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

此程式碼會具現化`TreePagerAdapter`，並傳入`MainActivity`做為內容 (`this`)。 具現化`TreeCatalog`會傳遞至建構函式的第二個引數。 `ViewPager`的`Adapter`屬性設定來具現化`TreePagerAdapter`物件; 此火星塞`TreePagerAdapter`成`ViewPager`。 

現在已完成的核心實作&ndash;建置並執行應用程式。 您應該會看到樹狀目錄的第一個影像，出現在畫面左邊的下一步 的螢幕擷取畫面所示。 撥動留以查看更多的樹狀結構檢視，然後向右撥動以向後移動樹狀目錄： 

[![撥動透過樹狀目錄中的映像的螢幕擷取畫面的 TreePager 應用程式](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>新增頁面巡覽區指示器

此最小`ViewPager`實作會顯示的樹狀目錄中，映像，但是它會提供任何指示，指出使用者所在目錄中。 下一個步驟是加入 `PagerTabStrip`。 `PagerTabStrip`會通知的使用者，使頁面隨即出現，並提供導覽內容顯示上一個和下一個頁面的提示。 `PagerTabStrip` 要做的目前頁面指標`ViewPager`; 它捲動，並為每個頁面上使用者 swipes 更新。 

開啟**Resources/layout/Main.axml** ，並新增`PagerTabStrip`版面配置：

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

`ViewPager` 和`PagerTabStrip`專為搭配運作。 當您宣告`PagerTabStrip`內`ViewPager`配置`ViewPager`會自動尋找`PagerTabStrip`並將它連接至配接器。 當您建置並執行應用程式時，您應該會看到空白`PagerTabStrip`顯示在每個畫面的頂端： 

[![空 PagerTabStrip 的特寫螢幕擷取畫面](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)



### <a name="display-a-title"></a>顯示標題

若要新增標題至每個頁面索引標籤，實作`GetPageTitleFormatted`方法中的`PagerAdapter`-衍生的類別。 `ViewPager` 呼叫`GetPageTitleFormatted`（如果實作） 來取得描述指定的位置 頁面的標題字串。 將下列方法加入`TreePagerAdapter`類別內**TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

此程式碼擷取樹狀目錄中指定的頁面 （位置） 中的樹狀目錄中的標題字串、 將它轉換為 Java `String`，並傳回它`ViewPager`。 當您執行應用程式使用這個新的方法時，每個頁面會顯示中的樹狀結構標題`PagerTabStrip`。 您應該會看到樹狀目錄名稱不含底線螢幕的頂端： 

[![文字區域分布 PagerTabStrip 索引標籤頁面的螢幕擷取畫面](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

您可以撥動來回檢視目錄中的每個上設定樹狀目錄中的映像。 



### <a name="pagertitlestrip-variation"></a>PagerTitleStrip 變化

`PagerTitleStrip` 非常類似`PagerTabStrip`不同之處在於`PagerTabStrip`加入目前選取的索引標籤的底線。您可以取代`PagerTabStrip`具有`PagerTitleStrip`以上述的版面配置和執行應用程式，一次，以查看其外觀與`PagerTitleStrip`: 

[![以從文字中移除的底線 PagerTitleStrip](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

請注意，當您將轉換成也會移除底線`PagerTitleStrip`。 


 
## <a name="summary"></a>總結

本逐步解說提供如何建置基本的逐步說明範例`ViewPager`為基礎的應用程式，而不需使用`Fragment`s。 它提供包含影像和標題字串的範例資料來源`ViewPager`版面配置來顯示影像，並`PagerAdapter`連接的子類別`ViewPager`到資料來源。 為了幫助使用者瀏覽資料集，所包含的指示，說明如何新增`PagerTabStrip`或`PagerTitleStrip`在每個頁面頂端顯示的影像標題。 


## <a name="related-links"></a>相關連結

- [TreePager （範例）](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
