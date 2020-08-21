---
title: 封裝磨損應用程式
description: 本文說明如何封裝 Android 磨損應用程式。
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 83c4ba8cbdc360682a4e06a885be15dd20d0f249
ms.sourcegitcommit: f4b26c5b8cc84f79123951e80c15061eb859452d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720301"
---
# <a name="packaging-wear-apps"></a>封裝磨損應用程式

> [!WARNING]
> 您可能無法再維護下列檔和範例專案。
> 從 [Xamarin. android 11.1][xa-11.1]，不再支援在 android 掌上型應用程式內自動封裝 android 磨損應用程式。 建議您改為將 Android 磨損應用程式發佈為 [獨立應用程式][standalone] 。

Android 磨損1.0 應用程式封裝了完整的 Android 應用程式，可在 Google Play 上散發。

Android 磨損2.0 應用程式可提交至 Google Play 作為 [獨立應用程式][standalone]。

[xa-11.1]: https://docs.microsoft.com/xamarin/android/release-notes/11/11.1
[standalone]: https://developer.android.com/training/wearables/apps/standalone-apps

## <a name="automatic-packaging"></a>自動封裝

從 Xamarin Android 5.0 開始，當您建立從掌上型專案到磨損專案的專案參考時，會自動將您的損耗應用程式封裝為掌上型應用程式中的資源。 您可以使用下列步驟來建立此關聯： 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 如果您的磨損應用程式還不是掌上型解決方案的一部分，請以滑鼠右鍵按一下方案節點，然後選取 [ **加入現有專案] > 加入現有專案**...]。

2. 流覽至您的「磨損」應用程式的 **.csproj** 檔案，選取它，然後按一下 [ **開啟**]。 您的掌上型方案現在應該會顯示「磨損應用程式」專案。

3. 以滑鼠右鍵按一下 [ **參考** ] 節點，然後選取 [ **加入參考**]。

4. 在 [ **參考管理員** ] 對話方塊中，啟用您的磨損專案 (按一下以新增核取記號) ，然後按一下 **[確定]**。

5. 變更您的磨損專案的套件名稱，使其符合掌上型專案的套件名稱 (套件名稱可在 [ **屬性 > Android 資訊清單**) ] 下變更。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 如果您的磨損應用程式還不是掌上型解決方案的一部分，請以滑鼠右鍵按一下方案節點，然後選取 [ **加入現有專案] > 加入現有專案**...]。

2. 流覽至您的「磨損」應用程式的 **.csproj** 檔案，選取它，然後按一下 [ **開啟**]。 您的掌上型方案現在應該會顯示「磨損應用程式」專案。

3. 以滑鼠右鍵按一下方案中的掌上型專案節點，然後按一下 [ **編輯參考 ...**]。

4. 在 [ **編輯參考** ] 對話方塊中，啟用您的磨損專案 (按一下以新增核取記號) ，然後按一下 **[確定]**。

5. 變更您的磨損專案的套件名稱，使其符合掌上型專案的套件名稱 (套件名稱可在 [專案選項] 下變更 **> Android 應用程式**) 。

-----

請注意，如果磨損應用程式的套件名稱與掌上型應用程式的套件名稱不相符，您將會收到 **XA5211** 錯誤。 例如：

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

若要更正這個錯誤，請變更「磨損」應用程式的套件名稱，使其符合掌上型應用程式的套件名稱。

當您按一下 [ **組建 >** 組建] 時，此關聯會觸發將磨損專案自動封裝至主要掌上型 (Phone) 專案的程式。 應用程式會自動建立並納入為掌上型應用程式中的資源。

在掌上型 (Phone) 專案中，不會使用磨損應用程式專案所產生的元件作為元件參考。 相反地，建立程式會執行下列作業：

- 確認封裝名稱相符。 

- 產生 XML 並將它新增至掌上型專案，以將它與磨損應用程式產生關聯。 例如： 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

- 將磨損應用程式新增為掌上型專案的 **原始** 資源。 

## <a name="manual-packaging"></a>手動封裝

您可以在5.0 版之前，在 Xamarin 中撰寫 Android 磨損應用程式，但您必須遵循這些手動封裝指示來散發應用程式： 

1. 確定您的穿戴式專案和掌上型 (Phone) 專案具有相同的版本號碼和套件名稱。

2. 手動將穿戴式專案建立為 **發行** 組建。

3. 手動新增發行 **。** 從步驟 (2) APK 至掌上型 (Phone) 專案的 **Resources/raw** 目錄。

4. 手動將新的 XML 資源 **資源/XML/wearable_app_desc.xml** 新增至掌上型專案中，該專案是指步驟 (3) 的穿戴式 **APK** ：

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. 手動將 `<meta-data />` 專案加入至掌上型專案的 **AndroidManifest.xml**專案 `<application>` ，以參考新的 XML 資源：

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

另請參閱 Android 開發人員網站的 [手動 packging 指示](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)。
