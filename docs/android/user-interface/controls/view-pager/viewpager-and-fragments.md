---
title: 使用片段的 ViewPager
description: ViewPager 是一種版面建構管理員，可讓您執行手勢導覽。 手勢流覽可讓使用者向左和向右滑動，以逐步執行資料頁面。 本指南說明如何使用片段做為資料頁，以使用 ViewPager 來執行 swipeable UI。
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 42035abb6b2cebc862d9c1ad0027d11dd6f47e44
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457298"
---
# <a name="viewpager-with-fragments"></a>使用片段的 ViewPager

_ViewPager 是一種版面建構管理員，可讓您執行手勢導覽。手勢流覽可讓使用者向左和向右滑動，以逐步執行資料頁面。本指南說明如何使用片段做為資料頁，以使用 ViewPager 來執行 swipeable UI。_

## <a name="overview"></a>概觀

`ViewPager` 通常會與片段搭配使用，以便更輕鬆地管理中每個頁面的生命週期 `ViewPager` 。 在這個逐步解說中， `ViewPager` 是用來建立名為 **FlashCardPager** 的應用程式，以在快閃卡上呈現一連串的數學問題。 每個快閃卡片都會實作為片段。 使用者會在快閃卡左右撥動，然後按一下數學問題來顯示其答案。 此應用程式會 `Fragment` 為每個快閃卡建立實例，並執行衍生自的介面卡 `FragmentPagerAdapter` 。 在 [Viewpager 和 Views](~/android/user-interface/controls/view-pager/viewpager-and-views.md)中，大部分的工作都是在 `MainActivity` 生命週期方法中完成。 在 **FlashCardPager**中，大部分的工作都是透過 `Fragment` 其中一個生命週期方法來完成。 

如果您還不熟悉 Xamarin 中的片段，本指南不會涵蓋片段的基本概念 &ndash; ，請參閱 [片段](~/android/platform/fragments/index.md) 以協助您開始使用片段。 

## <a name="start-an-app-project"></a>啟動應用程式專案

建立新的 Android 專案，稱為 **FlashCardPager**。 接著，啟動 NuGet 封裝管理員 (如需安裝 NuGet 套件的詳細資訊，請參閱 [逐步解說：在您的專案中包含 NuGet](/visualstudio/mac/nuget-walkthrough)) 。 尋找並安裝[Viewpager 和 Views](~/android/user-interface/controls/view-pager/viewpager-and-views.md)中所述的**支援 v4**套件。 

## <a name="add-an-example-data-source"></a>新增範例資料來源

在 **FlashCardPager**中，資料來源是由類別代表的一組閃爍卡， `FlashCardDeck` 此資料來源會提供 `ViewPager` 具有專案內容的。 `FlashCardDeck` 包含一組現成的數學問題和解答。 此 `FlashCardDeck` 函數不需要任何引數： 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

中的快閃卡片集合 `FlashCardDeck` 會組織成可由索引子存取每個快閃卡。 例如，下面這行程式碼會抓取牌組中的第四個快閃卡問題： 

```csharp
string problem = flashCardDeck[3].Problem;
```

這行程式碼會針對先前的問題抓取對應的答案：

```csharp
string answer = flashCardDeck[3].Answer;
```

由於的執行詳細資料與 `FlashCardDeck` 理解無關，因此 `ViewPager` 此 `FlashCardDeck` 程式碼不會列在此處。
您可以 `FlashCardDeck` 在 [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs)取得的原始程式碼。
下載此原始程式檔 (，或將程式碼複製並貼到新的 **FlashCardDeck.cs** 檔案) 並將其新增至您的專案。

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
using Android.Support.V4.App;
```

變更類別宣告， `MainActivity` 使其衍生自 `FragmentActivity` ：

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` 是衍生自 `FragmentActivity` (而不是 `Activity`) ，因為 `FragmentActivity` 知道如何管理片段的支援。 以下列程式碼取代 `OnCreate` 方法： 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

此程式碼會執行以下動作：

1. 設定 **.axml** 版面配置資源的視圖。

2. 從配置中抓取的參考 `ViewPager` 。

3. 具現化新的 `FlashCardDeck` 作為資料來源。

