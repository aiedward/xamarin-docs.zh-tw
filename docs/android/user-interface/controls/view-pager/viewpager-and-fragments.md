---
title: 使用片段的 ViewPager
description: ViewPager 是可讓您執行 gestural 導覽的版面建構管理員。 Gestural 導覽可讓使用者向左和向右滑動以逐步執行資料頁面。 本指南說明如何使用片段作為資料頁，以使用 ViewPager 來執行 swipeable UI。
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: d0fdfa44ce6caa0c5f0e0aa2eed6406e606eacc4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029028"
---
# <a name="viewpager-with-fragments"></a>使用片段的 ViewPager

_ViewPager 是可讓您執行 gestural 導覽的版面建構管理員。Gestural 導覽可讓使用者向左和向右滑動以逐步執行資料頁面。本指南說明如何使用片段作為資料頁，以使用 ViewPager 來執行 swipeable UI。_

## <a name="overview"></a>總覽

`ViewPager` 通常會與片段搭配使用，以便更輕鬆地管理 `ViewPager`中每個頁面的生命週期。 在本逐步解說中，`ViewPager` 是用來建立名為**FlashCardPager**的應用程式，它會呈現一系列有關快閃記憶體卡的數學問題。 每個快閃卡都會實作為片段。 使用者撥動向左和向右流覽快閃卡，然後按一下數學問題來顯示其答案。 此應用程式會為每個快閃卡片建立一個 `Fragment` 實例，並執行衍生自 `FragmentPagerAdapter`的介面卡。 在[Viewpager 和 Views](~/android/user-interface/controls/view-pager/viewpager-and-views.md)中，大部分的工作都是在 `MainActivity` 生命週期方法中完成。 在**FlashCardPager**中，大部分的工作都是由其中一個生命週期方法中的 `Fragment` 來完成。 

本指南並未涵蓋片段的基本概念 &ndash; 如果您還不熟悉 Xamarin 中的片段，請參閱[片段](~/android/platform/fragments/index.md)以協助您開始使用片段。 

## <a name="start-an-app-project"></a>啟動應用程式專案

建立名為**FlashCardPager**的新 Android 專案。 接下來，啟動 NuGet 套件管理員（如需有關安裝 NuGet 套件的詳細資訊，請參閱[逐步解說：在您的專案中包含 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)）。 尋找並安裝[Viewpager 和 Views](~/android/user-interface/controls/view-pager/viewpager-and-views.md)中所述的**Xamarin. 支援 v4**套件。 

## <a name="add-an-example-data-source"></a>新增範例資料來源

在**FlashCardPager**中，資料來源是由 `FlashCardDeck` 類別所代表的一組 flash 卡片;此資料來源會提供專案內容的 `ViewPager`。 `FlashCardDeck` 包含現成的數學問題和答案集合。 `FlashCardDeck` 的構造函式不需要任何引數： 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

`FlashCardDeck` 中的快閃卡集合會進行組織，讓索引子可以存取每個快閃卡。 例如，下列程式程式碼會抓取牌堆中的第四個閃光燈記憶卡問題： 

```csharp
string problem = flashCardDeck[3].Problem;
```

這行程式碼會抓取上一個問題的對應答案：

```csharp
string answer = flashCardDeck[3].Answer;
```

因為 `FlashCardDeck` 的執行詳細資料與瞭解 `ViewPager`無關，所以此處不會列出 `FlashCardDeck` 程式碼。
`FlashCardDeck` 的原始程式碼可在[FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs)取得。
下載此原始程式檔（或將程式碼複製並貼到新的**FlashCardDeck.cs**檔案），並將它新增至您的專案。

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
```

此 XML 會定義佔用整個螢幕的 `ViewPager`。 請注意，您必須使用完整名稱**ViewPager** ，因為 `ViewPager` 封裝在支援程式庫中。 `ViewPager` 僅適用于[Android 支援程式庫 v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);Android SDK 中無法使用它。

## <a name="set-up-viewpager"></a>設定 ViewPager

編輯**MainActivity.cs**並新增下列 `using` 語句：

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

變更 `MainActivity` 類別聲明，使其衍生自 `FragmentActivity`：

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` 衍生自`FragmentActivity` （而不是 `Activity`），因為 `FragmentActivity` 知道如何管理片段的支援。 以下列程式碼取代 `OnCreate` 方法： 

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

