---
title: ViewPager 與 Fragment
description: ViewPager 是可讓您實作手勢導覽的佈局管理員。 手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。 本指南說明如何使用 Fragments 作為資料頁面，使用 ViewPager 實作可滑動的UI。
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 1b6e1c8ce91eaad46e779527c5ba12e2187cad24
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038376"
---
# <a name="viewpager-with-fragments"></a>ViewPager 與 Fragment

_ViewPager 是可讓您實作手勢導覽的佈局管理員。手勢導覽允許用戶向左和向右滑動以逐步瀏覽資料頁面。本指南說明如何使用 Fragments 作為資料頁面，使用 ViewPager 實作可滑動的UI。_

 
## <a name="overview"></a>總覽

`ViewPager` 通常用於搭配片段，讓您更輕鬆地管理 `ViewPager` 中每個頁面的生命週期。 本逐步解說`ViewPager`用來建立應用程式呼叫**FlashCardPager** ，快閃記憶卡上顯示一連串數學問題。 每張字卡都是實作為片段。 使用者向左或向右撥動字卡，並點選數學問題以顯示其答案。 此應用程式會為每張字卡建立 `Fragment` 執行個體，並實作衍生自 `FragmentPagerAdapter` 的配接器。 在[Viewpager 與檢視表](~/android/user-interface/controls/view-pager/viewpager-and-views.md) 中，大部分的工作都是在 `MainActivity` 生命週期方法中完成的。 在 **FlashCardPager** 中，大部分的工作都會由它的其中一個生命週期方法中的 `Fragment` 來完成。 

本指南未涵蓋 Fragments 的基本概念&ndash;如果您還不熟悉 Xamarin.Android 中的 Fragments，請參閱[Fragments](~/android/platform/fragments/index.md)可協助您開始使用 Fragments。 



## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android 專案，稱為**FlashCardPager**。 接下來，啟動 NuGet 套件管理員 (如需有關如何安裝 NuGet 套件的詳細資訊，請參閱[逐步解說：在專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough))。 尋找並安裝**Xamarin.Android.Support.v4**封裝中所述[Viewpager 和檢視表](~/android/user-interface/controls/view-pager/viewpager-and-views.md)。 



## <a name="add-an-example-data-source"></a>新增範例資料來源

在 **FlashCardPager** 中，資料來源是由 `FlashCardDeck` 類別代表的一疊字卡，此資料來源使用項目內容提供 `ViewPager`。 `FlashCardDeck` 包含現成數學問題與解答的集合。 `FlashCardDeck` 建構函式不需要引數： 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

`FlashCardDeck` 中的字卡集合是依照每張字卡都可透過索引子來存取的方式組織。 例如，下列程式碼會擷取堆疊中的第四個字卡問題： 

```csharp
string problem = flashCardDeck[3].Problem;
```

這行程式碼會擷取對應的答案前, 一個問題：

```csharp
string answer = flashCardDeck[3].Answer;
```

