---
title: "建立的片段"
ms.topic: article
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/07/2018
ms.openlocfilehash: c1dd3495b0d7f76197126094cfd10e50d0ca760d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-fragment"></a>建立的片段

若要建立的片段，類別必須繼承自`Android.App.Fragment`，然後覆寫`OnCreateView`方法。 `OnCreateView` 將 「 裝載 」 活動所呼叫時開始，將片段放在畫面上，並會傳回`View`。 一般`OnCreateView`建立這個`View`因而誇大配置檔案，然後將它連接至父容器。 容器的特性是很重要，因為 Android 將會套用至片段的 UI 的父代的配置參數。 下面這個範例可說明這點：

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

上述程式碼將會增加檢視`Resource.Layout.Example_Fragment`，並新增為子檢視，以`ViewGroup`容器。


> [!NOTE]
> **注意：**片段子類別沒有引數建構函式都必須有公用的預設值。

## <a name="adding-a-fragment-to-an-activity"></a>加入到活動中的片段

有兩種方式，可能會裝載片段內的活動：

-   **以宣告方式**&ndash;片段可以用於以宣告方式在`.axml`版面配置檔案使用`<Fragment>`標記。

-   **以程式設計方式**&ndash;片段可以也具現化以動態方式使用`FragmentManager`類別的應用程式開發介面。

以程式設計方式透過使用`FragmentManager`類別將在本指南稍後會討論。

### <a name="using-a-fragment-declaratively"></a>以宣告方式使用片段

加入版面配置中的片段時，需要使用`<fragment>`標記，然後藉由提供 識別片段`class`屬性或`android:name`屬性。 下列程式碼片段示範如何使用`class`屬性來宣告`fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

這個下一步的程式碼片段示範如何宣告`fragment`使用`android:name`屬性來識別片段類別：

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Android 建立活動時，會具現化每個配置檔案中指定的片段，並插入建立從檢視`OnCreateView`取代`Fragment`項目。
以宣告方式加入至活動的片段都是靜態，然後終結; 之前，將會保留在活動您不可以動態地取代或移除這類片段来附加之活動的存留期間。

每個片段都必須指派唯一的識別項：

-  **android: id** &ndash;因為配置檔案中其他 UI 項目時，這是唯一的識別碼。

-  **android： 標記**&ndash;此屬性是唯一的字串。

如果先前的兩個方法都不使用時，片段會假設容器檢視的識別碼。 在下列範例中在兩者皆非`android:id`也`android:tag`提供 Android 將會指派識別碼`fragment_container`片段：

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

### <a name="package-name-case"></a>封裝名稱的大小寫

Android 不允許在封裝的名稱; 大寫字元嘗試擴大檢視，如果封裝名稱包含大寫字元時，它將會擲回例外狀況。 不過，Xamarin.Android 比較能夠容許，而且將會容許命名空間中的大寫字元。

比方說，這兩個下列的程式碼片段將使用 Xamarin.Android。 不過，第二個程式碼片段會導致`android.view.InflateException`純 Java 型 Android 應用程式所擲回。

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OR

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>片段生命週期

片段有稍微獨立的但仍受自己週期[裝載活動的生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)。
比方說，當活動暫停時，其相關聯的片段的所有已暫停。 下圖概述片段的生命週期。

[![說明片段生命週期的流程圖](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png)


### <a name="fragment-creation-lifecycle-methods"></a>片段建立存留週期方法

下列清單會顯示各種回呼流程片段的生命週期中正在建立如下：

-   **`OnInflate()`** &ndash; 正在建立片段檢視配置的一部分時，會呼叫它。 這可能會從 XML 配置檔案以宣告方式建立的片段之後，立即呼叫。 片段尚未與它的活動相關聯，但**活動**，**配套**，和**AttributeSet**檢視從階層中當做參數傳遞。 這個方法最適合用來剖析**AttributeSet** ，在儲存屬性的可能會使用更新版本的片段。

-   **`OnAttach()`** &ndash; 與活動相關聯的片段之後呼叫。 這是片段是可供使用時要執行的第一個方法。 一般情況下，片段不應實作建構函式或覆寫預設建構函式。 在此方法應該初始化任何元件所需的片段。

-   **`OnCreate()`** &ndash; 若要建立的片段在活動呼叫。 呼叫這個方法時，裝載活動檢視階層架構可能不是完全具現化，因此片段不應依賴直到以便稍後在片段的生命週期的活動檢視階層的任何組件。 比方說，請勿使用此方法執行任何做調整或調整應用程式的 UI。 這是片段可能會開始收集資料所需的最早時間。 此時片段執行 UI 執行緒中，因此避免冗長的處理，或在背景執行緒上執行這項處理。 如果這個方法可能會略過**SetRetainInstance(true)**呼叫。
    這個替代方案會在以下更詳細說明。

-   **`OnCreateView()`** &ndash; 建立檢視的片段。
    這個方法會呼叫一次活動的**OnCreate()**方法已完成。 此時，它可以安全地互動的活動檢視階層架構。 此方法應傳回會片段所使用的檢視。

-   **`OnActivityCreated()`** &ndash; 之後呼叫**activity.oncreate 完成**裝載活動尚未完成。
    此時，應該執行最終做調整使用者介面。

-   **`OnStart()`** &ndash; 包含的活動已繼續之後呼叫。 這讓使用者可以看見的片段。 在許多情況下，片段會包含程式碼，否則會在**onstart （)**活動的方法。

-   **`OnResume()`** &ndash; 這是最後一個使用者可以互動與片段之前呼叫的方法。 這個方法應該執行的程式碼類型的範例會啟用的使用者可能會與之互動，例如網路攝影機裝置功能的位置服務。 這類的服務可能會導致過多的電池清空，和應用程式應該降低其用來保留電池壽命。


### <a name="fragment-destruction-lifecycle-methods"></a>片段解構存留週期方法

下一個清單說明片段正在被終結，呼叫存留週期方法：

-   **`OnPause()`** &ndash; 使用者已不再與片段互動。 這種情況下存在是因為某些其他片段作業正在修改這個片段中，或裝載的活動已暫停。 很可能裝載此片段的活動可能仍然會出現，也就是在焦點活動是部分透明或不會佔用全螢幕。 這個方法會變成作用中，時，第一個使用者正在離開片段的指示。 片段應儲存任何變更。

-   **`OnStop()`** &ndash; 不會再看見的片段。 主機活動可能已停止，或片段作業正在修改活動中。 此回呼有相同的用途為**Activity.OnStop**。

-   **`OnDestroyView()`** &ndash; 清除與檢視相關聯的資源，會呼叫這個方法。 這稱為片段相關聯的檢視已被終結時。

-   **`OnDestroy()`** &ndash; 不再使用片段時，會呼叫這個方法。 它仍會關聯於活動中，但片段不再正常運作。 這個方法應該釋放任何資源使用中的片段，例如[ **SurfaceView** ](https://developer.xamarin.com/api/type/Android.Views.SurfaceView/) ，可能會使用相機。 如果這個方法可能會略過**SetRetainInstance(true)**呼叫。 這個替代方案會在以下更詳細說明。

-   **`OnDetach()`** &ndash; 片段已不再與活動相關聯之前，會呼叫這個方法。 片段的檢視階層架構不存在，並應該在此時釋放片段所使用的所有資源。


### <a name="using-setretaininstance"></a>使用 SetRetainInstance

若要指定，它應該不會完全終結是否活動正在重新建立的片段可能會。 `Fragment`類別會提供方法`SetRetainInstance`針對此目的。 如果`true`傳遞至這個方法，則重新啟動活動時，會使用相同的執行個體的片段。 如果發生這種情況，則會叫用所有的回呼方法，除了`OnCreate`和`OnDestroy`生命週期的回呼。 此程序如下所示 （以綠色虛線），如上所示的生命週期圖表。


## <a name="fragment-state-management"></a>片段的狀態管理

片段可能儲存和還原其狀態在片段生命週期期間，所使用的執行個體`Bundle`。 組合允許將資料儲存為索引鍵/值組的片段，並可用於不需要太多記憶體的簡單資料。 片段可以儲存其狀態，藉由呼叫`OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

