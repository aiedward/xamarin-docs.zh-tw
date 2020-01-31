---
title: ProGuard
description: Xamarin.Android ProGuard 是 Java 類別檔案壓縮工具、最佳化工具和預先驗證器。 它可以偵測和移除未使用的程式碼，分析位元組程式碼並予以最佳化。 本指南說明 ProGuard 的運作方式、如何在專案中予以啟用，以及如何加以設定。 文中也提供數個 ProGuard 組態範例。
ms.prod: xamarin
ms.assetid: 29C0E850-3A49-4618-9078-D59BE0284D5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 1ae7850951060f2e89a953ce554a0dbfa286c3a1
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487982"
---
# <a name="proguard"></a>ProGuard

_ProGuard 是 JAVA 類別檔案壓縮工具、優化工具和預先驗證器。它會偵測並移除未使用的程式碼，並分析和優化位元組程式碼。本指南說明 ProGuard 的運作方式、如何在專案中加以啟用，以及如何進行設定。它也提供數個 ProGuard 設定的範例。_

## <a name="overview"></a>概觀

ProGuard 可偵測和移除封裝應用程式的未使用類別、欄位、方法及屬性。 即使對參考的程式庫也能這樣做 (這有助於避免 64k 參考限制)。 Android SDK 提供的 ProGuard 工具也會將位元組程式碼最佳化，以及移除未使用的程式碼指令。 ProGuard 會讀取**輸入 jar**，然後予以壓縮、最佳化並預先驗證；它會將結果寫入一或多個**輸出 jar**。 

ProGuard 會使用下列步驟來處理 APK 的輸入： 

1. **壓縮步驟**&ndash; ProGuard 會以遞迴方式決定要使用的類別和類別成員。 所有其他類別與類別成員都會被捨棄。 

2. **優化步驟**&ndash; ProGuard 會進一步優化程式碼。 
    在其他最佳化之中，並非進入點的類別與方法可設為私人、靜態或最終的進入點，可以移除未使用的參數，而且可以內嵌某些方法。 

3. **混淆步驟**&ndash; 在原生 Android 開發中，ProGuard 會重新命名不是進入點的類別和類別成員。 保留進入點可確保仍可透過其原始名稱來存取。 但是 Xamarin.Android 並不支援這項步驟，原因是該應用程式以中繼語言 (IL) 編譯而成。

4. **預先驗證步驟**&ndash; 會在執行時間和標注類別檔案之前對 JAVA bytecode 執行檢查，以取得 java VM 的優點。 這是唯一不需要知道進入點的步驟。 

這些全都是「選擇性」步驟。 如下一節中所說明，Xamarin.Android ProGuard 只會使用這些步驟的某些步驟。 

## <a name="proguard-in-xamarinandroid"></a>Xamarin.Android 中的 ProGuard

Xamarin.Android ProGuard 組態不會混淆 APK。 事實上，您無法透過 ProGuard 啟用混淆功能 (即使是使用自訂的組態檔)。 因此，Xamarin.Android 的 ProGuard 只會執行**壓縮**和**最佳化**步驟： 

