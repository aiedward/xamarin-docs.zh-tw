---
title: 在 Xamarin.Android 中的權限
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 204dd903586164691d068a956e741c406df10b36
ms.sourcegitcommit: 9492e417f739772bf264f5944d6bae056e130480
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53746865"
---
# <a name="permissions-in-xamarinandroid"></a>在 Xamarin.Android 中的權限


## <a name="overview"></a>總覽

在自己的沙箱中執行的 android 應用程式和安全性原因而無法存取某些系統資源或硬體裝置上。 使用者必須明確授與應用程式的權限，才可以使用這些資源。 例如，應用程式無法從使用者存取 GPS 明確許可的裝置上。 Android 將會擲回`Java.Lang.SecurityException`如果應用程式嘗試存取受保護的資源，而不需要權限。

在中宣告的權限**AndroidManifest.xml**應用程式開發人員在開發應用程式時。 Android 提供兩個不同的工作流程，以取得這些權限的使用者的同意：
 
* 針對 Android 5.1 （API 層級 22） 或更舊版本為目標的應用程式，已安裝的應用程式發生權限要求。 如果使用者未授與權限，則應用程式就不會安裝。 應用程式安裝之後，沒有任何方法可以撤銷的權限，除了解除安裝應用程式。
* 從 Android 6.0 （API 層級 23） 開始，已提供使用者更充分掌控權限;他們可以授與或撤銷權限，只要在裝置上安裝應用程式。 此螢幕擷取畫面顯示 Google 連絡人 」 應用程式的權限設定。 它會列出各種權限，並可讓使用者啟用或停用權限：

![範例 [權限] 畫面](permissions-images/01-permissions-check.png) 

Android 應用程式必須在執行階段以查看它們是否已存取受保護的資源的權限檢查。 如果應用程式沒有權限，它就必須進行授與權限使用適用於使用者的 Android SDK 所提供的新 Api 的要求。 權限分成兩個類別：

