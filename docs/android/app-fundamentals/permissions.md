---
title: Xamarin 中的許可權
ms.prod: xamarin
ms.assetid: 3C440714-43E3-4D31-946F-CA59DAB303E8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 1426054b60d182f7f40bf3c4b0bf69b2287ad57e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509414"
---
# <a name="permissions-in-xamarinandroid"></a>Xamarin 中的許可權


## <a name="overview"></a>總覽

Android 應用程式會在自己的沙箱中執行, 且基於安全性考慮, 無法存取裝置上的特定系統資源或硬體。 使用者必須先明確授與應用程式的許可權, 才能使用這些資源。 例如, 應用程式無法存取裝置上的 GPS, 而不需要使用者的明確許可權。 `Java.Lang.SecurityException`如果應用程式嘗試在沒有許可權的情況下存取受保護的資源, 則 Android 會擲回。

應用程式開發人員會在**androidmanifest.xml**中宣告許可權。 Android 有兩個不同的工作流程, 可取得使用者對這些許可權的同意:
 
* 針對以 Android 5.1 (API 層級 22) 或更低版本為目標的應用程式, 在安裝應用程式時, 就會發生許可權要求。 如果使用者未授與許可權, 則不會安裝應用程式。 安裝應用程式之後, 除非卸載應用程式, 否則沒有任何方法可以撤銷許可權。
* 從 Android 6.0 (API 層級 23) 開始, 使用者對許可權擁有更多控制權;只要應用程式安裝在裝置上, 他們就可以授與或撤銷許可權。 此螢幕擷取畫面顯示 Google Contacts 應用程式的許可權設定。 它會列出各種許可權, 並允許使用者啟用或停用許可權:

![範例許可權畫面](permissions-images/01-permissions-check.png) 

Android 應用程式必須在執行時間檢查, 以查看他們是否有權存取受保護的資源。 如果應用程式沒有許可權, 則必須使用 Android SDK 提供的新 Api 提出要求, 以供使用者授與許可權。 許可權分成兩個類別:

