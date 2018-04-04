---
title: 以片段 ViewPager
description: ViewPager 是可讓您實作 gestural 巡覽配置管理員。 左邊和右邊來逐步執行的資料頁，gestural 瀏覽可讓使用者撥動。 本指南說明如何實作 swipeable ViewPager，使用片段做為資料頁與 UI。
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 83b9123b24b39e2d7bda392d05c6595f5fc3af0d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="viewpager-with-fragments"></a>以片段 ViewPager

_ViewPager 是可讓您實作 gestural 巡覽配置管理員。左邊和右邊來逐步執行的資料頁，gestural 瀏覽可讓使用者撥動。本指南說明如何實作 swipeable ViewPager，使用片段做為資料頁與 UI。_

 
## <a name="overview"></a>總覽

`ViewPager` 通常用於搭配片段，讓您更輕鬆地管理生命週期中每個頁面`ViewPager`。 在本逐步解說，`ViewPager`用來建立應用程式呼叫**FlashCardPager**其中會提供一系列的數學問題快閃記憶卡上。 每個 flash 卡會實作為片段。 使用者 swipes 左或向右快閃記憶卡，並點選以顯示其回應數學問題。 此應用程式會建立`Fragment`配接器會衍生自每個 flash 卡和實作的執行個體`FragmentPagerAdapter`。 在[Viewpager 和檢視表](~/android/user-interface/controls/view-pager/viewpager-and-views.md)，大部分的工作中完成的`MainActivity`存留週期方法。 在**FlashCardPager**，大部分的工作會完成的`Fragment`中其中一個存留週期方法。 

本指南未涵蓋的基本概念的片段&ndash;如果您還不熟悉 Xamarin.Android 中的片段，請參閱[片段](~/android/platform/fragments/index.md)可協助您開始使用片段。 



## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android 專案，稱為**FlashCardPager**。 接下來，啟動 NuGet 封裝管理員 (如需安裝 NuGet 封裝的詳細資訊，請參閱[逐步解說： 在您的專案包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough))。 尋找並安裝**Xamarin.Android.Support.v4**封裝中所述[Viewpager 和檢視表](~/android/user-interface/controls/view-pager/viewpager-and-views.md)。 



## <a name="add-an-example-data-source"></a>將範例資料來源

在**FlashCardPager**，資料來源是由快閃記憶卡一疊紙牌`FlashCardDeck`類別，不過這項資料來源提供`ViewPager`與項目內容。 `FlashCardDeck` 包含現成數學問題和解答的集合。 `FlashCardDeck`建構函式不需要引數： 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

快閃記憶卡中的集合`FlashCardDeck`的排序方式每個 flash 卡可依索引子存取。 比方說，下列程式碼會擷取投影片的第四個 flash 卡問題： 

```csharp
string problem = flashCardDeck[3].Problem;
```

這行程式碼會擷取對應前一個問題的答案：

```csharp
string answer = flashCardDeck[3].Answer;
```