因為的實作詳細資料`FlashCardDeck`了解無關`ViewPager`，則`FlashCardDeck`此處未列出的程式碼。
原始程式碼`FlashCardDeck`將會位於[FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs)。
下載這個原始程式檔 (或複製並貼到新的程式碼**FlashCardDeck.cs**檔案) 並將它新增至您的專案。



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
```

這段 XML 會定義佔滿整個畫面的 `ViewPager`。 請注意，您必須使用完整名稱 **android.support.v4.view.ViewPager**，因為 `ViewPager` 是封裝在支援程式庫中。 `ViewPager` 只能在 [Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) 中找到，Android SDK 中不提供。


## <a name="set-up-viewpager"></a>設定的 ViewPager

編輯 **MainActivity.cs** 並加入下列`using`陳述式：

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

變更`MainActivity`類別宣告，使它衍生自`AppCompatActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` 衍生自`FragmentActivity`(而非`Activity`) 因為`FragmentActivity`知道如何管理的片段的支援。 以下列程式碼取代 `OnCreate` 方法： 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

此程式碼會執行下列動作：

1.  設定從檢視**Main.axml**版面配置資源。

2.  擷取參考`ViewPager`與配置。

3.  實例化新`FlashCardDeck`做為資料來源。

當您建置並執行此程式碼時，您應該會看到類似下列螢幕擷取畫面顯示： 

[![螢幕擷取畫面的 FlashCardPager 應用程式，使用空的 ViewPager](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

在此時`ViewPager`是空的因為缺少的片段，可填入`ViewPager`，和它缺乏配接器中的資料，建立這些片段**FlashCardDeck**。 

在以下各節中，`FlashCardFragment` 是建立來實作每張字卡的功能，而 `FragmentPagerAdapter` 是建立來將 `ViewPager` 連線到從 `FlashCardDeck` 中的資料建立的片段。 



## <a name="create-the-fragment"></a>建立片段

每張字卡都會由稱為 `FlashCardFragment` 的 UI 片段管理。 `FlashCardFragment` 的檢視將會顯示單一字卡中所包含的資訊。 `FlashCardFragment` 的每個執行個體都會由 `ViewPager` 裝載。 
`FlashCardFragment` 的檢視將會包含會顯示字卡問題文字的 `TextView`。 此檢視將會實作使用 `Toast` 來在使用者點選字卡問題時顯示解答的事件處理常式。 



### <a name="create-the-flashcardfragment-layout"></a>建立 FlashCardFragment 版面配置

之前`FlashCardFragment`可以實作，其配置必須定義。 這種版面配置是片段容器配置的單一片段。 加入新的 Android 配置**資源/配置**稱為**flashcard_layout.axml**。 開啟**Resources/layout/flashcard_layout.axml**和其內容取代為下列程式碼： 

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

這種版面配置定義的單一 flash 卡片段;每個片段組成`TextView`，會顯示使用大型 (100sp) 字型數學問題。 這段文字會置於垂直和水平 flash 卡。 



### <a name="create-the-initial-flashcardfragment-class"></a>建立初始 FlashCardFragment 類別

加入新的檔名**FlashCardFragment.cs**和其內容取代為下列程式碼：

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

`OnCreateView`生命週期方法會建立並設定`TextView`。 它擴大的片段的版面配置`TextView`，並傳回擴大`TextView`給呼叫者。 `LayoutInflater` 並`ViewGroup`傳遞至`OnCreateView`，讓它可以擴充配置。 `savedInstanceState`配套包含資料的`OnCreateView`用以重新建立`TextView`從儲存狀態。 

呼叫所明確地擴大的片段檢視`inflater.Inflate`。 `container`引數是檢視表的父代，而`false`旗標會指示避免擴大的檢視新增至檢視表的父代 inflater (它就會加入當`ViewPager`呼叫的介面卡的`GetItem`稍後在此方法逐步解說）。 



### <a name="add-state-code-to-flashcardfragment"></a>加入 FlashCardFragment 的州/省代碼

如同活動，片段具有 `Bundle`，用來儲存及擷取其狀態。 在 **FlashCardPager** 中，這個 `Bundle` 是用來儲存相關聯之字卡的問題與解答文字。 在 **FlashCardFragment.cs** 中，將下列 `Bundle` 機碼新增到 `FlashCardFragment` 類別定義頂端： 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

修改`newInstance`factory 方法，因此它會建立`Bundle`物件，並使用上述的金鑰來儲存成功的問題和回答片段中的文字之後它會具現化,： 

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

修改片段生命週期方法`OnCreateView`從傳入的套件組合擷取此資訊並載入到的問題文字`TextBox`: 

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

`answer`不會在這裡，使用變數，但它稍後將使用事件處理常式程式碼新增至這個檔案時。 


## <a name="create-the-adapter"></a>建立配接器

`ViewPager` 使用配接器控制站物件，位於之間`ViewPager`與資料來源 (請參閱圖中的 ViewPager[配接器](~/android/user-interface/controls/view-pager/index.md#adapter)文章)。 若要存取此資料，請`ViewPager`會要求您提供自訂的配接器會衍生自`PagerAdapter`。 由於此範例使用片段，它會使用`FragmentPagerAdapter` &ndash; `FragmentPagerAdapter`衍生自`PagerAdapter`。 
`FragmentPagerAdapter` 代表每個網頁`Fragment`持續保持在片段管理員中，只要使用者可以返回頁面。 為透過頁面的使用者 swipes `ViewPager`，則`FragmentPagerAdapter`會從資料來源擷取資訊，並使用它來建立`Fragment`s`ViewPager`來顯示。 

當您實作`FragmentPagerAdapter`，您必須覆寫下列：

-   **計數**&ndash;唯讀屬性，傳回可用的檢視 （頁） 的數目。

-   **GetItem** &ndash;傳回要顯示指定的頁面片段。

加入新的檔名**FlashCardDeckAdapter.cs**和其內容取代為下列程式碼：

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

此程式碼虛設出基本`FragmentPagerAdapter`實作。 在下列章節中，每一種方法會取代工作程式碼。 建構函式的目的是要傳遞至片段 manager`FlashCardDeckAdapter`的基底類別建構函式。 



### <a name="implement-the-adapter-constructor"></a>實作配接器建構函式

當應用程式具現化`FlashCardDeckAdapter`，它提供的參考片段 manager 和具現化`FlashCardDeck`。 將下列成員變數加入至頂端`FlashCardDeckAdapter`類別內**FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

新增下列一行程式碼`FlashCardDeckAdapter`建構函式： 

```csharp
this.flashCardDeck = flashCards;
```

這行程式碼存放區`FlashCardDeck`執行個體`FlashCardDeckAdapter`會使用。 



### <a name="implement-count"></a>實作計數

`Count`實作是相當簡單： 它會傳回 flash 牌中的快閃記憶卡的數目。 以下列程式碼取代 `Count`： 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


`NumCards`屬性`FlashCardDeck`傳回資料集中的快閃記憶卡 （片段的數目） 的數目。 



### <a name="implement-getitem"></a>實作 GetItem

`GetItem` 方法會傳回與指定位置相關聯的片段。 當呼叫 `GetItem` 以取得字卡中的位置時，它會傳回設定為顯示該位置之字卡問題的 `FlashCardFragment`。 以下列程式碼取代 `GetItem` 方法： 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

此程式碼會執行下列動作：

1.  查閱中的數學問題字串`FlashCardDeck`投影片，針對指定的位置。 

2.  查閱中的回應字串`FlashCardDeck`投影片，針對指定的位置。 

3.  呼叫 `FlashCardFragment` 處理站方法 `newInstance`，並傳入字卡問題和解答字串。 

4.  建立並傳回新的字卡 `Fragment`，其中包含該位置的問題和答案文字。 

當 `ViewPager` 呈現位於 `position` 的 `Fragment` 時，它會顯示 `TextBox`，其中包含位於字卡堆疊之 `position` 位置的數學問題字串。 



## <a name="add-the-adapter-to-the-viewpager"></a>加入配接器的 ViewPager

既然`FlashCardDeckAdapter`是實作，就可以開始將它新增至`ViewPager`。 在  **MainActivity.cs**，將下列程式碼行新增至結尾`OnCreate`方法：

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

此程式碼會具現化`FlashCardDeckAdapter`，並傳入`SupportFragmentManager`中第一個引數。 ( `SupportFragmentManager` FragmentActivity 屬性用來取得參考`FragmentManager`&ndash;如需詳細資訊`FragmentManager`，請參閱[管理的片段](~/android/platform/fragments/managing-fragments.md)。) 

現在已完成的核心實作&ndash;建置並執行應用程式。
您應該會看到 flash 牌的第一個影像，出現在畫面左邊的下一步 的螢幕擷取畫面所示。 撥動留以查看更多的 flash 卡片，然後向右撥動以向後移動 flash 牌：

[![FlashCardPager 應用程式，而不需要頁面巡覽區標記的範例螢幕擷取畫面](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>新增頁面巡覽區指示器

這個最基本的 `ViewPager` 實作會顯示堆疊中的每張字卡，但並未提供使用者位於字卡中何處的指示。 下一個步驟是加入 `PagerTabStrip`。 `PagerTabStrip` 會通知使用者有關顯示的是哪個問題編號，並透過顯示上一張與下一張字卡的提示以提供瀏覽上下文。 

開啟**Resources/layout/Main.axml** ，並新增`PagerTabStrip`版面配置：

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

[![特寫的 PagerTabStrip 不包含文字](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>顯示標題

若要新增標題至每個頁面索引標籤，實作`GetPageTitleFormatted`配接器中的方法。 `ViewPager` 呼叫`GetPageTitleFormatted`（如果實作） 來取得描述指定的位置 頁面的標題字串。 將下列方法加入`FlashCardDeckAdapter`類別內**FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

此程式碼會將幾個問題的 flash 牌中的位置。 產生的字串會轉換成 Java`String`回到`ViewPager`。 當您執行應用程式使用這個新的方法時，每個頁面會顯示中的問題數字`PagerTabStrip`: 

[![螢幕擷取畫面的 FlashCardPager 每一頁上方顯示的問題數目](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

您可以前後撥動以查看字卡堆疊中顯示在每張字卡頂端的問題編號。 



## <a name="handle-user-input"></a>處理使用者輸入

**FlashCardPager** 提供 `ViewPager` 中的一系列片段型字卡，但它還沒有可以顯示每個問題解答的方式。 在此節中，會將事件處理常式加入 `FlashCardFragment` 以在使用者點選字卡問題文字時顯示解答。 

開啟**FlashCardFragment.cs**並將下列程式碼新增至結尾`OnCreateView`之前檢視會傳回給呼叫端的方法： 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

這個 `Click` 事件處理常式會在當使用者點選 `TextBox` 時出現的 Toast 中顯示解答。 `answer` 變數在從組合讀取狀態資訊 (傳遞到 `OnCreateView`) 前稍早見已初始化。 建置並執行應用程式，然後點選每張字卡上的問題文字以查看解答： 

[![螢幕擷取畫面的 FlashCardPager 應用程式時點選數學問題時的快顯通知](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

**FlashCardPager**本逐步解說會使用`MainActivity`衍生自`FragmentActivity`，但您也可以衍生`MainActivity`從`AppCompatActivity`（也提供支援管理片段）。 若要檢視`AppCompatActivity`範例中，請參閱 < [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/)範例資源庫中。 



## <a name="summary"></a>總結

此逐步解說提供如何使用 `Fragment` 來建置基本 `ViewPager` 型應用程式的逐步範例。 它會顯示包含字卡問題和解答的範例資料來源，其中包含字卡問題與解答、`ViewPager` 版面配置以顯示字卡，以及將 `ViewPager` 連線到資料來源的 `FragmentPagerAdapter` 子類別。 為了幫助使用者瀏覽字卡，已包含指示以說明如何新增 `PagerTabStrip` 以在每頁頂端顯示問題編號。 最後，加入事件處理程式碼以在使用者點選字卡問題時顯示解答。 



## <a name="related-links"></a>相關連結

- [FlashCardPager (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
