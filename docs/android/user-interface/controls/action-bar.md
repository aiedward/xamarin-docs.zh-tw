---
title: ActionBar
ms.topic: article
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 64a5ac7e0c448205da66f9790a506ca34a944140
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="actionbar"></a>ActionBar


## <a name="overview"></a>總覽

當使用`TabActivity`，建立索引標籤圖示的程式碼已針對 Android 4.0 framework 執行時沒有作用。 雖然功能上的 Android 2.3 之前, 的版本中一樣`TabActivity`類別本身在 4.0 中已被取代。 若要建立索引標籤式的介面的新方式引入使用動作列中，我們將在接下來討論。


## <a name="action-bar-tabs"></a>動作列索引標籤

在動作列包含 Android 4.0 中加入索引標籤式的介面的支援。
下列螢幕擷取畫面顯示這類介面的範例。

[![在模擬器中; 中執行的應用程式的螢幕擷取畫面會顯示兩個索引標籤](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

若要建立索引標籤，在動作列中，我們需要將其`NavigationMode`屬性，以支援索引標籤。 在 Android 4 中，`ActionBar`屬性會使用活動類別可用來設定`NavigationMode`如下所示：

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

一旦這麼做，我們可以呼叫來建立索引標籤`NewTab`在動作列上的方法。 與這個索引標籤執行個體中，我們就可以呼叫`SetText`和`SetIcon`方法來設定索引標籤的標籤文字和圖示; 這些呼叫的順序如下所示的程式碼中：

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

我們可以加入索引標籤，不過，我們需要先處理`TabSelected`事件。 在這個處理常式，我們可以建立索引標籤的內容。動作列索引標籤設計來搭配*片段*，這些是代表活動中的使用者介面的一部分的類別。 此範例中，為片段的檢視包含單一`TextView`，我們在擴大的我們`Fragment`像這樣子類別：

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

事件引數傳入`TabSelected`事件屬於型別`TabEventArgs`，其中包括`FragmentTransaction`屬性可讓我們加入片段，如下所示：

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

最後，我們可以將索引標籤加入動作列藉由呼叫`AddTab`方法在此程式碼所示：

```csharp
this.ActionBar.AddTab (tab);
```

完整的範例，請參閱*HelloTabsICS*這份文件的範例程式碼中的專案。


## <a name="shareactionprovider"></a>ShareActionProvider

`ShareActionProvider`類別可讓動作列之間的共用動作。 它會負責建立動作檢視與應用程式可以處理共用意圖，並且會保留先前使用的應用程式，以方便存取這些稍後從動作列清單。 這可讓應用程式共用資料透過整個 Android 都是一致的使用者體驗。


### <a name="image-sharing-example"></a>映像共用的範例

例如，以下是與功能表項目動作列共用映像的螢幕擷取畫面 (取自[ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)範例)。 當使用者點選 [動作] 列上的功能表項目時，ShareActionProvider 載入應用程式來處理相關聯的意圖`ShareActionProvider`。 在此範例中，訊息的應用程式已先前使用，因此它會顯示在 [動作] 列上。

[![訊息在動作列中的應用程式圖示的螢幕擷取畫面](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


當使用者按一下 [動作] 列中的項目時，且包含共用的映像傳訊應用程式啟動時，如下所示：

[![顯示猴子影像的郵件應用程式的螢幕擷取畫面](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>指定動作提供者類別

若要使用`ShareActionProvider`，將`android:actionProviderClass`上之屬性的 XML for 動作列功能表的功能表項目，如下所示：

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

若要增加 [] 功能表中，我們覆寫`OnCreateOptionsMenu`活動子類別中。 一旦功能表的參考，我們可以得到`ShareActionProvider`從`ActionProvider`屬性功能表項目，然後使用 SetShareIntent 方法，以設定`ShareActionProvider`的意圖，如下所示：

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


### <a name="creating-the-intent"></a>建立目的

`ShareActionProvider`將使用意圖，傳遞至`SetShareIntent`上述的程式碼，以啟動適當的活動中的方法。 在此情況下，我們建立試圖傳送映像使用下列程式碼：

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

在上述程式碼範例影像是包含為應用程式與資產，複製到可公開存取的位置建立活動時，因此將無法存取其他應用程式，例如傳訊應用程式存取。 本文章的範例程式碼包含完整的來源，此範例，說明其用途。



## <a name="related-links"></a>相關連結

- [Hello 索引標籤 ICS （範例）](https://developer.xamarin.com/samples/HelloTabsICS/)
- [ShareActionProvider 示範 （範例）](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [介紹的冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
