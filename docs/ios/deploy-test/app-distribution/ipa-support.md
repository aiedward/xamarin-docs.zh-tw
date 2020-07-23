---
title: Xamarin.iOS 中的 IPA 支援
description: 本文涵蓋如何使用臨機操作散發建立可用來部署應用程式的 IPA 檔案，以供測試或內部應用程式的內部作業散發使用。
ms.prod: xamarin
ms.assetid: D253C2DB-852E-6FC6-C9FD-574730B8DB19
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 9bb76fd43b5761f15537d37262f6950d7da2b43a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933142"
---
# <a name="ipa-support-in-xamarinios"></a>Xamarin.iOS 中的 IPA 支援

_本文涵蓋如何使用臨機操作散發建立可用來部署應用程式的 IPA 檔案，以供測試或內部應用程式的內部作業散發使用。_

發行應用程式除了可透過 iTunes App Store 銷售，還可以部署來進行下列用途：

- **臨機操作測試**：iOS 應用程式可部署至多達 100 個使用者 (透過特定 iOS 裝置 UUID 來識別)，以供 Alpha 和 Beta 測試之用。 如需將測試 iOS 裝置新增至 Apple 開發人員帳戶的詳細資訊，請參閱[佈建 iOS 裝置以進行開發](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)文件，而如需如何以這種方式散發的詳細資訊，請參閱[臨機操作](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)指南。
- **內部作業 / 企業部署**：iOS 應用程式能夠在公司內部部署，這需要 Apple Developer Enterprise Program 的成員資格。 內部作業散發的更多資訊詳細說明於[內部作業散發](~/ios/deploy-test/app-distribution/in-house-distribution.md)指南。

在任一情況下，都要以正確的散發佈建設定檔來建立和數位簽署 IPA 套件 (特殊的 zip 檔案類型)。 本文涵蓋建置 IPA 套件，以及在 Mac 或 Windows 電腦上使用 iTunes 於 iOS 裝置安裝套件所需的步驟。

<a name="iTunesMetadata"></a>

## <a name="the-itunesmetadataplist-file"></a>iTunesMetadata.plist 檔案

在 iTunes Connect 中建立 iOS 應用程式 (不論要從 iTunes App Store 銷售或免費發行) 時，開發人員可指定像是應用程式的內容類型、子內容類型、著作權標示、支援的 iOS 裝置以及所需裝置功能等資訊。

透過臨機操作或內部作業散發傳遞的 iOS 應用程式，需要一些支援此資訊的方法，才能在 iTunes 與使用者的裝置上顯示。 根據預設，您每次建置專案都會建立小型 iTunesMetadata.plist 檔案並儲存在專案目錄中。

