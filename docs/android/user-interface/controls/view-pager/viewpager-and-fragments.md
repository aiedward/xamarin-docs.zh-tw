---
title: ViewPager 與 Fragment
description: ViewPager 是可讓您實作手勢導覽的佈局管理員。 手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。 本指南說明如何使用 Fragments 作為資料頁面，使用 ViewPager 實作可滑動的UI。
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 90bffc2360654f571728f76810f144e702a81e57
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646095"
---
# <a name="viewpager-with-fragments"></a>ViewPager 與 Fragment

_ViewPager 是可讓您實作手勢導覽的佈局管理員。手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。本指南說明如何使用 Fragments 作為資料頁面，使用 ViewPager 實作可滑動的UI。_

 
## <a name="overview"></a>總覽

`ViewPager` 通常用於搭配片段，讓您更輕鬆地管理 `ViewPager` 中每個頁面的生命週期。 在本逐步解說`ViewPager`中, 是用來建立名為**FlashCardPager**的應用程式, 它會呈現一系列有關快閃記憶體卡的數學問題。 每張字卡都是實作為片段。 使用者向左或向右撥動字卡，並點選數學問題以顯示其答案。 此應用程式會為每張字卡建立 `Fragment` 執行個體，並實作衍生自 `FragmentPagerAdapter` 的配接器。 在[Viewpager 與檢視表](~/android/user-interface/controls/view-pager/viewpager-and-views.md) 中，大部分的工作都是在 `MainActivity` 生命週期方法中完成的。 在 **FlashCardPager** 中，大部分的工作都會由它的其中一個生命週期方法中的 `Fragment` 來完成。 

本指南未涵蓋 Fragments 的基本概念&ndash;如果您還不熟悉 Xamarin.Android 中的 Fragments，請參閱[Fragments](~/android/platform/fragments/index.md)可協助您開始使用 Fragments。 



## <a name="start-an-app-project"></a>啟動應用程式專案

建立名為**FlashCardPager**的新 Android 專案。 接下來, 啟動 nuget 套件管理員 (如需有關安裝 NuGet 套件的詳細資訊[, 請參閱逐步解說:在您的專案](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)中包含 NuGet)。 尋找並安裝[Viewpager 和 Views](~/android/user-interface/controls/view-pager/viewpager-and-views.md)中所述的**Xamarin. 支援 v4**套件。 



## <a name="add-an-example-data-source"></a>新增範例資料來源

在 **FlashCardPager** 中，資料來源是由 `FlashCardDeck` 類別代表的一疊字卡，此資料來源使用項目內容提供 `ViewPager`。 `FlashCardDeck` 包含現成數學問題與解答的集合。           `FlashCardDeck` 建構函式不需要引數： 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

          `FlashCardDeck` 中的字卡集合是依照每張字卡都可透過索引子來存取的方式組織。 例如，下列程式碼會擷取堆疊中的第四個字卡問題： 

```csharp
string problem = flashCardDeck[3].Problem;
```

這行程式碼會抓取上一個問題的對應答案:

```csharp
string answer = flashCardDeck[3].Answer;
```

因為的執行詳細資料`FlashCardDeck`與瞭解`ViewPager`無關, `FlashCardDeck`所以此處不會列出程式碼。
的原始程式碼`FlashCardDeck`可在[FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs)取得。
下載此原始程式檔 (或將程式碼複製並貼到新的**FlashCardDeck.cs**檔案), 並將它新增至您的專案。



## <a name="create-a-viewpager-layout"></a>建立 ViewPager 版面配置

開啟**Resources/layout/axml** , 並將其內容取代為下列 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    </android.support.v4.view.ViewPager>
```

這段 XML 會定義佔滿整個畫面的 `ViewPager`。 請注意，您必須使用完整名稱 **android.support.v4.view.ViewPager**，因為 `ViewPager` 是封裝在支援程式庫中。 `ViewPager` 只能在 [Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) 中找到，Android SDK 中不提供。


## <a name="set-up-viewpager"></a>設定 ViewPager

編輯 **MainActivity.cs** 並加入下列`using`陳述式：

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

變更類別宣告, 使其衍生自`FragmentActivity`: `MainActivity`

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity`衍生自`FragmentActivity` ( `Activity`而不是), `FragmentActivity`因為知道如何管理片段的支援。 以下列程式碼取代 `OnCreate` 方法： 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

此程式碼會執行下列動作:

1.  設定**axml**版面配置資源的視圖。

2.  從版面配置抓取的`ViewPager`參考。

3.  實例化新`FlashCardDeck`做為資料來源。

