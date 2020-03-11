---
title: 特製的片段類別
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/08/2018
ms.openlocfilehash: b004fbf121374a2bb3bf5d85f45d8cae293573bf
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027307"
---
# <a name="specialized-fragment-classes"></a>特製的片段類別

片段 API 會提供其他子類別，以封裝在應用程式中找到的一些較常見功能。 這些子類別包括：

- **ListFragment** &ndash; 此片段是用來顯示系結至資料來源（例如陣列或游標）的專案清單。

- **DialogFragment** &ndash; 此片段會當做對話周圍的包裝函式使用。 片段會在其活動上方顯示對話方塊。

- **PreferenceFragment** &ndash; 此片段是用來將喜好設定物件顯示為清單。

## <a name="the-listfragment"></a>ListFragment

`ListFragment` 在 `ListActivity`的概念和功能中非常相似;它是在片段中裝載 `ListView` 的包裝函式。 下圖顯示在平板電腦和手機上執行的 `ListFragment`：

[在平板電腦和手機上 ![ListFragment 螢幕擷取畫面](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)

### <a name="binding-data-with-the-listadapter"></a>使用 ListAdapter 系結資料

`ListFragment` 類別已經提供預設版面配置，因此不需要覆寫 `OnCreateView` 以顯示 `ListFragment`的內容。 `ListView` 使用 `ListAdapter` 的實體系結至資料。 下列範例顯示如何使用簡單的字串陣列來完成這項作業：

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    string[] values = new[] { "Android", "iPhone", "WindowsMobile",
                "Blackberry", "WebOS", "Ubuntu", "Windows7", "Max OS X",
                "Linux", "OS/2" };
    this.ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleExpandableListItem1, values);
}
```

設定 `ListAdapter`時，請務必使用 `ListFragment.ListAdapter` 屬性，而不是 `ListView.ListAdapter` 屬性。 使用 `ListView.ListAdapter` 將會略過重要的初始化程式碼。

### <a name="responding-to-user-selection"></a>回應使用者選取

若要回應使用者選取專案，應用程式必須覆寫 `OnListItemClick` 方法。 下列範例會顯示這種情況：

```csharp
public override void OnListItemClick(ListView l, View v, int index, long id)
{
    // We can display everything in place with fragments.
    // Have the list highlight this item and show the data.
    ListView.SetItemChecked(index, true);

    // Check what fragment is shown, replace if needed.
    var details = FragmentManager.FindFragmentById<DetailsFragment>(Resource.Id.details);
    if (details == null || details.ShownIndex != index)
    {
        // Make new fragment to show this selection.
        details = DetailsFragment.NewInstance(index);

        // Execute a transaction, replacing any existing
        // fragment with this one inside the frame.
        var ft = FragmentManager.BeginTransaction();
        ft.Replace(Resource.Id.details, details);
        ft.SetTransition(FragmentTransit.FragmentFade);
        ft.Commit();
    }
}
```

在上述程式碼中，當使用者選取 `ListFragment`中的專案時，[裝載] 活動中會顯示新的片段，並顯示所選取專案的更多詳細資料。

## <a name="dialogfragment"></a>DialogFragment

*DialogFragment*是用來在片段內顯示對話方塊物件的片段，此片段會浮動在使用中視窗的頂端。 其目的是要取代受控對話 Api （從 Android 3.0 開始）。 下列螢幕擷取畫面顯示 `DialogFragment`的範例：

[顯示 [新增車輛 EditBox] DialogFragment 的 ![螢幕擷取畫面](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

`DialogFragment` 可確保片段和對話方塊之間的狀態保持一致。 對話物件的所有互動和控制都應該透過 `DialogFragment` API 進行，而不是使用對話方塊物件的直接呼叫來進行。 `DialogFragment` API 會為每個實例提供一個用來顯示片段的 `Show()` 方法。 有兩種方式可以清除片段：

- 呼叫 `DialogFragment` 實例上的 `DialogFragment.Dismiss()`。 

- 顯示另一個 `DialogFragment`。

若要建立 `DialogFragment`，類別會繼承自 `Android.App.DialogFragment,`，然後覆寫下列兩個方法的其中一個：

- **OnCreateView** &ndash; 這會建立並傳回一個視圖。

- **OnCreateDialog** &ndash; 這會建立自訂對話方塊。 它通常用來顯示*AlertDialog*。 覆寫這個方法時，不需要覆寫 `OnCreateView`。

### <a name="a-simple-dialogfragment"></a>簡單的 DialogFragment

下列螢幕擷取畫面顯示具有 `TextView` 和兩個 `Button`s 的簡單 `DialogFragment`：

[具有 TextView 和兩個按鈕的 ![範例 DialogFragment](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

`TextView` 會顯示使用者在 `DialogFragment`中按下一個按鈕的次數，而按一下 [其他] 按鈕將會關閉該片段。 `DialogFragment` 的程式碼為：

```csharp
public class MyDialogFragment : DialogFragment
{
    private int _clickCount;
    public override void OnCreate(Bundle savedInstanceState)
    {
        _clickCount = 0;
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState)
        