片段的新執行個體建立的時，儲存在狀態`Bundle`將可供新的執行個體，透過`OnCreate`， `OnCreateView`，和`OnActivityCreated`的新執行個體方法。
下列範例將示範如何擷取值`current_choice`從`Bundle`:

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

覆寫`OnSaveInstanceState`是適當的機制來儲存暫時性資料片段中跨方向的變更，例如`current_choice`在上述範例中的值。 不過的預設實作`OnSaveInstanceState`負責儲存暫時性資料，將 UI 中用於指定識別碼的每個檢視。 例如，查看應用程式具有`EditText`，如下所示定義 XML 項目：

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

因為`EditText`控制項有`id`指派時，片段會自動儲存資料的 widget 時`OnSaveInstanceState`呼叫。


### <a name="bundle-limitations"></a>配套的限制

雖然使用`OnSaveInstanceState`讓它可以輕鬆地儲存暫時性資料，使用這個方法有一些限制：

-  如果片段不會加入至上一頁堆疊，則當使用者按下時，不會還原其狀態**回** 按鈕。

-  當組合用來儲存資料時，該資料會序列化。 這可能會導致處理延遲。


## <a name="contributing-to-the-menu"></a>提供給功能表

片段可能會導致其主控活動的功能表項目。
活動會先處理功能表項目。 如果活動沒有處理常式，然後事件將會傳遞給片段中，然後將處理它。

若要將項目加入至活動的功能表中，片段必須執行兩件事。
首先，此片段必須實作方法`OnCreateOptionsMenu`並將其項目放入 [] 功能表中，如下列程式碼所示：

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

在先前的程式碼片段功能表位於檔案中的下列 xml，膨脹`menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

接下來，必須呼叫片段`SetHasOptionsMenu(true)`。 呼叫此方法向 Android 片段具有可提供選項功能表的功能表項目。 這個方法呼叫，除非片段的功能表項目將不會加入至活動的 [選項] 功能表中。 這通常是在存留週期方法`OnCreate()`，在下一步的程式碼片段所示：

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

下列畫面顯示此功能表的外觀：

[![我的往返應用程式顯示功能表項目範例螢幕擷取畫面](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png)
