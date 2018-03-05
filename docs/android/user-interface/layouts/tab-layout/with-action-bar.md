---
title: "索引標籤式的配置與項"
description: "本指南介紹，並說明如何使用 Xamarin.Android 應用程式中建立索引標籤式的使用者介面項應用程式開發介面。"
ms.topic: article
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 14abb7a4b85b493bb0ab96a982d989fad783fabd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="tabbed-layouts-with-the-actionbar"></a>索引標籤式的配置與項

_本指南介紹，並說明如何使用 Xamarin.Android 應用程式中建立索引標籤式的使用者介面項應用程式開發介面。_

<a name="Overview" />

## <a name="overview"></a>總覽

在動作列是 Android UI 的模式，用來提供一致的使用者介面的主要功能，例如索引標籤，應用程式識別、 功能表和搜尋。 在 Android 3.0 （API 層級 11）、 Google 介紹 Android 平台項應用程式開發介面。 項 Api 導入 UI 佈景主題，以提供一致的外觀及操作和類別，可以讓索引標籤式的使用者介面。 本指南會討論如何 Xamarin.Android 應用程式中加入動作列索引標籤。 它也會討論如何使用 Android 支援程式庫 v7 到 Xamarin.Android 應用程式的目標 Android 2.3 至 Android 2.1 backport 項 索引標籤。 

請注意，`Toolbar`是較新且更通用的動作列元件，您應該使用而不是`ActionBar`(`Toolbar`設計用來取代`ActionBar`)。 如需詳細資訊，請參閱[工具列](~/android/user-interface/controls/tool-bar/index.md)。 


<a name="Requirements" />

## <a name="requirements"></a>需求

任何 Xamarin.Android 應用程式目標 API 層級 11 (Android 3.0) 或更高版本有項 Api 存取原生 Android Api 的一部分。 

