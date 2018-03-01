---
title: "封裝損耗應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: a3eb5cd5b4202db8c58870c2b2c679b47f79d4aa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="packaging-wear-apps"></a>封裝損耗應用程式

Android 損耗應用程式是使用 Google Play 上的發佈完整 Android 應用程式封裝。 

## <a name="automatic-packaging"></a>自動封裝

從 Xamarin Android 5.0 開始，損耗應用程式會自動封裝成掌上型應用程式中的資源時的專案參考從掌上型專案建立損耗專案。 若要建立此關聯，您可以使用下列步驟： 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 如果您損耗的應用程式尚未掌上型方案的一部分，以滑鼠右鍵按一下方案節點，然後選取**新增 > 加入現有專案...**.

2. 瀏覽至**.csproj**檔案損耗應用程式，請選取它，然後按一下 **開啟**。 損耗應用程式專案現在應該可以看到掌上型方案中。

3. 以滑鼠右鍵按一下**參考**節點，然後選取**加入參考**。

4. 在**參考管理員**對話方塊中，損耗專案 （按一下以核取記號），然後按一下 的啟用**確定**。

5. 變更損耗專案的封裝名稱，使其符合掌上型專案 （package） 名稱 (可以在變更封裝名稱**屬性 > Android 資訊清單**)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 如果您損耗的應用程式尚未掌上型方案的一部分，以滑鼠右鍵按一下方案節點，然後選取**新增 > 加入現有專案...**.

2. 瀏覽至**.csproj**檔案損耗應用程式，請選取它，然後按一下 **開啟**。 損耗應用程式專案現在應該可以看到掌上型方案中。

3. 以滑鼠右鍵按一下方案，然後按一下中的掌上型專案節點**編輯參考...**.

4. 在**編輯參考**對話方塊中，損耗專案 （按一下以核取記號），然後按一下 的啟用**確定**。

5. 變更損耗專案的封裝名稱，使其符合掌上型專案 （package） 名稱 (可以在變更封裝名稱**專案選項 > Android 應用程式**)。

-----


請注意，您會收到**XA5211**如果損耗應用程式封裝名稱與掌上型應用程式封裝名稱不符的錯誤。 例如: 

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

若要更正這個錯誤，變更損耗應用程式封裝名稱，使其符合掌上型應用程式封裝名稱。

當您按一下**建置 > 建置所有**，此關聯會觸發自動封裝損耗專案的主要 Handheld (Phone) 專案。 損耗應用程式會自動建置和包含做為掌上型應用程式中的資源。

Handheld (Phone) 專案中的組件參考不使用損耗應用程式專案產生的組件。 相反地，建置程序會執行下列動作：

-   確認封裝名稱相符者。 

-   產生的 XML，並將它加入至掌上型專案，以將它與損耗應用程式產生關聯。 例如:  

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

-   新增損耗應用程式做為**原始**掌上型專案的資源。 


## <a name="manual-packaging"></a>手動封裝

您可以撰寫 Android 戴上的應用程式的 Xamarin.Android 之前 5.0 版，但您必須遵循這些手動封裝以發佈應用程式： 

1. 確定您穿戴專案和 Handheld (Phone) 專案具有相同的版本號碼和封裝名稱。

2. 手動穿戴專案建置為**發行**建置。

3. 手動新增發行**。APK**逐步執行 (2) 從**資源/原始**Handheld (Phone) 專案的目錄。

4. 手動新增新的 XML 資源**Resources/xml/wearable_app_desc.xml**掌上型專案會參考可穿戴式**APK**步驟 (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. 手動新增`<meta-data />`掌上型專案項目**AndroidManifest.xml** `<application>`指的是新的 XML 資源的項目：

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

另請參閱 Android 開發人員網站的[手動 packging 指示](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)。