當您建立並執行此程式碼時，應該會看到類似下列螢幕擷取畫面的顯示： 

[![具有空白 ViewPager 的 FlashCardPager 應用程式螢幕擷取畫面](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

此時， `ViewPager` 會是空的，因為缺少用來填入的片段，且缺少可 `ViewPager` 從 **FlashCardDeck**中的資料建立這些片段的介面卡。 

在下列各節中， `FlashCardFragment` 會建立以執行每個快閃卡的功能，並建立， `FragmentPagerAdapter` 以將連接 `ViewPager` 到從中的資料建立的片段 `FlashCardDeck` 。 

## <a name="create-the-fragment"></a>建立片段

每個快閃卡片都是由稱為的 UI 片段所管理 `FlashCardFragment` 。 `FlashCardFragment`的 view 會顯示單一快閃卡所含的資訊。 的每個實例 `FlashCardFragment` 都會由裝載 `ViewPager` 。 
`FlashCardFragment`的 view 將由 `TextView` 顯示快閃卡問題文字的。 這個視圖會執行事件處理常式，以在 `Toast` 使用者按下快閃卡問題時，使用來顯示答案。 

### <a name="create-the-flashcardfragment-layout"></a>建立 FlashCardFragment 版面配置

在 `FlashCardFragment` 可以執行之前，必須先定義其版面配置。 此版面配置是單一片段的片段容器版面配置。 將新的 Android 配置新增至稱為**flashcard_layout .axml**的**資源/版面**配置。 開啟 **Resources/layout/flashcard_layout .axml** ，並將其內容取代為下列程式碼： 

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

此版面配置會定義單一快閃卡片段;每個片段都由 `TextView` 所組成，它會使用大型的 (100sp) 字型來顯示數學問題。 此文字會在快閃卡片上垂直和水準置中。 

### <a name="create-the-initial-flashcardfragment-class"></a>建立初始 FlashCardFragment 類別

加入名為 **FlashCardFragment.cs** 的新檔案，並將其內容取代為下列程式碼：

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

此程式碼 `Fragment` 會將用來顯示快閃記憶卡的基本定義設為存根。 請注意， `FlashCardFragment` 衍生自中定義的支援程式庫版本 `Fragment` `Android.Support.V4.App.Fragment` 。 此函式是空的，因此 `newInstance` factory 方法會用來建立新的，而不是建立新的函式 `FlashCardFragment` 。 

`OnCreateView`生命週期方法會建立和設定 `TextView` 。 它會擴大片段的版面配置 `TextView` ，並將放大的傳 `TextView` 回到呼叫者。 `LayoutInflater` 並 `ViewGroup` 傳遞至， `OnCreateView` 使其可以擴充版面配置。 套件組合 `savedInstanceState` 包含 `OnCreateView` 用來 `TextView` 從儲存的狀態重新建立的資料。 

的呼叫會明確放大片段的視圖 `inflater.Inflate` 。 `container`引數是視圖的父系，而旗標會 `false` 指示 inflater 避免將放大視圖新增至視圖的父系 (當您 `ViewPager` `GetItem` 稍後在本逐步解說) 中，呼叫的介面卡方法將會加入此引數。 

### <a name="add-state-code-to-flashcardfragment"></a>將狀態碼新增至 FlashCardFragment

就像活動一樣，片段具有用 `Bundle` 來儲存和取出其狀態的。 在 **FlashCardPager**中，這 `Bundle` 是用來儲存相關聯快閃卡片的問題和解答文字。 在 **FlashCardFragment.cs**中，將下列索引 `Bundle` 鍵加入至 `FlashCardFragment` 類別定義的頂端： 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

修改 `newInstance` factory 方法，讓它建立 `Bundle` 物件，並使用上述索引鍵，將傳遞的問題和解答文字儲存在片段化後的片段中： 

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

修改片段生命週期方法 `OnCreateView` ，從傳入的組合中取出此資訊，並將問題文字載入至 `TextBox` ： 

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

`answer`此處未使用變數，但稍後將事件處理常式程式碼新增至此檔案時，將會使用此變數。 

## <a name="create-the-adapter"></a>建立介面卡

`ViewPager` 使用位於與資料來源之間的介面卡控制器物件 `ViewPager` (參閱 ViewPager [adapter](~/android/user-interface/controls/view-pager/index.md#adapter) 文章) 中的圖例。 若要存取此資料， `ViewPager` 您需要提供衍生自的自訂介面卡 `PagerAdapter` 。 因為此範例使用片段，所以會使用 `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` 衍生自的 `PagerAdapter` 。 
`FragmentPagerAdapter` 將每個頁面以 `Fragment` 持續保留在片段管理員中的形式表示，只要使用者可以返回頁面即可。 當使用者透過的頁面撥動時 `ViewPager` ， `FragmentPagerAdapter` 會從資料來源中解壓縮資訊，然後使用它來建立，以便 `Fragment` `ViewPager` 顯示。 

當您執行時 `FragmentPagerAdapter` ，必須覆寫下列各項：

- **計數** &ndash; 唯讀屬性，可傳回 (頁面) 可用的視圖數目。

- **GetItem** &ndash; 傳回要針對指定頁面顯示的片段。

加入名為 **FlashCardDeckAdapter.cs** 的新檔案，並將其內容取代為下列程式碼：

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

此程式碼會將基本的執行程式程式碼端 `FragmentPagerAdapter` 。 在下列各節中，每個方法都會以工作程式碼取代。 此函式的目的是要將片段管理員傳遞給的基類的函式 `FlashCardDeckAdapter` 。 

### <a name="implement-the-adapter-constructor"></a>執行介面卡的函式

當應用程式具現化時 `FlashCardDeckAdapter` ，它會提供對片段管理員和具現化的參考 `FlashCardDeck` 。 `FlashCardDeckAdapter`在**FlashCardDeckAdapter.cs**中，將下列成員變數新增至類別的頂端： 

```csharp
public FlashCardDeck flashCardDeck;
```

將下列程式程式碼新增至函式 `FlashCardDeckAdapter` ： 

```csharp
this.flashCardDeck = flashCards;
```

這行程式碼會儲存 `FlashCardDeck` `FlashCardDeckAdapter` 將使用的實例。 

### <a name="implement-count"></a>執行計數

其實作 `Count` 相當簡單：它會傳回快閃卡片投影片中的閃爍卡數目。 以下列程式碼取代 `Count`： 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```

的 `NumCards` 屬性會傳回 `FlashCardDeck` 資料集內) 的 (數目的快閃卡片數目。 

### <a name="implement-getitem"></a>執行 GetItem

`GetItem`方法會傳回與指定位置相關聯的片段。 當 `GetItem` 針對快閃卡片中的某個位置呼叫時，會傳回 `FlashCardFragment` 已設定的，以在該位置顯示快閃卡問題。 以下列程式碼取代 `GetItem` 方法： 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

此程式碼會執行以下動作：

1. 針對指定的位置，查閱一組中的數學問題字串 `FlashCardDeck` 。 

2. 查閱投影片中指定位置的回應字串 `FlashCardDeck` 。 

3. 呼叫 `FlashCardFragment` factory 方法 `newInstance` ，並傳入快閃卡問題和答案字串。 

4. 建立並傳回新的快閃卡片 `Fragment` ，其中包含該位置的問題和解答文字。 

當在中 `ViewPager` 呈現 `Fragment` 時 `position` ，會顯示 `TextBox` 包含在 `position` 快閃卡片投影片中的數學問題字串。 

## <a name="add-the-adapter-to-the-viewpager"></a>將介面卡新增至 ViewPager

現在已完成 `FlashCardDeckAdapter` ，現在可以將它新增至 `ViewPager` 。 在 **MainActivity.cs**中，將下面這行程式碼加入至方法的結尾 `OnCreate` ：

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

此程式碼會具現化 `FlashCardDeckAdapter` ，並在 `SupportFragmentManager` 第一個引數中傳遞。  (`SupportFragmentManager` FragmentActivity 的屬性是用來取得的參考 `FragmentManager` &ndash; ，如需的詳細資訊 `FragmentManager` ，請參閱 [管理片段](~/android/platform/fragments/managing-fragments.md)。 )  

核心執行現在已完成 &ndash; 組建並執行應用程式。
您應該會看到快閃卡的第一個影像顯示在畫面上，如接下來的螢幕擷取畫面左邊所示。 向左滑動以查看更多的閃光燈卡片，然後向右滑動以移回快閃卡片牌：

[![FlashCardPager 應用程式不含呼機指標的範例螢幕擷取畫面](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>新增呼機指標

這種最基本 `ViewPager` 的執行會顯示每張牌中的每個快閃卡，但不會指出使用者在牌組中的位置。 下一步是加入 `PagerTabStrip` 。 `PagerTabStrip`會通知使用者所顯示的問題號碼，並顯示上一個和下一個快閃卡片的提示，以提供導覽內容。 

開啟 **Resources/layout/.axml** ，然後將加入 `PagerTabStrip` 至配置：

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

當您建立並執行應用程式時，您應該會看到 `PagerTabStrip` 顯示在每個快閃卡頂端的空白： 

[![不含文字的 PagerTabStrip 特寫](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)

### <a name="display-a-title"></a>顯示標題

若要將標題加入至每個頁面索引標籤，請 `GetPageTitleFormatted` 在介面卡中執行方法。 `ViewPager``GetPageTitleFormatted`如果實) 取得可在指定位置描述頁面的標題字串，則會呼叫 (。 將下列方法新增至 `FlashCardDeckAdapter` **FlashCardDeckAdapter.cs**中的類別： 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

這段程式碼會將 flash 卡片中的位置轉換成問題號碼。 產生的字串會轉換成 `String` 傳回的 JAVA `ViewPager` 。 當您使用這個新的方法來執行應用程式時，每個頁面都會在中顯示問題號碼 `PagerTabStrip` ： 

[![FlashCardPager 的螢幕擷取畫面，其中每個頁面上方都會顯示問題號碼](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

您可以來回切換，以查看顯示在每個快閃卡片頂端的 [快閃卡片] 牌中的問題號碼。 

## <a name="handle-user-input"></a>處理使用者輸入

**FlashCardPager** 在中提供一連串以片段為基礎的 flash 卡 `ViewPager` ，但還沒辦法顯示每個問題的答案。 在本節中，會將事件處理常式新增至， `FlashCardFragment` 以便在使用者按下快閃卡片問題文字時顯示答案。 

開啟 **FlashCardFragment.cs** ，並將下列程式碼加入至方法的結尾，然後 `OnCreateView` 再將視圖傳回給呼叫者： 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

此 `Click` 事件處理常式會在使用者點擊時出現的快顯通知中顯示答案 `TextBox` 。 `answer`當從傳遞給的組合讀取狀態資訊時，此變數就會稍早初始化 `OnCreateView` 。 建立並執行應用程式，然後在每個快閃卡片上點一下問題文字以查看答案： 

[![點擊數學問題時，FlashCardPager 應用程式快顯通知的螢幕擷取畫面](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

本**FlashCardPager**逐步解說中所提供的 FlashCardPager `MainActivity` 會使用衍生自 `FragmentActivity` ，但您也可以 `MainActivity` 從 (衍生， `AppCompatActivity` 而這也提供管理片段) 的支援。 若要查看 `AppCompatActivity` 範例，請參閱範例庫中的 [FlashCardPager](/samples/xamarin/monodroid-samples/userinterface-flashcardpager) 。

## <a name="summary"></a>摘要

本逐步解說提供如何使用來建立基本應用程式的逐步範例 `ViewPager` `Fragment` 。 它會顯示包含快閃卡片問題和答案的範例資料來源、 `ViewPager` 顯示快閃卡片的版面配置，以及將 `FragmentPagerAdapter` 連接 `ViewPager` 到資料來源的子類別。 為了協助使用者流覽快閃卡片，其中包含說明如何新增的指示， `PagerTabStrip` 以顯示每個頁面頂端的問題號碼。 最後，新增事件處理常式代碼，以便在使用者按下快閃卡問題時顯示答案。 

## <a name="related-links"></a>相關連結

- [FlashCardPager (範例) ](/samples/xamarin/monodroid-samples/userinterface-flashcardpager)