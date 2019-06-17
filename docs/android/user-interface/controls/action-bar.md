---
title: ActionBar
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 95ebcc8ef436c90e807045bd009b35ff1c3e9c1f
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740812"
---
# <a name="actionbar"></a>ActionBar


## <a name="overview"></a>總覽

當使用`TabActivity`，建立索引標籤圖示的程式碼有針對 Android 4.0 架構執行時沒有作用。 雖然的 Android 2.3 起之前, 的版本中所顯示的一樣，它的運作功能`TabActivity`類別本身在 4.0 中已被取代。 已引進使用動作列中，接下來，我們將討論新的方式，來建立索引標籤式的介面。


## <a name="action-bar-tabs"></a>動作列的索引標籤

動作列包括支援 Android 4.0 中加入索引標籤式的介面。
下列螢幕擷取畫面顯示這類介面的範例。

[![在模擬器中; 中執行的應用程式的螢幕擷取畫面兩個索引標籤會顯示](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

若要建立索引標籤，動作列中，我們必須先設定其`NavigationMode`屬性，以支援索引標籤。 在 Android 4 中，`ActionBar`屬性是用於活動類別，我們可以用來設定`NavigationMode`如下所示：

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

完成之後，我們還可以建立索引標籤，藉由呼叫`NewTab`動作列上的方法。 與這個索引標籤執行個體中，我們可以呼叫`SetText`和`SetIcon`方法來設定索引標籤的標籤文字和圖示，如下所示的程式碼中的順序進行這些呼叫：

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

我們可以加入索引標籤，不過之前，我們需要處理`TabSelected`事件。 在這個處理常式，我們可以建立索引標籤的內容。動作列的索引標籤設計用於搭配*片段*，這些是代表活動中的使用者介面的一部分的類別。 針對此範例中，片段的檢視包含單一`TextView`，這我們擴充中我們`Fragment`子類別，像這樣：

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

事件引數傳入`TabSelected`事件的型別是否`TabEventArgs`，其中包括`FragmentTransaction`可用來在新增的片段，如下所示的屬性：

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

最後，我們可以將索引標籤加入動作列藉由呼叫`AddTab`方法，在此程式碼所示：

```csharp
this.ActionBar.AddTab (tab);
```

完整的範例，請參閱*HelloTabsICS*這份文件的範例程式碼中的專案。


## <a name="shareactionprovider"></a>ShareActionProvider

`ShareActionProvider`類別可讓您採取的動作列中的共用動作。 它會負責建立動作檢視可以處理共用的意圖，並會保留先前使用的應用程式，以方便存取的歷程記錄，稍後從動作列中的應用程式的清單。 這可讓應用程式共用資料透過 Android 中一致的使用者體驗。


### <a name="image-sharing-example"></a>映像共用的範例

例如，以下是螢幕擷取畫面與功能表項目動作列共用映像 (取自[ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)範例)。 當使用者點選動作列上的功能表項目時，ShareActionProvider 載入應用程式處理相關聯之意圖`ShareActionProvider`。 在此範例中，訊息的應用程式已先前使用，因此它會顯示動作列上。

[![傳訊動作列中的應用程式圖示的螢幕擷取畫面](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


當使用者按一下動作列中的項目時，且包含共用的映像的傳訊應用程式啟動時，如下所示：

[![顯示 monkey 映像的傳訊應用程式的螢幕擷取畫面](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


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


### <a name="inflating-the-menu"></a>擴張功能表

若要擴充 [] 功能表中，我們覆寫`OnCreateOptionsMenu`中活動的子類別。 一旦我們有 [] 功能表中的參考，我們可以得到`ShareActionProvider`從`ActionProvider`屬性的功能表項目，然後使用 SetShareIntent 方法，以設定`ShareActionProvider`的意圖，如下所示：

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

`ShareActionProvider`將使用的意圖，傳遞至`SetShareIntent`上述程式碼，以啟動適當的活動中的方法。 在此情況下，我們會建立映像使用下列程式碼中傳送之意圖：

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

上述程式碼範例中的映像會包含做為與應用程式的資產，並複製到可公開存取的位置建立活動時，會將其他應用程式，例如訊息的應用程式存取。 本文所附的範例程式碼包含完整的來源，此範例，說明其用途。



## <a name="related-links"></a>相關連結

- [Hello 索引標籤 ICS （範例）](https://developer.xamarin.com/samples/monodroid/HelloTabsICS/)
- [ShareActionProvider 示範 （範例）](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [簡介 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
