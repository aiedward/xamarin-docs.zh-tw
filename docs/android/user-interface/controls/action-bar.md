---
title: 適用于 Xamarin 的 ActionBar
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 45b6c080f6e50efc4648c5b2eab6db0634dee7f1
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457363"
---
# <a name="actionbar-for-xamarinandroid"></a>適用于 Xamarin 的 ActionBar

使用時 `TabActivity` ，在 Android 4.0 架構上執行時，用來建立索引標籤圖示的程式碼不會有任何作用。 雖然功能上的運作方式與2.3 之前的 Android 版本相同，但是 `TabActivity` 類別本身在4.0 中已經被取代。 引進了新的方法來建立索引標籤式介面，其中會使用動作列，我們將在接下來討論。

## <a name="action-bar-tabs"></a>動作列] 索引標籤

動作列包含在 Android 4.0 中新增索引標籤式介面的支援。
下列螢幕擷取畫面顯示這類介面的範例。

[![在模擬器中執行之應用程式的螢幕擷取畫面;顯示兩個索引標籤](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

若要在動作列中建立索引標籤，我們必須先將其屬性設定為支援索引標籤 `NavigationMode` 。 在 Android 4 中，您 `ActionBar` 可以使用活動類別上的屬性來設定， `NavigationMode` 如下所示：

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

完成這項操作之後，我們就可以在動作列上呼叫方法來建立索引標籤 `NewTab` 。 使用這個索引標籤實例，我們可以呼叫 `SetText` 和 `SetIcon` 方法來設定索引標籤的標籤文字和圖示; 這些呼叫會依照下列程式碼中的順序進行，如下所示：

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

不過，我們必須先處理事件，才可以新增索引標籤 `TabSelected` 。 在此處理程式中，我們可以建立索引標籤的內容。動作列索引標籤的設計目的是要使用 *片段*，這是代表活動中使用者介面一部分的類別。 在此範例中，片段的視圖包含單一 `TextView` ，我們會在子類別中擴充， `Fragment` 如下所示：

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

事件中傳遞的事件引數屬於 `TabSelected` 型別 `TabEventArgs` ，其中包含 `FragmentTransaction` 可用於新增片段的屬性，如下所示：

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

最後，我們可以藉由呼叫方法，將索引標籤新增至動作列，如下列程式 `AddTab` 代碼所示：

```csharp
this.ActionBar.AddTab (tab);
```

如需完整範例，請參閱本檔的範例程式碼中的 *HelloTabsICS* 專案。

## <a name="shareactionprovider"></a>ShareActionProvider

`ShareActionProvider`類別可讓您從動作列進行共用動作。 它會使用可處理共用意圖的應用程式清單來建立動作視圖，並保留先前使用的應用程式記錄，以便稍後從動作列存取這些應用程式。 這可讓應用程式透過整個 Android 中一致的使用者體驗來共用資料。

### <a name="image-sharing-example"></a>影像共用範例

例如，以下是動作列的螢幕擷取畫面，其中有一個功能表項目可共用 (取自 [ShareActionProvider](/samples/xamarin/monodroid-samples/shareactionproviderdemo) 範例) 的影像。 當使用者在動作列上按下功能表項目時，ShareActionProvider 會載入應用程式以處理與相關聯的意圖 `ShareActionProvider` 。 在此範例中，先前已使用訊息應用程式，因此它會顯示在動作列上。

[![動作列中訊息應用程式圖示的螢幕擷取畫面](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)

當使用者按一下動作列中的專案時，就會啟動包含共用映射的訊息應用程式，如下所示：

[![顯示猴子影像的訊息應用程式螢幕擷取畫面](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)

### <a name="specifying-the-action-provider-class"></a>指定動作提供者類別

若要使用 `ShareActionProvider` ，請 `android:actionProviderClass` 針對動作列的功能表，在 XML 中設定功能表項目上的屬性，如下所示：

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

為了擴充功能表，我們會 `OnCreateOptionsMenu` 在活動子類別中覆寫。 一旦有了功能表的參考之後，就可以 `ShareActionProvider` 從 `ActionProvider` 功能表項目的屬性取得，然後使用 SetShareIntent 方法來設定 `ShareActionProvider` 意圖，如下所示：

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

`ShareActionProvider`將會使用傳遞至 `SetShareIntent` 上述程式碼中方法的意圖，以啟動適當的活動。 在此情況下，我們會使用下列程式碼來建立傳送影像的意圖：

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

上述程式碼範例中的影像會以資產的形式包含在應用程式中，並在建立活動時複製到可公開存取的位置，讓其他應用程式（例如訊息應用程式）都能存取。 本文隨附的範例程式碼包含此範例的完整來源，並說明其用法。

## <a name="related-links"></a>相關連結

- [Hello 定位 ICS (範例) ](/samples/xamarin/monodroid-samples/hellotabsics)
- [ShareActionProvider 示範 (範例) ](/samples/xamarin/monodroid-samples/shareactionproviderdemo)