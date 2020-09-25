---
title: 組建項目
description: 本檔將列出 Xamarin. Android 組建程式中所有支援的專案群組。
ms.prod: xamarin
ms.assetid: 5EBEE1A5-3879-45DD-B1DE-5CD4327C2656
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/23/2020
ms.openlocfilehash: 2d84152551b6c9ddfb2263dcd65e2def5ba20e33
ms.sourcegitcommit: 38496cfd4d30fd40a011011f303a31de639bd699
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91247255"
---
# <a name="build-items"></a>組建項目

組建專案可控制如何建立 Xamarin Android 應用程式或程式庫專案。

## <a name="androidasset"></a>AndroidAsset

支援 [Android 資產](https://developer.android.com/guide/topics/resources/providing-resources#OriginalFiles)，也就是 `assets` JAVA Android 專案的資料夾中所包含的檔案。

## <a name="androidaarlibrary"></a>AndroidAarLibrary

的組建動作 `AndroidAarLibrary` 應該用來直接參考檔案 `.aar` 。 Xamarin 元件最常使用此建置動作。 也就 `.aar` 是包含取得 Google Play 及其他服務運作所需的檔案參考。

具有此組建動作的檔案將會以類似于程式庫專案中的內嵌資源的方式處理。 `.aar`將會解壓縮到中繼目錄中。 然後，任何資產、資源和檔案 `.jar` 都會包含在適當的專案群組中。

## <a name="androidaotprofile"></a>AndroidAotProfile

用來提供 AOT 設定檔，以用於特性指引的 AOT。

## <a name="androidboundlayout"></a>AndroidBoundLayout

表示在 `AndroidGenerateLayoutBindings` 屬性設定為 `false` 的情況下，配置檔案將為其產生程式碼後置。 在所有其他層面上，它與上述的 `AndroidResource` 相同。 此動作**只**能與配置檔案搭配使用：

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

## <a name="androidenvironment"></a>AndroidEnvironment

建置動作為 `AndroidEnvironment` 的檔案可用來[在程序啟動期間初始化環境變數和系統屬性](~/android/deploy-test/environment.md)。
`AndroidEnvironment` 建置動作可套用到多個檔案，這些檔案並不會依特定順序來進行評估 (因此，請勿在多個檔案中指定相同的環境變數或系統屬性)。

## <a name="androidfragmenttype"></a>AndroidFragmentType

指定在產生配置繫結程式碼時，要用於所有 `<fragment>`配置元素的預設完整類型。 此屬性預設為標準 Android `Android.App.Fragment` 類型。

## <a name="androidjavalibrary"></a>AndroidJavaLibrary

建置動作為 `AndroidJavaLibrary` 的檔案是會納入到最終版 Android 套件的 Java 封存 (`.jar` 檔案)。

## <a name="androidjavasource"></a>AndroidJavaSource

建置動作為 `AndroidJavaSource` 的檔案是會納入到最終版 Android 套件的 Java 原始程式碼。

## <a name="androidlintconfig"></a>AndroidLintConfig

組建動作 ' AndroidLintConfig ' 應搭配使用 [`$(AndroidLintEnabled)`](~/android/deploy-test/building-apps/build-properties.md#androidlintenabled)
屬性中找到的值而定。 具有此建置動作的檔案會合併在一起，並傳遞給 Android `lint` 工具。 它們應該是 XML 檔案，其中包含要啟用及停用哪些測試的相關資訊。

如需詳細資訊，請參閱 [Lint 文件](https://developer.android.com/studio/write/lint)。

## <a name="androidnativelibrary"></a>AndroidNativeLibrary

[原生程式庫](~/android/platform/native-libraries.md)可藉由將其建置動作設定為 `AndroidNativeLibrary` 來新增至組建中。

請注意，由於 Android 支援多個應用程式二進位介面 (ABI)，所以建置系統必須知道所建置的原生程式庫適用於哪個 ABI。 方法有二：

1. 路徑「探查」。
2. 使用 `Abi` 項目屬性。

路徑探查會使用原生程式庫的父目錄名稱來指定程式庫的目標 ABI。 因此，如果您將 `lib/armeabi-v7a/libfoo.so` 新增至組建，則會以 `armeabi-v7a` 的形式來「探查」ABI。

### <a name="item-attribute-name"></a>項目屬性名稱

**Abi** &ndash; 指定原生程式庫的 ABI。

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```

## <a name="androidresource"></a>AndroidResource

所有具有 AndroidResource** 建置動作的檔案都會在建置程序進行期間編譯成 Android 資源，並可供透過 `$(AndroidResgenFile)` 來存取。

```xml
<ItemGroup>
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
```

更進階的使用者或許會想要在不同的組態中擁有不同的資源，但又想具有相同的有效路徑。 若要實現此目的，他們可以在手上準備多個資源目錄並在這些不同的目錄內放入具有相同相對路徑的檔案，以及使用 MSBuild 條件，有條件地將不同檔案納入到不同組態中。 例如：

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
<ItemGroup  Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug\values\strings.xml"/>
</ItemGroup>
<PropertyGroup>
  <MonoAndroidResourcePrefix>Resources;Resources-Debug<MonoAndroidResourcePrefix>
</PropertyGroup>
```

**LogicalName** &ndash; 明確指定資源路徑。 允許對檔案&ldquo;設定別名&rdquo;，讓檔案能夠以多個不同的資源名稱來提供。

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources/values/strings.xml"/>
</ItemGroup>
<ItemGroup Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug/values/strings.xml">
    <LogicalName>values/strings.xml</LogicalName>
  </AndroidResource>
</ItemGroup>
```

## <a name="androidresourceanalysisconfig"></a>AndroidResourceAnalysisConfig

組建動作會將檔案 `AndroidResourceAnalysisConfig` 標示為 Xamarin Android Designer 配置診斷工具的嚴重性層級設定檔案。 這項功能目前僅適用于配置編輯器，不適用於組建訊息。

如需詳細資訊，請參閱 [Android 資源分析檔](https://aka.ms/androidresourceanalysis) 。

已在 Xamarin. Android 10.2 中新增。

## <a name="content"></a>內容

不支援一般的 `Content` 建置動作 (因為我們還沒想出該如何提供支援，而又不會讓首次執行步驟的成本太高)。

從 Xamarin.Android 5.1 開始，嘗試使用 `@(Content)` 建置動作將會導致 `XA0101` 警告。

## <a name="linkdescription"></a>LinkDescription

具有 LinkDescription** 建置動作的檔案可用來[控制連結器行為](~/cross-platform/deploy-test/linker.md)。

## <a name="proguardconfiguration"></a>ProguardConfiguration

具有 ProguardConfiguration** 建置動作的檔案包含可用來控制 `proguard` 行為的選項。 如需此建置動作的詳細資訊，請參閱 [ProGuard](~/android/deploy-test/release-prep/proguard.md)。

除非有下列情況，否則會忽略這些檔案。 [`$(EnableProguard)`](~/android/deploy-test/building-apps/build-properties.md#enableproguard)
MSBuild 屬性為 `True` 。
