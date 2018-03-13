---
title: "Xamarin.Android 中的權限"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/09/2018
ms.openlocfilehash: 39ee7f826d4c775ead679a09ce56a7c0f92b60ed
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="permissions-in-xamarinandroid"></a>Xamarin.Android 中的權限


## <a name="overview"></a>總覽

他們自己的沙箱中執行的 android 應用程式和安全性的原因而無法存取某些系統資源或硬體裝置上。 使用者必須明確授與應用程式的權限，才可以使用這些資源。 例如，應用程式無法從使用者存取 GPS 沒有明確的權限在裝置上。 Android 將會擲回`Java.Lang.SecurityException`如果應用程式嘗試存取沒有權限受保護的資源。

在中宣告的權限**AndroidManifest.xml**應用程式開發人員在開發應用程式時。 Android 具有兩個不同的工作流程，以取得這些權限的使用者的同意：
 
* 針對 Android 5.1 （API 層級 22） 或更舊版本為目標的應用程式，已安裝應用程式發生權限要求。 如果使用者未授與權限，則應用程式將不會安裝。 應用程式安裝之後，沒有任何方法可以撤銷的權限，除了透過解除安裝應用程式。
* 從 Android 6.0 （API 層級 23） 開始，已提供使用者更充分掌控權限。在可以授與或撤銷的權限，只要在裝置上安裝應用程式。 這個螢幕擷取畫面顯示連絡人 Google 應用程式的權限設定。 它會列出不同的權限，並可讓使用者啟用或停用權限：

![範例 [權限] 畫面](permissions-images/01-permissions-check.png) 

Android 應用程式必須在執行階段以查看它們是否有存取受保護的資源的權限檢查。 如果應用程式沒有權限，就必須繼續使用 Android SDK，使用者所提供的新 Api 授與權限要求。 權限分為兩類：

