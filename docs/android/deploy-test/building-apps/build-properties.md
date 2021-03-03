---
title: 組建屬性
description: 本檔將列出 Xamarin. Android 組建程式中所有支援的屬性。
ms.prod: xamarin
ms.assetid: FC0DBC08-EBCB-4D2D-AB3F-76B54E635C22
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 03/01/2021
ms.openlocfilehash: 5c26c171c198698580e2b2170a6692d8fb80a171
ms.sourcegitcommit: 3aa9bdcaaedca74ab5175cb2338a1df122300243
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749313"
---
# <a name="build-properties"></a>組建屬性

MSBuild 屬性會控制 [目標](~/android/deploy-test/building-apps/build-targets.md)的行為。
這些專案是在 [MSBuild PropertyGroup](/visualstudio/msbuild/propertygroup-element-msbuild)中的專案檔（例如 **MyApp. .csproj**）內指定。

## <a name="adbtarget"></a>AdbTarget

`$(AdbTarget)`屬性會指定 android 套件可能安裝或移除的 android 目標裝置。
這個屬性的值與 [ [ `adb` 目標裝置] 選項](https://developer.android.com/tools/help/adb.html#issuingcommands)相同。

## <a name="aftergenerateandroidmanifest"></a>AfterGenerateAndroidManifest

這個屬性中所列的 MSBuild 目標將直接在內部 `_GenerateJavaStubs` 目標（也就是在中產生檔案的位置）之後執行 `AndroidManifest.xml` `$(IntermediateOutputPath)` 。 如果您想要對產生的檔案進行任何修改 `AndroidManifest.xml` ，您可以使用這個擴充點來執行該動作。

Xamarin.Android 9.4 已新增此支援。

## <a name="androidaapt2compileextraargs"></a>AndroidAapt2CompileExtraArgs

指定在處理 Android 資產和資源時，要傳遞至 **aapt2 compile** 命令的其他命令列選項。

已在 Xamarin.Android 9.1 中新增。

## <a name="androidaapt2linkextraargs"></a>AndroidAapt2LinkExtraArgs

指定在處理 Android 資產和資源時，要傳遞至 **aapt2 link** 命令的其他命令列選項。

已在 Xamarin.Android 9.1 中新增。

## <a name="androidaddkeepalives"></a>AndroidAddKeepAlives

此為布林值屬性，可控制連結器是否會在系結 `GC.KeepAlive()` 專案內插入調用，以防止物件收集過早。

發行設定 `True` 組建的預設值為。

這個屬性是在 Xamarin. Android 11.2 中新增的。

## <a name="androidaotcustomprofilepath"></a>AndroidAotCustomProfilePath

`aprofutil`應該建立以保存 profiler 資料的檔案。

## <a name="androidaotprofiles"></a>AndroidAotProfiles

字串屬性，可讓開發人員從命令列加入 AOT 設定檔。 它是以分號或逗號分隔的絕對路徑清單。
已在 Xamarin. Android 10.1 中新增。

## <a name="androidaotprofilerport"></a>AndroidAotProfilerPort

`aprofutil`取得分析資料時應該連接的埠。

## <a name="androidapkdigestalgorithm"></a>AndroidApkDigestAlgorithm

字串值，指定要搭配使用的摘要演算法 `jarsigner -digestalg` 。

預設值是 `SHA-256`。 在 Xamarin. Android 10.0 及更早版本中，預設值為 `SHA1` 。

Xamarin.Android 9.4 已新增此支援。

## <a name="androidapksigneradditionalarguments"></a>AndroidApkSignerAdditionalArguments

字串屬性，可讓開發人員提供工具的其他引數 `apksigner` 。

已在 Xamarin.Android 8.2 中新增。

## <a name="androidapksigningalgorithm"></a>AndroidApkSigningAlgorithm

指定要搭配使用之簽署演算法的字串值 `jarsigner -sigalg` 。

預設值是 `SHA256withRSA`。 在 Xamarin. Android 10.0 及更早版本中，預設值為 `md5withRSA` 。

已在 Xamarin.Android 8.2 中新增。

## <a name="androidapplication"></a>AndroidApplication

指出專案是否適用于 Android 應用程式的布林值 (`True`) 或 android 程式庫專案 (`False` 或不存在) 。

Android 套件內只能有一個具有 `<AndroidApplication>True</AndroidApplication>` 的專案。 (遺憾的是，此限制尚未經過驗證，因而可能造成 Android 資源發生些微異常錯誤。)

## <a name="androidapplicationjavaclass"></a>AndroidApplicationJAVAClass

`android.app.Application`當類別繼承自[Android 應用程式](xref:Android.App.Application)時，要用來取代的完整 JAVA 類別名稱。

這個屬性通常是由 *其他* 屬性（例如 `$(AndroidEnableMultiDex)` MSBuild 屬性）設定。

已在 Xamarin.Android 6.1 中新增。

## <a name="androidbinutilspath"></a>AndroidBinUtilsPath

包含 Android [binutils][binutils] 的目錄路徑 `ld` ，例如原生連結器和 `as` 原生組合語言。 這些工具是 Android NDK 的一部分，也包含在 Xamarin 安裝中。

預設值是 `$(MonoAndroidBinDirectory)\ndk\`。

已在 Xamarin. Android 10.0 中新增。

[binutils]: https://android.googlesource.com/toolchain/binutils/

## <a name="androidboundexceptiontype"></a>AndroidBoundExceptionType

字串值，指定當 Xamarin. Android 提供的型別根據 JAVA 類型（例如和）來實作為 .NET 型別或介面時，應如何傳播例外狀況 `Android.Runtime.InputStreamInvoker` `System.IO.Stream` `Android.Runtime.JavaDictionary` `System.Collections.IDictionary` 。

- `Java`：原始 JAVA 例外狀況類型會依原樣傳播。

  這表示，例如，可能會從擲回， `InputStreamInvoker` 而不是正確地執行 `System.IO.Stream` API `Java.IO.IOException` `Stream.Read()` `System.IO.IOException` 。

  這是在11.1 之前的所有 Xamarin 版本中的例外狀況傳播行為。

  這是 Xamarin. Android 11.1 中的預設值。

- `System`：原始 JAVA 例外狀況類型會被攔截並包裝在適當的 .NET 例外狀況型別中。

  這表示，例如， `InputStreamInvoker` 正確地實行 `System.IO.Stream` ，而且 `Stream.Read()` *不* 會擲回 `Java.IO.IOException` 實例。   (可以改為擲回， `System.IO.IOException` 其中包含 `Java.IO.IOException` 做為 `Exception.InnerException` 值。 ) 

  這會成為 .NET 6.0 中的預設值。

已在 Xamarin. Android 10.2 中新增。

## <a name="androidbuildapplicationpackage"></a>AndroidBuildApplicationPackage

布林值，指出是否要建立和簽署封裝 (. apk) 。 將這個值設定為 `True` 相當於使用 [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#install)
組建目標。

在 Xamarin.Android 7.1 之後已新增這個屬性的支援。

這個屬性的預設值是 `False`。

## <a name="androidbundleconfigurationfile"></a>AndroidBundleConfigurationFile

指定在建立 Android 應用程式套件組合時，用來做為 [設定檔][bundle-config-format] 的檔案名 `bundletool` 。 此檔案可控制如何從套件組合產生 Apk 的一些層面，例如，將分割區分為哪些維度來產生 Apk。 請注意，Xamarin 會自動設定其中一些設定，包括要保留未壓縮的副檔名清單。

只有當設定為時，這個屬性才會相關 [`$(AndroidPackageFormat)`](#androidpackageformat) `aab` 。

已在 Xamarin. Android 10.3 中新增。

[bundle-config-format]: https://developer.android.com/studio/build/building-cmdline#bundleconfig

## <a name="androidclassparser"></a>AndroidClassParser

字串屬性，控制檔案的剖析方式 `.jar` 。 可能的值包括：

- **class-parse**：使用 `class-parse.exe` 來直接剖析 Java 位元組程式碼，而不需要 JVM 的協助。 這是實驗值。

- **jar2xml**：使用 `jar2xml.jar` 以使用 Java 反映來從 `.jar` 檔案擷取類型和成員。

`class-parse` 相對於 `jar2xml` 的優勢在於：

- `class-parse` 可以從 JAVA 位元組字元（包含 *debug* 符號）解壓縮參數名稱， (使用) 編譯的位元組 `javac -g` 。

- `class-parse` 不會「略過」繼承自或包含無法解析類型成員的類別。

**實驗** 性。 已在 Xamarin.Android 6.0 中新增。

預設值是 `jar2xml`。

的支援 `jar2xml` 已淘汰，而且的支援 `jar2xml` 將會移除為 .net 6 的一部分。

## <a name="androidcodegentarget"></a>AndroidCodegenTarget

控制程式代碼產生目標 ABI 的字串屬性。
可能的值包括：

- **XamarinAndroid**：使用自 Mono for Android 1.0 之後存在的 JNI 系結 API。 使用 Xamarin.Android 5.0 或更新版本所建置的繫結組件只可在 Xamarin.Android 5.0 或更新版本 (API/ABI 新增項目) 上執行，但「來源」與舊版產品相容。

- **XAJavaInterop1**：在 JNI 引動過程使用 Java.Interop。 使用 `XAJavaInterop1` 的繫結組件只可使用 Xamarin.Android 6.1 或更新版本來建置及執行。 Xamarin.Android 6.1 及更新版本使用此值繫結 `Mono.Android.dll`。

`XAJavaInterop1` 的優點包括：

- 組件較小。

- 以 `jmethodID` 快取 `base` 方法引動過程，但繼承階層中所有其他繫結類型皆必須使用 `XAJavaInterop1` 或更新版本來建置。

- 以 `jmethodID` 快取受控子類別的 Java 可呼叫包裝函式建構函式。

預設值是 `XAJavaInterop1`。

## <a name="androidcreatepackageperabi"></a>AndroidCreatePackagePerAbi

此為布林值屬性，可決定是否要建立每個在中指定的一 *組* 檔案， [`$(AndroidSupportedAbis)`](#androidsupportedabis) 而不是支援單一中的所有 abi `.apk` 。

另請參閱 [大樓 ABI-Specific apk](~/android/deploy-test/building-apps/abi-specific-apks.md) 指南。

## <a name="androiddebugkeyalgorithm"></a>AndroidDebugKeyAlgorithm

指定要用於的預設演算法 `debug.keystore` 。 其預設為 `RSA`。

## <a name="androiddebugkeyvalidity"></a>AndroidDebugKeyValidity

指定要用於的預設有效性 `debug.keystore` 。 預設值為 `10950`、`30 * 365` 或 `30 years`。

## <a name="androiddebugstoretype"></a>AndroidDebugStoreType

指定要用於的金鑰存放區檔案格式 `debug.keystore` 。 其預設為 `pkcs12`。

已在 Xamarin. Android 10.2 中新增。


## <a name="androiddeviceuserid"></a>AndroidDeviceUserId

允許在來賓或公司帳戶下部署和偵測應用程式。 值是 `uid` 您從下列 adb 命令取得的值：

```
adb shell pm list users
```

這會傳回下列資料：

```
Users:
    UserInfo{0:Owner:c13} running
    UserInfo{10:Guest:404}
```

`uid`是第一個整數值。 在範例中，它們是 `0` 和 `10` 。

這個屬性是在 Xamarin. Android 11.2 中新增的。

## <a name="androiddextool"></a>AndroidDexTool

具有或值之有效值的列舉樣式屬性 `dx` `d8` 。 指出在 Xamarin.Android 建置流程期間使用哪一個 Android [dex][dex] 編譯器。
目前預設為 `dx`。 如需進一步的詳細資訊，請參閱有關 [D8 和 R8][d8-r8] 的文件。

[dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
[d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

## <a name="androidenabledesugar"></a>AndroidEnableDesugar

此為布林值屬性，可判斷是否已 `desugar` 啟用。 Android 目前不支援所有 Java 8 功能，預設工具鏈會藉由在 `javac` 編譯器的輸出上執行位元組程式碼轉換 (稱為 `desugar`)，以實作新的語言功能。 如果使用，則預設為， `False` `AndroidDexTool=dx` `True` 如果使用，則預設為 [`$(AndroidDexTool)`](#androiddextool) = `d8` 。

## <a name="androidenablegoogleplaystorechecks"></a>AndroidEnableGooglePlayStoreChecks

此為布林值屬性，可讓開發人員停用下列 Google Play 商店檢查： XA1004、XA1005 和 XA1006。 這適用於不是以 Google Play 商店為目標，所以不想要執行這些檢查的開發人員。

Xamarin.Android 9.4 已新增此支援。

## <a name="androidenablemultidex"></a>AndroidEnableMultiDex

此為布林值屬性，決定是否會在最終中使用多 dex 支援 `.apk` 。

在 Xamarin.Android 5.1 中已新增這個屬性的支援。

這個屬性的預設值是 `False`。

## <a name="androidenablepreloadassemblies"></a>AndroidEnablePreloadAssemblies

此為布林值屬性，可控制是否在處理常式啟動期間載入應用程式封裝內所含的所有 managed 元件。

設定為 `True` 時，就會在流程啟動期間先載入應用程式套件內隨附的所有組件，然後再叫用任何應用程式程式碼。
這與 Xamarin.Android 9.2 之前版本的做法一致。

設定為 `False` 時，組件將只會在需要時載入。
這可加快應用程式啟動速度，同時也能夠與傳統型 .NET 語意更加一致。  若要查看省下的時間，請設定 `debug.mono.log` 系統屬性以包括 `timing`，並在 `adb logcat` 內尋找 `Finished loading assemblies: preloaded` 訊息。

如果使用相依性插入的應用程式或程式庫要求 `AppDomain.CurrentDomain.GetAssemblies()` 傳回應用程式組合內的所有組件 (即使還不需要那些組件)，它們可能就會 *要求* 此屬性為 `True`。

此值預設為 `True`。

已在 Xamarin.Android 9.2 中新增。

## <a name="androidenableprofiledaot"></a>AndroidEnableProfiledAot

此為布林值屬性，可決定在預先編譯期間是否要使用 AOT 設定檔。

設定檔會列在 [`@(AndroidAotProfile)`](~/android/deploy-test/building-apps/build-items.md#androidaotprofile)
專案群組。 此 ItemGroup 包含預設設定檔。 您可以藉由移除現有的 () ，並新增您自己的 AOT 設定檔來加以覆寫。

Xamarin.Android 9.4 已新增此屬性支援。

這個屬性的預設值是 `False`。

## <a name="androidenablesgenconcurrent"></a>AndroidEnableSGenConcurrent

布林值屬性，決定是否會使用 Mono 的 [並行 GC 收集器](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen) 。

在 Xamarin.Android 7.2 中已新增這個屬性的支援。

這個屬性的預設值是 `False`。

## <a name="androiderroroncustomjavaobject"></a>AndroidErrorOnCustomJAVAObject

此為布林值屬性，可判斷型別是否可以 `Android.Runtime.IJavaObject` 
 在不同時繼承自或的 *情況下* 執行 `Java.Lang.Object` `Java.Lang.Throwable` ：

```csharp
class BadType : IJavaObject {
    public IntPtr Handle {
        get {return IntPtr.Zero;}
    }

    public void Dispose()
    {
    }
}
```

若為 True，這類類型會產生 XA4212 錯誤，否則會產生 XA4212 警告。

在 Xamarin.Android 8.1 中已新增這個屬性的支援。

這個屬性的預設值是 `True`。

## <a name="androidexplicitcrunch"></a>AndroidExplicitCrunch

在 Xamarin. Android 11.0 中已不再支援。

## <a name="androidextraaotoptions"></a>AndroidExtraAotOptions

字串屬性，允許在 `Aot` 具有 [`$(AndroidEnableProfiledAot)`](#androidenableprofiledaot) 或設定為之專案的工作期間，將其他選項傳遞至 Mono 編譯器 [`$(AotAssemblies)`](#aotassemblies) `true` 。
呼叫 Mono 跨編譯器時，會將屬性的字串值新增至回應檔。

一般來說，這個屬性應該保持空白，但在某些特殊情況下，它可能會提供有用的彈性。

請注意，這個屬性與相關屬性不同 `$(AndroidAotAdditionalArguments)` 。 該屬性會將逗點分隔的引數放入 `--aot` Mono 編譯器的選項中。 `$(AndroidExtraAotOptions)` 改為將完整的獨立空間分隔選項（例如或）傳遞 `--verbose` `--debug` 給編譯器。

已在 Xamarin. Android 10.2 中新增。

<a name="AndroidFastDeploymentType"></a>

## <a name="androidfastdeploymenttype"></a>AndroidFastDeploymentType

`:`值的 (冒號) 分隔的值清單，可在 MSBuild 屬性為時，控制可部署至目標裝置上[快速部署目錄](~/android/deploy-test/building-apps/build-process.md#Fast_Deployment)的類型 [`$(EmbedAssembliesIntoApk)`](#embedassembliesintoapk) `False` 。 資源如果使用快速部署，則不會內嵌到所產生的 `.apk`，從而可加快部署時間   (快速部署的更多，則需要重建的頻率較低， `.apk` 而且安裝程式可能會更快。 ) 有效的值包括：

- `Assemblies`：部署應用程式組件。
- `Dexes`：部署檔案 `.dex` 、原生程式庫和 typemaps。
  **執行 Android 4.4 或更新版本 (API-19) 的裝置上才能使用這個值。**

預設值是 `Assemblies`。

認可 [f0d565fe](https://github.com/xamarin/xamarin-android/commit/f0d565fe4833f16df31378c77bbb492ffd2904b9)中已移除透過該系統快速部署資源和資產的支援。 這是因為它需要使用已淘汰的 API 才能運作。

**實驗** 性。 這個屬性是在 Xamarin. Android 6.1 中新增的。

## <a name="androidgeneratejnimarshalmethods"></a>AndroidGenerateJniMarshalMethods

Bool 屬性，可讓您產生 JNI 封送處理方法做為組建程式的一部分。 這可大幅減少系結 `System.Reflection` helper 程式碼中的使用方式。

根據預設，這會設定為 False。 如果開發人員想要使用新的 JNI 封送處理方法功能，他們可以在其 csproj 中設定下列程式碼：

```xml
<AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
```

在其中 `.csproj` 。 或者，透過下列參數在命令列上提供屬性：

```shell
/p:AndroidGenerateJniMarshalMethods=True
```

**實驗** 性。 已在 Xamarin.Android 9.2 中新增。
預設值是 False。

## <a name="androidgeneratejnimarshalmethodsadditionalarguments"></a>AndroidGenerateJniMarshalMethodsAdditionalArguments

可以用來將其他參數加入至調用的字串屬性 `jnimarshalmethod-gen.exe` 。  這適用於偵錯，因此可以使用 `-v`、`-d` 或 `--keeptemp` 等選項。

預設值為空字串。 您可以在檔案中 `.csproj` 或在命令列上設定它。 例如：

```xml
<AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
```

或者：

```shell
/p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
```

已在 Xamarin.Android 9.2 中新增。

## <a name="androidgeneratelayoutbindings"></a>AndroidGenerateLayoutBindings

如果設定為，則會啟用配置 [程式碼後端](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md) 的產生（如果設定為，則 `true` 會完全停用） `false` 。 預設值是 `false`。

## <a name="androidhttpclienthandlertype"></a>AndroidHttpClientHandlerType

控制預設的函式 `System.Net.Http.HttpMessageHandler` 將使用的預設實作為 `System.Net.Http.HttpClient` 。 值是子類別的元件限定型別名稱 `HttpMessageHandler` ，適合搭配使用 [`System.Type.GetType(string)`](/dotnet/api/system.type.gettype#System_Type_GetType_System_String_) 。
此屬性最常見的值：

- `Xamarin.Android.Net.AndroidClientHandler`：使用 Android JAVA Api 來執行網路要求。 這允許在基礎 Android 版本支援 TLS 1.2 時存取 TLS 1.2 URL。 只有 Android 5.0 及更新版本能夠可靠地透過 Java 提供 TLS 1.2 支援。

  這對應到 Visual Studio 屬性頁中的 [Android] 選項及 Visual Studio for Mac 屬性頁中的 [AndroidClientHandler] 選項。

  當 [Android 最低版本] 的設定為 [Android 5.0 (Lollipop)]，或當 Visual Studio for Mac 中之 [目標平台] 的設定為 [最新及最高] 時，[新增專案] 精靈會為新專案選取此選項。

- 取消設定/空字串：這相當於 `System.Net.Http.HttpClientHandler, System.Net.Http`

  這對應到 Visual Studio屬性頁中的 [預設] 選項。

  當 Visual Studio 中之 [Android 最低版本] 的設定為 [Android 4.4.87] 或更低，或當 Visual Studio for Mac 中之 [目標平台] 的設定為[新式開發] 或 [相容性上限] 時，[新增專案] 精靈會為新專案選取此選項。

- `System.Net.Http.HttpClientHandler, System.Net.Http`：使用 managed `HttpMessageHandler` 。

  這對應到 Visual Studio屬性頁中的 [受控] 選項。

> [!NOTE]
> 如果在5.0 之前的 Android 版本上需要 TLS 1.2 支援， *或是* 和相關的 API 需要 tls 1.2 支援 `System.Net.WebClient` ，則 [`$(AndroidTlsProvider)`](#androidtlsprovider) 應該使用。

> [!NOTE]
> 您可以藉由設定[ `XA_HTTP_CLIENT_HANDLER_TYPE` 環境變數](~/android/deploy-test/environment.md)來支援此屬性。
> `$XA_HTTP_CLIENT_HANDLER_TYPE`在檔案中找到的值，其組建動作為[`@(AndroidEnvironment)`](~/android/deploy-test/building-apps/build-items.md#androidenvironment)
> 將會優先使用。

已在 Xamarin.Android 6.1 中新增。

## <a name="androidincludewrapsh"></a>AndroidIncludeWrapSh

布林值，指出是否應該將 Android 包裝函式腳本 ([`wrap.sh`](https://developer.android.com/ndk/guides/wrap-script)) 封裝到 APK 中。 屬性預設為， `false` 因為包裝函式腳本可能會大幅影響應用程式啟動和運作的方式，而且應該只在必要時才包含腳本，例如用於偵錯工具或變更應用程式啟動/執行時間行為。

腳本會使用來新增至專案 [`@(AndroidNativeLibrary)`](~/android/deploy-test/building-apps/build-items.md#androidnativelibrary)
組建動作，因為它會放在與架構特定原生程式庫相同的目錄中，而且必須命名為 `wrap.sh` 。

指定腳本路徑最簡單的方式， `wrap.sh` 就是將它放在以目標架構命名的目錄中。 如果您的每個架構只有一個，這個方法會運作 `wrap.sh` ：

```xml
<AndroidNativeLibrary Include="path/to/arm64-v8a/wrap.sh" />
```

但是，如果您的專案在每個架構中都需要一個以上的專案， `wrap.sh` 基於不同用途，此方法將無法運作。
相反地，在這種情況下，可以使用的 `Link` 中繼資料來指定名稱 `AndroidNativeLibrary` ：

```xml
<AndroidNativeLibrary Include="/path/to/my/arm64-wrap.sh">
  <Link>lib\arm64-v8a\wrap.sh</Link>
</AndroidNativeLibrary>
```

如果 `Link` 使用中繼資料，其值中所指定的路徑必須是有效的原生架構特定程式庫路徑（相對於 APK 根目錄）。 路徑的格式為 `lib\ARCH\wrap.sh` ，其中 `ARCH` 可以是下列其中一個：

+ `arm64-v8a`
+ `armeabi-v7a`
+ `x86_64`
+ `x86`


## <a name="androidkeystore"></a>AndroidKeyStore

布林值，指出是否應該使用自訂簽署資訊。 預設值是 `False`，也就是會使用預設的偵錯簽署金鑰來簽署套件。

## <a name="androidlaunchactivity"></a>AndroidLaunchActivity

要啟動的 Android 活動。

## <a name="androidlinkmode"></a>AndroidLinkMode

指定應在包含于 Android 套件內的元件上執行的 [連結](~/android/deploy-test/linker.md) 類型。 僅限用於 Android 應用程式專案內。 預設值是 SdkOnly。 有效值為：

- **無**：不會嘗試任何連結。

- **SdkOnly**：只會對基底類別庫執行連結，而不會對使用者的組件執行。

- **完整**：會對基底類別庫和使用者組件執行連結。

  > [!NOTE]
  > 若使用的 `AndroidLinkMode` 值為 *Full*，通常會導致應用程式損壞，特別是在使用了「反映」時。 除非您確實了解您正在執行的動作，否則請避免使用此值。

```xml
<AndroidLinkMode>SdkOnly</AndroidLinkMode>
```

## <a name="androidlinkskip"></a>AndroidLinkSkip

指定以分號分隔的 (`;` ，) 不應連結之元件的元件名稱清單（不含副檔名）。 僅限用於 Android 應用程式專案內。

```xml
<AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
```

## <a name="androidlinktool"></a>AndroidLinkTool

具有或值之有效值的列舉樣式屬性 `proguard` `r8` 。 指出哪一個程式碼壓縮工具用於 Java 程式碼。 目前預設為空字串，如果 `$(AndroidEnableProguard)` 為 `True`，則預設為 `proguard`。 如需進一步的詳細資訊，請參閱有關 [D8 和 R8][d8-r8] 的文件。

[d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

## <a name="androidlintenabled"></a>AndroidLintEnabled

Bool 屬性，可讓開發人員在 `lint` 封裝流程中執行 android 工具。

當 `$(AndroidLintEnabled)` = True 時，會使用下列屬性：

- [`$(AndroidLintEnabledIssues)`](#androidlintenabledissues):
- [`$(AndroidLintDisabledIssues)`](#androidlintdisabledissues):
- [`$(AndroidLintCheckIssues)`](#androidlintcheckissues):

也可以使用下列組建動作：

- [`@(AndroidLintConfig)`](~/android/deploy-test/building-apps/build-items.md#androidlintconfig):

如需 Android `lint` 工具的詳細資訊，請參閱 [Lint 說明](https://developer.android.com/studio/write/lint)。

## <a name="androidlintenabledissues"></a>AndroidLintEnabledIssues

只有當 = True 時，才會使用這個屬性 [`$(AndroidLintEnabled)`](#androidlintenabled) 。

要啟用的不起毛問題清單（以逗號分隔）。

## <a name="androidlintdisabledissues"></a>AndroidLintDisabledIssues

只有當 = True 時，才會使用這個屬性 [`$(AndroidLintEnabled)`](#androidlintenabled) 。

要停用的不起毛問題清單（以逗號分隔）。

## <a name="androidlintcheckissues"></a>AndroidLintCheckIssues

只有當 = True 時，才會使用這個屬性 [`$(AndroidLintEnabled)`](#androidlintenabled) 。

要檢查的不起毛問題清單（以逗號分隔）。

注意：只會檢查這些問題。

## <a name="androidmanagedsymbols"></a>AndroidManagedSymbols

此為布林值屬性，可控制是否要產生序列點，以便從堆疊追蹤中解壓縮檔案名稱和行號資訊 `Release` 。

已在 Xamarin.Android 6.1 中新增。

## <a name="androidmanifest"></a>AndroidManifest

指定要做為應用程式之範本的檔案名 [`AndroidManifest.xml`](~/android/platform/android-manifest.md) 。
建置期間會合併任何其他必要值以產生實際的 `AndroidManifest.xml`。
`$(AndroidManifest)` 必須在 `/manifest/@package` 屬性中包含套件名稱。

## <a name="androidmanifestmerger"></a>AndroidManifestMerger

指定用於合併 *AndroidManifest.xml* 檔案的執行。 這是列舉樣式的屬性，其中會 `legacy` 選取原始的 c # 執行，並 `manifestmerger.jar` 選取 Google 的 JAVA 執行。

預設值為 [目前] `legacy` 。 這會 `manifestmerger.jar` 在未來的版本中變更為，以配合 Android Studio 的行為。

Google 的合併可啟用的支援， `xmlns:tools="http://schemas.android.com/tools"` 如 [Android 檔][manifest-merger]中所述。

在 Xamarin. Android 10.2 中引進

[manifest-merger]: https://developer.android.com/studio/build/manifest-merge

## <a name="androidmanifestplaceholders"></a>AndroidManifestPlaceholders

*AndroidManifest.xml* 的索引鍵/值取代組分號分隔清單，其中每個配對都具有格式 `key=value` 。

例如，的屬性值 `assemblyName=$(AssemblyName)` `${assemblyName}` 會定義可以出現在 *AndroidManifest.xml* 中的預留位置：

```xml
<application android:label="${assemblyName}"
```

這可讓您從組建程式將變數插入 *AndroidManifest.xml* 檔案中。

## <a name="androidmultidexclasslistextraargs"></a>AndroidMultiDexClassListExtraArgs

字串屬性，可讓開發人員在產生檔案時，將額外的引數傳遞給 `com.android.multidex.MainDexListBuilder` `multidex.keep` 。

其中一個特定情況是您在 `dx` 編譯期間收到下列錯誤時。

```text
com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
```

如果您收到這個錯誤，您可以將下列新增至 `.csproj` 。

```xml
<DxExtraArguments>--force-jumbo </DxExtraArguments>
<AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
```

此舉應該可讓 `dx` 步驟成功完成。

已在 Xamarin.Android 8.3 中新增。

## <a name="androidpackageformat"></a>AndroidPackageFormat

具有或值之有效值的列舉樣式屬性 `apk` `aab` 。 這會指出您要將 Android 應用程式封裝為 [ APK 檔案][apk]或[ Android 應用程式套件組合][bundle]。 應用程式套件組合是 `Release` 的新格式，專供 Google Play 中的提交之用。 此值目前的預設值為 `apk`。

當 `$(AndroidPackageFormat)` 的設定為 `aab` 時，將會一併設定 Android 應用程式套件組合所需的其他 MSBuild 屬性：

- [`$(AndroidUseAapt2)`](~/android/deploy-test/building-apps/build-properties.md#androiduseaapt2) 是 `True` 。
- [`$(AndroidUseApkSigner)`](#androiduseapksigner) 是 `False` 。
- [`$(AndroidCreatePackagePerAbi)`](#androidcreatepackageperabi) 是 `False` 。

[apk]: https://en.wikipedia.org/wiki/Android_application_package
[bundle]: https://developer.android.com/platform/technology/app-bundle

## <a name="androidpackagenamingpolicy"></a>AndroidPackageNamingPolicy

列舉樣式屬性，用於指定所產生 JAVA 原始程式碼的 JAVA 封裝名稱。

在 Xamarin. Android 10.2 和更新版本中，唯一支援的值為 `LowercaseCrc64` 。

在 Xamarin. Android 10.1 中， `LowercaseMD5` 也提供轉換值，允許切換回原始的 JAVA 套件名稱樣式，如同在 xamarin. Android 10.0 及更早版本中使用。 此選項已在 Xamarin. Android 10.2 中移除，以改善與已強制執行 FIPS 合規性之組建環境的相容性。

已在 Xamarin. Android 10.1 中新增。

## <a name="androidproguardmappingfile"></a>AndroidProguardMappingFile

指定的 `-printmapping` proguard 規則 `r8` 。 這表示檔案 `mapping.txt` 將會在資料夾中產生 `$(OutputPath)` 。 然後，將套件上傳至 Google Play 商店時，即可使用此檔案。

預設值是 `$(OutputPath)mapping.txt`。

這個屬性是在 Xamarin. Android 11.2 中新增的。

## <a name="androidr8ignorewarnings"></a>AndroidR8IgnoreWarnings

指定的 `-ignorewarnings` proguard 規則 `r8` 。 這可讓您 `r8` 繼續進行 dex 編譯，即使遇到特定警告也是如此。 預設為 `True` ，但可以設定為， `False` 以強制執行更嚴格的行為。 如需詳細資訊，請參閱 [ProGuard 手冊](https://www.guardsquare.com/products/proguard/manual/usage) 。

已在 Xamarin. Android 10.3 中新增。

## <a name="androidr8jarpath"></a>AndroidR8JarPath

要 `r8.jar` 與 r8 dex-編譯器和壓縮工具搭配使用的路徑。 預設為 Xamarin.Android 安裝中的路徑。 如需進一步的詳細資訊，請參閱有關 [D8 和 R8][d8-r8] 的文件。

## <a name="androidresgenextraargs"></a>AndroidResgenExtraArgs

指定在處理 Android 資產和資源時，要傳遞至 **aapt** 命令的其他命令列選項。

## <a name="androidresgenfile"></a>AndroidResgenFile

指定要產生的資源檔名稱。 預設範本會將此值設定為 `Resource.designer.cs`。

## <a name="androidsdkbuildtoolsversion"></a>AndroidSdkBuildToolsVersion

Android SDK build tools 套件提供 **aapt** 和 **zipalign** 工具，還有其他工具。 您可以同時安裝建置工具套件的多個不同版本。 如果「慣用的」建置工具版本存在，可藉由檢查及使用該版本來完成選擇要封裝的建置工具套件，如果「慣用的」版本不存在，則會使用已安裝的建置工具套件中版本最高者。

`$(AndroidSdkBuildToolsVersion)` MSBuild 屬性包含慣用的建置工具版本。 Xamarin.Android 建置系統會在 `Xamarin.Android.Common.targets` 中提供預設值，而如果 (舉例來說) 最新的 aapt 損毀但您知道有舊版 aapt 能夠正常運作，則可以在專案檔內複寫此預設值，以選擇其他建置工具版本。

## <a name="androidsigningkeyalias"></a>AndroidSigningKeyAlias

在金鑰存放區中指定金鑰的別名。 這是在建立金鑰儲存區時所使用的 **keytool -alias** 值。

## <a name="androidsigningkeypass"></a>AndroidSigningKeyPass

在金鑰儲存區檔案中指定金鑰的密碼。 這是在 `keytool` 要求 **輸入 $(AndroidSigningKeyAlias) 的金鑰密碼** 時所輸入的值。

在 Xamarin. Android 10.0 及更早版本中，此屬性只支援純文字密碼。

在 Xamarin. Android 10.1 和更新版本中，此屬性也支援和前置詞， `env:` `file:` 可用來指定包含密碼的環境變數或檔案。 這些選項提供防止密碼出現在組建記錄檔中的方法。

例如，若要使用名為 *AndroidSigningPassword* 的環境變數：

```xml
<PropertyGroup>
    <AndroidSigningKeyPass>env:AndroidSigningPassword</AndroidSigningKeyPass>
</PropertyGroup>
```

若要使用位於下列位置的檔案 `C:\Users\user1\AndroidSigningPassword.txt` ：

```xml
<PropertyGroup>
    <AndroidSigningKeyPass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningKeyPass>
</PropertyGroup>
```

> [!NOTE]
> `env:`當設為時，不支援前置詞 [`$(AndroidPackageFormat)`](#androidpackageformat) `aab` 。

## <a name="androidsigningkeystore"></a>AndroidSigningKeyStore

指定所建立之金鑰儲存區檔案的檔案名 `keytool` 。 這會對應至提供給 **keytool -keystore** 選項的值。

## <a name="androidsigningstorepass"></a>AndroidSigningStorePass

指定的密碼 [`$(AndroidSigningKeyStore)`](#androidsigningkeystore) 。
這是在建立金鑰儲存區檔案時提供給 `keytool`，並且會要求 **輸入金鑰儲存區密碼:** 的值。

在 Xamarin. Android 10.0 及更早版本中，此屬性只支援純文字密碼。

在 Xamarin. Android 10.1 和更新版本中，此屬性也支援和前置詞， `env:` `file:` 可用來指定包含密碼的環境變數或檔案。 這些選項提供防止密碼出現在組建記錄檔中的方法。

例如，若要使用名為 *AndroidSigningPassword* 的環境變數：

```xml
<PropertyGroup>
    <AndroidSigningStorePass>env:AndroidSigningPassword</AndroidSigningStorePass>
</PropertyGroup>
```

若要使用位於下列位置的檔案 `C:\Users\user1\AndroidSigningPassword.txt` ：

```xml
<PropertyGroup>
    <AndroidSigningStorePass>file:C:\Users\user1\AndroidSigningPassword.txt</AndroidSigningStorePass>
</PropertyGroup>
```

> [!NOTE]
> `env:`當設為時，不支援前置詞 [`$(AndroidPackageFormat)`](#androidpackageformat) `aab` 。

## <a name="androidsupportedabis"></a>AndroidSupportedAbis

字串屬性，其中包含分號 (`;`) 分隔的 abi 清單，這些清單應包含在中 `.apk` 。

支援的值包括：

- `armeabi-v7a`
- `x86`
- `arm64-v8a`：需要 Xamarin.Android 5.1 和更新版本。
- `x86_64`：需要 Xamarin.Android 5.1 和更新版本。

## <a name="androidtlsprovider"></a>AndroidTlsProvider

指定應用程式中應使用哪個 TLS 提供者的字串值。 可能的值包括：

- 取消設定/空字串：在 Xamarin. Android 7.3 和更新版本中，這相當於 `btls` 。

  在 Xamarin.Android 7.1 中，這相當於 `legacy`。

  這對應到 Visual Studio屬性頁中的 [預設] 設定。

- `btls`：使用 [Boring SSL](https://boringssl.googlesource.com/boringssl) 以利用 [HttpWebRequest](xref:System.Net.HttpWebRequest) 進行 TLS 通訊。

  這允許在所有的 Android 版本上使用 TLS 1.2。

  這對應到 Visual Studio 屬性頁中的 [原生 TLS 1.2+] 設定。

- `legacy`：在 Xamarin. Android 10.1 及更早版本中，請使用歷程記錄管理的 SSL 實行進行網路互動。 這不支援 TLS 1.2。

  這對應到 Visual Studio 屬性頁中的 [原生 TLS 1.0] 設定。

  在 Xamarin. Android 10.2 和更新版本中，會忽略此值，並 `btls` 使用此設定。

- `default`：此值不太可能用於 Xamarin. Android 專案。 建議改用對應到 Visual Studio 屬性頁中之 [預設] 設定的空字串值。

  Visual Studio 屬性頁不提供 `default` 值。

  目前此值等同於 `legacy`。

在 Xamarin.Android 7.1 中已新增。

## <a name="androiduseaapt2"></a>AndroidUseAapt2

此為布林值屬性，可讓開發人員控制用來 `aapt2` 封裝的工具。
根據預設，這會是 False，且 Xamarin 會使用 `aapt` 。
如果開發人員想要使用新 `aapt2` 功能，請新增：

```xml
<AndroidUseAapt2>True</AndroidUseAapt2>
```

在其中 `.csproj` 。 或者，在命令列上提供屬性：

```shell
/p:AndroidUseAapt2=True
```

這個屬性是在 Xamarin. Android 8.3 中新增的。 將設定 `AndroidUseAapt2` 為 `false` 已在 Xamarin. Android 11.2 中淘汰。

## <a name="androiduseapksigner"></a>AndroidUseApkSigner

布林值屬性，可讓開發人員使用 `apksigner` 工具，而不是 `jarsigner` 。

已在 Xamarin.Android 8.2 中新增。

## <a name="androidusedefaultaotprofile"></a>AndroidUseDefaultAotProfile

Bool 屬性，可讓開發人員隱藏預設 AOT 設定檔的使用方式。

若要隱藏預設的 AOT 設定檔，請將屬性設定為 `false` 。

已在 Xamarin. Android 10.1 中新增。

## <a name="androiduselegacyversioncode"></a>AndroidUseLegacyVersionCode

此為布林值屬性，可讓開發人員將 versionCode 計算還原回舊的預先 Xamarin. Android 8.2 行為。 這應該「僅」針對 Google Play 商店中擁有現有應用程式的開發人員使用。 強烈建議使用新的 [`$(AndroidVersionCodePattern)`](#androidversioncodepattern) 屬性。

已在 Xamarin.Android 8.2 中新增。

## <a name="androidusemanageddesigntimeresourcegenerator"></a>AndroidUseManagedDesignTimeResourceGenerator

此為布林值屬性，會切換設計階段組建以使用受控資源剖析器，而不是 `aapt` 。

已在 Xamarin.Android 8.1 中新增。

## <a name="androidusesharedruntime"></a>AndroidUseSharedRuntime

此為布林值屬性，可決定是否需要 *共用執行時間封裝* ，才能在目標裝置上執行應用程式。 依賴共用執行階段套件可讓應用程式套件小一些、加快套件的建立和部署程序速度，進而提升建置/部署/偵錯往返循環速度。

在 11.2 Xamarin 之前的版本中，這個屬性應該是 `True` 用於 Debug 組建和 `False` 發行專案。

此屬性已在 Xamarin. Android 11.2 中 *移除* 。

## <a name="androidversioncodepattern"></a>AndroidVersionCodePattern

字串屬性，可讓開發人員自訂 `versionCode` 資訊清單中的。
如需有關決定 `versionCode` 的資訊，請參閱[為 APK 建立版本代碼](~/android/deploy-test/building-apps/abi-specific-apks.md)。

ome 範例（如果 `abi` 是 `armeabi` 和 `versionCode` 在資訊清單中）不會產生的 versionCode （若為 s）， `123` `{abi}{versionCode}` `1123` `$(AndroidCreatePackagePerAbi)` 否則會產生123的值。
f `abi` 是 `x86_64` 和 `versionCode` 在資訊清單中 `44` 。 若 `544` `$(AndroidCreatePackagePerAbi)` 為 True，則會產生，否則會產生的值 `44` 。

如果我們納入左側填補格式字串 `{abi}{versionCode:0000}`，它會產生 `50044`，因為我們會在 `versionCode` 左側填補 `0`。 或者，您也可以使用小數點填補 (例如 `{abi}{versionCode:D4}`)，
其作用與上一個範例相同。

值必須是整數，因此僅支援 '0' 和 'Dx' 填補格式字串。

預先定義的索引鍵項目

- **abi**  &ndash; 為應用程式插入設為目標的 abi
  - 2 &ndash;`armeabi-v7a`
  - 3 &ndash;`x86`
  - 4 &ndash;`arm64-v8a`
  - 5 &ndash;`x86_64`

- **minSDK**  &ndash; 若未定義任何項目，則插入所支援的最小 Sdk 值 (`AndroidManifest.xml` 或 `11`)。

- **versionCode** &ndash; 直接使用 `Properties\AndroidManifest.xml` 中的版本代碼。

您可使用 `$(AndroidVersionCodeProperties)` 屬性 (於下一步定義) 定義自訂項目。

根據預設，值會設定為 `{abi}{versionCode:D6}`。 如果開發人員想要保留舊的行為，則您可以將 `$(AndroidUseLegacyVersionCode)` 屬性設定為 `true` 來覆寫預設值

已在 Xamarin.Android 7.2 中新增。

## <a name="androidversioncodeproperties"></a>AndroidVersionCodeProperties

字串屬性，可讓開發人員定義要與搭配使用的自訂專案 [`$(AndroidVersionCodePattern)`](#androidversioncodepattern) 。
其格式為 `key=value` 組。 `value` 中的所有項目都應該是整數值。 例如：`screen=23;target=$(_AndroidApiLevel)`。 如您所見，您可以在字串中利用現有或自訂的 MSBuild 屬性。

已在 Xamarin.Android 7.2 中新增。

## <a name="aotassemblies"></a>AotAssemblies

此為布林值屬性，可決定元件是否會預先編譯成機器碼，並包含在中 `.apk` 。

在 Xamarin.Android 5.1 中已新增這個屬性的支援。

這個屬性的預設值是 `False`。

## <a name="aprofutilextraoptions"></a>AProfUtilExtraOptions

要傳遞的額外選項 `aprofutil` 。

## <a name="beforegenerateandroidmanifest"></a>BeforeGenerateAndroidManifest

這個屬性中所列的 MSBuild 目標將直接執行 `_GenerateJavaStubs` 。

Xamarin.Android 9.4 已新增此支援。

## <a name="configuration"></a>組態

指定要使用的組建設定，例如 "Debug" 或 "Release"。 組態屬性可用來決定其他會決定目標行為之屬性的預設值。 您可以在 IDE 內建立其他組態。

*根據預設*，設定 `Debug` 會導致 [`Install`](~/android/deploy-test/building-apps/build-targets.md#install)
和 [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#signandroidpackage)
目標是建立較小的 Android 套件，需要有其他檔案和套件才能運作。

預設設定 `Release` 會導致 [`Install`](~/android/deploy-test/building-apps/build-targets.md#install)
和 [`SignAndroidPackage`](~/android/deploy-test/building-apps/build-targets.md#signandroidpackage)
目標是建立 *獨立* 的 Android 套件，而且可以在不安裝任何其他套件或檔案的情況下使用。

## <a name="debugsymbols"></a>DebugSymbols

布林 *值，判斷 Android 封裝是否與* 屬性搭配使用 [`$(DebugType)`](#debugtype) 。
可調試封裝包含 debug 符號、將[ `//application/@android:debuggable` 屬性](https://developer.android.com/guide/topics/manifest/application-element#debug)設為 `true` ，以及自動將[`INTERNET`](https://developer.android.com/reference/android/Manifest.permission#INTERNET)
許可權，讓偵錯工具可以附加至進程。 如果 `DebugSymbols` 是  且 `True`  `DebugType` 為空字串或 `Full`，則應用程式是可偵錯的。

## <a name="debugtype"></a>DebugType

指定要產生為組建一部分的 [debug 符號類型](/visualstudio/msbuild/csc-task) ，這也會影響應用程式是否可調試。 可能的值包括：

- **完整**：產生完整符號。 如果 [`DebugSymbols`](#debugsymbols)
  MSBuild 屬性也是 `True` ，應用程式封裝是可調試的。

- **PdbOnly**：產生 "PDB" 符號。 應用程式封裝無法進行調試。

如果 `DebugType` 未設定或者是空字串，則 `DebugSymbols` 屬性會控制應用程式是否可偵錯。

## <a name="embedassembliesintoapk"></a>EmbedAssembliesIntoApk

此為布林值屬性，可決定應用程式的元件是否應內嵌到應用程式封裝中。

如果是發行組建，這個屬性應該是 `True`，如果是偵錯組建，則應該是 `False`。 如果快速部署不支援目標裝置，則在偵錯組建中，這個屬性可能需為 `True`。

當這個屬性為時 `False` ， [`$(AndroidFastDeploymentType)`](#androidfastdeploymenttype)
MSBuild 屬性也會控制要內嵌至的內容 `.apk` ，這可能會影響部署和重建時間。

## <a name="enablellvm"></a>EnableLLVM

此為布林值屬性，可決定當預先將元件編譯成機器碼時，是否會使用 LLVM。

必須安裝 Android NDK，才能建立已啟用這個屬性的專案。

在 Xamarin.Android 5.1 中已新增這個屬性的支援。

這個屬性的預設值是 `False`。

除非 MSBuild 屬性為，否則會忽略這個屬性 [`$(AotAssemblies)`](#aotassemblies) `True` 。

## <a name="enableproguard"></a>EnableProguard

此為布林值屬性，可決定是否在封裝程式中執行 [proguard](https://developer.android.com/tools/help/proguard.html) 以連結 JAVA 程式碼。

在 Xamarin.Android 5.1 中已新增這個屬性的支援。

這個屬性的預設值是 `False`。

當 `True` 為時， [@ (ProguardConfiguration) ](~/android/deploy-test/building-apps/build-items.md#proguardconfiguration) 檔將用來控制 `proguard` 執行。

## <a name="javamaximumheapsize"></a>JAVAMaximumHeapSize

指定在 
 `-Xmx` `.dex` 封裝進程中建立檔案時，要使用的 java 參數值值。 如果未指定，則 `-Xmx` 選項會為 **java** 提供值 `1G`。 相較於其他平台，這在 Windows 上通常是必要項目。

如果[ `_CompileDex` 目標 `java.lang.OutOfMemoryError` ](https://bugzilla.xamarin.com/show_bug.cgi?id=18327)擲回，則必須指定這個屬性。

藉由變更下列項目來自訂值：

```xml
<JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
```

## <a name="javaoptions"></a>JAVAOptions

指定在建立檔案時，要傳遞至 **java** 的其他命令列選項 `.dex` 。

## <a name="jarsignertimestampauthoritycertificatealias"></a>JarsignerTimestampAuthorityCertificateAlias

這個屬性可讓您針對時間戳記授權單位指定金鑰儲存區中的別名。
如需詳細資訊，請參閱 JAVA [Signature Timestamp 支援](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) 檔。

```xml
<PropertyGroup>
    <JarsignerTimestampAuthorityCertificateAlias>Alias</JarsignerTimestampAuthorityCertificateAlias>
</PropertyGroup>
```

## <a name="jarsignertimestampauthorityurl"></a>JarsignerTimestampAuthorityUrl

這個屬性可讓您指定時間戳記授權單位服務的 URL。 這可以用來確保您的簽章 `.apk` 包含時間戳記。
如需詳細資訊，請參閱 JAVA [Signature Timestamp 支援](https://docs.oracle.com/javase/8/docs/technotes/guides/security/time-of-signing.html) 檔。

```xml
<PropertyGroup>
    <JarsignerTimestampAuthorityUrl>http://example.tsa.url</JarsignerTimestampAuthorityUrl>
</PropertyGroup>
```

## <a name="linkerdumpdependencies"></a>LinkerDumpDependencies

布林值屬性，可產生連結器相依性檔案。 此檔案可作為 [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md) 工具的輸入使用。

名為的相依性檔案 `linker-dependencies.xml.gz` 會寫入至專案目錄。 個.NET5/6 會在目錄中的連結元件旁邊寫入 `obj/<Configuration>/android<ABI>/linked` 。

預設值是 False。

## <a name="mandroidi18n"></a>MandroidI18n

指定應用程式隨附的國際化支援，例如定序和排序表。 此值是下列一或多個不區分大小寫之值的逗號或分號分隔清單：

- **無**：未包含其他編碼。

- **全部**：包含所有可用的編碼。

- **CJK**：包含中文、日文和韓文編碼，例如日文 (EUC) \[enc-jp, CP51932\]、日文 (Shift-JIS) \[iso-2022-jp, shift\_jis, CP932\]、日文 (JIS) \[CP50220\]、簡體中文 (GB2312) \[gb2312, CP936\]、韓文 (UHC) \[ks\_c\_5601-1987, CP949\]、韓文 (EUC) \[euc-kr, CP51949\]、繁體中文(Big5) \[big5, CP950\] 和簡體中文 (GB18030) \[GB18030, CP54936\]。

- **中東**：包含中東編碼，例如土耳其文 (Windows) \[iso-8859-9, CP1254\]、希伯來文 (Windows) \[windows-1255, CP1255\]、阿拉伯文 (Windows) \[windows-1256, CP1256\]、阿拉伯文 (ISO) \[iso-8859-6, CP28596\]、希伯來文 (ISO) \[iso-8859-8, CP28598\]、拉丁文 5 (ISO) \[iso-8859-9, CP28599\] 和希伯來文 (Iso Alternative) \[iso-8859-8, CP38598\]。

- **其他**：包含其他編碼，例如斯拉夫文 (Windows) \[CP1251\]、波羅的海文 (Windows) \[iso-8859-4, CP1257\]、越南文 (Windows) \[CP1258\]、斯拉夫文 (KOI8-R) \[koi8-r, CP1251\]、烏克蘭文 (KOI8-U) \[koi8-u, CP1251\]、波羅的海文 (ISO) \[iso-8859-4, CP1257\]、斯拉夫文 (ISO) \[iso-8859-5, CP1251\]、ISCII 梵文 \[x-iscii-de, CP57002\]、ISCII 孟加拉文 \[x-iscii-be, CP57003\]、ISCII 坦米爾文 \[x-iscii-ta, CP57004\]、ISCII 特拉古文 \[x-iscii-te, CP57005\]、ISCII 阿薩姆文 \[x-iscii-as, CP57006\]、ISCII 歐利亞文 \[x-iscii-or, CP57007\]、ISCII 坎那達文 \[x-iscii-ka, CP57008\]、ISCII 馬來亞拉姆文 \[x-iscii-ma, CP57009\]、ISCII 古吉拉特文 \[x-iscii-gu, CP57010\]、ISCII 旁遮普文 \[x-iscii-pa, CP57011\] 和泰文 (Windows) \[CP874\]。

- **罕見**：包含罕見的編碼，例如 IBM EBCDIC (土耳其文) \[CP1026\]、IBM EBCDIC (開啟系統拉丁文 1) \[CP1047\]、IBM EBCDIC (美國-加拿大與歐元區) \[CP1140\]、IBM EBCDIC (德國與歐元區) \[CP1141\]、IBM EBCDIC (丹麥/挪威與歐元區) \[CP1142\]、IBM EBCDIC (芬蘭/瑞典與歐元區) \[CP1143\]、IBM EBCDIC (義大利與歐元區) \[CP1144\]、IBM EBCDIC (拉丁美洲/西班牙與歐元區) \[CP1145\]、IBM EBCDIC (英國與歐元區) \[CP1146\]、IBM EBCDIC (法國與歐元區) \[CP1147\]、IBM EBCDIC (國際與歐元區) \[CP1148\]、IBM EBCDIC (冰島與歐元區) \[CP1149\]、IBM EBCDIC (德國) \[CP20273\]、IBM EBCDIC (丹麥/挪威) \[CP20277\]、IBM EBCDIC (芬蘭/瑞典) \[CP20278\]、IBM EBCDIC (義大利) \[CP20280\]、IBM EBCDIC (拉丁美洲/西班牙) \[CP20284\]、IBM EBCDIC (英國) \[CP20285\]、IBM EBCDIC (日文片假名延伸) \[CP20290\]、IBM EBCDIC (法國) \[CP20297\]、IBM EBCDIC (阿拉伯文) \[CP20420\]、IBM EBCDIC (希伯來文) \[CP20424\]、IBM EBCDIC (冰島文) \[CP20871\]、IBM EBCDIC (斯拉夫文-塞爾維亞文、保加利亞文) \[CP21025\]、IBM EBCDIC (美國-加拿大) \[CP37\]、IBM EBCDIC (國際) \[CP500\]、阿拉伯文 (ASMO 708) \[CP708\]、中歐語系 (DOS) \[CP852\]、斯拉夫文 (DOS) \[CP855\]、土耳其文 (DOS) \[CP857\]、西歐語系 (DOS 與歐元區) \[CP858\]、希伯來文 (DOS) \[CP862\]、阿拉伯文 (DOS) \[CP864\]、俄文 (DOS) \[CP866\]、Greek (DOS) \[CP869\]、IBM EBCDIC (拉丁文 2) \[CP870\] 和 IBM EBCDIC (希臘文) \[CP875\]。

- **西方**：包含西方編碼，例如西歐語系 (Mac) \[macintosh, CP10000\]、冰島文 (Mac) \[x-mac-icelandic, CP10079\]、中歐語系 (Windows) \[iso-8859-2, CP1250\]、西歐語系 (Windows) \[iso-8859-1, CP1252\]、希臘文 (Windows) \[iso-8859-7, CP1253\]、中歐語系 (ISO) \[iso-8859-2, CP28592\]、拉丁文 3 (ISO) \[iso-8859-3, CP28593\]、希臘文 (ISO) \[iso-8859-7, CP28597\]、拉丁文 9 (ISO) \[iso-8859-15, CP28605\]、OEM 美國 \[CP437\]、西歐語系 (DOS) \[CP850\]、葡萄牙文 (DOS) \[CP860\]、冰島文 (DOS) \[CP861\]、加拿大法文 (DOS) \[CP863\] 和北歐語言 (DOS) \[CP865\]。

```xml
<MandroidI18n>West</MandroidI18n>
```

## <a name="monoandroidresourceprefix"></a>MonoAndroidResourcePrefix

指定從檔案名開頭移除的 *路徑前置* 詞，其組建動作為 `AndroidResource` 。 這是為了允許變更資源所在位置。

預設值是 `Resources`。 若為 Java 專案結構，請將此值變更為 `res`。

## <a name="monosymbolarchive"></a>MonoSymbolArchive

此為布林值屬性，可控制是否要建立成品以 `.mSYM` 供稍後使用 `mono-symbolicate` ，以 &ldquo; &rdquo; 從發行堆疊追蹤中解壓縮實際的檔案名和行號資訊。

針對 &ldquo; &rdquo; 已啟用偵錯工具符號的發行應用程式 [`$(EmbedAssembliesIntoApk)`](#embedassembliesintoapk) ，預設為 true。 [`$(DebugSymbols)`](~/android/deploy-test/building-apps/build-properties.md#debugsymbols)
為 True，且為 [`$(Optimize)`](/visualstudio/msbuild/common-msbuild-project-properties)
為 True。

在 Xamarin.Android 7.1 中已新增。
