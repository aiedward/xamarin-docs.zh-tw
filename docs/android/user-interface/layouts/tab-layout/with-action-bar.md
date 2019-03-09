---
title: 使用 ActionBar 的索引標籤式版面配置
description: 本指南介紹，並說明如何使用 ActionBar Api 來建立 Xamarin.Android 應用程式中的索引標籤式的使用者介面。
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 6ce8099aa4230a11a12f4fe8aeffe850f9ef2ce9
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670996"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>使用 ActionBar 的索引標籤式版面配置

_本指南介紹，並說明如何使用 ActionBar Api 來建立 Xamarin.Android 應用程式中的索引標籤式的使用者介面。_


## <a name="overview"></a>總覽

動作列是 Android 的 UI 的模式，用來提供一致的使用者介面的索引標籤、 應用程式身分識別、 功能表和搜尋等重要功能。 在 Android 3.0 （API 層級 11） 中，Google 導入了 ActionBar 的 Api，以 Android 平台。 ActionBar Api 導入 UI 佈景主題，以提供一致的外觀及操作和類別，可讓索引標籤式的使用者介面。 本指南會討論如何將 Xamarin.Android 應用程式中的動作列的索引標籤。 它也會討論如何使用 Android 支援程式庫 v7，到目標 Android 2.3 至 Android 2.1 的 Xamarin.Android 應用程式的向下移植 ActionBar 索引標籤。 

請注意，`Toolbar`是較新且更通用的動作列元件，您應該使用而不是`ActionBar`(`Toolbar`旨在取代`ActionBar`)。 如需詳細資訊，請參閱 <<c0> [ 工具列](~/android/user-interface/controls/tool-bar/index.md)。 



## <a name="requirements"></a>需求

任何的 Xamarin.Android 應用程式的目標 API 層級 11 (Android 3.0) 或更高版本 ActionBar Api 存取原生的 Android Api 的一部分的人員。 

