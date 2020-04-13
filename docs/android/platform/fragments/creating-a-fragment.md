---
title: 建立片段
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 0e8d3748c7ddd337cf2f27f5b272b208e79d503a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027509"
---
# <a name="creating-a-fragment"></a>建立片段

要創建片段,類必須繼承`Android.App.Fragment`,然後重`OnCreateView`寫 該方法。 `OnCreateView`當將片段放在螢幕上時,重新主活動將呼叫 該操作,並將傳`View`回 。 典型`OnCreateView`值將通過膨脹`View`佈局 文件,然後將其附加到父容器來創建此情況。 容器的特徵很重要,因為 Android 將父項的佈局參數應用於片段的 UI。 下列範例會加以說明：

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

上面的代碼將膨脹檢視`Resource.Layout.Example_Fragment`,並將其作為子視圖添加到`ViewGroup`容器。

> [!NOTE]
> 片段子類必須具有公共預設值,沒有參數構造函數。

## <a name="adding-a-fragment-to-an-activity"></a>新增片段

片段在作用中託管有兩種方式:

- **聲明性**&ndash;片段可以`.axml``<Fragment>`使用 標記在佈局檔中聲明性使用。

- **也可以**&ndash;通過使用`FragmentManager`類的 API 動態實例化程式設計片段。

本指南稍後將討論通過`FragmentManager`該類程式設計使用。

### <a name="using-a-fragment-declaratively"></a>以宣告方式使用片段

在佈局中添加片段需要使用標記,`<fragment>`然後透過`class`提供`android:name`屬性或 屬性來識別片段。 以下程式碼段展示如何使用`class`屬性`fragment`的聲明 :

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

下一個程式碼段展示如何透過`fragment`使用`android:name`屬性 識別區段來宣告 a:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

創建活動時,Android 將實例化佈局檔中指定的每個片段,並插入`OnCreateView`從`Fragment`元素處 創建的視圖。
聲明添加到活動中的片段是靜態的,將保留在活動上,直到銷毀為止;在附加到該活動的生存期內,無法動態替換或刪除此類片段。

必須為每個片段分配唯一識別碼:

- **android:id**&ndash;與佈局檔中的其他 UI 元素一樣,這是一個唯一的 ID。

- **android:標記**&ndash;此屬性是一個唯一的字串。

如果上述兩種方法均未使用,則片段將假定容器視圖的 ID。 在以下未`android:id`提供`android:tag`或 未提供的情況下,Android`fragment_container`會將 ID 分配給片段:

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

### <a name="package-name-case"></a>套件名稱案例

Android 不允許包名稱中的大寫字元;如果包名稱包含大寫字元,則在嘗試膨脹視圖時,將引發異常。 但是,Xamarin.Android 更寬容,並且會容忍命名空間中的大寫字元。

例如,以下兩個代碼段將適用於 Xamarin.Android。 但是,第二個`android.view.InflateException`代碼段將導致純基於 Java 的 Android 應用程式引發 。

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

OR

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

## <a name="fragment-lifecycle"></a>片段生命週期

片段有自己的生命週期,它在某種程度上與[託管活動的生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)無關,但仍受其影響。
例如,當活動暫停時,其所有關聯的片段都暫停。 下圖概述了片段的生命週期。