當您建立並執行此程式碼時, 您應該會看到類似下列螢幕擷取畫面的顯示: 

[![具有空白 ViewPager 的 FlashCardPager 應用程式螢幕擷取畫面](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

此時, 是空的`ViewPager` , 因為缺少用來填入的`ViewPager`片段, 而且缺少介面卡, 無法從**FlashCardDeck**中的資料建立這些片段。 

在以下各節中，`FlashCardFragment` 是建立來實作每張字卡的功能，而 `FragmentPagerAdapter` 是建立來將 `ViewPager` 連線到從 `FlashCardDeck` 中的資料建立的片段。 



## <a name="create-the-fragment"></a>建立片段

每張字卡都會由稱為 `FlashCardFragment` 的 UI 片段管理。 `FlashCardFragment` 的檢視將會顯示單一字卡中所包含的資訊。           `FlashCardFragment` 的每個執行個體都會由 `ViewPager` 裝載。 
`FlashCardFragment` 的檢視將會包含會顯示字卡問題文字的 `TextView`。 此檢視將會實作使用 `Toast` 來在使用者點選字卡問題時顯示解答的事件處理常式。 



### <a name="create-the-flashcardfragment-layout"></a>建立 FlashCardFragment 版面配置

在`FlashCardFragment`可以執行之前, 必須先定義其版面配置。 此配置是單一片段的片段容器版面配置。 將新的 Android 配置新增至**資源/版面**配置, 稱為**flashcard_layout. axml**。 開啟**Resources/layout/flashcard_layout. axml** , 並將其內容取代為下列程式碼: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/flash_card_question"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="@android:style/TextAppearance.Large"
            android:textSize="100sp"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"
            android:text="Question goes here" />
    </RelativeLayout>
```

此配置會定義單一 flash 記憶卡片段;每個片段都是由`TextView`使用大型 (100sp) 字型來顯示數學問題的所組成。 此文字會在快閃卡上垂直和水準置中。 



### <a name="create-the-initial-flashcardfragment-class"></a>建立初始 FlashCardFragment 類別

新增名為**FlashCardFragment.cs**的新檔案, 並將其內容取代為下列程式碼:

```csharp
using System;
using Android.OS;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    public class FlashCardFragment : Android.Support.V4.App.Fragment
    {
        public FlashCardFragment() { }

        public static FlashCardFragment newInstance(String question, String answer)
        {
            FlashCardFragment fragment = new FlashCardFragment();
            return fragment;
        }
        public override View OnCreateView (
            LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
        {
            View view = inflater.Inflate (Resource.Layout.flashcard_layout, container, false);
            TextView questionBox = (TextView)view.FindViewById (Resource.Id.flash_card_question);
            return view;
        }
    }
}
```

此程式碼會虛設出將用來顯示字卡的基本 `Fragment` 定義。 請注意，`FlashCardFragment` 衍生自 `Android.Support.V4.App.Fragment` 中所定義的 `Fragment` 支援程式庫版本。 建構函式是空的，讓 `newInstance` 處理站方法可用來建立新的 `FlashCardFragment` 而非建構函式。 

生命週期方法會建立並`TextView`設定。 `OnCreateView` 它會擴大片段`TextView`的版面配置, 並將膨脹`TextView`的傳回給呼叫者。 `LayoutInflater`和`ViewGroup`會傳遞至`OnCreateView` , 使其可以擴充配置。 套件組合包含`OnCreateView`用來從儲存狀態重新`TextView`建立的資料。 `savedInstanceState` 

對的呼叫`inflater.Inflate`會明確放大片段的視圖。 引數是視圖的父系, `false`而旗標會指示 inflater 避免將放大的視圖加入至視圖的父系 (在此的稍後呼叫介面卡的`ViewPager` `GetItem`方法時, 將會新增此功能)。 `container`逐步解說)。 



### <a name="add-state-code-to-flashcardfragment"></a>將狀態碼新增至 FlashCardFragment

如同活動，片段具有 `Bundle`，用來儲存及擷取其狀態。 在 **FlashCardPager** 中，這個 `Bundle` 是用來儲存相關聯之字卡的問題與解答文字。 在 **FlashCardFragment.cs** 中，將下列 `Bundle` 機碼新增到 `FlashCardFragment` 類別定義頂端： 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

修改 factory 方法, 讓它`Bundle`建立物件, 並使用上述的索引鍵, 將已傳遞的問題和答案文字儲存在片段中具現化之後: `newInstance` 

```csharp
public static FlashCardFragment newInstance(String question, String answer)
{
    FlashCardFragment fragment = new FlashCardFragment();

    Bundle args = new Bundle();
    args.PutString(FLASH_CARD_QUESTION, question);
    args.PutString(FLASH_CARD_ANSWER, answer);
    fragment.Arguments = args;

    return fragment;
}
```

修改片段生命週期方法`OnCreateView` , 以從傳入的組合中抓取這項資訊, 並將問題文字載入`TextBox`至: 

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    string question = Arguments.GetString(FLASH_CARD_QUESTION, "");
    string answer = Arguments.GetString(FLASH_CARD_ANSWER, "");

    View view = inflater.Inflate(Resource.Layout.flashcard_layout, container, false);
    TextView questionBox = (TextView)view.FindViewById(Resource.Id.flash_card_question);
    questionBox.Text = question;

    return view;
}
```

此處不會使用變數,但稍後會在將事件處理常式程式碼加入此檔案時使用。`answer` 


## <a name="create-the-adapter"></a>建立介面卡

`ViewPager`使用位於`ViewPager`和資料來源之間的介面卡控制器物件 (請參閱 ViewPager[介面卡](~/android/user-interface/controls/view-pager/index.md#adapter)文章中的圖例)。 若要存取此資料`ViewPager` , 您需要提供`PagerAdapter`衍生自的自訂介面卡。 因為此範例使用片段, 所以它會`FragmentPagerAdapter`使用&ndash; `FragmentPagerAdapter`衍生自`PagerAdapter`的。 
`FragmentPagerAdapter`將每個頁面`Fragment`表示為, 只要使用者可以返回頁面, 就會持續保存在片段管理員中。 `ViewPager`當使用者透過的頁面撥動時`FragmentPagerAdapter` , 會從資料來源中解壓縮資訊, 並使用它來建立`Fragment`要顯示的`ViewPager` 。 

當您執行`FragmentPagerAdapter`時, 必須覆寫下列各項:

-   **計數**&ndash;唯讀屬性, 可傳回可用的視圖 (頁面) 數目。

-   **GetItem**&ndash;傳回要針對指定頁面顯示的片段。

新增名為**FlashCardDeckAdapter.cs**的新檔案, 並將其內容取代為下列程式碼:

```csharp
using System;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    class FlashCardDeckAdapter : FragmentPagerAdapter
    {
        public FlashCardDeckAdapter (Android.Support.V4.App.FragmentManager fm, FlashCardDeck flashCards)
            : base(fm)
        {
        }

        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override Android.Support.V4.App.Fragment GetItem(int position)
        {
            throw new NotImplementedException();
        }
    }
}
```

這段程式碼會將`FragmentPagerAdapter`基本的執行工作。 在下列各節中, 每個方法都會取代為工作程式碼。 此函式的目的是要將片段管理員傳遞至`FlashCardDeckAdapter`的基類處理常式。 



### <a name="implement-the-adapter-constructor"></a>執行介面卡的函式

當應用程式具現`FlashCardDeckAdapter`化時, 它會提供片段管理員和具現化`FlashCardDeck`的參考。 在 FlashCardDeckAdapter.cs 中, 將下列成員變數新增至`FlashCardDeckAdapter`類別的  頂端: 

```csharp
public FlashCardDeck flashCardDeck;
```

將下列程式程式碼新增至`FlashCardDeckAdapter`函式: 

```csharp
this.flashCardDeck = flashCards;
```

這行`FlashCardDeck` `FlashCardDeckAdapter`程式碼會儲存將使用的實例。 



### <a name="implement-count"></a>執行計數

`Count`執行方式相當簡單: 它會傳回快閃卡的快閃卡數。 以下列程式碼取代 `Count`： 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


`NumCards` 的`FlashCardDeck`屬性會傳回資料集內的快閃卡片 (片段數目) 數目。 



### <a name="implement-getitem"></a>執行 GetItem

          `GetItem` 方法會傳回與指定位置相關聯的片段。 當呼叫 `GetItem` 以取得字卡中的位置時，它會傳回設定為顯示該位置之字卡問題的 `FlashCardFragment`。 以下列程式碼取代 `GetItem` 方法： 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

此程式碼會執行下列動作:

1.  針對指定的位置, 查閱`FlashCardDeck`牌中的數學問題字串。 

2.  針對指定的位置, 查閱`FlashCardDeck`牌中的答案字串。 

3.  呼叫 `FlashCardFragment` 處理站方法 `newInstance`，並傳入字卡問題和解答字串。 

4.  建立並傳回新的字卡 `Fragment`，其中包含該位置的問題和答案文字。 

當 `ViewPager` 呈現位於 `position` 的 `Fragment` 時，它會顯示 `TextBox`，其中包含位於字卡堆疊之 `position` 位置的數學問題字串。 



## <a name="add-the-adapter-to-the-viewpager"></a>將介面卡新增至 ViewPager

現在已實作為, 接下來可以將它新增`ViewPager`至。 `FlashCardDeckAdapter` 在**MainActivity.cs**中, 將下列程式程式碼新增至`OnCreate`方法的結尾:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

此程式碼會`FlashCardDeckAdapter`具現化, `SupportFragmentManager`並在第一個引數中傳入。 (FragmentActivity `SupportFragmentManager`的屬性會用來取得的參考`FragmentManager` &ndash; , 如需的`FragmentManager`詳細資訊, 請參閱[管理片段](~/android/platform/fragments/managing-fragments.md))。 

核心執行現在已完成&ndash;組建並執行應用程式。
您應該會看到螢幕上的第一個影像顯示在畫面上, 如下一個螢幕擷取畫面所示。 向左滑動以查看更多的快閃卡, 然後向右滑動以向後快轉到 flash 卡上:

[![FlashCardPager 應用程式的範例螢幕擷取畫面 (不含呼機指示器)](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>新增分頁指標

這個最基本的 `ViewPager` 實作會顯示堆疊中的每張字卡，但並未提供使用者位於字卡中何處的指示。 下一個步驟是加入 `PagerTabStrip`。           `PagerTabStrip` 會通知使用者有關顯示的是哪個問題編號，並透過顯示上一張與下一張字卡的提示以提供瀏覽上下文。 

開啟**Resources/layout/axml** , 並將新增`PagerTabStrip`至版面配置:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
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

當您建置並執行應用程式時，您應該會看到空白 `PagerTabStrip` 顯示在每張字卡的最上方： 

[![不含文字的 PagerTabStrip 特寫](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>顯示標題

若要將標題新增至每個頁面索引卷`GetPageTitleFormatted`標, 請在介面卡中執行方法。 `ViewPager`呼叫`GetPageTitleFormatted` (如果已實作為), 以取得描述指定位置之頁面的標題字串。 將下列方法新增至`FlashCardDeckAdapter` **FlashCardDeckAdapter.cs**中的類別: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

這段程式碼會將 flash 卡中的位置轉換成問題號碼。 產生的字串會轉換成將傳回`String`的`ViewPager`JAVA。 當您使用這個新的方法執行應用程式時, 每個頁面會在中`PagerTabStrip`顯示問題號碼: 

[![FlashCardPager 的螢幕擷取畫面, 其中每個頁面上顯示的問題號碼](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

您可以前後撥動以查看字卡堆疊中顯示在每張字卡頂端的問題編號。 



## <a name="handle-user-input"></a>處理使用者輸入

**FlashCardPager** 提供 `ViewPager` 中的一系列片段型字卡，但它還沒有可以顯示每個問題解答的方式。 在此節中，會將事件處理常式加入 `FlashCardFragment` 以在使用者點選字卡問題文字時顯示解答。 

開啟**FlashCardFragment.cs** , 並將下列程式碼新增至`OnCreateView`方法的結尾, 然後再將此視圖傳回給呼叫者: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

這個 `Click` 事件處理常式會在當使用者點選 `TextBox` 時出現的 Toast 中顯示解答。           `answer` 變數在從組合讀取狀態資訊 (傳遞到 `OnCreateView`) 前稍早見已初始化。 建置並執行應用程式，然後點選每張字卡上的問題文字以查看解答： 

[![FlashCardPager 應用程式在點擊數學問題時所快顯通知的螢幕擷取畫面](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

本  逐步解說中所呈現的 FlashCardPager `MainActivity`會使用`FragmentActivity`衍生自, 但您也`MainActivity`可以`AppCompatActivity`衍生自 (這也提供管理片段的支援)。 若要查看`AppCompatActivity`範例, 請參閱範例庫中的[FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) 。



## <a name="summary"></a>總結

此逐步解說提供如何使用 `Fragment` 來建置基本 `ViewPager` 型應用程式的逐步範例。 它會顯示包含字卡問題和解答的範例資料來源，其中包含字卡問題與解答、`ViewPager` 版面配置以顯示字卡，以及將 `ViewPager` 連線到資料來源的 `FragmentPagerAdapter` 子類別。 為了幫助使用者瀏覽字卡，已包含指示以說明如何新增 `PagerTabStrip` 以在每頁頂端顯示問題編號。 最後，加入事件處理程式碼以在使用者點選字卡問題時顯示解答。 



## <a name="related-links"></a>相關連結

- [FlashCardPager (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
