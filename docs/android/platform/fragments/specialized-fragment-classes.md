---
title: 特製的片段類別
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 75d95d630415cdaa4c0c1ed3b8ddebb32b8e3c4d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670061"
---
# <a name="specialized-fragment-classes"></a>特製的片段類別

片段 API 提供封裝中的應用程式的常見功能的一些其他子類別。 這些子類別是：

-   **ListFragment** &ndash;此片段用來顯示繫結至資料來源，例如陣列或資料指標的項目清單。

-   **DialogFragment** &ndash;此片段做為對話周圍的包裝函式。 片段會顯示對話方塊，在它的活動之上。

-   **PreferenceFragment** &ndash;此片段用來顯示為清單的喜好設定物件。



## <a name="the-listfragment"></a>ListFragment

`ListFragment`非常類似的概念和功能`ListActivity`; 它是裝載的包裝函式`ListView`片段中。 下的圖顯示`ListFragment`平板電腦和手機上執行：

[![螢幕擷取畫面的 ListFragment 手機和平板電腦上](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)


### <a name="binding-data-with-the-listadapter"></a>繫結資料與 ListAdapter

`ListFragment`類別已提供預設版面配置，因此不需要覆寫`OnCreateView`來顯示內容`ListFragment`。 `ListView`使用繫結至資料`ListAdapter`實作。 下列範例會示範如何這可能由使用簡單的字串陣列：

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

設定時`ListAdapter`，請務必使用`ListFragment.ListAdapter`屬性，而非`ListView.ListAdapter`屬性。 使用`ListView.ListAdapter`會導致重要的初始化程式碼會略過。



### <a name="responding-to-user-selection"></a>回應使用者選取項目

若要回應使用者選取項目，應用程式必須覆寫`OnListItemClick`方法。 下列範例會顯示其中一個這種可能性：

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

上方，當使用者選取的項目中的程式碼`ListFragment`，新的片段會顯示在主控的活動中，顯示所選取的項目相關的更多詳細資料。



## <a name="dialogfragment"></a>DialogFragment

*DialogFragment*是用來顯示對話方塊物件的片段會漂浮在活動的時段內的片段。 它是為了要取代受管理的對話方塊 （從 Android 3.0） 的 Api。 下列螢幕擷取畫面顯示的範例`DialogFragment`:

[![螢幕擷取畫面的 DialogFragment，顯示加入新的車輛編輯方塊](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

A`DialogFragment`可確保此片段，而且對話方塊之間的狀態保持一致。 所有互動和對話方塊物件的控制項都應該透過`DialogFragment`API，且不進行直接呼叫對話方塊物件上。 `DialogFragment` API 提供每個執行個體`Show()`用來顯示片段的方法。 有兩種方式，若要移除的片段：

-  呼叫`DialogFragment.Dismiss()`上`DialogFragment`執行個體。 

-  顯示另一個`DialogFragment`。

若要建立`DialogFragment`，類別繼承自`Android.App.DialogFragment,`，然後覆寫下列兩種方法之一：

- **OnCreateView** &ndash;這會建立並傳回的檢視。

- **OnCreateDialog** &ndash;這會建立自訂對話方塊。 通常用來顯示*AlertDialog*。 在覆寫這個方法，不需要覆寫`OnCreateView`。



### <a name="a-simple-dialogfragment"></a>簡單 DialogFragment

下列螢幕擷取畫面顯示簡單`DialogFragment`具有`TextView`並將兩個`Button`s:

[![TextView 與兩個按鈕的範例 DialogFragment](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

`TextView`會顯示使用者已按一下中的一個按鈕的次數`DialogFragment`，而按一下 [其他] 按鈕將會關閉該片段。 程式碼`DialogFragment`是：

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

像所有的片段`DialogFragment`顯示的內容中`FragmentTransaction`。

`Show()`方法`DialogFragment`採用`FragmentTransaction`和`string`做為輸入。 對話將會加入至活動，而`FragmentTransaction`認可。

下列程式碼示範一種可能的方式，可能會使用活動`Show()`方法，以顯示`DialogFragment`:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```


### <a name="dismissing-a-fragment"></a>正在解除片段

呼叫`Dismiss()`的執行個體上`DialogFragment`會導致要從活動中移除的片段，並認可該交易。
就會呼叫標準的片段生命週期方法所涉及的損毀的片段。


### <a name="alert-dialog"></a>警示對話方塊

而不是覆寫`OnCreateView`，則`DialogFragment`可能會改為覆寫`OnCreateDialog`。 這可讓應用程式建立[AlertDialog](https://developer.xamarin.com/api/type/Android.App.AlertDialog/)所管理的片段。 下列程式碼是使用範例`AlertDialog.Builder`來建立`Dialog`:

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

為了協助管理喜好設定，片段 API 提供`PreferenceFragment`子類別。 `PreferenceFragment`大致[PreferenceActivity](https://developer.xamarin.com/api/type/Android.Preferences.PreferenceActivity/) &ndash;它會顯示給使用者的喜好設定的階層架構中的片段。 當使用者互動的喜好設定時，它們將會自動儲存到[SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html)。
在 Android 3.0 或更高的應用程式中，使用`PreferenceFragment`應付應用程式中的喜好設定。 下圖顯示的範例`PreferenceFragment`:

[![範例 PreferencesFragment 內嵌、 對話方塊中，與啟動喜好設定](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)


### <a name="create-a-preference-fragment-from-a-resource"></a>從資源建立喜好設定片段

片段可能會擴大為 XML 資源檔從使用喜好設定[PreferenceFragment.AddPreferencesFromResource](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromResource/p/System.Int32/)方法。 片段的生命週期中呼叫這個方法在邏輯上會在`OnCreate`方法。

`PreferenceFragment`圖所示更新版本所建立從 XML 載入資源。 此資源檔為：

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



### <a name="querying-activities-to-create-a-preference-fragment"></a>若要建立的喜好設定片段的查詢活動

建立另一個技術`PreferenceFragment`需要查詢活動。 每個活動可以使用[中繼資料\_金鑰\_喜好設定](https://developer.xamarin.com/api/field/Android.Preferences.PreferenceManager.MetadataKeyPreferences/)將指向的 XML 資源檔的屬性。 在 Xamarin.Android 中，這是藉由裝飾的活動`MetaDataAttribute`，然後指定要使用的資源檔。 `PreferenceFragment`類別提供方法[AddPreferenceFromIntent](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromIntent/p/Android.Content.Intent/))，可用來查詢來尋找此 XML 資源及擴充它的喜好設定階層的活動。

使用下列程式碼片段中提供此程序範例`AddPreferencesFromIntent`來建立`PreferenceFragment`:

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

Android 會查看類別`MyActivityWithPreference`。 此類別必須標有`MetaDataAttribute,`如下列程式碼片段所示：

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

`MetaDataAttribute`宣告為 XML 資源檔`PreferenceFragment`將用以擴充喜好設定階層。 如果`MetatDataAttribute`未提供，則會在執行階段擲回的例外狀況。 此程式碼執行時，`PreferenceFragment`隨即出現，如下列螢幕擷取畫面所示：

[![範例應用程式的螢幕擷取畫面，顯示 PreferenceFragment 與](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