[![顯示片段生命週期的流程圖](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)

### <a name="fragment-creation-lifecycle-methods"></a>片段建立生命週期方法

下面的清單顯示了建立片段時片段生命週期中各種回調的流:

- **`OnInflate()`**&ndash;當片段作為檢視佈局的一部分創建時調用。 在從 XML 佈局檔以聲明性方式創建片段後,可以立即調用這一點。 片段尚未與其活動關聯,但檢視層次結構中**的活動**、**捆綁包**和**屬性集**作為參數傳入。 此方法最適合用於分析**屬性集**和保存片段以後可能使用的屬性。

- **`OnAttach()`**&ndash;在片段與活動關聯後調用。 這是片段準備好使用時運行的第一種方法。 通常,片段不應實現構造函數或重寫預設構造函數。 片段所需的任何元件都應在此方法中初始化。

- **`OnCreate()`**&ndash;活動調用以創建片段。 調用此方法時,託管活動的視圖層次結構可能不完全實例化,因此片段不應依賴於活動視圖層次結構的任何部分,直到片段生命週期的後期。 例如,不要使用此方法對應用程式的 UI 執行任何調整或調整。 這是片段開始收集所需數據的最早時間。 此時,片段在 UI 線程中運行,因此請避免任何冗長的處理,或在後台線程上執行該處理。 如果調用**SetRetainInstance(true),** 則可能會跳過此方法。
    下面將更詳細地介紹此備選方案。

- **`OnCreateView()`**&ndash;為片段創建檢視。
    完成活動的**OnCreate()** 方法後,將調用此方法。 此時,可以安全地與活動的視圖層次結構進行交互。 此方法應返回片段將使用的檢視。

- **`OnActivityCreated()`**&ndash;活動後調用 **.OnCreate**已由託管活動完成。
    此時應對用戶介面執行最終調整。

- **`OnStart()`**&ndash;恢復包含活動後調用。 這使片段對用戶可見。 在許多情況下,片段將包含代碼,否則代碼將位於活動的**OnStart()** 方法中。

- **`OnResume()`**&ndash;這是使用者與片段交互之前調用的最後一個方法。 在此方法中應執行的代碼類型的一個範例是啟用使用者可以與之互動的裝置的功能,例如定位服務的攝像機。 但是,此類服務可能會導致電池消耗過大,應用程式應盡量減少其使用,以節省電池使用時間。

### <a name="fragment-destruction-lifecycle-methods"></a>磁碟中銷毀生命週期方法

下一個清單將解釋被銷毀為片段的生命週期方法:

- **`OnPause()`**&ndash;使用者不再能夠與片段進行互動。 存在此情況是因為其他一些片段操作正在修改此片段,或者託管活動暫停。 承載此片段的活動可能仍然可見,也就是說,焦點活動部分透明或未佔用全屏。 當此方法變為活動狀態時,它是用戶離開片段的第一個指示。 片段應保存任何更改。

- **`OnStop()`**&ndash;片段不再可見。 主機活動可能會停止,或者片段操作正在"活動"中修改它。 此回調的作用與**活動相同。**

- **`OnDestroyView()`**&ndash;調用此方法以清理與檢視關聯的資源。 當與片段關聯的檢視已銷毀時,將調用此選項。

- **`OnDestroy()`**&ndash;當片段不再使用時,將調用此方法。 它仍然與活動關聯,但片段不再正常工作。 此方法應釋放片段正在使用的任何資源,例如可用於攝像機的[**SurfaceView。**](xref:Android.Views.SurfaceView) 如果調用**SetRetainInstance(true),** 則可能會跳過此方法。 下面將更詳細地介紹此備選方案。

- **`OnDetach()`**&ndash;此方法是在片段不再與活動關聯之前調用的。 片段的檢視層次結構不再存在,此時應釋放片段使用的所有資源。

### <a name="using-setretaininstance"></a>使用設定保留實體

片段可以指定,如果正在重新創建活動,則不應完全銷毀該片段。 類別`Fragment`的服務`SetRetainInstance`。 如果`true`傳遞給此方法,則當活動重新啟動時,將使用片段的相同實例。 如果發生這種情況,則除`OnCreate``OnDestroy`和生命周期回調外,將調用所有回調方法。 此過程在上面所示的生命週期圖中(由綠色虛線)中說明。

## <a name="fragment-state-management"></a>片段狀態管理

片段可以使用`Bundle`的實例在片段生命週期中保存和恢復其狀態。 捆綁包允許片段將數據保存為鍵/值對,並且對於不需要太多記憶體的簡單數據非常有用。 片段可以通過呼`OnSaveInstanceState`叫 來儲存其狀態:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

建立片段`Bundle`的新實例時,保存在 中的狀態`OnCreate`將通過 的新實`OnCreateView`例 的`OnActivityCreated`和, 和新實例的方法對新實例可用。
以下範例簡報如何從`current_choice``Bundle`中 檢索值:

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

重寫`OnSaveInstanceState`是一種適當的機制,用於跨方向更改(如上述示例`current_choice`中的值)在片段中保存瞬態數據。 但是,默認`OnSaveInstanceState`實現 負責為分配 ID 的每個檢視在 UI 中保存瞬態數據。 例如,檢視在 XML`EditText`中定義 元素的應用程式,如下所示:

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

由於`EditText`控件已`id`分配,因此在調用時`OnSaveInstanceState`,片段會自動將數據保存在小部件中。

### <a name="bundle-limitations"></a>捆綁限制

儘管使用`OnSaveInstanceState`使保存暫態數據變得容易,但使用此方法有一些限制:

- 如果未將片段添加到後堆疊中,則當使用者按下 **「後退」** 按鈕時,將不會恢復其狀態。

- 當捆綁包用於保存數據時,該數據將序列化。 這可能導致處理延遲。

## <a name="contributing-to-the-menu"></a>對選單的貢獻

片段可能會向其託管活動的功能表貢獻專案。
活動首先處理功能表項。 如果活動沒有處理程式,則事件將傳遞給片段,然後該片段將處理它。

要將項添加到活動功能表,片段必須執行兩項操作。
首先,片段必須實現該方法`OnCreateOptionsMenu`並將其項放入菜單中,如以下代碼所示:

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

前面的代碼段中的選單從位於檔案中`menu_fragment_vehicle_list.xml`的以下 XML 中膨脹:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

接下來,片段必須呼叫`SetHasOptionsMenu(true)`。 對此方法的調用向 Android 宣佈片段具有功能表項,以便貢獻到選項功能表。 除非對此方法進行了調用,否則片段的功能表項將不會添加到活動的選項功能表中。 這通常在生命週期方法`OnCreate()`中完成,如下一個代碼段所示:

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

以下螢幕顯示此選單的外觀:

[![顯示選單項目的「我的行程」應用程式的範例螢幕截圖](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