* **一般權限**&ndash;這些是會以使用者的安全性或隱私權的一些安全性風險的權限。 Android 6.0 在安裝時，將會自動授予一般權限。 請參閱 Android 文件，如[一般使用權限的完整清單](https://developer.android.com/guide/topics/permissions/normal-permissions.html)。
* **危險的使用權限**&ndash;相較於一般的使用權限，危險的使用權限是保護使用者的安全性或隱私權。 這些都必須明確地授與使用者。 傳送或接收 SMS 訊息是動作的需要危險的使用權限的範例。

> [!IMPORTANT]
> 權限所屬的類別可能會隨著時間改變。  可以，已歸類為 「 正常 」 的權限可能是權限提升未來 API 層級為危險的使用權限。

危險的使用權限會進一步細分成[_權限群組_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups)。 權限群組將會保留以邏輯方式相關的權限。 當使用者權限授與某個群組的成員權限，Android 會自動授與權限給該群組的所有成員。 例如， [ `STORAGE` ](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE)權限群組會保存同時`WRITE_EXTERNAL_STORAGE`和`READ_EXTERNAL_STORAGE`權限。 如果使用者授與的權限`READ_EXTERNAL_STORAGE`，然後在`WRITE_EXTERNAL_STORAGE`權限自動授與在相同的時間。

在要求之前一或多個權限，最好提供有關為什麼應用程式所需的權限，才能要求的權限的基本原理。 一旦使用者瞭解基本原理，應用程式可以向使用者要求權限。 了解基本原理，使用者可以做出明智的決策，如果他們想要授與權限，並了解其影響，如果不相符。 

整個工作流程的檢查，並要求權限就所謂_執行階段權限_檢查，與下圖概述： 

[![執行階段權限核取流程圖表](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

Android 支援程式庫反向移植的一些新的權限較舊版本的 Android Api。 這些向前移植 Api 會自動檢查裝置上的 Android 版本，因此它不需要執行 API 層級檢查每一次。  

本文將討論如何將權限新增至 Xamarin.Android 應用程式以及目標為 Android 6.0 （API 層級 23） 應用程式或更高版本應該執行的執行階段權限檢查。


> [!NOTE]
> 可以針對硬體的權限可能會影響應用程式的 Google Play 所篩選的方式。 例如，如果應用程式需要權限的相機，然後 Google Play 不會顯示應用程式在 Google Play 商店，並沒有安裝相機的裝置上。


<a name="requirements" />

## <a name="requirements"></a>需求

強烈建議，Xamarin.Android 專案會包含[Xamarin.Android.Support.Compat](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/) NuGet 套件。 特定的 Api，較舊版本的 Android，提供一個通用介面而不需要持續此封裝會將權限檢查應用程式執行的 Android 的版本。


## <a name="requesting-system-permissions"></a>要求系統權限

使用 Android 的使用權限的第一個步驟是宣告的權限，在 Android 資訊清單檔案。 這必須完成的 API 層級不論應用程式為目標。

目標 Android 6.0 或更新版本無法假設因為授與使用者在過去，在某個時間點的權限的權限將會有效下一次的應用程式。 目標 Android 6.0 的應用程式必須一律會執行執行階段權限檢查。 目標 Android 5.1 或較低的應用程式不需要執行的執行階段權限檢查。

> [!NOTE]
> 應用程式應該只要求它們需要的權限。


### <a name="declaring-permissions-in-the-manifest"></a>宣告資訊清單中的權限

若要新增權限**AndroidManifest.xml**使用`uses-permission`項目。 例如，如果應用程式是要找出裝置的位置，它需要正常並且課程位置的權限。 下列兩個項目會加入至資訊清單： 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

它是可以宣告使用 Visual Studio 內建工具支援的權限：

1. 按兩下**屬性**中**方案總管**，然後選取**Android 資訊清單** 索引標籤，在 屬性 視窗中：

    [![在 [Android 資訊清單] 索引標籤中的必要權限](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. 如果應用程式還沒有 AndroidManifest.xml，按一下 **否 AndroidManifest.xml 找到。按一下即可加入一個**，如下所示：

    [![任何 AndroidManifest.xml 訊息](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. 選取您的應用程式需要的任何權限**必要的權限**清單，並儲存：

    [![選取範例相機權限](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

它是可以宣告使用建置到 Visual Studio for Mac 的工具支援的權限：

1. 按兩下專案中的**Solution Pad** ，然後選取**選項 > 建置 > Android 應用程式**:

    [![所示的必要權限區段](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. 按一下 **新增 Android 資訊清單**按鈕，如果專案已經沒有**AndroidManifest.xml**:

    [![遺漏專案的 Android 資訊清單](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. 選取您的應用程式需要的任何權限**必要的權限**清單，然後按一下**確定**:

    [![選取範例相機權限](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin.Android 會自動加入某些權限在建置階段偵錯組建。 這會讓偵錯應用程式更容易。 特別是，兩個值得注意的權限都`INTERNET`和`READ_EXTERNAL_STORAGE`。 這些會自動設定權限不會出現在中啟用**必要的權限**清單。 發行組建，不過，請使用明確設定中的權限**必要的權限**清單。 

針對 Android 5.1 （API 層級 22） 或更舊版本為目標的應用程式，就沒什麼，必須完成。 將執行 Android 6.0 （API 23 層級 23） 或更高的應用程式應該繼續進行下一節有關如何執行權限檢查的執行的階段。 


### <a name="runtime-permission-checks-in-android-60"></a>在 Android 6.0 的執行階段權限檢查

`ContextCompat.CheckSelfPermission` （適用於 Android 支援程式庫） 的方法用來檢查特定的權限已被授與。 這個方法會傳回[ `Android.Content.PM.Permission` ](https://developer.xamarin.com/api/type/Android.Content.PM.Permission/)列舉有兩個值之一：

* **`Permission.Granted`** &ndash; 已授與的指定權限。
* **`Permission.Denied`** &ndash; 未授與的指定權限。

此程式碼片段是如何在活動中的 [相機] 權限檢查的範例： 

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

它是以通知使用者權限為何是必要的應用程式，以便可以進行明智的決策，授與權限的最佳作法。 舉例來說，這會是應用程式採用相片和地理標記它們。 很顯然給使用者，相機的權限是必要的但它可能不清楚為什麼應用程式也需要裝置的位置。 基本原理應該會顯示訊息來幫助使用者了解 [位置] 使用權限為何偏向和需要的相機的權限。

`ActivityCompat.ShouldShowRequestPermissionRationale`方法用來判斷是否應該向使用者顯示的基本原理。 這個方法會傳回`true`如果應顯示指定的權限的基本原理。 此螢幕擷取畫面顯示 Snackbar，說明為什麼應用程式必須知道裝置位置的應用程式所顯示的範例：

![位置的基本原理](permissions-images/07-rationale-snackbar.png) 

如果使用者授與權限，`ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)`應該呼叫方法。 這個方法需要下列參數：

* **活動**&ndash;這是正在要求權限和 Android 的結果所要通知的活動。
* **權限**&ndash;要求的權限清單。
* **requestCode** &ndash;整數值，用來比對的權限要求的結果`RequestPermissions`呼叫。 這個值應大於零。

此程式碼片段是已討論過的兩個方法的範例。 首先，會進行檢查，以判斷是否應該顯示的權限的基本原理。 如果要顯示的基本原理，則會顯示 Snackbar 與基本原理。 如果使用者按一下**確定**中 Snackbar，然後應用程式會要求這些權限。 如果使用者不接受的基本原理，然後應用程式應該不繼續要求的權限。 如果未顯示基本原理，活動將會要求權限：

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

`RequestPermission` 即使使用者已授與權限，可以呼叫。 後續的呼叫不是必要的但可提供使用者有機會確認 （或撤銷） 的權限。 當`RequestPermission`是呼叫，控制會遞交給作業系統，它將會顯示 UI，以接受權限：  

![權限對話方塊](permissions-images/08-location-permission-dialog.png)

使用者已完成之後，Android 會傳回結果至活動的回呼方法，透過`OnRequestPermissionResult`。 這個方法屬於介面`ActivityCompat.IOnRequestPermissionsResultCallback`它必須實作的活動。 這個介面具有單一方法`OnRequestPermissionsResult`，這會叫用 Android 通知活動的使用者的選擇。 如果使用者已授與權限，然後應用程式可以繼續並使用受保護的資源。 如何實作的範例`OnRequestPermissionResult`如下所示： 

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
            Snackbar.Make(layout, Resource.String.permission_available_camera, Snackbar.LengthShort).Show();            
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

本指南會討論如何新增和檢查在 Android 裝置的權限。 權限 （API 層級 < 23） 的舊 Android 應用程式與新的 Android 應用程式之間的運作方式的差異 （API 層級 > 22）。 它討論如何在 Android 6.0 中執行執行階段權限檢查。


## <a name="related-links"></a>相關連結

- [一般權限的清單](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [執行階段權限範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [在 Xamarin.Android 中的處理權限](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
