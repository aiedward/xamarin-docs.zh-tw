---
title: 建立片段
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 0e8d3748c7ddd337cf2f27f5b272b208e79d503a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027509"
---
# <a name="creating-a-fragment"></a>建立片段

若要建立片段，類別必須繼承自 `Android.App.Fragment`，然後覆寫 `OnCreateView` 方法。 當您將片段放在螢幕上時，主控活動會呼叫 `OnCreateView`，而且會傳回 `View`。 一般 `OnCreateView` 會藉由因而誇大配置檔案，然後將它附加至父容器，來建立此 `View`。 容器的特性很重要，因為 Android 會將父系的版面配置參數套用至片段的 UI。 下列範例會加以說明：

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

上述程式碼會將 view `Resource.Layout.Example_Fragment`擴大，並將其當做子視圖加入 `ViewGroup` 容器。

> [!NOTE]
> 片段子類別必須有公用預設無引數的函式。

## <a name="adding-a-fragment-to-an-activity"></a>將片段新增至活動

有兩種方式可將片段裝載于活動內：

- 您可以使用 `<Fragment>` 標記，以宣告方式在 `.axml` 版面配置檔案內**使用 &ndash; 片段**。

- **以**程式設計方式 &ndash; 片段也可以使用 `FragmentManager` 類別的 API 來動態具現化。

本指南稍後會討論透過 `FragmentManager` 類別的程式設計使用方式。

### <a name="using-a-fragment-declaratively"></a>以宣告方式使用片段

在配置內加入片段需要使用 `<fragment>` 標記，然後藉由提供 `class` 屬性或 `android:name` 屬性來識別片段。 下列程式碼片段顯示如何使用 `class` 屬性來宣告 `fragment`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

下一個程式碼片段說明如何使用 `android:name` 屬性來識別片段類別，以宣告 `fragment`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

建立活動時，Android 會將配置檔案中指定的每個片段具現化，並插入從 `OnCreateView` 建立的視圖來取代 `Fragment` 元素。
以宣告方式加入至活動的片段是靜態的，並且會保留在活動上，直到終結為止;在所附加活動的存留期內，不可能動態地取代或移除這類片段。

每個片段都必須指派一個唯一識別碼：

- **android： id** &ndash; 與版面配置檔案中的其他 UI 元素相同，這是唯一的識別碼。

- **android：標記**&ndash; 此屬性是唯一的字串。

如果先前的兩個方法都未使用，則片段會假設容器視圖的識別碼。 在下列範例中，沒有提供 `android:id` 或 `android:tag`，Android 會將識別碼 `fragment_container` 指派給片段：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>封裝名稱案例

Android 不允許在封裝名稱中有大寫字元;如果封裝名稱包含大寫字元，則嘗試擴大視圖時，將會擲回例外狀況。 不過，Xamarin 較容許，而且可容忍命名空間中的大寫字元。

例如，下列兩個程式碼片段都適用于 Xamarin。 不過，第二個程式碼片段會導致以 JAVA 為基礎的純 Android 應用程式擲回 `android.view.InflateException`。

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OR

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

## <a name="fragment-lifecycle"></a>片段生命週期

片段有自己的生命週期，其與[裝載活動的生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)不相關，但仍受到影響。
例如，當活動暫停時，其所有關聯的片段都會暫停。 下圖概述片段的生命週期。

