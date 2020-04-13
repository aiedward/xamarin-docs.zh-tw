---
title: 特製的片段類別
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/08/2018
ms.openlocfilehash: b004fbf121374a2bb3bf5d85f45d8cae293573bf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027307"
---
# <a name="specialized-fragment-classes"></a>特製的片段類別

片段 API 提供了其他子類,這些子類封裝了應用程式中的一些更常見功能。 這些子類是:

- **清單片段**&ndash;此片段用於顯示綁定到資料來源(如陣列或游標)的專案清單。

- **對話框片段**&ndash;此片段用作對話框周圍的包裝器。 片段將在其活動頂部顯示對話方塊。

- **設定項目**&ndash;片段 此片段用於將首選項物件顯示為清單。

## <a name="the-listfragment"></a>清單片段

在`ListFragment`概念與功能上`ListActivity`與它是在片段中承載的`ListView`包裝器。 下圖顯示了`ListFragment`在平板電腦和手機上運行的:

[![平板電腦和手機上清單片段的螢幕截圖](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)

### <a name="binding-data-with-the-listadapter"></a>使用清單配接器繫結資料

類`ListFragment`已提供默認佈局,因此無需重`OnCreateView`寫 來顯示`ListFragment`的內容。 `ListView`通過使用`ListAdapter`實現綁定到數據。 下面的範例展示如何使用簡單的字串陣列來實現此原因:

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

設置`ListAdapter`時 ,`ListFragment.ListAdapter`使用 屬性 很重要,`ListView.ListAdapter`而不是使用 屬性。 使用`ListView.ListAdapter`將導致跳過重要的初始化代碼。

### <a name="responding-to-user-selection"></a>回應使用者選擇

要回應使用者選擇,應用程式必須重寫`OnListItemClick`方法 。 下面的範例顯示了一種這樣的可能性:

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

在上面的代碼中,當使用者在`ListFragment`中選擇中的專案時,託管活動中將顯示一個新片段,顯示有關所選項的更多詳細資訊。

## <a name="dialogfragment"></a>對話片段

*對話片段*是一個片段,用於在片段內部顯示一個對話框物件,該物件將浮在活動視窗的頂部。 它旨在替換託管對話框 API(從 Android 3.0 開始)。 以下螢幕截圖顯示的範例`DialogFragment`:

[![顯示新增新的視窗視窗的對話片段螢幕擷取](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

A`DialogFragment`確保片段和對話框之間的狀態保持一致。 對話物件的所有交互和控制都應通過`DialogFragment`API 進行,而不是透過對對話框物件的直接呼叫進行。 API`DialogFragment`為每個實例提供一`Show()`個 用於顯示片段的方法。 有兩種方法可以刪除片段:

- 調用`DialogFragment.Dismiss()``DialogFragment`實例。 

- 顯示另`DialogFragment`一個 。

要建立`DialogFragment`一個 類`Android.App.DialogFragment,`,請 繼承,然後重寫以下兩種方法之一:

- **在"創建檢視**&ndash;"上,這將創建並返回檢視。

- **在創建對話框**&ndash;上,這將創建一個自定義對話方塊。 它通常用於顯示*警報對話框*。 重寫此方法時,不需要重寫`OnCreateView`。

### <a name="a-simple-dialogfragment"></a>簡單的對話片段

下面的螢幕截圖顯示了一個簡單的`DialogFragment`,具有`TextView``Button`a 和 2:

[![範例對話片段,其中包含文字檢視和兩個按鈕](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

將顯示`TextView`使用者按下 中的一個按鈕`DialogFragment`的次數,而單擊另一個按鈕將關閉片段。 的代碼`DialogFragment`為:

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

與所有片段一樣,`DialogFragment`在上下文中`FragmentTransaction`顯示 。

上`Show()`的方法`DialogFragment`以和`FragmentTransaction`作為`string`輸入。 該對話框將添加到"活動"和"`FragmentTransaction`已提交」。

以下程式碼展示了作用方式可以`Show()`使用方法 :`DialogFragment`的一個可能方式:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

### <a name="dismissing-a-fragment"></a>關閉片段

呼叫`Dismiss()`的實體`DialogFragment`會導致從活動中刪除片段並提交該事務。
將調用與銷毀片段有關的標準片段生命週期方法。

### <a name="alert-dialog"></a>警示對話框

不要重寫`OnCreateView`,`DialogFragment`可以改為重`OnCreateDialog`寫 。 這允許應用程式建立由片段管理的[警示對話框](xref:Android.App.AlertDialog)。 以下代碼是使用`AlertDialog.Builder`創建的範例。 `Dialog`

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

## <a name="preferencefragment"></a>偏好設定

為了説明管理首選項,片段 API`PreferenceFragment`提供子類。 `PreferenceFragment`與[「首選項活動」](xref:Android.Preferences.PreferenceActivity)&ndash;類似,它將向片段中的使用者顯示首選項層次結構。 當使用者與設定互動時,它們會自動儲存到[分享設定 。](https://developer.android.com/reference/android/content/SharedPreferences.html)
在 Android 3.0`PreferenceFragment`或更高版本 應用程式中,使用處理應用程式中的首選項。 下圖顯示 : `PreferenceFragment`

[![範例設定片段,包含內聯、對話框和啟動偏好設定](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)

### <a name="create-a-preference-fragment-from-a-resource"></a>從資源建立喜好選項

通過使用[首選項片段.Add從資源中添加首選項](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*),可以從 XML 資源檔中誇大首選項片段。 在片段的生命週期中呼叫此方法的邏輯位置將位於該方法中`OnCreate`。

上圖`PreferenceFragment`是通過從 XML 載入資源創建的。 資源檔案為:

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

首選項片段的代碼如下所示:

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

### <a name="querying-activities-to-create-a-preference-fragment"></a>查詢活動以建立喜好項目區段

創建的另一種`PreferenceFragment`技術涉及查詢活動。 每個活動都可以使用將指向 XML 資源檔的[METADATA\_\_KEY 首選項](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences)屬性。 在 Xamarin.Android 中,這是通過使用`MetaDataAttribute`修飾活動 ,然後指定要使用的資源檔來完成的。 該`PreferenceFragment`類提供可用於查詢活動以查找此 XML 資源併為其膨脹首選項層次結構的方法[Addpreference From Intent。](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*)

這個過程的範例在以下代碼段中提供,該代碼段用於`AddPreferencesFromIntent`建立`PreferenceFragment`:

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

安卓系統將看類`MyActivityWithPreference`。 類別必須使用`MetaDataAttribute,`以下代碼段中所示的 修飾:

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

聲明`MetaDataAttribute``PreferenceFragment`將用來膨脹首選項層次結構的 XML 資源檔。 如果未提供`MetatDataAttribute`,則將在運行時引發異常。 執行此代碼時,將顯示`PreferenceFragment`如下螢幕截圖所示:

[![顯示首選項片段的範例螢幕擷取](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