一些 ActionBar 的 Api 已傳回移植到 API 層級 7 (Android 2.1)，而且可透過[V7 AppCompat 媒體櫃](https://developer.android.com/tools/support-library/features.html#v7-appcompat)，其中會提供給 Xamarin.Android 應用程式可透過[Xamarin Android 支援程式庫-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)封裝。



## <a name="introducing-tabs-in-the-actionbar"></a>簡介中 ActionBar 的索引標籤

動作列會嘗試同時顯示所有其索引標籤，並讓所有索引標籤中最寬索引標籤的標籤的寬度為基礎的大小相等。 下列螢幕擷取畫面說明這種情況： 

![範例的螢幕擷取畫面 ActionBar 所有相等大小的索引標籤所示](with-action-bar-images/image1.png)

當 ActionBar 無法顯示所有索引標籤時，它會設定可水平捲動檢視中的索引標籤。 使用者可能撥動左邊或右邊，以查看剩餘的索引標籤。 從 Google Play 這個螢幕擷取畫面會顯示這個範例： 

![水平捲動方式檢視中的索引標籤的範例螢幕擷取畫面](with-action-bar-images/image2.png)

每個索引標籤，動作列中的，應該與相關聯[*片段*](~/android/platform/fragments/index.md)。 當使用者選取索引標籤時，應用程式會顯示 [] 索引標籤相關聯的片段。ActionBar 不負責向使用者顯示適當的片段。 相反地，ActionBar 會通知有關透過實作 ActionBar.ITabListener 介面的類別 索引標籤中的狀態變更的應用程式。 這個介面會提供三個索引標籤的狀態變更時，Android 會叫用的回呼方法： 

-  **OnTabSelected** -當使用者選取 [] 索引標籤，會呼叫這個方法。它應該會顯示，片段。

-  **OnTabReselected** -[] 索引標籤已選取，但一次由使用者選取時，會呼叫這個方法。 此回呼是通常用來重新整理/更新顯示的片段。

-  **OnTabUnselected** -當使用者選取另一個索引標籤，會呼叫這個方法。此回呼用來顯示片段中儲存的狀態，才會消失。

Xamarin.Android 會包裝`ActionBar.ITabListener`上的事件與`ActionBar.Tab`類別。 應用程式可能會將一或多個這些事件的事件處理常式。 有三個事件 (一個用於每個方法`ActionBar.ITabListener`) 將會引發的動作列 索引標籤： 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>加入 ActionBar 的索引標籤

ActionBar 是原生 Android 3.0 （API 層級 11） 及更高版本，則使用任何 Xamarin.Android 應用程式，此 API 至少為目標。 

下列步驟說明如何將 ActionBar 的索引標籤新增至 Android 活動： 

1. 在`OnCreate`活動方法&ndash;*之前初始化任何 UI widgets* &ndash;應用程式必須設定`NavigationMode`上`ActionBar`至`ActionBar.NavigationModeTabs`這段程式碼所示程式碼片段：

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. 建立新的索引標籤使用`ActionBar.NewTab()`。

3. 指定事件處理常式，或提供自訂`ActionBar.ITabListener`實作所將回應使用者互動 ActionBar 的索引標籤時所引發的事件。

4. 加入在上一個步驟中建立的索引標籤`ActionBar`。


下列程式碼是一個範例使用下列步驟將索引標籤新增至使用事件處理常式來回應狀態變更的應用程式： 

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
                       };
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);
}
```


#### <a name="event-handlers-vs-actionbaritablistener"></a>事件處理常式 vs ActionBar.ITabListener

應用程式應該使用事件處理常式和`ActionBar.ITabListener`針對不同的案例。 事件處理常式，並提供一段特定的語法便利性;它們讓您不用建立類別並實作`ActionBar.ITabListener`。 這種便利性會是有代價&ndash;Xamarin.Android 會為您，建立一個類別和實作來執行此轉換`ActionBar.ITabListener`您。 當應用程式具有有限的數目的索引標籤時，這是正常的。 

當處理許多索引標籤上，或共用 ActionBar 的索引標籤之間的一般功能，它可能會更有效率的記憶體和效能，以建立自訂的類別，實作方面`ActionBar.ITabListener`，及共用單一類別的執行個體。 這會減少 GRF 的 Xamarin.Android 應用程式所使用的數目。 



### <a name="backwards-compatibility-for-older-devices"></a>回溯相容性的較舊的裝置

[Android 支援程式庫 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)後連接埠 ActionBar 的索引標籤，以 Android 2.1 （API 層級 7）。 此元件新增至專案之後，會在 Xamarin.Android 應用程式中存取索引標籤。

若要使用 ActionBar，活動必須子類別化`ActionBarActivity`，並使用 AppCompat 佈景主題，如下列程式碼片段所示：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

活動可能會取得從其 ActionBar 的參考`ActionBarActivity.SupportingActionBar`屬性。 下列程式碼片段說明如何設定活動中 ActionBar 的範例：

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


## <a name="summary"></a>總結

本指南中，我們會討論如何在 Xamarin.Android 中使用 ActionBar 建立索引標籤式的使用者介面。 我們將探討如何將索引標籤新增至 ActionBar 活動如何互動 索引標籤上的事件，透過`ActionBar.ITabListener`介面。 此外，我們還瞭解到 Android 支援程式庫 v7 AppCompat 封裝反向 ActionBar 的 Android 的較舊版本的索引標籤。 


## <a name="related-links"></a>相關連結

- [ActionBarTabs （範例）](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [工具列](~/android/user-interface/controls/tool-bar/index.md)
- [片段](~/android/platform/fragments/index.md)
- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](https://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [動作列模式](https://developer.android.com/design/patterns/actionbar.html)
- [Android v7 AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Xamarin.Android 支援程式庫 v7 AppCompat NuGet 封裝](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