        var view = inflater.Inflate(Resource.Layout.dialog_fragment_layout, container, false);
        var textView = view.FindViewById<TextView>(Resource.Id.dialog_text_view);
            
        view.FindViewById<Button>(Resource.Id.dialog_button).Click += delegate
            {
                textView.Text = "You clicked the button " + _clickCount++ + " times.";
            };

        // Set up a handler to dismiss this DialogFragment when this button is clicked.
        view.FindViewById<Button>(Resource.Id.dismiss_dialog_button).Click += (sender, args) => Dismiss();
        return view;
        }
    }
}
```

### <a name="displaying-a-fragment"></a>顯示片段

就像所有片段一樣，`DialogFragment` 會顯示在 `FragmentTransaction`的內容中。

`DialogFragment` 上的 `Show()` 方法會將 `FragmentTransaction` 和 `string` 當做輸入。 對話方塊將會加入至活動，並認可 `FragmentTransaction`。

下列程式碼示範一種可能的方式，活動可以使用 `Show()` 方法來顯示 `DialogFragment`：

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

### <a name="dismissing-a-fragment"></a>關閉片段

在 `DialogFragment` 的實例上呼叫 `Dismiss()`，會導致從活動中移除片段並認可該交易。
將會呼叫與片段的銷毀相關的標準片段生命週期方法。

### <a name="alert-dialog"></a>警示對話方塊

`DialogFragment` 可以改為覆寫 `OnCreateDialog`，而不是覆寫 `OnCreateView`。 這可讓應用程式建立由片段管理的[AlertDialog](xref:Android.App.AlertDialog) 。 下列程式碼是使用 `AlertDialog.Builder` 建立 `Dialog`的範例：

```csharp
public class AlertDialogFragment : DialogFragment
{
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        EventHandler<DialogClickEventArgs> okhandler;
        var builder = new AlertDialog.Builder(Activity)
            .SetMessage("This is my dialog.")
            .SetPositiveButton("Ok", (sender, args) =>
                                         {
                                             // Do something when this button is clicked.
                                         })
            .SetTitle("Custom Dialog");
        return builder.Create();
    }
}
```

## <a name="preferencefragment"></a>PreferenceFragment

為了協助管理喜好設定，片段 API 提供了 `PreferenceFragment` 子類別。 `PreferenceFragment` 類似于[PreferenceActivity](xref:Android.Preferences.PreferenceActivity) &ndash; 它會在片段中顯示使用者的喜好設定階層。 當使用者與喜好設定互動時，系統會自動將其儲存至[SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html)。
在 Android 3.0 或更高版本的應用程式中，使用 `PreferenceFragment` 來處理應用程式中的喜好設定。 下圖顯示 `PreferenceFragment`的範例：

[使用內嵌、對話方塊和啟動喜好設定 ![範例 PreferencesFragment](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)

### <a name="create-a-preference-fragment-from-a-resource"></a>從資源建立喜好設定片段

您可以使用[PreferenceFragment. AddPreferencesFromResource](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*)方法，從 XML 資源檔放大喜好設定片段。 在片段的生命週期中呼叫這個方法的邏輯位置會在 `OnCreate` 方法中。

上面所示的 `PreferenceFragment` 是從 XML 載入資源所建立。 資源檔為：

```xml
<?xml version="1.0" encoding="utf-8"?>

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

  <PreferenceCategory android:title="Inline Preferences">
    <CheckBoxPreference android:key="checkbox_preference"
                        android:title="Checkbox Preference Title"
                        android:summary="Checkbox Preference Summary" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Dialog Based Preferences">

    <EditTextPreference android:key="edittext_preference"
                        android:title="EditText Preference Title"
                        android:summary="EditText Preference Summary"
                        android:dialogTitle="Edit Text Preferrence Dialog Title" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Launch Preferences">

    <!-- This PreferenceScreen tag serves as a screen break (similar to page break
             in word processing). Like for other preference types, we assign a key
             here so it is able to save and restore its instance state. -->
    <PreferenceScreen android:key="screen_preference"
                      android:title="Title Screen Preferences"
                      android:summary="Summary Screen Preferences">

      <!-- You can place more preferences here that will be shown on the next screen. -->

      <CheckBoxPreference android:key="next_screen_checkbox_preference"
                          android:title="Next Screen Toggle Preference Title"
                          android:summary="Next Screen Toggle Preference Summary" />

    </PreferenceScreen>

    <PreferenceScreen android:title="Intent Preference Title"
                      android:summary="Intent Preference Summary">

      <intent android:action="android.intent.action.VIEW"
              android:data="http://www.android.com" />

    </PreferenceScreen>

  </PreferenceCategory>