部分項應用程式開發介面已回移植到應用程式開發介面層級 7 (Android 2.1)，且可透過[V7 AppCompat 程式庫](http://developer.android.com/tools/support-library/features.html#v7-appcompat)，這供透過 Xamarin.Android 應用程式[Xamarin Android 支援程式庫-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)封裝。


<a name="Introducing_tabs_in_the_ActionBar" />

## <a name="introducing-tabs-in-the-actionbar"></a>簡介項中的索引標籤

動作列會同時顯示所有其 索引標籤，並根據最寬的索引標籤寬度的大小相等的所有索引標籤。 下列螢幕擷取畫面說明： 

![範例的螢幕擷取畫面項與所有大小相等索引標籤顯示](with-action-bar-images/image1.png)

當項無法顯示所有索引標籤時，它會設定可水平捲動檢視中的索引標籤。 使用者可能撥動左邊或右邊才能看到剩餘的索引標籤。 從 Google Play 這個螢幕擷取畫面顯示此動作的範例： 

![索引標籤中水平捲動檢視範例螢幕擷取畫面](with-action-bar-images/image2.png)

在動作列中的每個索引標籤應該與相關聯[*片段*](~/android/platform/fragments/index.md)。 當使用者選取索引標籤時，應用程式會顯示 [] 索引標籤相關聯的片段。項不負責向使用者顯示適當的片段。 相反地，項將會通知應用程式需透過實作 ActionBar.ITabListener 介面的類別 索引標籤中的狀態變更。 這個介面會提供三個索引標籤的狀態變更時，Android 將會叫用的回呼方法： 

-  **OnTabSelected** -當使用者選取 [] 索引標籤，會呼叫這個方法。它應該會顯示在片段。

-  **OnTabReselected** -[] 索引標籤已選取，但一次由使用者選取時，會呼叫這個方法。 此回呼通常用來重新整理/更新顯示的片段。

-  **OnTabUnselected** -當使用者選取另一個索引標籤，會呼叫這個方法。此回呼用來顯示片段中儲存的狀態之前它就會消失。

Xamarin.Android 包裝`ActionBar.ITabListener`上的事件與`ActionBar.Tab`類別。 應用程式可能指派給一或多個事件的事件處理常式。 有三個事件 (在每個方法的其中一個`ActionBar.ITabListener`) 將會引發在動作列的索引標籤： 

-  TabSelected
-  TabReselected
-  TabUnselected


<a name="Adding_Tabs_to_the_ActionBar" />

### <a name="adding-tabs-to-the-actionbar"></a>將索引標籤加入至項

項是原生 Android 3.0 （API 層級 11） 和更高版本，並可用於任何 Xamarin.Android 應用程式為目標的最小值為這個應用程式開發介面。 

下列步驟說明如何將項 索引標籤新增至 Android 的活動： 

1. 在`OnCreate`方法的活動&ndash;*之前初始化任何 UI widgets* &ndash;應用程式必須設定`NavigationMode`上`ActionBar`至`ActionBar.NavigationModeTabs`這段程式碼所示程式碼片段：

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. 建立新的索引標籤使用`ActionBar.NewTab()`。

3. 指定事件處理常式，或提供自訂`ActionBar.ITabListener`實作所將回應使用者互動項 索引標籤時所引發的事件。

4. 加入前一個步驟中所建立的索引標籤`ActionBar`。


下列程式碼是一個範例使用事件處理常式來回應狀態變更的應用程式中加入索引標籤中使用下列步驟： 

```csharp
protected override void OnCreate(Bundle bundle)
{
    ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
    SetContentView(Resource.Layout.Main);

    ActionBar.Tab tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab1_text));
    tab.SetIcon(Resource.Drawable.tab1_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       }
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       }
    ActionBar.AddTab(tab);
}
```

<a name="Event_Handlers_vs_ActionBar.ITabListener" />

#### <a name="event-handlers-vs-actionbaritablistener"></a>事件處理常式與 ActionBar.ITabListener

應用程式應該使用事件處理常式和`ActionBar.ITabListener`針對不同的案例。 事件處理常式，並提供特定數量的語法便利性。它們會儲存您不必建立類別並實作`ActionBar.ITabListener`。 這種便利性會帶來成本&ndash;Xamarin.Android 執行此轉換的您，建立一個類別和實作`ActionBar.ITabListener`您。 當應用程式具有有限的數目的索引標籤時，這是正常的。 

當處理多個索引標籤上，共用通用項 索引標籤之間的功能，也可以更有效率的記憶體和效能，以建立自訂的類別，實作`ActionBar.ITabListener`，及共用單一類別的執行個體。 這會降低 GREF 的 Xamarin.Android 應用程式所使用的數目。 


<a name="Backwards_Compatibility_for_Older_Devices" />

### <a name="backwards-compatibility-for-older-devices"></a>回溯相容性較舊的裝置

[Android 支援程式庫 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)後連接埠項索引標籤，以 Android 2.1 (API level 7)。 此元件加入至專案之後，會在 Xamarin.Android 應用程式中存取索引標籤。

若要使用項，活動必須子類別化`ActionBarActivity`並使用 AppCompat 佈景主題，如下列程式碼片段所示：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

活動可能會取得從其項參考`ActionBarActivity.SupportingActionBar`屬性。 下列程式碼片段將說明在活動中項設定的範例：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity : ActionBarActivity, ActionBar.ITabListener
{
    static readonly string Tag = "ActionBarTabsSupport";

    public void OnTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Optionally refresh/update the displayed tab.
        Log.Debug(Tag, "The tab {0} was re-selected.", tab.Text);
    }

    public void OnTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Display the fragment the user should see
        Log.Debug(Tag, "The tab {0} has been selected.", tab.Text);
    }

    public void OnTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Save any state in the displayed fragment.
        Log.Debug(Tag, "The tab {0} as been unselected.", tab.Text);
    }

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SupportActionBar.NavigationMode = ActionBar.NavigationModeTabs;
        SetContentView(Resource.Layout.Main);
    }

    void AddTabToActionBar(int labelResourceId, int iconResourceId)
    {
        ActionBar.Tab tab = SupportActionBar.NewTab()
                                            .SetText(labelResourceId)
                                            .SetIcon(iconResourceId)
                                            .SetTabListener(this);
        SupportActionBar.AddTab(tab);
    }
}
```

<a name="Summary" />

## <a name="summary"></a>總結

本指南中，我們會討論如何在使用項 Xamarin.Android 中建立的索引標籤式的使用者介面。 我們涵蓋如何將索引標籤加入至項和活動可以互動方式 索引標籤事件透過`ActionBar.ITabListener`介面。 我們也會看到 Android 支援程式庫 v7 AppCompat 封裝 backports 項較舊版本的 Android 的索引標籤。 


## <a name="related-links"></a>相關連結

- [ActionBarTabs （範例）](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [工具列](~/android/user-interface/controls/tool-bar/index.md)
- [片段](~/android/platform/fragments/index.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](http://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [動作列模式](http://developer.android.com/design/patterns/actionbar.html)
- [Android v7 AppCompat](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Xamarin.Android 支援程式庫 v7 AppCompat NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