您也可以建立自訂 **iTunesMetadata.plist** 以提供散發的其他資訊。 若要深入了解此檔案內容和如何建立，請參閱 [iTunesMetadata.plist 內容](~/ios/deploy-test/app-distribution/itunesmetadata.md#iTunesMetadata_contents)與[建立 iTunesMetadata.plist 檔案](~/ios/deploy-test/app-distribution/itunesmetadata.md#iTunesMetadata_creating)文件。

<a name="iTunesArtwork"></a>

## <a name="itunes-artwork"></a>iTunes 插圖

透過非 App Store 的方式傳遞應用程式時，您也需要包括 512x512 與 1024x1024 的影像，用以在 iTunes 中呈現應用程式。

若要指定 iTunes 插圖，請執行下列動作：

1. 在 [方案總管]**** 中，按兩下 [Info.plist]**** 檔案以開啟它進行編輯。
2. 捲動到編輯器的 [iTunes 插圖]**** 區段。
3. 針對任何缺少的影像，按一下編輯器中的縮圖，從 [開啟檔案]**** 對話方塊中選取所需 iTunes 插圖的影像檔案，然後按一下 [確定]**** 或 [開啟]**** 按鈕。
4. 重複上述步驟，直到為應用程式指定所有必要的影像為止。

如需詳細資訊，請參閱 [iTunes 插圖](~/ios/app-fundamentals/images-icons/app-icons.md)文件。

<a name="createipa"></a>

## <a name="creating-an-ipa"></a>建立 IPA

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

建立 IPA 現在內建於新的發行工作流程。 若要執行此動作，請依照下列指示來封存、簽署應用程式以及儲存 IPA。

開始建立跨平台解決方案的 IPA 之前，請確定您已將 iOS 專案選取為啟始專案：

![將 iOS 專案選取為啟始專案](ipa-support-images/setasstartup.png)

### <a name="build-your-archive"></a>建置封存

若要建置 IPA，必須建立應用程式發行組建的「封存」__。 此封存包含應用程式和相關的識別資訊。

1. 在 Visual Studio for Mac 中選取 [發行] | [裝置]**** 組態：  !

    ![選取 [發行] | [裝置] 組態](ipa-support-images/buildxs01new.png)

1. 從 [建置]**** 功能表選取 [Archive for Publishing] (封存以供發行)****：

    ![選取 [Archive for Publishing] (封存以供發行)](ipa-support-images/buildxs02new.png)

1. 建立封存後，[封存]**** 檢視會隨即顯示：

    ![隨即顯示 [封存] 檢視](ipa-support-images/buildxs03new.png)

### <a name="sign-and-distribute-your-app"></a>簽署並散發應用程式

每次建置應用程式進行封存，都會自動開啟**封存檢視**，顯示依解決方案分組的所有已封存專案。 根據預設，此檢視只會顯示目前開啟的解決方案。 若要查看所有具有封存的解決方案，請按一下 [Show all archives] (顯示所有封存)**** 選項。

建議您保留部署至客戶的封存 (臨機操作或內部作業部署)，以便日後代表所產生的任何偵錯資訊。

請注意，對於非 App Store 組建，如果在封存中找到 **iTunesMetadata.plist** 檔案與 iTunes 插圖，就會自動納入 IPA 中。

若要簽署應用程式並準備散發：

1. 選取 [簽署並散發]**** 按鈕，如下所示：

    ![選取 [簽署並散發]](ipa-support-images/buildxs04new.png)

1. 如此將開啟發行精靈。 選取 [臨機操作]**** 或 [企業]**** \(內部作業\) 散發通道以建立套件：

    ![選取臨機操作或企業內部作業散發](ipa-support-images/distribute01.png)

1. 在 [佈建設定檔] 畫面上，選取您的簽署識別與對應的佈建設定檔，或重新簽署另一個身分識別：

    ![選取簽署識別與對應的佈建設定檔](ipa-support-images/distribute02.png)

1. 請確認套件的詳細資料，然後按一下 [發行]****：

    ![確認套件詳細資料](ipa-support-images/distribute03.png)

1. 最後，將 IPA 儲存至您的電腦：

    ![將 IPA 儲存到電腦](ipa-support-images/distribute04.png)

### <a name="building-via-the-command-line-on-mac"></a>透過命令列建置 (在 Mac 上)

在某些特定案例中 (例如在 CI 環境中)，您可能需要透過命令列來建置 IPA。 請遵循下列步驟來進行這項作業：

1. 確定已選取 [專案選項] > [iOS IPA 選項] > [包含 iTunesArtwork 影像]**** 和已選取 [建置臨機操作/企業版套件 (IPA)]****：

    ![已選取 [包含 iTunesArtwork 影像] 和 [建置臨機操作/企業版套件 (IPA)]](ipa-support-images/imagexs04.png)

    您想要的話，可以改為以文字編輯器來編輯 **.csproj** 檔案，並針對要用來建置應用程式的組態，將兩個對應屬性手動加入至 `PropertyGroup`：

    ```xml
    <BuildIpa>true</BuildIpa>
    <IpaIncludeArtwork>false</IpaIncludeArtwork>
    ```

1. 如果您要納入選擇性的 **iTunesMetadata.plist** 檔案，請按一下 [...]**** 按鈕，從清單中選取該檔案，然後按一下 [確定]**** 按鈕：

     ![從清單中選取 iTunesMetadata.plist](ipa-support-images/imagexs03.png)

1. 在命令列上直接呼叫 **msbuild** 並傳遞這個屬性：

    ```bash
    /Library/Frameworks/Mono.framework/Commands/msbuild YourSolution.sln /p:Configuration=Ad-Hoc /p:Platform=iPhone /p:BuildIpa=true
    ```

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中，建立並選取佈建設定檔，建立選擇性的 **iTunesMetadata.plist** 和設定 iTunes 插圖之後，即可建置 IPA 以進行散發。 接著，您必須設定專案。 執行下列動作：

1. 在方案總管**** 中，於 Xamarin.iOS 專案名稱上按一下滑鼠右鍵，然後選取 [屬性]**** 將其開啟進行編輯：

    ![選取 [屬性]](ipa-support-images/imagevs01.png)

2. 選取 [iOS IPA 選項]****，然後從 [組態]**** 下拉式清單中選取 [臨機操作]****：

    ![從 [組態] 下拉式清單中選取 [臨機操作]](ipa-support-images/imagevs02.png)

    > [!NOTE]
    > 較新的 Xamarin.iOS 專案可能無法使用臨機操作組態。 如果無法使用，請選取 [發行]**** 組態。

3. 如果您要納入選擇性的 **iTunesMetadata.plist** 檔案，請按一下 [...]**** 按鈕，從清單中選取該檔案，然後按一下 [開啟]**** 按鈕：

    ![從清單中選取 iTunesMetadata.plist](ipa-support-images/imagevs03.png)

4. 您可以選擇性地指定 IPA 的 [套件名稱]****，如未指定，則會使用和 Xamarin.iOS 專案相同的名稱。
5. 將您的變更儲存至專案屬性。
6. 如果可用的話，從 [組建組態]**** 下拉式清單中選取 [臨機操作]****。 否則，請選取 [發行]****：

    ![從 [組建組態] 下拉式清單中選取 [臨機操作]](ipa-support-images/imagevs05.png)

7. 建置專案以建立 IPA 套件。
8. IPA 會建置在 **Bin > iOS 裝置 > 臨機操作 (或發行)** 資料夾中：

    ![[檔案總管] 中的 IPA](ipa-support-images/imagevs06.png)

-----

<a name="Customizing-the-IPA-Location"></a>

## <a name="customizing-the-ipa-location"></a>自訂 IPA 位置

已新增的 **MSBuild** 屬性 `IpaPackageDir` 可讓您輕鬆自訂 **.ipa** 檔案輸出位置。 如果將 `IpaPackageDir` 設定為自訂位置，**.ipa** 檔案將會置於其中，而不是放在預設時間戳記子目錄。 建立須仰賴特定目錄路徑才能正常運作的自動組建 (例如用於持續整合 (CI) 組建) 時，這會很有用。

新屬性有數個可能的使用方式：

例如，若要將 **.ipa** 檔案輸出至舊的預設目錄 (如 Xamarin.iOS 9.6 和較舊版本)，您可以使用下列其中一種方法來將 `IpaPackageDir` 屬性設定為 `$(OutputPath)`。 兩種方法都與所有 Unified API Xamarin.iOS 組建相容，包括 IDE 組建以及使用 **msbuild**、**xbuild** 或 **mdtool** 的命令列組建：

- 第一種選擇是在 **MSBuild** 檔案的 `<PropertyGroup>` 元素內設定 `IpaPackageDir` 屬性。 例如，您可以將下列 `<PropertyGroup>` 新增至 iOS 應用程式專案 **.csproj** 檔案的底部 (在結尾的 `</Project>` 標記之前)：

    ```xml
    <PropertyGroup>
        <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

- 較好的方法是將 `<IpaPackageDir>` 元素加入至現有 `<PropertyGroup>` 的底部，其對應用於建置 **.ipa** 檔案的組態。 這種做法較好，因為會在 iOS IPA 選項專案屬性頁面上以規劃的設定，準備具有未來相容性的專案。 如果您目前使用 `Release|iPhone` 組態來建置 **.ipa** 檔案，整個更新後的屬性群組看起來可能會類似下列內容：

    ```xml
    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone' ">
        <Optimize>true</Optimize>
        <OutputPath>bin\iPhone\Release</OutputPath>
        <ErrorReport>prompt</ErrorReport>
        <WarningLevel>4</WarningLevel>
        <ConsolePause>false</ConsolePause>
        <CodesignKey>iPhone Developer</CodesignKey>
        <MtouchUseSGen>true</MtouchUseSGen>
        <MtouchUseRefCounting>true</MtouchUseRefCounting>
        <MtouchFloat32>true</MtouchFloat32>
        <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
        <MtouchLink>SdkOnly</MtouchLink>
        <MtouchArch>;ARMv7, ARM64</MtouchArch>
        <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
        <MtouchTlsProvider>Default</MtouchTlsProvider>
        <PlatformTarget>x86&</PlatformTarget>
        <BuildIpa>true</BuildIpa>
        <IpaPackageDir>$(OutputPath</IpaPackageDir>
    </PropertyGroup>
    ```

**msbuild** 或 **xbuild** 命令列組建這項替代技術是新增 `/p:` 引數來設定 `IpaPackageDir` 屬性。 在此情況下，請注意 **msbuild** 不會展開命令列上傳入的 `$()` 運算式，因此無法使用 `$(OutputPath)` 語法。 您必須改為提供完整路徑名稱。 Mono 的 **xbuild** 命令不會展開 `$()` 運算式，但仍偏好使用完整路徑名稱，原因是已改用 [**msbuild** 的跨平台版本](https://www.mono-project.com/docs/about-mono/releases/5.0.0/#msbuild)來取代 **xbuild**。

使用這種方法的完整範例在 Windows 上看起來可能會類似下列內容：

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

或在 Mac 上可能如下：

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

<a name="installipa"></a>

## <a name="installing-an-ipa-using-itunes"></a>使用 iTunes 安裝 IPA

產生的 IPA 套件可以傳遞給測試使用者在其 iOS 裝置上安裝，或可發行以供企業部署。 不論選擇哪種方法，使用者都要按兩下 IPA 檔案 (或拖曳至開啟的 iTunes 視窗)，在其 Mac 或 Windows 電腦上的 iTunes 應用程式中安裝套件。

新的 iOS 應用程式會顯示在 [我的應用程式]**** 區段，您可以在應用程式上按一下滑鼠右鍵並取得相關資訊：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

 ![新的 iOS 應用程式在 [我的應用程式] 區段中](ipa-support-images/installxs01.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 ![新的 iOS 應用程式在 [我的應用程式] 區段中](ipa-support-images/installvs01.png)

-----

使用者現在可以將 iTunes 與其裝置同步來安裝新的 iOS 應用程式。

<a name="Summary"></a>

## <a name="summary"></a>總結

本文涵蓋準備非 App Store 組建的 Xamarin.iOS 應用程式所需的設定步驟。 文中說明如何建立 IPA 套件和如何在使用者的 iOS 裝置上安裝產生的 iOS 應用程式，以進行測試或內部作業散發。

## <a name="related-links"></a>相關連結

- [App Store 散發](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [發佈至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [內部作業散發](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [臨機操作散發](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Itunesmetadata.plist. plist 檔案](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
- [iTunes 插圖](~/ios/app-fundamentals/images-icons/app-icons.md#itunes)
- [Develop and distribute enterprise apps (Apple)](https://help.apple.com/xcode/mac/current/#/devba5e7054d) (開發與散發企業應用程式 (Apple))
- [Distributing Enterprise Apps (WWDC Video)](https://developer.apple.com/videos/play/wwdc2014/705/) (散發企業應用程式 (WWDC 影片))