1. 設定**axml**版面配置資源的視圖。

2. 從版面配置中抓取 `ViewPager` 的參考。

3. 具現化新的 `FlashCardDeck` 做為資料來源。

當您建立並執行此程式碼時，您應該會看到類似下列螢幕擷取畫面的顯示： 

[以空白 ViewPager![FlashCardPager 應用程式的螢幕擷取畫面](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

此時，`ViewPager` 是空的，因為缺少用來填入 `ViewPager`的片段，而且缺少介面卡，無法從**FlashCardDeck**中的資料建立這些片段。 

在下列各節中，`FlashCardFragment` 是建立來執行每個快閃記憶體卡的功能，並建立 `FragmentPagerAdapter`，將 `ViewPager` 連接到從 `FlashCardDeck`中的資料建立的片段。 

## <a name="create-the-fragment"></a>建立片段

每個快閃卡都會由稱為 `FlashCardFragment`的 UI 片段來管理。 `FlashCardFragment`的 view 將會顯示包含在單一 flash 記憶卡中的資訊。 `FlashCardFragment` 的每個實例都是由 `ViewPager`所主控。 
`FlashCardFragment`的 view 將包含顯示快閃記憶卡問題文字的 `TextView`。 這個視圖會執行一個事件處理常式，以在使用者按下 flash 卡問題時，使用 `Toast` 來顯示答案。 

### <a name="create-the-flashcardfragment-layout"></a>建立 FlashCardFragment 版面配置

在可以執行 `FlashCardFragment` 之前，必須先定義其版面配置。 此配置是單一片段的片段容器版面配置。 將新的 Android 配置新增至**資源/版面**配置，稱為**flashcard_layout. axml**。 開啟**Resources/layout/flashcard_layout. axml** ，並將其內容取代為下列程式碼： 

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

此配置會定義單一 flash 記憶卡片段;每個片段都是由使用大型（100sp）字型來顯示數學問題的 `TextView` 所組成。 此文字會在快閃卡上垂直和水準置中。 

### <a name="create-the-initial-flashcardfragment-class"></a>建立初始 FlashCardFragment 類別

新增名為**FlashCardFragment.cs**的新檔案，並將其內容取代為下列程式碼：

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

這段程式碼會將用來顯示閃光燈的基本 `Fragment` 定義做為存根。 請注意，`FlashCardFragment` 衍生自 `Android.Support.V4.App.Fragment`中定義 `Fragment` 的支援程式庫版本。 此函式是空的，因此 `newInstance` factory 方法用來建立新的 `FlashCardFragment`，而不是使用的函式。 

`OnCreateView` 生命週期方法會建立並設定 `TextView`。 它會擴大片段 `TextView` 的版面配置，並將膨脹的 `TextView` 傳回給呼叫者。 `LayoutInflater` 和 `ViewGroup` 會傳遞至 `OnCreateView`，使其可以擴充配置。 `savedInstanceState` 套件組合包含 `OnCreateView` 用來從儲存狀態重新建立 `TextView` 的資料。 

片段的視圖會藉由對 `inflater.Inflate`的呼叫明確膨脹。 `container` 引數是視圖的父系，而 `false` 旗標會指示 inflater 避免將膨脹的視圖加入至視圖的父系（當 `ViewPager` 呼叫的此逐步解說稍後的 `GetItem` 方法時，將會新增）。 

### <a name="add-state-code-to-flashcardfragment"></a>將狀態碼新增至 FlashCardFragment

就像活動一樣，片段具有用來儲存和取出其狀態的 `Bundle`。 在**FlashCardPager**中，此 `Bundle` 用來儲存相關聯閃光燈的問題和答案文字。 在**FlashCardFragment.cs**中，將下列 `Bundle` 鍵新增至 `FlashCardFragment` 類別定義的頂端： 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

修改 `newInstance` factory 方法，讓它建立 `Bundle` 物件，並使用上述的索引鍵，將已傳遞的問題和答案文字儲存在片段中具現化之後： 

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

修改片段生命週期方法 `OnCreateView` 以從傳入的組合中抓取這項資訊，並將問題文字載入 `TextBox`： 

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

這裡不會使用 `answer` 變數，但是稍後當事件處理常式程式碼新增至此檔案時，將會用到它。 

## <a name="create-the-adapter"></a>建立介面卡

`ViewPager` 使用位於 `ViewPager` 和資料來源之間的介面卡控制器物件（請參閱 ViewPager[介面卡](~/android/user-interface/controls/view-pager/index.md#adapter)文章中的圖例）。 若要存取此資料，`ViewPager` 需要提供衍生自 `PagerAdapter`的自訂介面卡。 因為此範例使用片段，所以會使用 `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` 衍生自 `PagerAdapter`。 
`FragmentPagerAdapter` 將每個頁面表示為 `Fragment`，只要使用者可以返回頁面，就會持續保存在片段管理員中。 當使用者透過 `ViewPager`的頁面撥動時，`FragmentPagerAdapter` 會從資料來源中提取資訊，並使用它來建立要顯示 `ViewPager` 的 `Fragment`。 

當您執行 `FragmentPagerAdapter`時，必須覆寫下列各項：

- **計數**&ndash; 唯讀屬性，可傳回可用的視圖（頁面）數目。

- **GetItem** &ndash; 會傳回要針對指定頁面顯示的片段。

新增名為**FlashCardDeckAdapter.cs**的新檔案，並將其內容取代為下列程式碼：

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

這段程式碼會將必要的 `FragmentPagerAdapter` 實作為存根。 在下列各節中，每個方法都會取代為工作程式碼。 此函式的目的是要將片段管理員傳遞至 `FlashCardDeckAdapter`的基類檢查程式。 

### <a name="implement-the-adapter-constructor"></a>執行介面卡的函式

當應用程式具現化 `FlashCardDeckAdapter`時，它會提供片段管理員和具現化 `FlashCardDeck`的參考。 在**FlashCardDeckAdapter.cs**中，將下列成員變數新增至 `FlashCardDeckAdapter` 類別的頂端： 

```csharp
public FlashCardDeck flashCardDeck;
```

將下列程式程式碼新增至 `FlashCardDeckAdapter` 的函式： 

```csharp
this.flashCardDeck = flashCards;
```

這行程式碼會儲存 `FlashCardDeckAdapter` 將使用的 `FlashCardDeck` 實例。 

### <a name="implement-count"></a>執行計數

`Count` 的執行相當簡單：它會傳回快閃卡牌中的快閃卡片數目。 以下列程式碼取代 `Count`： 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```

`FlashCardDeck` 的 `NumCards` 屬性會傳回資料集內的快閃卡片（片段數目）數目。 

### <a name="implement-getitem"></a>執行 GetItem

`GetItem` 方法會傳回與指定位置相關聯的片段。 針對快閃卡中的某個位置呼叫 `GetItem` 時，它會傳回 `FlashCardFragment` 設定為顯示該位置的快閃記憶卡問題。 以下列程式碼取代 `GetItem` 方法： 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

此程式碼會執行下列動作：

1. 在 [`FlashCardDeck`] 中，查閱指定位置的數學問題字串。 

2. 在 [`FlashCardDeck`] 中，查閱指定位置的答案字串。 

3. 呼叫 `FlashCardFragment` factory 方法 `newInstance`，傳入快閃卡問題和回應字串。 

4. 建立並傳回新的快閃卡 `Fragment`，其中包含該位置的問題和答案文字。 

當 `ViewPager` 在 `position`轉譯 `Fragment` 時，它會顯示 `TextBox`，其中包含位於快閃卡片中 `position` 的數學問題字串。 

## <a name="add-the-adapter-to-the-viewpager"></a>將介面卡新增至 ViewPager

既然 `FlashCardDeckAdapter` 已經完成，現在可以將它新增至 `ViewPager`。 在**MainActivity.cs**中，將下列程式程式碼新增至 `OnCreate` 方法的結尾：

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

此程式碼會具現化 `FlashCardDeckAdapter`，傳入第一個引數中的 `SupportFragmentManager`。 （FragmentActivity 的 `SupportFragmentManager` 屬性是用來取得 `FragmentManager` 的參考 &ndash; 如需 `FragmentManager`的詳細資訊，請參閱[管理片段](~/android/platform/fragments/managing-fragments.md)）。 

核心執行現在已完成，&ndash; 建立並執行應用程式。
您應該會看到螢幕上的第一個影像顯示在畫面上，如下一個螢幕擷取畫面所示。 向左滑動以查看更多的快閃卡，然後向右滑動以向後快轉到 flash 卡上：

[![FlashCardPager 應用程式的範例螢幕擷取畫面（不含呼機指示器）](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>新增分頁指標

這個最小的 `ViewPager` 執行會顯示牌堆中的每個快閃卡，但不提供使用者在牌堆中的指示。 下一個步驟是新增 `PagerTabStrip`。 `PagerTabStrip` 會向使用者通知顯示的問題號碼，並藉由顯示先前和下一個閃光燈卡的提示，來提供導覽內容。 

開啟**Resources/layout/axml** ，並將 `PagerTabStrip` 新增至版面配置：

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

當您建立並執行應用程式時，您應該會看到空白的 `PagerTabStrip` 顯示在每個快閃卡的頂端： 

[不含文字的 PagerTabStrip![特寫](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)

### <a name="display-a-title"></a>顯示標題

若要將標題新增至每個頁面索引標籤，請在介面卡中執行 `GetPageTitleFormatted` 方法。 `ViewPager` 會呼叫 `GetPageTitleFormatted` （若已實作為），以取得描述指定位置之頁面的標題字串。 將下列方法新增至**FlashCardDeckAdapter.cs**中的 `FlashCardDeckAdapter` 類別： 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

這段程式碼會將 flash 卡中的位置轉換成問題號碼。 產生的字串會轉換為 JAVA `String`，並傳回 `ViewPager`。 當您使用這個新的方法執行應用程式時，每個頁面都會顯示 `PagerTabStrip`中的問題號碼： 

[![FlashCardPager 的螢幕擷取畫面，其中每個頁面上顯示的問題號碼](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

您可以在每個快閃卡頂端顯示的快閃卡牌堆中，來回滑動以查看問題號碼。 

## <a name="handle-user-input"></a>處理使用者輸入

**FlashCardPager**在 `ViewPager`中提供一系列以片段為基礎的快閃卡，但還沒有方法可以顯示每個問題的答案。 在本節中，會將事件處理常式新增至 `FlashCardFragment`，以在使用者按下 flash 記憶卡問題文字時顯示答案。 

開啟**FlashCardFragment.cs** ，並將下列程式碼加入至 `OnCreateView` 方法的結尾，然後再將此視圖傳回給呼叫者： 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

這個 `Click` 事件處理常式會在使用者按下 `TextBox`時出現的快顯中顯示答案。 從傳遞給 `OnCreateView`的配套中讀取狀態資訊時，`answer` 變數先前已初始化。 建立並執行應用程式，然後在每個快閃卡上，按一下問題文字來查看答案： 

[當出現數學問題時，![FlashCardPager 應用程式快顯通知的螢幕擷取畫面](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

本逐步解說中所呈現的**FlashCardPager**會使用衍生自 `FragmentActivity`的 `MainActivity`，但您也可以從 `AppCompatActivity` 衍生 `MainActivity` （這也提供管理片段的支援）。 若要查看 `AppCompatActivity` 範例，請參閱範例庫中的[FlashCardPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager) 。

## <a name="summary"></a>總結

本逐步解說提供如何使用 `Fragment`s 來建立基本 `ViewPager`型應用程式的逐步範例。 它呈現的範例資料來源包含閃光燈的問題和答案、用來顯示快閃卡的 `ViewPager` 版面配置，以及將 `ViewPager` 連接至資料來源的 `FragmentPagerAdapter` 子類別。 為了協助使用者流覽快閃卡，其中包含說明如何新增 `PagerTabStrip` 以顯示每個頁面頂端的問題號碼的指示。 最後，新增事件處理常式代碼，以在使用者按下快閃記憶卡問題時顯示答案。 

## <a name="related-links"></a>相關連結

- [FlashCardPager （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-flashcardpager)
