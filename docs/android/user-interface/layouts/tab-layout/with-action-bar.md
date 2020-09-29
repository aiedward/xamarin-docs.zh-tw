---
title: 使用 ActionBar 的索引標籤式版面配置
description: 本指南介紹和說明如何使用 ActionBar Api 在 Xamarin Android 應用程式中建立索引標籤式使用者介面。
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: b1e6fadb52f6009e28d18143ac24c5180dae037c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457038"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>使用 ActionBar 的索引標籤式版面配置

_本指南介紹和說明如何使用 ActionBar Api 在 Xamarin Android 應用程式中建立索引標籤式使用者介面。_

## <a name="overview"></a>概觀

動作列是 Android UI 模式，用來為索引標籤、應用程式識別、功能表和搜尋等主要功能提供一致的使用者介面。 在 Android 3.0 (API 層級 11) 中，Google 將 ActionBar Api 引進至 Android 平臺。 ActionBar Api 引進 UI 主題，以提供一致的外觀和風格，以及允許索引標籤式使用者介面的類別。 本指南將討論如何將動作列索引標籤新增至 Xamarin Android 應用程式。 它也會討論如何使用 Android 支援程式庫 v7，將 ActionBar 索引標籤將至 Xamarin android 應用程式（以 Android 2.1 至 Android 2.3 為目標）。 

請注意， `Toolbar` 您應該使用較新且更一般化的動作列元件，而不是 `ActionBar` (`Toolbar` 是設計來取代 `ActionBar`) 。 如需詳細資訊，請參閱 [工具列](~/android/user-interface/controls/tool-bar/index.md)。 

## <a name="requirements"></a>需求

任何以 API 層級 11 (Android 3.0) 或更高版本為目標的 Xamarin Android 應用程式，都可以存取 ActionBar Api 做為原生 Android Api 的一部分。 

