---
title: 建立片段
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 339de4930242e35c40b034af2ce6ba47fe1543af
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122461"
---
# <a name="creating-a-fragment"></a>建立片段

若要建立片段，類別必須繼承自`Android.App.Fragment`然後覆寫`OnCreateView`方法。 `OnCreateView` 將裝載的活動所呼叫時它就可以將片段放在畫面上，並將傳回`View`。 典型`OnCreateView`會建立此`View`擴張版面配置檔，然後再將它附加至父容器。 容器的特性很重要，因為 Android 將套用至片段的 UI 的版面配置參數的父代。 下面這個範例可說明這點：

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

上述程式碼會擴充檢視`Resource.Layout.Example_Fragment`，並將它新增為子檢視，以`ViewGroup`容器。


> [!NOTE]
> 沒有引數建構函式時，片段子類別必須具有公用的預設值。

## <a name="adding-a-fragment-to-an-activity"></a>將片段新增至活動

有兩種方式，都可能裝載在片段內的活動：

-   **以宣告方式**&ndash;片段可用於以宣告方式內`.axml`使用的版面配置檔`<Fragment>`標記。

-   **以程式設計方式**&ndash;片段可以也具現化以動態方式使用`FragmentManager`類別的 API。

透過以程式設計方式使用`FragmentManager`類別將會稍後在本指南中討論。

### <a name="using-a-fragment-declaratively"></a>以宣告方式使用片段

新增版面配置中的片段需要使用`<fragment>`標記，然後藉由提供其中一個識別片段`class`屬性或`android:name`屬性。 下列程式碼片段示範如何使用`class`屬性宣告`fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

這個下一步 的程式碼片段示範如何宣告`fragment`使用`android:name`屬性，以識別片段類別：

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Android 建立活動時，會具現化版面配置檔案中指定每個片段，並插入從建立檢視`OnCreateView`代替`Fragment`項目。
以宣告方式新增至活動的片段是靜態的而且會保留在活動中，直到它被終結;您不可以動態取代或移除這類片段来附加之活動的存留期間。

每個片段必須獲派唯一的識別項：

-  **android: id** &ndash;配置檔案中其他 UI 項目時，此為唯一的識別碼。

-  **android: tag** &ndash;此屬性是唯一的字串。

如果前兩個方法都不使用時，片段會假設容器檢視的識別碼。 在下列範例中，兩者皆非`android:id`也`android:tag`提供 Android 將會指派識別碼`fragment_container`至片段：

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

### <a name="package-name-case"></a>封裝名稱的情況

Android 不允許封裝的名稱; 中使用大寫字元嘗試將擴充的檢視，如果封裝名稱包含大寫字元時，它將會擲回例外狀況。 不過，Xamarin.Android 會比較能夠容許，而且將可容忍命名空間中的大寫字元。

比方說，這兩個下列的程式碼片段會使用 Xamarin.Android。 不過，第二個程式碼片段將會導致`android.view.InflateException`純 Java 型 Android 應用程式所擲回。

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OR

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>片段生命週期

片段有自己的生命週期，是無關的但仍然受到[裝載活動的生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)。
例如，當活動暫停時，所有與其相關聯的片段都會暫停。 下圖概述片段的生命週期。

[![說明片段生命週期的流程圖](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)


### <a name="fragment-creation-lifecycle-methods"></a>片段建立生命週期方法

正在建立時，下列清單會顯示各種回呼的流程片段的生命週期中：

-   **`OnInflate()`** &ndash; 正在建立片段檢視版面配置的一部分時，會呼叫它。 此功能可能會從 XML 版面配置檔案以宣告方式建立的片段之後，立即呼叫。 片段尚無法與它的活動相關聯，但**活動**，**配套**，並**AttributeSet**檢視從階層中當做參數傳遞。 這個方法最適合用於剖析**AttributeSet** ，在儲存屬性可能會使用更新版本的片段。

-   **`OnAttach()`** &ndash; 片段是與活動相關聯之後呼叫。 這是當片段已準備好可供執行第一種方法。 一般情況下，片段應該不會實作建構函式或覆寫預設的建構函式。 片段所需的任何元件應該在此方法中初始化。

-   **`OnCreate()`** &ndash; 若要建立片段活動呼叫。 呼叫這個方法時，檢視階層架構的控管的活動可能不完全具現化，因此片段不應依賴活動的檢視階層，直到以便稍後在片段的生命週期的任何組件。 比方說，不要執行任何調整或調整 UI 的應用程式使用此方法。 這是片段可能會開始收集資料所需的最早時間。 此時片段執行 UI 執行緒中，因此避免冗長的處理，或在背景執行緒上執行這項處理。 如果這個方法可能會略過**SetRetainInstance(true)** 呼叫。
    此替代方案將會在下面詳細說明。

-   **`OnCreateView()`** &ndash; 建立片段的檢視。
    這個方法會呼叫一次活動的**OnCreate()** 方法已完成。 此時，很安全互動之活動的檢視階層。 此方法應傳回會片段所使用的檢視。

-   **`OnActivityCreated()`** &ndash; 之後呼叫**activity.oncreate 完成**裝載活動尚未完成。
    此時，應該執行最後的調整使用者介面。

-   **`OnStart()`** &ndash; 包含的活動已繼續之後呼叫。 這可讓片段顯示給使用者。 在許多情況下，該片段會包含程式碼，否則會在**onstart （)** 活動的方法。

-   **`OnResume()`** &ndash; 這是最後一個使用者可以互動與片段之前呼叫的方法。 一種應該在此方法中執行的程式碼範例會啟用的使用者可能會與之互動，例如相機的裝置功能的定位服務。 這類的服務可能會導致過多的電池清空，不過，和應用程式應該盡可能減少其用以保留電池壽命。


### <a name="fragment-destruction-lifecycle-methods"></a>片段解構存留週期方法

下一個清單說明正在損毀的片段，呼叫的生命週期方法：

-   **`OnPause()`** &ndash; 使用者已不再片段和互動。 其他片段作業正在修改此片段中，或裝載的活動已暫停，就會發生這種情況。 可以裝載此片段的活動可能仍會顯示，也就是焦點中的活動為部分透明或不會佔用整個螢幕。 這個方法會變成作用中，時，使用者會離開片段的第一個徵兆。 片段應該儲存任何變更。

-   **`OnStop()`** &ndash; 片段不再可見。 主機活動可能會停止，或片段作業在活動中修改它。 此回呼中有相同的用途，作為**Activity.OnStop**。

-   **`OnDestroyView()`** &ndash; 清除檢視相關聯的資源，會呼叫這個方法。 這稱為片段相關聯的檢視已被終結時。

-   **`OnDestroy()`** &ndash; 不再使用的片段時，會呼叫這個方法。 它仍會關聯活動，但片段不再正常運作。 這個方法應該釋放任何資源，例如正在使用中的片段中， [ **SurfaceView** ](https://developer.xamarin.com/api/type/Android.Views.SurfaceView/) ，可能會用於相機。 如果這個方法可能會略過**SetRetainInstance(true)** 呼叫。 此替代方案將會在下面詳細說明。

-   **`OnDetach()`** &ndash; 片段已不再與活動相關聯之前，會呼叫這個方法。 片段的檢視階層不存在，並應該在此時釋放所有的資源所使用的片段。


### <a name="using-setretaininstance"></a>使用 SetRetainInstance

您可指定它應該不會完全銷毀如果活動正在重新建立片段。 `Fragment`類別提供方法`SetRetainInstance`針對此目的。 如果`true`傳遞至此方法，則重新啟動活動時，會使用相同的執行個體的片段。 如果發生這種情況，則會叫用所有的回呼方法，除了`OnCreate`和`OnDestroy`生命週期的回呼。 如上所示 （以綠色虛線） 生命週期圖表說明此程序。


## <a name="fragment-state-management"></a>片段的狀態管理

片段可能儲存並還原其狀態片段生命週期期間，所使用的執行個體`Bundle`。 套件組合可讓要將資料儲存為索引鍵/值組的片段，並可用於不需要太多記憶體的簡單資料。 片段可以儲存其狀態，藉由呼叫`OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

