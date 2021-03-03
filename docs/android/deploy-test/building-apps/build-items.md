---
title: 組建項目
description: 本檔將列出 Xamarin. Android 組建程式中所有支援的專案群組。
ms.prod: xamarin
ms.assetid: 5EBEE1A5-3879-45DD-B1DE-5CD4327C2656
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 03/01/2021
ms.openlocfilehash: 2ba9c4203b6bd196242183915127dd74fa6ddee7
ms.sourcegitcommit: 3aa9bdcaaedca74ab5175cb2338a1df122300243
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749287"
---
# <a name="build-items"></a>組建項目

組建專案可控制如何建立 Xamarin Android 應用程式或程式庫專案。

## <a name="androidasset"></a>AndroidAsset

支援 [Android 資產](https://developer.android.com/guide/topics/resources/providing-resources#OriginalFiles)，也就是 `assets` JAVA Android 專案的資料夾中所包含的檔案。

## <a name="androidaarlibrary"></a>AndroidAarLibrary

的組建動作 `AndroidAarLibrary` 應該用來直接參考檔案 `.aar` 。 Xamarin 元件最常使用此建置動作。 亦即包含 `.aar` 取得 Google Play 和其他服務運作所需的檔案參考。

具有此組建動作的檔案將會以類似于程式庫專案中的內嵌資源的方式處理。 `.aar`將會解壓縮到中繼目錄中。 然後，任何資產、資源和檔案 `.jar` 都會包含在適當的專案群組中。

## <a name="androidaotprofile"></a>AndroidAotProfile

用來提供 AOT 設定檔，以用於特性指引的 AOT。

您也可以從 Visual Studio 使用它，方法是將 `AndroidAotProfile` 組建動作設定為包含 AOT 設定檔的檔案。

## <a name="androidboundlayout"></a>AndroidBoundLayout

表示在 `AndroidGenerateLayoutBindings` 屬性設定為 `false` 的情況下，配置檔案將為其產生程式碼後置。 在所有其他層面上，它與上述的 `AndroidResource` 相同。 此動作 **只** 能與配置檔案搭配使用：

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

## <a name="androidlibrary"></a>AndroidLibrary

**AndroidLibrary** 是新的組建動作，可簡化 `.jar` `.aar` 專案中包含和檔案的方式。

任何專案都可以指定：

```xml
<ItemGroup>
  <AndroidLibrary Include="foo.jar" />
  <AndroidLibrary Include="bar.aar" />
</ItemGroup>
```

上述程式碼片段的結果對每個 Xamarin. Android 專案類型都有不同的效果：

* 應用程式和類別庫專案：
  * `foo.jar` 對應至 [**AndroidJAVALibrary**](#androidjavalibrary)。
  * `bar.aar` 對應至 [**AndroidAarLibrary**](#androidaarlibrary)。
* JAVA 系結專案：
  * `foo.jar` 對應至 [**EmbeddedJar**](#embeddedjar)。
  * `foo.jar`如果[](#embeddedreferencejar) `Bind="false"` 加入中繼資料，則對應至 EmbeddedReferenceJar。
  * `bar.aar` 對應至 [**LibraryProjectZip**](#libraryprojectzip)。

這種簡化方式表示您可以在任何地方使用 **AndroidLibrary** 。

此組建動作已在 Xamarin. Android 11.2 中新增。

## <a name="androidlintconfig"></a>AndroidLintConfig

組建動作 ' AndroidLintConfig ' 應搭配使用 [`$(AndroidLintEnabled)`](~/android/deploy-test/building-apps/build-properties.md#androidlintenabled)
屬性中找到的值而定。 具有此建置動作的檔案會合併在一起，並傳遞給 Android `lint` 工具。 它們應該是 XML 檔案，其中包含要啟用及停用哪些測試的相關資訊。

如需詳細資訊，請參閱 [Lint 文件](https://developer.android.com/studio/write/lint)。

## <a name="androidmanifestoverlay"></a>AndroidManifestOverlay

`AndroidManifestOverlay`Build 動作可以用來提供其他檔案 `AndroidManifest.xml` 給[資訊清單合併](https://developer.android.com/studio/build/manifest-merge)工具。
具有此組建動作的檔案將會傳遞至資訊清單合併，以及參考中的主要檔案 `AndroidManifest.xml` 和任何其他資訊清單檔案。 這些將會合並到最後的資訊清單中。

您可以使用此組建動作，根據您的組建設定，為您的應用程式提供其他變更和設定。 例如，如果您只需要在進行偵錯工具時擁有特定許可權，您可以在偵錯工具中使用覆迭來插入該許可權。 例如，假設有下列覆迭檔案內容：

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
  <uses-permission android:name="android.permission.CAMERA" />
</manifest>
```

您可以使用下列程式，為偵錯工具組建新增此項：

```
<ItemGroup>
  <AndroidManifestOverlay Include="DebugPermissions.xml" Condition=" '$(Configuration)' == 'Debug' " />
</ItemGroup>
```

此組建動作是在 Xamarin. Android 11.2 中引進。

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

所有具有 AndroidResource 建置動作的檔案都會在建置程序進行期間編譯成 Android 資源，並可供透過 `$(AndroidResgenFile)` 來存取。

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
  <MonoAndroidResourcePrefix>Resources;Resources-Debug</MonoAndroidResourcePrefix>
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

Build 動作會將檔案 `AndroidResourceAnalysisConfig` 標示為 Xamarin Android 設計工具版面配置診斷工具的嚴重性層級設定檔案。 這項功能目前僅適用于配置編輯器，不適用於組建訊息。

如需詳細資訊，請參閱 [Android 資源分析檔](../../user-interface/android-designer/diagnostics.md) 。

已在 Xamarin. Android 10.2 中新增。

## <a name="content"></a>Content

不支援一般的 `Content` 建置動作 (因為我們還沒想出該如何提供支援，而又不會讓首次執行步驟的成本太高)。

從 Xamarin.Android 5.1 開始，嘗試使用 `@(Content)` 建置動作將會導致 `XA0101` 警告。

## <a name="embeddedjar"></a>EmbeddedJar

在 Xamarin. Android 系結專案中， **EmbeddedJar** 組建動作會系結 JAVA/Kotlin 程式庫，並將檔案內嵌 `.jar` 至程式庫。 當 Xamarin. Android 應用程式專案取用程式庫時，它將可從 c # 存取 JAVA/Kotlin Api，並在最終的 Android 應用程式中包含 JAVA/Kotlin 程式碼。

從 Xamarin. Android 11.2 開始，您可以使用 [**AndroidLibrary**](#androidlibrary) build 動作作為替代方案，例如：

```xml
<Project>
  <ItemGroup>
    <AndroidLibrary Include="Library.jar" />
  </ItemGroup>
</Project>
```

## <a name="embeddednativelibrary"></a>EmbeddedNativeLibrary

在 Xamarin 類別庫或 JAVA 系結專案中， **EmbeddedNativeLibrary** 組建動作會將原生程式庫（例如） `lib/armeabi-v7a/libfoo.so` 組合至程式庫。 當 Xamarin. Android 應用程式取用程式庫時，檔案 `libfoo.so` 會包含在最終的 Android 應用程式中。

從 Xamarin. Android 11.2 開始，您可以使用 [**AndroidNativeLibrary**](#androidnativelibrary) build 動作作為替代方案。

## <a name="embeddedreferencejar"></a>EmbeddedReferenceJar

在 Xamarin. Android 系結專案中， **EmbeddedReferenceJar** build 動作會將檔案內嵌 `.jar` 至程式庫，但不會像 [**EmbeddedJar**](#embeddedjar) 一樣建立 c # 系結。 當 Xamarin. Android 應用程式專案取用程式庫時，它會在最終的 Android 應用程式中包含 JAVA/Kotlin 程式碼。

從 Xamarin. Android 11.2 開始，您可以使用 [**AndroidLibrary**](#androidlibrary) build 動作作為替代方案，例如 `<AndroidLibrary Include="..." Bind="false" />` ：

```xml
<Project>
  <ItemGroup>
    <!-- A .jar file to bind & embed -->
    <AndroidLibrary Include="Library.jar" />
    <!-- A .jar file to only embed -->
    <AndroidLibrary Include="Dependency.jar" Bind="false" />
  </ItemGroup>
</Project>
```

## <a name="javadocjar"></a>JAVADocJar

在 Xamarin. Android 系結專案中， **JAVADocJar** build 動作是用於 `.jar` 包含 *JAVAdoc HTML* 的檔案。  會剖析 JAVAdoc HTML，以便解壓縮參數名稱。

僅支援某些「JAVAdoc HTML 方言」，包括：

  * JDK 1.7 `javadoc` 輸出。
  * JDK 1.8 `javadoc` 輸出。
  * Droiddoc 輸出。

此組建動作在 Xamarin. Android 11.3 中已被取代，在 .NET 6 中將不受支援。
`@(JavaSourceJar)`慣用的是組建動作。

## <a name="javasourcejar"></a>JAVASourceJar

在「Xamarin Android 系結」專案中， **JAVASourceJar** build 動作用於 `.jar` 包含包含 JAVAdoc 檔 [批註](https://www.oracle.com/technical-resources/articles/java/javadoc-tool.html)之 *JAVA 原始程式碼* 的檔案。

在 Xamarin. Android 11.3 之前，JAVAdoc 會在 `javadoc` 組建階段透過公用程式轉換成 HTML，稍後再轉換成 XML 檔。

從 Xamarin. Android 11.3 開始，JAVAdoc 會改為在產生的系結原始程式碼內轉換成 [c # XML 檔批註](/dotnet/csharp/codedoc) 。

`$(AndroidJavadocVerbosity)` 控制匯入的 JAVAdoc 如何「詳細資訊」或「完成」。

從 Xamarin. Android 11.3 開始，支援下列 MSBuild 中繼資料：

* `%(CopyrightFile)`：檔案的路徑，其中包含 JAVAdoc 內容的著作權資訊，將會附加至所有匯入的檔。

* `%(UrlPrefix)`： URL 前置詞，可支援連結至匯入檔內的線上檔。

* `%(UrlStyle)`：連結至線上檔時要產生的 Url 「樣式」。  目前只支援一個樣式： `developer.android.com/reference@2020-Nov` 。


## <a name="libraryprojectzip"></a>LibraryProjectZip

在 Xamarin. Android 系結專案中， **LibraryProjectZip** 組建動作會系結 JAVA/Kotlin 程式庫，並將或檔案內嵌 `.zip` `.aar` 至程式庫。 當 Xamarin. Android 應用程式專案取用程式庫時，它將可從 c # 存取 JAVA/Kotlin Api，並在最終的 Android 應用程式中包含 JAVA/Kotlin 程式碼。

> [!NOTE]
> 只有單一 **LibraryProjectZip** 可以包含在 Xamarin. Android 系結專案中。 .NET 6 將會移除這項限制。

## <a name="linkdescription"></a>LinkDescription

具有 LinkDescription 建置動作的檔案可用來[控制連結器行為](~/cross-platform/deploy-test/linker.md)。

## <a name="proguardconfiguration"></a>ProguardConfiguration

具有 ProguardConfiguration 建置動作的檔案包含可用來控制 `proguard` 行為的選項。 如需此建置動作的詳細資訊，請參閱 [ProGuard](~/android/deploy-test/release-prep/proguard.md)。

除非有下列情況，否則會忽略這些檔案。 [`$(EnableProguard)`](~/android/deploy-test/building-apps/build-properties.md#enableproguard)
MSBuild 屬性為 `True` 。