* **一般許可權**&ndash;這些是對使用者的安全性或隱私權造成較少安全性風險的許可權。 Android 6.0 會在安裝時自動授與一般許可權。 如需[一般許可權的完整清單](https://developer.android.com/guide/topics/permissions/normal-permissions.html), 請參閱 Android 檔。
* **危險的許可權**&ndash;相對於一般許可權, 危險的許可權是保護使用者的安全性或隱私權。 這些必須由使用者授與明確。 傳送或接收 SMS 訊息是需要危險許可權的動作範例。

> [!IMPORTANT]
> 許可權所屬的類別目錄可能會隨著時間而變更。  已分類為「一般」許可權的許可權可能會在未來的 API 層級提升為危險許可權。

危險的許可權會進一步細分為[_許可權群組_](https://developer.android.com/guide/topics/permissions/requesting.html#perm-groups)。 許可權群組會保留邏輯上相關的許可權。 當使用者將許可權授與許可權群組的其中一個成員時, Android 會自動將許可權授與該群組的所有成員。 例如, [`STORAGE`](https://developer.android.com/reference/android/Manifest.permission_group.html#STORAGE)許可權群組會`WRITE_EXTERNAL_STORAGE`同時保留和`READ_EXTERNAL_STORAGE`許可權。 如果使用者授與`READ_EXTERNAL_STORAGE`的許可權, 則會同時自動授與`WRITE_EXTERNAL_STORAGE`許可權。

在要求一個或多個許可權之前, 最好先提供理由, 說明為什麼應用程式需要許可權, 然後再要求許可權。 一旦使用者瞭解其基本原理, 應用程式就可以向使用者要求許可權。 藉由瞭解其基本概念, 如果使用者想要授與許可權並瞭解影響 (如果沒有的話), 則可以做出明智的決定。 

檢查和要求許可權的整個工作流程稱為「_執行時間」許可權_檢查, 並可在下圖中摘要說明: 

[![執行時間許可權檢查流程圖](permissions-images/02-permissions-workflow-sml.png)](permissions-images/02-permissions-workflow.png#lightbox)

Android 支援程式庫反向移植一些新的 Api, 以取得舊版 Android 的許可權。 這些 backport Api 會自動檢查裝置上的 Android 版本, 因此不需要每次都執行 API 層級檢查。  

本檔將討論如何將許可權新增至 Xamarin Android 應用程式, 以及以 Android 6.0 (API 層級 23) 或更高版本為目標的應用程式應該如何執行執行時間許可權檢查。


> [!NOTE]
> 硬體的許可權可能會影響應用程式 Google Play 的篩選方式。 例如, 如果應用程式需要相機的許可權, 則 Google Play 不會在未安裝相機的裝置上顯示 Google Play 商店中的應用程式。


<a name="requirements" />

## <a name="requirements"></a>需求

強烈建議使用 Xamarin Android 專案, 包括[支援相容](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)的 NuGet 套件。 此套件會將許可權特定的 Api 將到舊版的 Android, 提供一個通用介面, 而不需要持續檢查應用程式執行所在的 Android 版本。


## <a name="requesting-system-permissions"></a>要求系統許可權

使用 Android 許可權的第一個步驟是在 Android 資訊清單檔案中宣告許可權。 無論應用程式瞄準的 API 層級為何, 都必須執行此動作。

以 Android 6.0 或更高版本為目標的應用程式無法假設, 因為使用者在過去某個時間點授與許可權, 所以該許可權將會在下一次生效。 以 Android 6.0 為目標的應用程式必須一律執行執行時間許可權檢查。 以 Android 5.1 或更低版本為目標的應用程式不需要執行執行時間許可權檢查。

> [!NOTE]
> 應用程式應該只要求其所需的許可權。


### <a name="declaring-permissions-in-the-manifest"></a>在資訊清單中宣告許可權

許可權會新增至**androidmanifest.xml** , 並包含`uses-permission`元素。 例如, 如果應用程式要找出裝置的位置, 它需要良好和課程的位置許可權。 下列兩個元素會新增至資訊清單: 

```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

您可以使用內建于 Visual Studio 的工具支援來宣告許可權:

1. 按兩下 **方案總管**中的 **屬性**, 然後選取屬性視窗中的  **Android 資訊清單** 索引標籤:

    [![[Android 資訊清單] 索引標籤中的必要許可權](permissions-images/04-required-permissions-vs-sml.png)](permissions-images/04-required-permissions-vs.png#lightbox)

2. 如果應用程式還沒有 androidmanifest.xml, 請按一下 **[找不到 androidmanifest.xml]。按一下以新增一個** , 如下所示:

    [![沒有 Androidmanifest.xml 的 xml 訊息](permissions-images/05-no-manifest-vs-sml.png)](permissions-images/05-no-manifest-vs.png#lightbox)

3. 從 [**必要許可權**] 清單中選取您的應用程式所需的任何許可權, 並儲存:

    [![已選取範例攝影機許可權](permissions-images/06-selected-permission-vs-sml.png)](permissions-images/06-selected-permission-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

您可以使用內建于 Visual Studio for Mac 的工具支援來宣告許可權:

1. 按兩下  **Solution Pad**中的專案, 然後選取 **選項 > 組建 > Android 應用程式**:

    [![顯示的必要許可權區段](permissions-images/04-required-permissions-xs-sml.png)](permissions-images/04-required-permissions-xs.png#lightbox)

2. 如果專案還沒有**androidmanifest.xml**, 請按一下 [**新增 Android 資訊清單**] 按鈕:

    [![缺少專案的 Android 資訊清單](permissions-images/05-no-manifest-xs-sml.png)](permissions-images/05-no-manifest-xs.png#lightbox)

3. 從 [**必要許可權**] 清單中選取您應用程式所需的任何許可權, 然後按一下 **[確定]** :

    [![已選取範例攝影機許可權](permissions-images/03-select-permission-xs-sml.png)](permissions-images/03-select-permission-xs.png#lightbox)
    
-----

Xamarin 會在組建階段自動將部分許可權新增至 Debug 組建。 這可讓您更輕鬆地對應用程式進行偵錯工具。 特別的是, 兩個值得`INTERNET`注意`READ_EXTERNAL_STORAGE`的許可權是和。 這些自動設定的許可權將不會在 [**必要許可權**] 清單中啟用。 不過, 發行組建只會使用在 [**必要許可權**] 清單中明確設定的許可權。 

若為以 Android 5.1 (API 層級 22) 或更低版本為目標的應用程式, 則不需要執行其他動作。 將在 Android 6.0 (API 23 層級 23) 或更高版本上執行的應用程式, 應該繼續進行下一節, 以瞭解如何執行執行時間許可權檢查。 


### <a name="runtime-permission-checks-in-android-60"></a>Android 6.0 中的執行時間許可權檢查

`ContextCompat.CheckSelfPermission`方法 (可用於 Android 支援程式庫) 用來檢查是否已授與特定的許可權。 這個方法會[`Android.Content.PM.Permission`](xref:Android.Content.PM.Permission)傳回列舉, 其中包含兩個值的其中一個:

* **`Permission.Granted`** &ndash;已授與指定的許可權。
* **`Permission.Denied`** &ndash;尚未授與指定的許可權。

此程式碼片段是如何檢查活動中的攝影機許可權的範例: 

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

最佳做法是將應用程式的必要許可權通知使用者, 以做出明智的決策來授與許可權。 其中一個範例是將相片和異地標記拍照的應用程式。 使用者可以清楚瞭解相機許可權, 但可能不清楚為什麼應用程式也需要裝置的位置。 基本原理應該會顯示一則訊息, 協助使用者瞭解為什麼需要 location 許可權, 以及需要攝影機許可權。

`ActivityCompat.ShouldShowRequestPermissionRationale`方法是用來判斷是否應該向使用者顯示基本原理。 `true`如果應該顯示指定許可權的基本原理, 這個方法會傳回。 此螢幕擷取畫面顯示應用程式所顯示的 Snackbar 範例, 說明應用程式為何需要知道裝置的位置:

![位置的基本原理](permissions-images/07-rationale-snackbar.png) 

如果使用者授與許可權, 則`ActivityCompat.RequestPermissions(Activity activity, string[] permissions, int requestCode)`應該呼叫方法。 這個方法需要下列參數:

* **活動**&ndash;這是要求許可權的活動, 而且會由 Android 的結果通知。
* **許可權**&ndash;所要求之許可權的清單。
* **requestCode**用來比對`RequestPermissions`呼叫之許可權要求結果的整數值。 &ndash; 這個值應大於零。

此程式碼片段是所討論兩種方法的範例。 首先, 會進行檢查, 以判斷是否應該顯示許可權的理由。 如果要顯示基本原理, 則會顯示 Snackbar, 其中包含基本原理。 如果使用者在 Snackbar 中按一下 **[確定]** , 則應用程式會要求許可權。 如果使用者不接受基本原理, 則應用程式不應該繼續要求許可權。 如果沒有顯示 [基本], 則活動會要求許可權:

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

`RequestPermission`即使使用者已授與許可權, 也可以呼叫。 後續的呼叫並不是必要的, 但可讓使用者有機會確認 (或撤銷) 許可權。 當`RequestPermission`呼叫時, 控制權會交給作業系統, 這會顯示可接受許可權的 UI:  

![許可權對話方塊](permissions-images/08-location-permission-dialog.png)

使用者完成之後, Android 會透過回呼方法, `OnRequestPermissionResult`將結果傳回給活動。 這個方法是必須由活動實作為`ActivityCompat.IOnRequestPermissionsResultCallback`介面的一部分。 此介面具有單一方法, `OnRequestPermissionsResult`其將由 Android 叫用以通知活動使用者的選擇。 如果使用者已授與許可權, 則應用程式可以繼續使用受保護的資源。 如何執行`OnRequestPermissionResult`的範例如下所示: 

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

本指南討論如何在 Android 裝置中新增和檢查許可權。 舊版 Android 應用程式 (API 層級 < 23) 和新的 Android 應用程式 (API 層級 > 22) 之間的許可權使用方式上的差異。 它討論了如何在 Android 6.0 中執行執行時間許可權檢查。


## <a name="related-links"></a>相關連結

- [一般許可權的清單](https://developer.android.com/guide/topics/permissions/normal-permissions.html)
- [執行時間許可權範例應用程式](https://github.com/xamarin/monodroid-samples/tree/master/android-m/RuntimePermissions)
- [在 Xamarin 中處理許可權](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)
