---
title: 適用于 Xamarin 的 ActionBar
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 7717e247ca3b3ecf82e1814ab43e8351b4f75ba6
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510445"
---
# <a name="actionbar-for-xamarinandroid"></a>適用于 Xamarin 的 ActionBar

使用時`TabActivity`, 用來建立索引標籤圖示的程式碼在針對 Android 4.0 架構執行時, 不會有任何作用。 雖然功能的`TabActivity`運作方式與2.3 之前的 Android 版本相同, 但類別本身在4.0 中已被取代。 建立索引標籤式介面的新方式引進了使用動作列, 我們將在下一步討論。


## <a name="action-bar-tabs"></a>動作列索引標籤

動作列包含在 Android 4.0 中新增索引標籤式介面的支援。
下列螢幕擷取畫面顯示這類介面的範例。

[![在模擬器中執行之應用程式的螢幕擷取畫面;顯示兩個索引標籤](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

若要在動作列中建立索引標籤, 我們必須先`NavigationMode`將其屬性設定為支援索引標籤。 在 Android 4 中, `ActionBar`活動類別上提供屬性, 我們可以用它來設定, 如下所`NavigationMode`示:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

完成後, 我們就可以在動作列上呼叫`NewTab`方法來建立索引標籤。 有了這個索引標籤實例, 我們`SetText`可以`SetIcon`呼叫和方法來設定索引標籤的標記文字和圖示; 這些呼叫會依照下列程式碼中的順序來進行:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

不過, 我們必須先處理`TabSelected`事件, 才可以加入索引標籤。 在此處理程式中, 我們可以建立索引標籤的內容。動作列索引標籤是設計用來處理*片段*, 這是代表活動中部分使用者介面的類別。 在此範例中, 片段的 view 包含單一`TextView`, 我們`Fragment`在子類別中擴充, 如下所示:

```csharp
class SampleTabFragment: Fragment
{           
    public override View OnCreateView (LayoutInflater inflater,
        ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreateView (inflater, container, savedInstanceState);

        var view = inflater.Inflate (
            Resource.Layout.Tab, container, false);

        var sampleTextView =
            view.FindViewById<TextView> (Resource.Id.sampleTextView);            
        sampleTextView.Text = "sample fragment text";


        return view;
    }
}
```

`TabSelected`事件中傳遞的事件引數屬於類型`TabEventArgs`, 其中包含`FragmentTransaction`可用來新增片段的屬性, 如下所示:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

最後, 我們可以藉由呼叫`AddTab`方法 (如下列程式碼所示), 將索引標籤新增至動作列:

```csharp
this.ActionBar.AddTab (tab);
```

如需完整範例, 請參閱本檔的範例程式碼中的*HelloTabsICS*專案。


## <a name="shareactionprovider"></a>ShareActionProvider

`ShareActionProvider`類別可讓共用動作從動作列進行。 它會負責建立一個 [動作] 視圖, 其中包含可處理共用意圖的應用程式清單, 並保留先前使用之應用程式的歷程記錄, 方便日後從動作列存取。 這可讓應用程式透過 Android 中一致的使用者體驗來共用資料。


### <a name="image-sharing-example"></a>影像共用範例

例如, 下面是一個動作列的螢幕擷取畫面, 其中包含可共用影像的功能表項目 (取自[ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)範例)。 當使用者在動作列上按下功能表項目時, ShareActionProvider 會載入應用程式來處理與`ShareActionProvider`相關聯的意圖。 在此範例中, 先前已使用訊息應用程式, 因此會顯示在動作列上。

[![動作列中訊息應用程式圖示的螢幕擷取畫面](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


當使用者按一下動作列中的專案時, 就會啟動包含共用映射的訊息應用程式, 如下所示:

[![顯示猴子影像的訊息應用程式螢幕擷取畫面](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>指定動作提供者類別

若要使用`ShareActionProvider`, 請在`android:actionProviderClass`動作列功能表的 XML 中設定功能表項目的屬性, 如下所示:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>因而誇大功能表

為了擴充功能表, 我們在活動`OnCreateOptionsMenu`子類別中覆寫。 一旦有了功能表的參考, 我們就可以`ShareActionProvider` `ActionProvider`從功能表項目的屬性取得, 然後使用`ShareActionProvider`SetShareIntent 方法來設定的意圖, 如下所示:

```csharp
public override bool OnCreateOptionsMenu (IMenu menu)
{
    MenuInflater.Inflate (Resource.Menu.ActionBarMenu, menu);       

    var shareMenuItem = menu.FindItem (Resource.Id.shareMenuItem);           
    var shareActionProvider =
       (ShareActionProvider)shareMenuItem.ActionProvider;
    shareActionProvider.SetShareIntent (CreateIntent ());
}
```


### <a name="creating-the-intent"></a>建立意圖

會使用意圖, 傳遞給上述程式碼`SetShareIntent`中的方法, 以啟動適當的活動。 `ShareActionProvider` 在此情況下, 我們會使用下列程式碼來建立要傳送影像的意圖:

```csharp
Intent CreateIntent ()
{  
    var sendPictureIntent = new Intent (Intent.ActionSend);
    sendPictureIntent.SetType ("image/*");
    var uri = Android.Net.Uri.FromFile (GetFileStreamPath ("monkey.png"));          
    sendPictureIntent.PutExtra (Intent.ExtraStream, uri);
    return sendPictureIntent;
}
```

上述程式碼範例中的影像會以資產的形式包含在活動建立時, 並複製到可公開存取的位置, 讓其他應用程式 (例如訊息應用程式) 可以存取。 本文隨附的範例程式碼包含此範例的完整來源, 並說明其用法。



## <a name="related-links"></a>相關連結

- [Hello 索引標籤 ICS (範例)](https://developer.xamarin.com/samples/monodroid/HelloTabsICS/)
- [ShareActionProvider 示範 (範例)](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [霜淇淋三明治簡介](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平臺](https://developer.android.com/sdk/android-4.0.html)
