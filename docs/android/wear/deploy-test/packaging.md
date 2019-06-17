---
title: 封裝 Wear 應用程式
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 585c276b327a9092bdd13fa633307477017558c5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276786"
---
# <a name="packaging-wear-apps"></a>封裝 Wear 應用程式

Android Wear 應用程式隨附在 Google Play 上發佈的完整 Android 應用程式。 

## <a name="automatic-packaging"></a>自動封裝

從 Xamarin Android 5.0 開始，您穿戴式應用程式會自動封裝成手持式應用程式中的資源時從掌上型專案建立的專案參考至 Wear 專案。 若要建立此關聯，您可以使用下列步驟： 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 如果您穿戴式應用程式尚未手持式方案的一部分，以滑鼠右鍵按一下方案節點，然後選取**新增 > 加入現有專案...**.

2. 瀏覽至 **.csproj**檔案您穿戴式應用程式中，選取它，然後按一下**開啟**。 Wear 應用程式專案現在應該可以看到掌上型方案中。

3. 以滑鼠右鍵按一下**參考**節點，然後選取**加入參考**。

4. 在 **參考管理員**對話方塊中，啟用您 Wear 專案 （按一下以新增核取記號），然後按一下**確定**。

5. 變更 Wear 專案的封裝名稱，使其符合掌上型專案的封裝名稱 (可以變更封裝名稱底下**屬性 > Android 資訊清單**)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 如果您穿戴式應用程式尚未手持式方案的一部分，以滑鼠右鍵按一下方案節點，然後選取**新增 > 加入現有專案...**.

2. 瀏覽至 **.csproj**檔案您穿戴式應用程式中，選取它，然後按一下**開啟**。 Wear 應用程式專案現在應該可以看到掌上型方案中。

3. 以滑鼠右鍵按一下 掌上型專案節點中的方案，然後按一下**編輯參考...**.

4. 在 **編輯參考**對話方塊中，啟用您 Wear 專案 （按一下以新增核取記號），然後按一下**確定**。

5. 變更 Wear 專案的封裝名稱，使其符合掌上型專案的封裝名稱 (可以變更封裝名稱底下**專案選項 > Android 應用程式**)。

-----


請注意，您會收到**XA5211**如果穿戴式應用程式的封裝名稱與掌上型應用程式封裝名稱不符的錯誤。 例如：

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

若要更正這個錯誤，請變更穿戴式應用程式的封裝名稱，使其符合手持式應用程式封裝名稱。

當您按一下 **建置 > 全部建置**，此關聯會觸發自動封裝 Wear 專案至主要 Handheld (Phone) 專案。 穿戴式應用程式會自動建立和加入做為手持式應用程式中的資源。

Wear 應用程式專案產生的組件不作為 Handheld (Phone) 專案中的組件參考。 相反地，在建置程序會執行下列作業：

-   確認封裝名稱相符者。 

-   產生的 XML，並將它加入至掌上型的專案，以將它與穿戴式應用程式產生關聯。 例如:  

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

-   新增穿戴式應用程式，作為**原始**掌上型專案的資源。 


## <a name="manual-packaging"></a>手動封裝

您可以在 Xamarin.Android 中撰寫 Android Wear 的應用程式之前的版本 5.0 版，但您必須遵循這些手動封裝以發佈應用程式： 

1. 請確定您到穿戴專案和 Handheld (Phone) 專案都有相同的版本號碼和封裝名稱。

2. 手動建立為專案到穿戴**發行**建置。

3. 手動新增版本 **。APK**逐步執行 (2) 從**未經處理的資源/** Handheld (Phone) 專案的目錄。

4. 手動加入新的 XML 資源**Resources/xml/wearable_app_desc.xml**指的可穿戴式掌上型專案中**APK**步驟 (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. 手動新增`<meta-data />`掌上型專案的項目**AndroidManifest.xml** `<application>`指的是新的 XML 資源的項目：

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

另請參閱 Android 開發人員網站[手動 packging 指示](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)。