</PreferenceScreen>
```

喜好設定片段的程式碼如下所示：

```csharp
public class PrefFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        AddPreferencesFromResource(Resource.Xml.preferences);
    }
}
```

### <a name="querying-activities-to-create-a-preference-fragment"></a>查詢活動以建立喜好設定片段

建立 `PreferenceFragment` 的另一個技術牽涉到查詢活動。 每個活動都可以使用[中繼資料\_KEY\_喜好](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences)設定屬性，以指向 XML 資源檔。 在 Xamarin 中，這是藉由使用 `MetaDataAttribute`裝飾活動，然後指定要使用的資源檔來完成。 `PreferenceFragment` 類別提供可用來查詢活動的方法[AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*) ，以尋找此 XML 資源並擴充其喜好設定階層。

下列程式碼片段中提供此程式的範例，其使用 `AddPreferencesFromIntent` 建立 `PreferenceFragment`：

```csharp
public class MyPreferenceFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        var intent = new Intent(this.Activity, typeof (MyActivityWithPreferences));
        AddPreferencesFromIntent(intent);
    }
}
```

Android 會查看類別 `MyActivityWithPreference`。 類別必須使用 `MetaDataAttribute,` 進行裝飾，如下列程式碼片段所示：

```csharp
[Activity(Label = "My Activity with Preferences")]
[MetaData(PreferenceManager.MetadataKeyPreferences, Resource = "@xml/preference_from_intent")]
public class MyActivityWithPreferences : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        // This is deliberately blank
    }
}
```

`MetaDataAttribute` 宣告 `PreferenceFragment` 將用來擴大喜好設定階層的 XML 資源檔。 如果未提供 `MetatDataAttribute`，則會在執行時間擲回例外狀況。 當此程式碼執行時，`PreferenceFragment` 會如下列螢幕擷取畫面所示：

[顯示 PreferenceFragment 範例應用程式的 ![螢幕擷取畫面](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