[說明片段生命週期的 ![流程圖](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)

### <a name="fragment-creation-lifecycle-methods"></a>片段建立生命週期方法

下列清單顯示建立片段時，其生命週期中各種回呼的流程：

- 將片段建立為視圖配置的一部分時，會呼叫 **`OnInflate()`** &ndash;。 這可能會在從 XML 配置檔案以宣告方式建立片段之後立即呼叫。 此片段尚未與其活動相關聯，但是來自 view 階層的活動 **、組合**和**AttributeSet**會當做參數傳入。 這個方法最適合用來剖析**AttributeSet** ，以及用來儲存片段稍後可能會用到的屬性。

- **`OnAttach()`** 在片段與活動相關聯後呼叫 &ndash;。 這是當片段備妥可供使用時，所要執行的第一個方法。 一般來說，片段不應執行一個函式，或覆寫預設的函式。 片段所需的任何元件都應該在這個方法中初始化。

- **`OnCreate()`** 由活動呼叫 &ndash; 以建立片段。 呼叫這個方法時，裝載活動的視圖階層可能不會完全具現化，因此片段不應依賴活動的視圖階層架構的任何部分，直到稍後在片段的生命週期中。 例如，請勿使用這個方法對應用程式的 UI 進行任何調整或調整。 這是片段可以開始收集所需資料的最早時間。 此時，片段會在 UI 執行緒中執行，因此請避免任何冗長的處理，或在背景執行緒上執行該處理。 如果呼叫**SetRetainInstance （true）** ，則可以略過這個方法。
    下面將更詳細地說明此替代方案。

- **`OnCreateView()`** &ndash; 會建立片段的視圖。
    一旦活動的**OnCreate （）** 方法完成後，就會呼叫這個方法。 此時，可以安全地與活動的視圖階層進行互動。 這個方法應該會傳回片段將使用的 view。

- **OnCreate**已由裝載活動完成後呼叫 &ndash;。 **`OnActivityCreated()`**
    此時應該會對使用者介面進行最後的調整。

- 在包含的活動已繼續後呼叫 **`OnStart()`** &ndash;。 這會讓使用者看到片段。 在許多情況下，片段會包含在活動的**OnStart （）** 方法中的程式碼。

- **`OnResume()`** &ndash; 這是在使用者可以與片段互動之前呼叫的最後一個方法。 在此方法中應該執行的程式碼種類範例，會啟用使用者可能與之互動的裝置功能，例如位置服務的相機。 這類服務可能會造成過多的電池耗盡，而應用程式應該將其使用方式降到最低，以維持電池壽命。

### <a name="fragment-destruction-lifecycle-methods"></a>片段銷毀生命週期方法

下一份清單說明被終結的生命週期方法，會被終結：

- **`OnPause()`** &ndash; 使用者無法再與片段互動。 這種情況存在的原因是其他片段作業正在修改此片段，或主控活動已暫停。 裝載此片段的活動可能仍然可見，也就是說，焦點的活動是部分透明的，或不會佔用全螢幕。 當此方法變成作用中時，這就是使用者離開片段的第一個指示。 片段應會儲存任何變更。

- **`OnStop()`** &ndash; 不會再看到該片段。 主機活動可能已停止，或片段作業正在活動中修改。 此回呼的用途與**活動**的相同。

- **`OnDestroyView()`** &ndash; 會呼叫這個方法來清除與此視圖相關聯的資源。 當與片段相關聯的視圖已終結時，就會呼叫此。

- **`OnDestroy()`** &ndash; 當片段不再使用時，會呼叫這個方法。 它仍與活動相關聯，但該片段已不再運作。 這個方法應該釋放任何由片段使用的資源，例如可能用於攝影機的[**SurfaceView**](xref:Android.Views.SurfaceView) 。 如果呼叫**SetRetainInstance （true）** ，則可以略過這個方法。 下面將更詳細地說明此替代方案。

- **`OnDetach()`** &ndash; 這個方法只會在片段不再與活動相關聯之前呼叫。 片段的視圖階層已不存在，而且該片段使用的所有資源都應該在此時釋放。

### <a name="using-setretaininstance"></a>使用 SetRetainInstance

片段可能會指定如果重新建立活動，則不應完全終結它。 `Fragment` 類別會針對此用途提供 `SetRetainInstance` 的方法。 如果將 `true` 傳遞至這個方法，則在重新開機活動時，將會使用相同的片段實例。 如果發生這種情況，則會叫用所有回呼方法，但 `OnCreate` 和 `OnDestroy` 生命週期回呼除外。 上面顯示的生命週期圖表中會說明此程式（以綠色虛線表示）。

## <a name="fragment-state-management"></a>片段狀態管理

片段可以使用 `Bundle`的實例，在片段生命週期期間儲存和還原其狀態。 配套可讓片段將資料儲存為索引鍵/值組，並適用于不需要太多記憶體的簡單資料。 片段可以透過呼叫 `OnSaveInstanceState`來儲存其狀態：

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

建立片段的新實例時，儲存在 `Bundle` 中的狀態會透過新實例的 `OnCreate`、`OnCreateView`和 `OnActivityCreated` 方法，提供給新的實例使用。
下列範例示範如何從 `Bundle`取出 `current_choice` 的值：

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

覆寫 `OnSaveInstanceState` 是一種適當的機制，可跨方向變更（例如上述範例中的 `current_choice` 值）儲存片段中的暫時性資料。 不過，`OnSaveInstanceState` 的預設執行會負責針對已指派識別碼的每個視圖，儲存 UI 中的暫時性資料。 例如，查看在 XML 中定義了 `EditText` 元素的應用程式，如下所示：

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

由於 `EditText` 控制項已指派 `id`，因此當呼叫 `OnSaveInstanceState` 時，片段會自動將資料儲存在 widget 中。

### <a name="bundle-limitations"></a>配套限制

雖然使用 `OnSaveInstanceState` 可讓您輕鬆地儲存暫時性資料，但使用此方法有一些限制：

- 如果未將片段新增至後端堆疊，則當使用者按下 [**上一頁**] 按鈕時，將不會還原其狀態。

- 當配套用來儲存資料時，該資料會進行序列化。 這可能會導致處理延遲。

## <a name="contributing-to-the-menu"></a>參與功能表

片段可能會將專案提供給其裝載活動的功能表。
活動會先處理功能表項目。 如果活動沒有處理常式，則會將事件傳遞至片段，然後處理它。

若要將專案加入至活動的功能表，片段必須執行兩項動作。
首先，片段必須執行方法 `OnCreateOptionsMenu` 並將其專案放入功能表中，如下列程式碼所示：

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

先前程式碼片段中的功能表會從下列 XML 擴充，位於檔案 `menu_fragment_vehicle_list.xml`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

接下來，此片段必須呼叫 `SetHasOptionsMenu(true)`。 此方法的呼叫會向 Android 公告，而該片段具有可參與 [選項] 功能表的功能表項目。 除非呼叫此方法，否則不會將片段的功能表項目加入至活動的 [選項] 功能表。 這通常會在生命週期方法 `OnCreate()`中完成，如以下的程式碼片段所示：

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

下列畫面顯示此功能表的外觀：

[顯示功能表項目的 [我的旅程] 應用程式 ![範例螢幕擷取畫面](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