因為的實作詳細資料`FlashCardDeck`不了解相關`ViewPager`、`FlashCardDeck`此處未列出程式碼。
原始程式碼`FlashCardDeck`位於[FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs)。
下載這個原始程式檔 (或複製並貼到新的程式碼**FlashCardDeck.cs**檔案) 並將它加入至您的專案。



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
```

這段 XML 會定義`ViewPager`所佔滿整個螢幕。 請注意，您必須使用完整限定名稱**android.support.v4.view.ViewPager**因為`ViewPager`封裝在支援程式庫中。 `ViewPager` 就只能從[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); 不是可在 Android SDK。


## <a name="set-up-viewpager"></a>設定 ViewPager

編輯**Weatherapp**並加入下列`using`陳述式：

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

變更`MainActivity`類別宣告，因此它衍生自`AppCompatActivity`:

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

1.  設定從檢視**Main.axml**配置資源。

2.  擷取參考`ViewPager`與配置。

3.  具現化新`FlashCardDeck`做為資料來源。

當您建置並執行此程式碼時，您應該會看到類似下列的螢幕擷取畫面顯示： 

[![空白 ViewPager 螢幕擷取畫面的 FlashCardPager 應用程式](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

此時，`ViewPager`是空白因為缺乏可用的片段填入`ViewPager`，和它缺少配接器中的資料，建立這些片段**FlashCardDeck**。 

在下列章節中，`FlashCardFragment`是建立來實作每個 flash 卡功能和`FragmentPagerAdapter`是用來連接`ViewPager`從資料中建立的片段`FlashCardDeck`。 



## <a name="create-the-fragment"></a>建立片段

每個 flash 卡會受呼叫 UI 片段`FlashCardFragment`。 `FlashCardFragment`檢視會顯示具有單一 flash 卡所包含的資訊。 每個執行個體`FlashCardFragment`可由裝載`ViewPager`。 
`FlashCardFragment`檢視將會包含`TextView`會顯示 flash 卡問題的文字。 此檢視會實作事件處理常式使用`Toast`顯示當使用者點選 flash 卡問題的答案。 



### <a name="create-the-flashcardfragment-layout"></a>建立 FlashCardFragment 版面配置

之前`FlashCardFragment`可以是實作，其配置必須定義。 此配置命名為片段容器版面配置的單一片段。 加入新 Android 配置**資源/配置**呼叫**flashcard_layout.axml**。 開啟**Resources/layout/flashcard_layout.axml** ，並以下列程式碼取代它的內容： 

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

此配置會定義單一 flash 卡片段;每個片段組成`TextView`，會顯示使用大型 (100sp) 字型數學問題。 此文字置中以垂直和水平 flash 卡上。 



### <a name="create-the-initial-flashcardfragment-class"></a>建立初始 FlashCardFragment 類別

加入新的檔案稱為**FlashCardFragment.cs** ，並以下列程式碼取代它的內容：

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

此程式碼會虛設出基本`Fragment`會用來顯示 flash 卡的定義。 請注意，`FlashCardFragment`衍生自的支援程式庫版本`Fragment`中定義`Android.Support.V4.App.Fragment`。 是空的建構函式，讓`newInstance`factory 方法用於建立新`FlashCardFragment`而不是建構函式。 

`OnCreateView`存留週期方法會建立並設定`TextView`。 它會擴大的版面配置，此片段`TextView`並傳回擴大`TextView`給呼叫者。 `LayoutInflater` 和`ViewGroup`傳遞至`OnCreateView`，讓它可以擴大版面配置。 `savedInstanceState`配套包含資料的`OnCreateView`用於重新建立`TextView`從儲存狀態。 

片段的檢視明確膨脹的呼叫所`inflater.Inflate`。 `container`引數是檢視的父和`false`旗標指示 inflater 避免擴大的檢視加入至檢視的父代 (將會加入時`ViewPager`呼叫的介面卡的`GetItem`稍後在這個方法逐步解說）。 



### <a name="add-state-code-to-flashcardfragment"></a>將狀態的程式碼加入 FlashCardFragment

如同活動，具有片段`Bundle`用來儲存和擷取其狀態。 在**FlashCardPager**，這個`Bundle`用來儲存這個問題，並回答 flash 卡相關聯的文字。 在**FlashCardFragment.cs**，加入下列`Bundle`頂端的索引鍵`FlashCardFragment`類別定義： 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

修改`newInstance`factory 方法，使它建立`Bundle`物件，並使用上述的金鑰儲存傳遞的問題和回答具現化後的片段中的文字： 

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

修改片段存留週期方法`OnCreateView`從傳入的配套擷取這項資訊和載入問題的文字放到`TextBox`: 

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

`answer`不會使用變數，但它稍後將會用到這個檔案加入事件處理常式程式碼時。 


## <a name="create-the-adapter"></a>建立配接器

`ViewPager` 使用配接器的控制器物件位於之間`ViewPager`和資料來源 (請參閱圖中 ViewPager[配接器](~/android/user-interface/controls/view-pager/index.md#adapter)文章)。 若要存取此資料，`ViewPager`會要求您提供自訂的配接器會衍生自`PagerAdapter`。 由於這個範例使用片段，它會使用`FragmentPagerAdapter` &ndash; `FragmentPagerAdapter`衍生自`PagerAdapter`。 
`FragmentPagerAdapter` 代表做為每一頁`Fragment`會持續保留在片段管理員，只要使用者可以返回頁面。 為透過頁面使用者 swipes `ViewPager`、`FragmentPagerAdapter`會從資料來源擷取資訊，並使用它來建立`Fragment`s`ViewPager`顯示。 

當您實作`FragmentPagerAdapter`，您必須覆寫下列：

-   **計數**&ndash;唯讀屬性，傳回可用的檢視 （頁） 的數目。

-   **GetItem** &ndash;傳回針對指定的頁面顯示片段。

加入新的檔案稱為**FlashCardDeckAdapter.cs** ，並以下列程式碼取代它的內容：

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

此程式碼會虛設出基本`FragmentPagerAdapter`實作。 在下列章節中，每一種方法會以運作的程式碼取代。 建構函式的目的是要傳遞的片段管理員`FlashCardDeckAdapter`的基底類別建構函式。 



### <a name="implement-the-adapter-constructor"></a>實作配接器建構函式

應用程式具現化`FlashCardDeckAdapter`，其提供的參考片段管理員和具現化`FlashCardDeck`。 將下列成員變數加入至頂端`FlashCardDeckAdapter`類別**FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

加入下列一行程式碼`FlashCardDeckAdapter`建構函式： 

```csharp
this.flashCardDeck = flashCards;
```

這一行程式碼儲存區`FlashCardDeck`執行個體`FlashCardDeckAdapter`將會使用。 



### <a name="implement-count"></a>實作計數

`Count`實作是相當簡單： 它會傳回 flash 牌中的快閃記憶卡的數目。 以下列程式碼取代 `Count`： 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


`NumCards`屬性`FlashCardDeck`資料集中傳回的快閃記憶卡 （片段的數目） 數目。 



### <a name="implement-getitem"></a>實作 GetItem

`GetItem`方法會傳回與指定的位置相關聯的片段。 當`GetItem`稱為 flash 牌中的位置則會傳回`FlashCardFragment`設定為該位置顯示 flash 卡問題。 以下列程式碼取代 `GetItem` 方法： 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

此程式碼會執行下列動作：

1.  查閱中的數學問題字串`FlashCardDeck`指定之位置的投影片。 

2.  查閱中的回應字串`FlashCardDeck`指定之位置的投影片。 

3.  呼叫`FlashCardFragment`factory 方法`newInstance`、 flash 卡問題和解答字串中傳遞。 

4.  建立並傳回新的 flash 卡`Fragment`，其中包含該位置的問題和答案文字。 

當`ViewPager`呈現`Fragment`在`position`，它會顯示`TextBox`包含內建數學問題字串`position`flash 卡紙牌。 



## <a name="add-the-adapter-to-the-viewpager"></a>加入 ViewPager 配接器

既然`FlashCardDeckAdapter`是實作之後，就可以將它加入至`ViewPager`。 在**Weatherapp**，加入下列程式碼行結尾`OnCreate`方法：

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

此程式碼會具現化`FlashCardDeckAdapter`，並傳入`SupportFragmentManager`中第一個引數。 ( `SupportFragmentManager` FragmentActivity 屬性用來取得參考`FragmentManager`&ndash;如需有關`FragmentManager`，請參閱[管理片段](~/android/platform/fragments/managing-fragments.md)。) 

現在已完成的核心實作&ndash;建置並執行應用程式。
您應該會看到 flash 牌的第一個影像，出現在畫面左側的下一個螢幕擷取畫面所示。 撥動保留以查看更多的快閃記憶卡，然後向右撥動以向後移動 flash 牌：

[![未使用頁面巡覽區標記 FlashCardPager 應用程式的範例螢幕擷取畫面](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>加入頁面巡覽區指標

此最小`ViewPager`實作紙牌，顯示每個 flash 卡，但是它會提供使用者所在的紙牌內任何指示。 下一個步驟是加入`PagerTabStrip`。 `PagerTabStrip`會通知的使用者有關的問題數字顯示，並提供所顯示的提示之前及後續的快閃記憶卡的瀏覽內容。 

開啟**Resources/layout/Main.axml**並加入`PagerTabStrip`版面配置：

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

當您建置並執行應用程式時，您應該會看到空白`PagerTabStrip`顯示在每個 flash 卡的最上方： 

[![經過的 PagerTabStrip 不包含文字](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>顯示標題

若要加入至每個頁面 索引標籤的標題，實作`GetPageTitleFormatted`配接器中的方法。 `ViewPager` 呼叫`GetPageTitleFormatted`（如果實作） 以取得描述指定的位置 頁面的標題字串。 將下列方法加入`FlashCardDeckAdapter`類別**FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

此程式碼將轉換的問題數字 flash 牌中的位置。 結果字串會轉換成 Java`String`傳回給`ViewPager`。 當您執行應用程式與這個新的方法時，每一頁會顯示中的問題編號`PagerTabStrip`: 

[![螢幕擷取畫面的 FlashCardPager 與每一頁上方顯示的問題數目](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

您可以查看問題中的數字會顯示在每個 flash 卡頂端 flash 紙牌花色來回撥動。 



## <a name="handle-user-input"></a>處理使用者輸入

**FlashCardPager**提供一系列中的片段型快閃記憶卡`ViewPager`，但它還沒有可以顯示每個問題的解答。 在本節中，加入事件處理常式`FlashCardFragment`來顯示答案，當使用者點選 flash 卡問題文字。 

開啟**FlashCardFragment.cs**並將下列程式碼加入至結尾`OnCreateView`之前檢視會傳回給呼叫端的方法： 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

這`Click`事件處理常式會出現在使用者點選時快顯通知中顯示答案`TextBox`。 `answer`時狀態資訊已從組合傳遞給先前初始化變數`OnCreateView`。 建置並執行應用程式，然後點選以查看回應每個 flash 卡上的問題文字： 

[![螢幕擷取畫面的 FlashCardPager 應用程式快顯通知時所點選數學問題](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

**FlashCardPager**本逐步解說會使用`MainActivity`衍生自`FragmentActivity`，但您也可以衍生`MainActivity`從`AppCompatActivity`（也可支援用於管理片段）。 若要檢視`AppCompatActivity`範例中，請參閱[FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/)範例庫中。 



## <a name="summary"></a>總結

本逐步解說提供如何建立基本的逐步範例`ViewPager`為基礎的應用程式使用`Fragment`s。 它會顯示包含 flash 卡問題和解答，範例資料來源`ViewPager`版面配置，以顯示快閃記憶卡中，與`FragmentPagerAdapter`連接的子類別`ViewPager`到資料來源。 為了幫助使用者瀏覽快閃記憶卡，已包含指示，說明如何加入`PagerTabStrip`顯示每個頁面頂端的問題數。 最後，加入事件處理程式碼顯示當使用者點選 flash 卡問題的答案。 



## <a name="related-links"></a>相關連結

- [FlashCardPager (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