片段的新執行個體建立的時，儲存在狀態`Bundle`將可供新的執行個體，透過`OnCreate`， `OnCreateView`，和`OnActivityCreated`的新執行個體的方法。
下列範例示範如何擷取值`current_choice`從`Bundle`:

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

覆寫`OnSaveInstanceState`是適當的機制來儲存暫時性資料片段中在方向變更，例如`current_choice`在上述範例中的值。 不過的預設實作`OnSaveInstanceState`會負責將暫時性的資料儲存在具有指定識別碼的每個檢視的 UI 中。 例如，看看應用程式的`EditText`，如下所示，在 XML 中定義的項目：

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

由於`EditText`控制項有`id`指派時，片段會自動將資料儲存在小工具時`OnSaveInstanceState`呼叫。


### <a name="bundle-limitations"></a>套件組合的限制

雖然使用`OnSaveInstanceState`可讓您輕鬆地儲存暫時性資料，使用這個方法有一些限制：

-  如果片段不會加入至上一頁堆疊，則將不會還原其狀態，當使用者按下**回** 按鈕。

-  當組合用來儲存資料時，該資料會序列化。 這可能會導致處理延遲。


## <a name="contributing-to-the-menu"></a>提供給功能表

片段可能會導致其裝載活動的功能表項目。
活動會先處理功能表項目。 如果活動沒有處理常式，然後事件會被傳遞給片段中，然後將處理它。

若要將項目加入至活動的功能表中，片段必須做兩件事。
首先，將片段必須實作方法`OnCreateOptionsMenu`並將其項目放入 [] 功能表中，如下列程式碼所示：

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

在先前的程式碼片段功能表來自下列的 XML 中，位於檔案擴大`menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

接下來，必須呼叫片段`SetHasOptionsMenu(true)`。 這個方法的呼叫宣佈 Android 片段有貢獻到 [選項] 功能表的功能表項目。 除非進行呼叫此方法時，片段的功能表項目將不會加入活動的 [選項] 功能表。 這通常是在開發週期方法`OnCreate()`下, 一步 的程式碼片段所示：

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

下列畫面顯示此功能表中看起來的樣子：

[![我的車程應用程式 功能表項目顯示的範例螢幕擷取畫面](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
