---
title: 封裝磨損應用程式
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: aa4a4f1ab3ae3024de2d969f9325c2efa4db48af
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028637"
---
# <a name="packaging-wear-apps"></a>封裝磨損應用程式

Android 磨損應用程式會使用完整的 Android 應用程式封裝，以在 Google Play 上散發。 

## <a name="automatic-packaging"></a>自動封裝

從 Xamarin Android 5.0 開始，當您建立從掌上型專案到磨損專案的專案參考時，您的磨損應用程式會自動封裝為您的掌上型應用程式中的資源。 您可以使用下列步驟來建立此關聯： 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 如果您的磨損應用程式尚未屬於您的掌上型解決方案，請以滑鼠右鍵按一下方案節點，然後選取 [**新增] > [加入現有專案**...]。

2. 流覽至您的磨損應用程式 **.csproj**檔案，加以選取，然後按一下 [**開啟**]。 您現在應該可以在掌上型方案中看到「磨損應用程式」專案。

3. 以滑鼠右鍵按一下 [**參考**] 節點，然後選取 [**加入參考**]。

4. 在 [**參考管理員**] 對話方塊中，啟用您的磨損專案（按一下以新增核取記號），然後按一下 **[確定]** 。

5. 變更您的磨損專案的套件名稱，使其符合掌上型專案的套件名稱（可以在 [屬性] > [ **Android 資訊清單**] 下變更套件名稱）。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 如果您的磨損應用程式尚未屬於您的掌上型解決方案，請以滑鼠右鍵按一下方案節點，然後選取 [**新增] > [加入現有專案**...]。

2. 流覽至您的磨損應用程式 **.csproj**檔案，加以選取，然後按一下 [**開啟**]。 您現在應該可以在掌上型方案中看到「磨損應用程式」專案。

3. 以滑鼠右鍵按一下方案中的手持專案節點，然後按一下 [**編輯參考 ...** ]。

4. 在 [**編輯參考**] 對話方塊中，啟用您的磨損專案（按一下以新增核取記號），然後按一下 **[確定]** 。

5. 變更您的磨損專案的套件名稱，使其符合掌上型專案的套件名稱（可以在 [**專案選項] > [Android 應用程式**] 下變更套件名稱）。

-----

請注意，如果磨損應用程式的套件名稱不符合掌上型應用程式的套件名稱，您將會收到**XA5211**錯誤。 例如:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

若要更正此錯誤，請變更磨損應用程式的套件名稱，使其符合掌上型應用程式的套件名稱。

當您按一下 [**組建] > [全部建立**] 時，此關聯會觸發將磨損專案自動封裝到主要的掌上型（Phone）專案中。 系統會自動建立磨損應用程式，並將其納入為掌上型應用程式中的資源。

「磨損應用程式」專案所產生的元件不會當做掌上（Phone）專案中的元件參考使用。 相反地，組建程式會執行下列作業：

- 驗證套件名稱是否相符。 

- 產生 XML，並將它新增至掌上型專案，以將它與磨損應用程式建立關聯。 例如: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

- 將磨損應用程式新增為掌上型專案中的**原始**資源。 

## <a name="manual-packaging"></a>手動封裝

您可以在5.0 版之前，在 Xamarin 中撰寫 Android 磨損應用程式，但必須遵循下列手動封裝指示來散發應用程式： 

1. 請確定您的穿戴式專案和掌上型（電話）專案具有相同的版本號碼和套件名稱。

2. 以手動方式將穿戴式專案建立為**發行**組建。

3. 手動新增發行 **。** 從步驟（2） APK 到掌上型（Phone）專案的**資源/原始**目錄。

4. 以手動方式將新的 XML 資源**Resources/xml/wearable_app_desc**加入至穿戴式**APK** from step （3）的手持型專案中：

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. 手動將 `<meta-data />` 元素新增至掌上型專案的**androidmanifest.xml** `<application>` 專案，此元素會參考新的 xml 資源：

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

另請參閱 Android 開發人員網站的[手動 packging 指示](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)。
