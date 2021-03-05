---
title: 建置流程
description: 本檔將概述 Xamarin. Android build process。
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2021
ms.openlocfilehash: d5222e347967c8ad26e562ce5fba250d19fe4bcc
ms.sourcegitcommit: 854efa710e5906fdd35cdddb2e8155ffe66b410d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197064"
---
# <a name="build-process"></a>建置流程

Xamarin. Android build process 負責將所有專案互相對應： [ `Resource.designer.cs` 產生](~/android/internals/api-design.md)、支援 [`@(AndroidAsset)`](~/android/deploy-test/building-apps/build-items.md#androidasset) 、 [`@(AndroidResource)`](~/android/deploy-test/building-apps/build-items.md#androidresource) 和其他[組建動作](~/android/deploy-test/building-apps/build-items.md)、產生 android 可呼叫[包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)，以及產生可 `.apk` 在 android 裝置上執行的。

## <a name="application-packages"></a>應用程式套件

廣泛來說，Xamarin.Android 建置系統可以產生的 Android 應用程式套件 (`.apk` 檔案) 類型有兩種：

- **發行** 組建是完全獨立的，不需要額外的封裝即可執行。 這些是提供給 App store 的套件。

- **偵錯** 組建則非如此。

這些封裝類型符合 `Configuration` 產生封裝的 MSBuild。

## <a name="shared-runtime"></a>共用執行階段

在 Xamarin. Android 11.2 之前， *共用運行* 時間是一組額外的 Android 套件，可提供基礎類別庫 (等等 `mscorlib.dll` ) 和 android 系結程式庫 (等 `Mono.Android.dll` ) 。 偵錯組建依賴共用執行階段來取代在 Android 應用程式套件內納入基底類別庫和繫結組件，讓偵錯套件能夠小一些。

您可以藉由設定，在 Debug 組建中停用共用執行時間。 [`$(AndroidUseSharedRuntime)`](~/android/deploy-test/building-apps/build-properties.md#androidusesharedruntime)
屬性設為 `False` 。

已移除 Xamarin. Android 11.2 中的共用執行時間支援。

<a name="Fast_Deployment"></a>

## <a name="fast-deployment"></a>快速部署

*快速部署* 的運作方式是進一步縮減 Android 應用程式封裝大小。 這是藉由從套件中排除應用程式的元件來完成，而改為將應用程式的元件直接部署到應用程式的內部 `files` 目錄（通常位於） `/data/data/com.some.package` 。 內部 `files` 目錄不是可全域寫入的資料夾，因此 `run-as` 會使用此工具來執行所有命令，以將檔案複製到該目錄。

此程式會加快組建/部署/偵錯工具的速度，因為 *只有* 在變更元件時，不會重新安裝套件。
只有更新的元件才會重新同步處理至目標裝置。

> [!WARNING]
> 快速部署在封鎖的裝置上已知會失敗 `run-as` ，這通常會包含早于 Android 5.0 的裝置。

快速部署預設為啟用狀態，藉由將 `$(EmbedAssembliesIntoApk)` 屬性設定為 `True` 即可在偵錯組建中加以停用。

[增強的快速部署](~/android/deploy-test/building-apps/build-properties.md#androidfastdeploymenttype)模式可以與此功能搭配使用，以加快部署速度。
這會將元件、原生程式庫、typemaps 和 dexes 部署到 `files` 目錄。 但是，如果您要變更原生程式庫、系結或 JAVA 程式碼，則只需要啟用此程式碼。

## <a name="msbuild-projects"></a>MSBuild 專案

Xamarin.Android 建置程序是以 MSBuild 作為基礎，而這也是 Visual Studio for Mac 和 Visual Studio 所使用的專案檔格式。
一般來說，使用者不需要以手動方式編輯 MSBuild 檔案 &ndash; IDE 會負責建立功能完整的專案並使用任何所做的變更來為這些專案進行更新，然後視需要自動叫用建置目標。

進階使用者或許會想執行 IDE 之 GUI 所不支援的作業，因此建置程序是可藉由直接編輯專案檔來加以自訂。
本頁面只會記載 Xamarin.Android 特有的功能和自訂項目 &ndash; 使用一般的 MSBuild 項目、屬性和目標所能進行的工作不只這些。

<a name="Build_Targets"></a>

## <a name="binding-projects"></a>系結專案

下列 MSBuild 屬性可與[繫結專案](~/android/platform/binding-java-library/index.md)搭配使用：

- [`$(AndroidClassParser)`](~/android/deploy-test/building-apps/build-properties.md#androidclassparser)
- [`$(AndroidCodegenTarget)`](~/android/deploy-test/building-apps/build-properties.md#androidcodegentarget)

## <a name="resourcedesignercs-generation"></a>`Resource.designer.cs` 生成

下列 MSBuild 屬性可用來控制檔案的產生 `Resource.designer.cs` ：

- [`$(AndroidAapt2CompileExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidaapt2compileextraargs)
- [`$(AndroidAapt2LinkExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidaapt2linkextraargs)
- [`$(AndroidExplicitCrunch)`](~/android/deploy-test/building-apps/build-properties.md#androidexplicitcrunch)
- [`$(AndroidR8IgnoreWarnings)`](~/android/deploy-test/building-apps/build-properties.md#androidr8ignorewarnings)
- [`$(AndroidResgenExtraArgs)`](~/android/deploy-test/building-apps/build-properties.md#androidresgenextraargs)
- [`$(AndroidResgenFile)`](~/android/deploy-test/building-apps/build-properties.md#androidresgenfile)
- [`$(AndroidUseAapt2)`](~/android/deploy-test/building-apps/build-properties.md#androiduseaapt2)
- [`$(MonoAndroidResourcePrefix)`](~/android/deploy-test/building-apps/build-properties.md#monoandroidresourceprefix)

## <a name="signing-properties"></a>簽署屬性

簽署屬性可控制應用程式套件的簽署方式，以便能夠將它安裝到 Android 裝置。 為了加快建置反覆運算，Xamarin.Android 工作不會在建置程序進行期間簽署套件，因為簽署作業很慢。 相反地，會在安裝前或匯出期間由 IDE 或安裝建置目標簽署套件 (如有必要)。 叫用 SignAndroidPackage 目標會在輸出目錄中產生後置詞為 `-Signed.apk` 的套件。

根據預設，簽署目標會產生新的偵錯簽署金鑰 (如有必要)。 如果您想要使用特定的金鑰（例如在組建伺服器上），則會使用下列 MSBuild 屬性：

- [`$(AndroidDebugKeyAlgorithm)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugkeyalgorithm)
- [`$(AndroidDebugKeyValidity)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugkeyvalidity)
- [`$(AndroidDebugStoreType)`](~/android/deploy-test/building-apps/build-properties.md#androiddebugstoretype)
- [`$(AndroidKeyStore)`](~/android/deploy-test/building-apps/build-properties.md#androidkeystore)
- [`$(AndroidSigningKeyAlias)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeyalias)
- [`$(AndroidSigningKeyPass)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeypass)
- [`$(AndroidSigningKeyStore)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningkeystore)
- [`$(AndroidSigningStorePass)`](~/android/deploy-test/building-apps/build-properties.md#androidsigningstorepass)
- [`$(JarsignerTimestampAuthorityCertificateAlias)`](~/android/deploy-test/building-apps/build-properties.md#jarsignertimestampauthoritycertificatealias)
- [`$(JarsignerTimestampAuthorityUrl)`](~/android/deploy-test/building-apps/build-properties.md#jarsignertimestampauthorityurl)

### <a name="keytool-option-mapping"></a>`keytool` 選項對應

請考慮下列 `keytool` 調用：

```shell
$ keytool -genkey -v -keystore filename.keystore -alias keystore.alias -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password: keystore.filename password
Re-enter new password: keystore.filename password
...
Is CN=... correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: ...
Enter key password for keystore.alias
        (RETURN if same as keystore password): keystore.alias password
[Storing filename.keystore]
```

若要使用上面所產生的金鑰儲存區，請使用屬性群組：

```xml
<PropertyGroup>
    <AndroidKeyStore>True</AndroidKeyStore>
    <AndroidSigningKeyStore>filename.keystore</AndroidSigningKeyStore>
    <AndroidSigningStorePass>keystore.filename password</AndroidSigningStorePass>
    <AndroidSigningKeyAlias>keystore.alias</AndroidSigningKeyAlias>
    <AndroidSigningKeyPass>keystore.alias password</AndroidSigningKeyPass>
</PropertyGroup>
```

## <a name="build-extension-points"></a>建置擴充點

Xamarin. Android 建置系統為想要在我們的建置系統中建立掛鉤的使用者，提供了一些公用擴充點。 若要使用其中任何一個擴充點，您必須將您的自訂目標新增到 `PropertyGroup` 中的適當 MSBuild 屬性內。 例如：

```xml
<PropertyGroup>
   <AfterGenerateAndroidManifest>
      $(AfterGenerateAndroidManifest);
      YourTarget;
   </AfterGenerateAndroidManifest>
</PropertyGroup>
```

擴充點包括：

- [' $ (AfterGenerateAndroidManifest) ](~/android/deploy-test/building-apps/build-properties.md#aftergenerateandroidmanifest)
- [' $ (BeforeGenerateAndroidManifest) ](~/android/deploy-test/building-apps/build-properties.md#beforegenerateandroidmanifest)

擴充組建程式的注意事項：如果未正確寫入，組建延伸模組可能會影響您的組建效能，特別是在每個組建上執行時。 極力建議您先閱讀 MSBuild [文件](/visualstudio/msbuild/msbuild)，然後再執行這類延伸模組。

## <a name="target-definitions"></a>目標定義

建置程序的 Xamarin.Android 特有組件會定義在 `$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets` 中，但還需要有一般的語言特有目標 (例如 Microsoft.CSharp.targets) 才能建置組件。

在匯入任何語言目標之前，必須先設定下列組建屬性：

```xml
<PropertyGroup>
  <TargetFrameworkIdentifier>MonoDroid</TargetFrameworkIdentifier>
  <MonoDroidVersion>v1.0</MonoDroidVersion>
  <TargetFrameworkVersion>v2.2</TargetFrameworkVersion>
</PropertyGroup>
```

藉由匯入 Xamarin.Android.CSharp.targets，即可針對 C# 納入這些目標和屬性：

```xml
<Import Project="$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets" />
```

此檔案可輕鬆地適用於其他語言。
