---
title: "特製化的片段類別"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: e7b4349ee2664a94ef6dff3c6a58d5f8f97682a1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="specialized-fragment-classes"></a>特製化的片段類別

片段 API 提供封裝中的應用程式的常見功能的某些其他子類別。 這些子類別如下：

-   **ListFragment** &ndash;此片段用來顯示繫結至資料來源，例如陣列或資料指標的項目清單。

-   **DialogFragment** &ndash;此片段做為對話的包裝函式。 片段會顯示對話方塊，在它的活動之上。

-   **PreferenceFragment** &ndash;此片段用來顯示為清單的喜好設定物件。


<a name="The_ListFragment" />

## <a name="the-listfragment"></a>ListFragment

`ListFragment`在概念和功能非常類似`ListActivity`; 它是裝載的包裝函式`ListView`在片段中。 下的圖顯示`ListFragment`平板電腦和手機上執行：

[![螢幕擷取畫面的 ListFragment 電話和平板電腦上](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png)

<a name="Binding_Data_With_The_ListAdapter" />

### <a name="binding-data-with-the-listadapter"></a>繫結資料與 ListAdapter

`ListFragment`類別已提供預設版面配置，因此不需要覆寫`OnCreateView`來顯示內容`ListFragment`。 `ListView`使用繫結至資料`ListAdapter`實作。 下列範例會示範如何做到這點可以使用簡單的字串陣列：

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

設定時`ListAdapter`，請務必使用`ListFragment.ListAdapter`屬性，而非`ListView.ListAdapter`屬性。 使用`ListView.ListAdapter`會導致略過重要的初始化程式碼。

<a name="Responding_to_User_Selection" />


### <a name="responding-to-user-selection"></a>回應使用者選取

若要回應使用者選取項目，必須覆寫應用程式`OnListItemClick`方法。 下列範例會示範一個這種可能性：

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

上方，當使用者選取的項目中的程式碼`ListFragment`，新的片段會顯示在主控的活動中，顯示有關選取之項目的更多詳細資料。

<a name="DialogFragment" />


## <a name="dialogfragment"></a>DialogFragment

*DialogFragment*是用來顯示對話方塊物件內的活動視窗頂端會浮動的片段的片段。 它是用來取代受管理的應用程式開發介面 （從 Android 3.0 開始） 對話方塊。 下列螢幕擷取畫面顯示的範例`DialogFragment`:

[![螢幕擷取畫面的 DialogFragment，顯示加入新的一種載具，編輯方塊](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png)

A`DialogFragment`可確保此片段，而且對話方塊之間的狀態維持一致。 透過應該發生的所有互動和對話方塊物件的控制`DialogFragment`應用程式開發介面，且不進行直接呼叫對話方塊物件上。 `DialogFragment` API 提供與每個執行個體`Show()`用來顯示某個片段的方法。 有兩種方式，若要移除的片段：

-  呼叫`DialogFragment.Dismiss()`上`DialogFragment`執行個體。 

-  顯示另一個`DialogFragment`。

若要建立`DialogFragment`，類別繼承自`Android.App.DialogFragment,`，然後覆寫下列兩種方法之一：

- **OnCreateView** &ndash;這會建立並傳回的檢視。

- **OnCreateDialog** &ndash;這會建立自訂對話方塊。 通常用來顯示*AlertDialog*。 在覆寫這個方法，不需要覆寫`OnCreateView`。


<a name="A_Simple_DialogFragment" />

### <a name="a-simple-dialogfragment"></a>簡單 DialogFragment

下列螢幕擷取畫面顯示簡單`DialogFragment`具有`TextView`和兩個`Button`s:

[![TextView 與兩個按鈕的範例 DialogFragment](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png)

`TextView`會顯示使用者已按一下中的一個按鈕的次數`DialogFragment`，而按一下 [其他] 按鈕，將會關閉片段。 程式碼`DialogFragment`是：

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

<a name="Displaying_a_Fragment" />

### <a name="displaying-a-fragment"></a>顯示片段

像所有片段，`DialogFragment`顯示的內容中`FragmentTransaction`。

`Show()`方法`DialogFragment`採用`FragmentTransaction`和`string`做為輸入。 對話將會加入至活動，而`FragmentTransaction`認可。

下列程式碼將示範一個可能的方式，活動可能會使用`Show()`方法，以顯示`DialogFragment`:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

<a name="Dismissing_a_Fragment" />

### <a name="dismissing-a-fragment"></a>解除片段

呼叫`Dismiss()`的執行個體上`DialogFragment`會導致從活動移除片段時，便認可該交易。
將呼叫所涉及的片段解構標準片段存留週期方法。

<a name="Alert_Dialog" />

### <a name="alert-dialog"></a>警示 對話方塊

而不是覆寫`OnCreateView`、`DialogFragment`可能會改為覆寫`OnCreateDialog`。 這可讓應用程式建立[AlertDialog](https://developer.xamarin.com/api/type/Android.App.AlertDialog/)所管理的片段。 下列程式碼是使用範例`AlertDialog.Builder`建立`Dialog`:

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

 <a name="PreferenceFragment" />


## <a name="preferencefragment"></a>PreferenceFragment

為了協助管理喜好設定，片段 API 提供`PreferenceFragment`子類別。 `PreferenceFragment`類似於[PreferenceActivity](https://developer.xamarin.com/api/type/Android.Preferences.PreferenceActivity/
) &ndash;它會顯示給使用者的喜好設定的階層架構在片段中。 當使用者互動時，喜好設定，它們將會自動儲存到[SharedPreferences](http://developer.android.com/reference/android/content/SharedPreferences.html)。
在 Android 3.0 或更高的應用程式中，使用`PreferenceFragment`處理應用程式中的喜好設定。 下圖顯示的範例`PreferenceFragment`:

[![範例 PreferencesFragment 內嵌、 對話方塊中，與啟動喜好設定](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png)

<a name="Create_A_Preference_Fragment_from_a_Resource" />

### <a name="create-a-preference-fragment-from-a-resource"></a>從資源中建立的喜好設定片段

片段可能會膨脹從 XML 資源檔使用的喜好設定[PreferenceFragment.AddPreferencesFromResource](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromResource/p/System.Int32/)方法。 片段的生命週期中呼叫這個方法在邏輯上會設定在`OnCreate`方法。

`PreferenceFragment`圖所示更新版本所建立從 XML 載入資源。 資源檔是：

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

片段的喜好設定的程式碼如下所示：

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

 <a name="Querying_Activities_to_Create_a_Preference_Fragment" />


### <a name="querying-activities-to-create-a-preference-fragment"></a>查詢活動，以建立喜好設定片段

建立另一個技術`PreferenceFragment`需要查詢活動。 每個活動可以使用[中繼資料\_金鑰\_喜好設定](https://developer.xamarin.com/api/field/Android.Preferences.PreferenceManager.MetadataKeyPreferences/)將指向的 XML 資源檔的屬性。 這由在裝飾的活動中 Xamarin.Android， `MetaDataAttribute`，然後指定要使用的資源檔。 `PreferenceFragment`類別會提供方法[AddPreferenceFromIntent](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromIntent/p/Android.Content.Intent/))，可以用來查詢來尋找此 XML 資源，並擴大喜好設定的階層架構的活動。

此程序的範例中會使用下列程式碼片段提供`AddPreferencesFromIntent`建立`PreferenceFragment`:

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

Android 會查看類別`MyActivityWithPreference`。 此類別必須裝飾與`MetaDataAttribute,`如下列程式碼片段所示：

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

`MetaDataAttribute`會宣告為 XML 資源檔`PreferenceFragment`將可擴大喜好設定階層。 如果`MetatDataAttribute`未提供，則會在執行階段擲回例外狀況。 當執行此程式碼時，`PreferenceFragment`隨即出現，如下列螢幕擷取畫面所示：

[![範例應用程式的顯示 PreferenceFragment 螢幕擷取畫面](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)