* **一般權限**&ndash;這些是風險的小安全性使用者的安全性或隱私權的權限。 Android 6.0 會自動授與一般權限時的安裝。 請參閱 Android 文件[一般權限的完整清單](https://developer.android.com/guide/topics/permissions/normal-permissions.html)。
* **危險的使用權限**&ndash;相較於一般權限，危險的使用權限是保護使用者的安全性或隱私權。 這些都必須明確授與使用者。 傳送或接收的 SMS 訊息是動作的需要危險的使用權限的範例。

> [!IMPORTANT]
> 經過一段時間，可能會變更權限所屬的類別目錄。  有可能，已分類為 「 標準 」 的權限可能是權限提升未來應用程式開發介面層級為危險的使用權限。

危險的使用權限會進一步細分成[_權限群組_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups)。 權限群組將會保留邏輯上相關的權限。 當使用者權限授與的權限群組，其中一個成員 Android 自動授與權限給該群組的所有成員。 例如， [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE)權限群組會保存同時`WRITE_EXTERNAL_STORAGE`和`READ_EXTERNAL_STORAGE`權限。 如果使用者授與的權限`READ_EXTERNAL_STORAGE`，然後在`WRITE_EXTERNAL_STORAGE`同時會自動授與權限。

在要求之前一或多個權限，最好提供有關為何應用程式所需的權限，才能要求的權限的基本原理。 一旦使用者了解基本原理，應用程式可以向使用者要求權限。 了解基本原理，使用者可以進行明智的決策，如果他們想要授與權限，並了解其影響，如果不相符。 

檢查並要求權限的整個工作流程稱為_執行階段權限_檢查，並可摘要於下圖： 

[![執行階段權限核取流程圖](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

Android 支援程式庫 backports 某些較舊版本的 Android 的權限的全新 Api。 如此就不需要執行應用程式開發介面層級檢查每次這些 backported Api 將自動檢查裝置上的 Android 版本。  

本文將討論如何加入 Xamarin.Android 應用程式的權限以及目標 Android 6.0 （API 層級 23） 的應用程式或更高版本執行執行階段權限檢查。


> [!NOTE]
> 很可能硬體的權限可能會影響應用程式的 Google Play 的篩選方式。 例如，如果應用程式所需的權限的相機，然後 Google Play 不會顯示應用程式在 Google Play 商店，並沒有安裝網路攝影機裝置上。


<a name="requirements" />

## <a name="requirements"></a>需求

強烈建議 Xamarin.Android 專案包含[Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) NuGet 封裝。 較舊版本的 Android，提供一個常見的特定 Api 不斷介面而不需要此封裝將 backport 權限檢查 Android 上執行應用程式的版本。


## <a name="requesting-system-permissions"></a>要求系統權限

使用 Android 的權限的第一個步驟是宣告在 Android 中的權限資訊清單檔案。 必須這樣不論應用程式開發介面層級應用程式為目標。

應用程式的目標 Android 6.0 或更新版本無法假設因為授與使用者在過去，在某個時間點的權限的權限將會有效下一次。 Android 6.0 為目標的應用程式必須一律會執行執行階段權限檢查。 目標 Android 5.1 或更低的應用程式不需要執行，執行階段權限檢查。

> [!NOTE]
> 應用程式應該只會要求他們所需的權限。


### <a name="declaring-permissions-in-the-manifest"></a>宣告資訊清單中的權限

若要新增權限**AndroidManifest.xml**與`uses-permission`項目。 例如，如果應用程式是要找出裝置的位置，它可以正常需要並且課程位置的權限。 下列兩個項目會加入至資訊清單： 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

它是可以宣告使用建置到 Visual Studio 的工具支援的權限：

1. 按兩下**屬性**中**方案總管 中**選取**Android 資訊清單** 索引標籤中 屬性 視窗：

    [![在 [Android 資訊清單] 索引標籤中的必要權限](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. 如果應用程式尚無 AndroidManifest.xml，按一下**否 AndroidManifest.xml 找到。按一下以加入一個**如下所示：

    [![No AndroidManifest.xml message](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. 選取您的應用程式需要從任何權限**必要的權限**清單並儲存：

    [![選取範例相機權限](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

它是可以宣告使用建置到 Visual Studio for Mac 的工具支援的權限：

1. 按兩下專案中的**方案板**選取**選項 > 建置 > Android 應用程式**:

    [![必要的權限 > 一節所示](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. 按一下**新增 Android 資訊清單**按鈕如果專案沒有的 collectioncontainers **AndroidManifest.xml**:

    [![遺漏專案的 Android 資訊清單](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. 選取您的應用程式需要從任何權限**必要的權限**清單，然後按一下**確定**:

    [![選取範例相機權限](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin.Android 自動將某些權限在建置階段加入偵錯組建。 這會讓偵錯應用程式更容易。 特別是，兩個值得注意的權限是`INTERNET`和`READ_EXTERNAL_STORAGE`。 這些會自動設定權限不會出現在中啟用**必要的權限**清單。 發行組建，不過，使用明確設定中的權限**必要的權限**清單。 

針對 Android 5.1 （API 層級 22） 或更舊版本為目標的應用程式，沒有任何多個需要執行的動作。 將執行 Android 6.0 （API 23 層級 23） 或更高的應用程式應該繼續進行下一節有關如何執行執行的階段權限檢查。 


### <a name="runtime-permission-checks-in-android-60"></a>在 Android 6.0 中的執行階段權限檢查

`ContextCompat.CheckSelfPermission`方法 （適用於 Android 的支援程式庫） 用來檢查是否被授與特定權限。 這個方法會傳回[ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/)列舉具有兩個值之一：

* **`Permission.Granted`** &ndash; 被授與指定權限。
* **`Permission.Denied`** &ndash; 未授與指定權限。

此程式碼片段是如何檢查相機權限，在活動中的範例： 

```csharp
if (ContextCompat.CheckSelfPermission(this, Manifest.Permission.Camera) == (int)Permission.Granted) 
{
    // We have permission, go ahead and use the camera.
} 
else 
{
    // Camera permission is not granted. If necessary display rationale & request.
}
```

最好通知使用者權限為何需要應用程式，以便做出明智的決策可以對授與權限。 這個範例就是應用程式採用相片和地理標記它們。 相機權限有必要，但它可能無法清除應用程式也需要裝置的位置為何，使用者清楚了解。 基本原理應該會顯示可協助使用者了解為什麼位置 使用權限，而且也比較偏向相機權限是必要的訊息。

`ActivityCompat.ShouldShowRequestPermissionRational`方法用來判斷是否應該向使用者顯示的基本原理。 這個方法會傳回`true`如果應該顯示指定的權限的基本原理。 這個螢幕擷取畫面顯示 Snackbar，說明為什麼的應用程式必須知道裝置位置的應用程式所顯示的範例：

![位置的基本原理](permissions-images/07-rationale-snackbar.png) 

如果使用者授與權限，`ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)`應該呼叫方法。 這個方法需要下列參數：

* **活動**&ndash;這是正在要求權限和 Android 的結果所要通知的活動。
* **權限**&ndash;所要求的權限清單。
* **requestCode** &ndash;整數值，用來比對結果的權限要求`RequestPermissions`呼叫。 這個值應大於零。

此程式碼片段是我們已經討論過的兩種方法的範例。 首先，會進行檢查，以判斷是否要顯示的權限的基本原理。 如果要顯示的基本原理，則會顯示 Snackbar 與基本原理。 如果使用者按一下**確定**中 Snackbar，然後應用程式將要求的權限。 如果使用者不接受基本原理，然後應用程式應該不會繼續以要求權限。 如果未顯示的基本原理，活動將要求的權限：

```csharp
if (ActivityCompat.ShouldShowRequestPermissionRationale(this, Manifest.Permission.AccessFineLocation)) 
{
    // Provide an additional rationale to the user if the permission was not granted
    // and the user would benefit from additional context for the use of the permission.
    // For example if the user has previously denied the permission.
    Log.Info(TAG, "Displaying camera permission rationale to provide additional context.");

    var requiredPermissions = new String[] { Manifest.Permission.AccessFineLocation };
    Snackbar.Make(layout, 
                   Resource.String.permission_location_rationale,
                   Snackbar.LengthIndefinite)
            .SetAction(Resource.String.ok, 
                       new Action<View>(delegate(View obj) {
                           ActivityCompat.RequestPermissions(this, requiredPermissions, REQUEST_LOCATION);
                       }    
            )
    ).Show();
}
else 
{
    ActivityCompat.RequestPermissions(this, new String[] { Manifest.Permission.Camera }, REQUEST_LOCATION);
}
```

`RequestPermission` 可以被呼叫，即使使用者已授與權限。 後續呼叫並非必要，但可提供使用者有機會確認 （或撤銷） 權限。 當`RequestPermission`是呼叫，控制會遞交給作業系統，將會顯示 UI，以便接受權限：  

![權限對話方塊](permissions-images/08-location-permission-dialog.png)

使用者完成後，Android 會傳回結果至活動的回呼方法，透過`OnRequestPermissionResult`。 這個方法是介面的一部分`ActivityCompat.IOnRequestPermissionsResultCallback`活動必須實作它。 這個介面具有單一方法`OnRequestPermissionsResult`，Android 通知活動的使用者的選擇會叫用。 如果使用者已授與權限，然後應用程式可以繼續並使用受保護的資源。 如何實作的範例`OnRequestPermissionResult`如下所示： 

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Permission[] grantResults)
{
    if (requestCode == REQUEST_LOCATION) 
    {
        // Received permission result for camera permission.
        Log.Info(TAG, "Received response for Location permission request.");

        // Check if the only required permission has been granted
        if ((grantResults.Length == 1) && (grantResults[0] == Permission.Granted)) {
            // Location permission has been granted, okay to retrieve the location of the device.
            Log.Info(TAG, "Location permission has now been granted.");
            Snackbar.Make(layout, Resource.String.permision_available_camera, Snackbar.LengthShort).Show();            
        } 
        else 
        {
            Log.Info(TAG, "Location permission was NOT granted.");
            Snackbar.Make(layout, Resource.String.permissions_not_granted, Snackbar.LengthShort).Show();
        }
    } 
    else 
    {
        base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
    }
}
```  


## <a name="summary"></a>總結

本指南將討論如何加入及 Android 裝置中的權限檢查。 權限如何運作舊 Android 應用程式 （API 層級 < 23） 和新的 Android 應用程式之間的差異 （API 層級 > 22）。 它將討論如何在 Android 6.0 中執行的執行階段權限檢查。


## <a name="related-links"></a>相關連結

- [一般權限的清單](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [執行階段權限範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [Xamarin.Android 中的處理權限](https://developer.xamarin.com/recipes/android/general/projects/add_permissions_to_android_manifest)