某些 ActionBar Api 已回到 (Android 2.1) 的 API 層級7，可透過 [V7 AppCompat 程式庫](https://developer.android.com/tools/support-library/features.html#v7-appcompat)取得，可透過 [xamarin Android 支援程式庫-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) 套件提供給 xamarin. Android 應用程式使用。

## <a name="introducing-tabs-in-the-actionbar"></a>ActionBar 中的索引標籤簡介

動作列會嘗試同時顯示其所有索引標籤，並根據最寬索引標籤標籤的寬度，使所有索引標籤的大小相等。 下列螢幕擷取畫面會說明這一點： 

![顯示所有大小相同索引標籤的 ActionBar 範例螢幕擷取畫面](with-action-bar-images/image1.png)

當 ActionBar 無法顯示所有索引標籤時，它會在水準可滾動的視圖中設定索引標籤。 使用者可以向左或向右滑動以查看其餘索引標籤。 Google Play 的此螢幕擷取畫面顯示此範例： 

![水準可滾動視圖中索引標籤的範例螢幕擷取畫面](with-action-bar-images/image2.png)

動作列中的每個索引標籤都應與 [*片段*](~/android/platform/fragments/index.md)相關聯。 當使用者選取索引標籤時，應用程式會顯示與索引標籤相關聯的片段。ActionBar 不負責向使用者顯示適當的片段。 相反地，ActionBar 會透過實 ActionBar ITabListener 介面的類別，在索引標籤中通知應用程式狀態變更。 此介面提供三個回呼方法，在索引標籤的狀態變更時，Android 會叫用此方法： 

- **OnTabSelected** -當使用者選取索引標籤時，就會呼叫這個方法。它應該會顯示片段。

- **OnTabReselected** -當已選取索引標籤，但使用者再次選取此方法時，就會呼叫這個方法。 此回呼通常用來重新整理/更新所顯示的片段。

- **OnTabUnselected** -當使用者選取其他索引標籤時，就會呼叫這個方法。此回呼是用來在顯示的片段中儲存狀態，然後才會消失。

Xamarin 會在 `ActionBar.ITabListener` 類別上將包含事件包裝 `ActionBar.Tab` 。 應用程式可能會將事件處理常式指派給其中一或多個事件。 有三個事件 () 中的每個方法都有一個動作列索引標籤 `ActionBar.ITabListener` 會引發： 

- TabSelected
- TabReselected
- TabUnselected

### <a name="adding-tabs-to-the-actionbar"></a>將索引標籤新增至 ActionBar

ActionBar 是 Android 3.0 的原生 (API 層級 11) 和更高版本，可供任何以此 API 為目標的任何 Xamarin. Android 應用程式使用。 

下列步驟說明如何將 ActionBar 索引標籤新增至 Android 活動： 

1. 在 `OnCreate` &ndash; *初始化任何 UI Widget 之前*的活動方法中， &ndash; 應用程式必須將上的設為 `NavigationMode` `ActionBar` ，如下列 `ActionBar.NavigationModeTabs` 程式碼片段所示：

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. 使用建立新的索引標籤 `ActionBar.NewTab()` 。

3. 指派事件處理常式，或提供自訂的 `ActionBar.ITabListener` 執行，以回應使用者與 [ActionBar] 索引標籤互動時引發的事件。

4. 將在上一個步驟中建立的索引標籤加入至 `ActionBar` 。

下列程式碼是使用這些步驟將索引標籤新增至應用程式的其中一個範例，該應用程式會使用事件處理常式來回應狀態變更： 

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

#### <a name="event-handlers-vs-actionbaritablistener"></a>事件處理常式與 ActionBar. ITabListener

應用程式應該使用事件處理常式和 `ActionBar.ITabListener` 不同的案例。 事件處理常式確實提供特定數量的語法便利性;它們可讓您不必建立類別和實行 `ActionBar.ITabListener` 。 這項便利性的確是成本 &ndash; Xamarin。 Android 會為您執行這項轉換，建立一個類別並 `ActionBar.ITabListener` 為您執行。 當應用程式的索引標籤數目有限時，這就很好用。 

處理許多索引標籤，或在 ActionBar 索引標籤之間共用一般功能時，在記憶體和效能方面可能更有效率，以建立可執行檔自訂類別， `ActionBar.ITabListener` 並共用類別的單一實例。 這會減少 Xamarin Android 應用程式所使用的 GRF 數目。 

### <a name="backwards-compatibility-for-older-devices"></a>舊版裝置的回溯相容性

[Android 支援程式庫 V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)後端埠 ActionBar 索引標籤至 android 2.1 (API 層級 7) 。 一旦將此元件新增至專案，就可以在 Xamarin. Android 應用程式中存取索引標籤。

若要使用 ActionBar，活動必須子類別 `ActionBarActivity` 並使用 AppCompat 主題，如下列程式碼片段所示：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

活動可能會從屬性取得其 ActionBar 的參考 `ActionBarActivity.SupportingActionBar` 。 下列程式碼片段說明在活動中設定 ActionBar 的範例：

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

## <a name="summary"></a>摘要

在本指南中，我們討論了如何使用 ActionBar 在 Xamarin 中建立索引標籤式使用者介面。 我們已討論如何將索引標籤新增至 ActionBar，以及活動如何透過介面與 tab 事件互動 `ActionBar.ITabListener` 。 我們也已瞭解 Android 支援程式庫 v7 AppCompat 套件如何將 ActionBar 索引標籤反向移植至舊版 Android。 

## <a name="related-links"></a>相關連結

- [ActionBarTabs (範例) ](/samples/xamarin/monodroid-samples/userinterface-actionbartabs)
- [工具列](~/android/user-interface/controls/tool-bar/index.md)
- [片段](~/android/platform/fragments/index.md)
- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](https://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [動作列模式](https://developer.android.com/design/patterns/actionbar.html)
- [Android v7 AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Xamarin. Android 支援程式庫 v7 AppCompat NuGet 套件](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)