[![壓縮及最佳化步驟](proguard-images/01-xa-chain-sml.png)](proguard-images/01-xa-chain.png#lightbox)

使用 ProGuard 之前要事先知道的一個重要項目就是它在 `Xamarin.Android` 建置處理序中的運作方式。 此程序會使用兩個不同的步驟： 

1. Xamarin Android 連結器

2. ProGuard

所有這些步驟都會描述如下。

### <a name="linker-step"></a>連結器步驟

Xamarin.Android 連結器會為您的應用程式使用靜態分析，以判斷下列各項： 

- 實際上會使用哪些組件。

- 實際上會使用哪些類型。

- 實際上會使用哪些成員。 

連結器一律會在 ProGuard 步驟之前執行。 因此，連結器能移除您可能預期 ProGuard 要在其上執行的組件/類型/成員 (如需 Xamarin.Android 中之連結的詳細資訊，請參閱 [Android 上的連結](~/android/deploy-test/linker.md))。

### <a name="proguard-step"></a>ProGuard 步驟

成功完成連結器步驟之後，ProGuard 會執行以移除未使用的 Java 位元組程式碼。 這是將 APK 最佳化的步驟。 

## <a name="using-proguard"></a>使用 ProGuard

若要在應用程式專案中使用 ProGuard，您必須先啟用 ProGuard。 接下來，您可以讓 Xamarin.Android 建置處理序使用預設 ProGuard 組態檔，或您可以建立自己的組態檔以供 ProGuard 使用。 

### <a name="enabling-proguard"></a>啟用 ProGuard

使用下列步驟以在您的應用程式專案中啟用 ProGuard：

1. 請務必將專案設定為 [發行] 組態 (這相當重要，因為連結器必須執行，ProGuard 才能執行)： 

    [![選取發行組態](proguard-images/02-set-release-sml.png)](proguard-images/02-set-release.png#lightbox)
   
2. 從 [ **Android 選項] 視窗 > 屬性**的 [程式**代碼壓縮工具**] 下拉式清單中選擇 [ **ProGuard** ]： 

    [![已選取 Proguard 程式碼壓縮工具](proguard-images/03-enable-proguard-shrinker-sml.png)](proguard-images/03-enable-proguard-shrinker.png#lightbox)

針對大部分的 Xamarin.Android 應用程式而言，Xamarin.Android 提供的預設 ProGuard 組態檔即足以移除所有 (且僅限) 未使用的程式碼。 若要檢視預設 ProGuard 組態，請開啟位在 **obj\\Release\\proguard\\proguard_xamarin.cfg** 的檔案。

下列範例示範一般產生的 **proguard_xamarin.cfg** 檔案：

```cfg
# This is Xamarin-specific (and enhanced) configuration.

-dontobfuscate

-keep class mono.MonoRuntimeProvider { *; <init>(...); }
-keep class mono.MonoPackageManager { *; <init>(...); }
-keep class mono.MonoPackageManager_Resources { *; <init>(...); }
-keep class mono.android.** { *; <init>(...); }
-keep class mono.java.** { *; <init>(...); }
-keep class mono.javax.** { *; <init>(...); }
-keep class opentk.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk.GameViewBase { *; <init>(...); }
-keep class opentk_1_0.platform.android.AndroidGameView { *; <init>(...); }
-keep class opentk_1_0.GameViewBase { *; <init>(...); }

-keep class android.runtime.** { <init>(***); }
-keep class assembly_mono_android.android.runtime.** { <init>(***); }
# hash for android.runtime and assembly_mono_android.android.runtime.
-keep class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }
-keepclassmembers class md52ce486a14f4bcd95899665e9d932190b.** { *; <init>(...); }

# Android's template misses fluent setters...
-keepclassmembers class * extends android.view.View {
   *** set*(***);
}

# also misses those inflated custom layout stuff from xml...
-keepclassmembers class * extends android.view.View {
   <init>(android.content.Context,android.util.AttributeSet);
   <init>(android.content.Context,android.util.AttributeSet,int);
}
```

下一節說明如何建立自訂的 ProGuard 組態檔。 

### <a name="customizing-proguard"></a>自訂 ProGuard

您可以選擇新增自訂的 ProGuard 組態檔以對 ProGuard 工具施加更多控制。 例如，您可能想要明確告訴 ProGuard 要保留哪些類別。 若要執行此動作，請建立新的 **.cfg** 檔案，並在 [方案總管] 的 [屬性] 窗格中套用 `ProGuardConfiguration` 建置動作： 

[![已選取 ProguardConfiguration 建置動作](proguard-images/04-build-action-sml.png)](proguard-images/04-build-action.png#lightbox)

請記住，由於 ProGuard 會使用兩者，所以此組態檔不會取代 Xamarin.Android **proguard_xamarin.cfg** 檔案。 

有些情況可能是 ProGuard 無法適當分析應用程式；它可能會移除應用程式真正需要的程式碼。 如果發生這種情況，您可以將 `-keep` 這行加入自訂 ProGuard 組態檔： 

```
-keep public class MyClass
```

在此範例中，`MyClass` 設定為您想要 ProGuard 略過的實際類別名稱。

您也可以使用 `[Register]` 註釋來登錄自己的名稱並使用這些名稱來自訂 ProGuard 規則。 您可以登錄 Adapters、Views、BroadcastReceivers、Services、ContentProviders、Activities 及 Fragments 的名稱。 如需使用 `[Register]` 自訂屬性的詳細資訊，請參閱[使用 JNI](~/android/platform/java-integration/working-with-jni.md)。

### <a name="proguard-options"></a>ProGuard 選項

ProGuard 提供一些您可以設定的選項，以對其作業進行更細微的控制。 [ProGuard 手冊](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/introduction.html) \(英文\) 提供使用 ProGuard 的完整參考文件。 

Xamarin.Android 支援下列 ProGuard 選項： 

- [輸入/輸出選項](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#iooptions)

- [保留選項](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptions)

- [壓縮選項](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#shrinkingoptions)

- [一般選項](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#generaloptions)

- [類別路徑](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classpath)

- [檔案名稱](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filename)

- [檔案篩選條件](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filefilters)

- [篩選](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#filters)

- [`Keep` 選項概觀](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoverview)

- [保留選項修飾詞](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#keepoptionmodifiers)

- [類別規格](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#classspecification)

Xamarin.Android 會乎略下列選項：

- [最佳化選項](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#optimizationoptions)

- [混淆選項](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#obfuscationoptions) 

- [預先驗證選項](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/manual/usage.html#preverificationoptions)

## <a name="proguard-and-android-nougat"></a>ProGuard 和 Android Nougat

如果您嘗試使用 ProGuard Android 7.0 或更新版本，您必須下載較新版的 ProGuard，因為 Android SDK 並未隨附與 JDK 1.8 相容的新版本。

您可以使用此 [NuGet 套件](https://www.nuget.org/packages/name.atsushieno.proguard.facebook/5.3.0)來安裝較新版的 `proguard.jar`。 如需更新預設 Android SDK `proguard.jar` 的詳細資訊，請參閱此 [Stack Overflow](https://stackoverflow.com/questions/39514518/xamarin-android-proguard-unsupported-class-version-number-52-0/39514706#39514706) 討論。

您可以在 [SourceForge 頁面](https://sourceforge.net/projects/proguard/files/)找到所有 ProGuard 版本。 

## <a name="example-proguard-configurations"></a>範例 ProGuard 組態

兩個範例 ProGuard 組態檔如下所列。 請注意，在這些情況下，Xamarin.Android 建置處理序會提供**輸入**、**輸出**及**程式庫** jar。 因此，您可以專注於其他選項，例如 `-keep`。 

### <a name="a-simple-android-activity"></a>簡單的 Android 活動

下列範例說明簡單 Android 活動的組態：

```
-injars  bin/classes
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic

-keep public class mypackage.MyActivity
```

### <a name="a-complete-android-application"></a>完整的 Android 應用程式

下列範例說明完整 Android 應用程式的組態：

```
-injars  bin/classes
-injars  libs
-outjars bin/classes-processed.jar
-libraryjars /usr/local/java/android-sdk/platforms/android-9/android.jar

-dontpreverify
-repackageclasses ''
-allowaccessmodification
-optimizations !code/simplification/arithmetic
-keepattributes *Annotation*

-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider

-keep public class * extends android.view.View {
public <init>(android.content.Context);
public <init>(android.content.Context, android.util.AttributeSet);
public <init>(android.content.Context, android.util.AttributeSet, int);
public void set*(...);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet);
}

-keepclasseswithmembers class * {
public <init>(android.content.Context, android.util.AttributeSet, int);
}

-keepclassmembers class * implements android.os.Parcelable {
static android.os.Parcelable$Creator CREATOR;
}

-keepclassmembers class **.R$* {
public static <fields>;
}
```

## <a name="proguard-and-the-xamarinandroid-build-process"></a>ProGuard 與 Xamarin.Android 建置處理序

下列各節會說明 ProGuard 在 Xamarin.Android **發行**建置期間的執行方式。

### <a name="what-command-is-proguard-running"></a>ProGuard 會執行什麼命令？

ProGuard 只是隨 Android SDK 提供的 `.jar`。 因此它是以命令叫用： 

```shell
java -jar proguard.jar options ...
```

### <a name="the-proguard-task"></a>ProGuard 工作

ProGuard 工作可在 **Xamarin.Android.Build.Tasks.dll** 組件內找到。 它是 `_CompileToDalvikWithDx` 目標的一部分，也是 `_CompileDex` 目標的一部分。 

下列清單提供的範例是您使用 [檔案] > [新增專案] 建立新專案之後產生的預設參數： 

```
ProGuardJarPath = C:\Android\android-sdk\tools\proguard\lib\proguard.jar
AndroidSdkDirectory = C:\Android\android-sdk\
JavaToolPath = C:\Program Files (x86)\Java\jdk1.8.0_92\\bin
ProGuardToolPath = C:\Android\android-sdk\tools\proguard\
JavaPlatformJarPath = C:\Android\android-sdk\platforms\android-25\android.jar
ClassesOutputDirectory = obj\Release\android\bin\classes
AcwMapFile = obj\Release\acw-map.txt
ProGuardCommonXamarinConfiguration = obj\Release\proguard\proguard_xamarin.cfg
ProGuardGeneratedReferenceConfiguration = obj\Release\proguard\proguard_project_references.cfg
ProGuardGeneratedApplicationConfiguration = obj\Release\proguard\proguard_project_primary.cfg
ProGuardConfigurationFiles

    {sdk.dir}tools\proguard\proguard-android.txt;
    {intermediate.common.xamarin};
    {intermediate.references};
    {intermediate.application};
    ;

JavaLibrariesToEmbed = C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar
ProGuardJarInput = obj\Release\proguard\__proguard_input__.jar
ProGuardJarOutput = obj\Release\proguard\__proguard_output__.jar
DumpOutput = obj\Release\proguard\dump.txt
PrintSeedsOutput = obj\Release\proguard\seeds.txt
PrintUsageOutput = obj\Release\proguard\usage.txt
PrintMappingOutput = obj\Release\proguard\mapping.txt
```

接下來的範例說明從 IDE 執行的典型 ProGuard 命令：

```cmd
C:\Program Files (x86)\Java\jdk1.8.0_92\\bin\java.exe -jar C:\Android\android-sdk\tools\proguard\lib\proguard.jar -include obj\Release\proguard\proguard_xamarin.cfg -include obj\Release\proguard\proguard_project_references.cfg -include obj\Release\proguard\proguard_project_primary.cfg "-injars 'obj\Release\proguard\__proguard_input__.jar';'C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\MonoAndroid\v7.0\mono.android.jar'" "-libraryjars 'C:\Android\android-sdk\platforms\android-25\android.jar'" -outjars "obj\Release\proguard\__proguard_output__.jar" -optimizations !code/allocation/variable
```

## <a name="troubleshooting"></a>疑難排解

### <a name="file-issues"></a>檔案問題

當 ProGuard 讀取其組態檔時，可能會顯示下列錯誤訊息： 

```
Unknown option '-keep' in line 1 of file 'proguard.cfg'
```

此問題通常是在 Windows 上發生，原因是 `.cfg` 檔案的編碼錯誤。 ProGuard 無法處理可能會以文字檔呈現的「位元組順序標記」(BOM)。 如果有 BOM 存在，ProGuard 會隨即結束並出現上述錯誤。 

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要避免這個問題，請從儲存檔案時允許不含 BOM 的文字編輯器中編輯自訂組態檔。 若要解決此問題，請確定文字編輯器將其編碼設定為 `UTF-8`。 例如，在儲存檔案時，在文字編輯器 [Notepad++](https://notepad-plus-plus.org/) 選取 [編碼] &gt; [編譯成 UTF-8 碼〈檔首無 BOM〉]，即可儲存不含 BOM 的檔案。 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要避免這個問題，請從可讓您省略 BOM 的文字編輯器儲存自訂組態檔。 

-----

### <a name="other-issues"></a>其他問題

ProGuard [疑難排解](https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/troubleshooting.html) \(英文\) 頁面討論使 ProGuard 時會遇到的常見問題 (和解決方案)。

## <a name="summary"></a>總結

本指南說明 ProGuard 如何在 Xamarin.Android 中運作、如何在應用程式專案中予以啟用，以及如何加以設定。 文中提供範例 ProGuard 組態，並描述常見問題的解決方式。 如需 ProGuard 工具與 Android 的詳細資訊，請參閱[壓縮程式碼和資源](https://developer.android.com/tools/help/proguard.html) \(英文\)。 

## <a name="related-links"></a>相關連結

- [準備可供發行的應用程式](~/android/deploy-test/release-prep/index.md)
