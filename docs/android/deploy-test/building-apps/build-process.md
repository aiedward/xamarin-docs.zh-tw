---
title: 建置流程
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/22/2019
ms.openlocfilehash: 45d57f818fc6d90cb712b9f43ef815d44059ea68
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021377"
---
# <a name="build-process"></a>建置流程

## <a name="overview"></a>總覽

Xamarin.Android 建置程序負責將所有作業結合在一起：[產生 `Resource.designer.cs`](~/android/internals/api-design.md)、支援 `AndroidAsset`、`AndroidResource` 和其他[建置動作](#Build_Actions)、產生 [Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)，以及產生 `.apk` 以便在 Android 裝置上執行。

## <a name="application-packages"></a>應用程式套件

廣泛來說，Xamarin.Android 建置系統可以產生的 Android 應用程式套件 (`.apk` 檔案) 類型有兩種：

- **發行**組建，完全獨立的組建，不需要額外的套件就能執行。 這些組建是會提供至 App Store 的套件。

- **偵錯**組建則非如此。

這些組建理所當然地符合會產生套件的 MSBuild `Configuration`。

### <a name="shared-runtime"></a>共用執行階段

共用執行階段是成對的其他 Android 套件，這些套件可提供基底類別庫 (`mscorlib.dll` 等) 和 Android 繫結程式庫 (`Mono.Android.dll` 等)。 偵錯組建依賴共用執行階段來取代在 Android 應用程式套件內納入基底類別庫和繫結組件，讓偵錯套件能夠小一些。

將 `$(AndroidUseSharedRuntime)` 屬性設定為 `False` 即可在偵錯組建中停用共用執行階段。

<a name="Fast_Deployment" />

### <a name="fast-deployment"></a>快速部署

快速部署會與共用執行階段搭配運作，以進一步縮減 Android 應用程式套件的大小。 其作法並非是將應用程式的組件全部塞入套件內。 而是透過 `adb push` 將組件複製到目標。 此程序可加快建置/部署/偵錯循環的速度，原因是如果變更的項目「只有」組件，就不會重新安裝套件。 而是只有更新的組件會重新同步至目標裝置。

我們已經知道如果裝置會封鎖 `adb`，不讓其同步至 `/data/data/@PACKAGE_NAME@/files/.__override__` 目錄的話，就無法在裝置上進行快速部署。

快速部署預設為啟用狀態，藉由將 `$(EmbedAssembliesIntoApk)` 屬性設定為 `True` 即可在偵錯組建中加以停用。

## <a name="msbuild-projects"></a>MSBuild 專案

Xamarin.Android 建置程序是以 MSBuild 作為基礎，而這也是 Visual Studio for Mac 和 Visual Studio 所使用的專案檔格式。
一般來說，使用者不需要以手動方式編輯 MSBuild 檔案 &ndash; IDE 會負責建立功能完整的專案並使用任何所做的變更來為這些專案進行更新，然後視需要自動叫用建置目標。

進階使用者或許會想執行 IDE 之 GUI 所不支援的作業，因此建置程序是可藉由直接編輯專案檔來加以自訂。
本頁面只會記載 Xamarin.Android 特有的功能和自訂項目 &ndash; 使用一般的 MSBuild 項目、屬性和目標所能進行的工作不只這些。

<a name="Build_Targets" />

## <a name="build-targets"></a>建置目標

下列是針對 Xamarin.Android 專案所定義的建置目標：

- **建置** &ndash; 建置套件。

- **清除** &ndash; 移除建置程序所產生的所有檔案。

- **安裝** &ndash; 將套件安裝到預設裝置或虛擬裝置上。

- **解除安裝** &ndash; 在預設裝置或虛擬裝置上將套件解除安裝。

- **SignAndroidPackage** &ndash; 建立和簽署套件 (`.apk`)。 與 `/p:Configuration=Release` 搭配使用來產生獨立的「發行」套件。

- **UpdateAndroidResources** &ndash; 更新 `Resource.designer.cs` 檔案。 當專案新增資源時，IDE 通常會呼叫此目標。

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

以下是擴充建置流程的注意事項：若編寫不正確，建置延伸模組可能會影響建置的效能，特別是對每個組建執行時。 極力建議您先閱讀 MSBuild [文件](https://docs.microsoft.com/visualstudio/msbuild/msbuild)，然後再執行這類延伸模組。

- 此屬性中所列的 **AfterGenerateAndroidManifest** &ndash; 目標會緊接在內部 `_GenerateJavaStubs` 目標之後執行。 這是 `AndroidManifest.xml` 檔案在 `$(IntermediateOutputPath)` 之中的產生位置。 因此，若您想要修改所產生的 `AndroidManifest.xml` 檔案，可使用此擴充點來執行此作業。

  Xamarin.Android 9.4 已新增此支援。

- 此屬性中所列的 **BeforeGenerateAndroidManifest** &ndash; 目標會緊接在 `_GenerateJavaStubs` 之前執行。

  Xamarin.Android 9.4 已新增此支援。

## <a name="build-properties"></a>組建屬性

MSBuild 屬性可控制目標的行為。 您可以在專案檔 (例如 **MyApp.csproj**) 的 [MSBuild PropertyGroup 元素](https://docs.microsoft.com/visualstudio/msbuild/propertygroup-element-msbuild)內指定這些屬性。

- **組態** &ndash; 指定要使用的組建組態，例如「偵錯」或「發行」。 組態屬性可用來決定其他會決定目標行為之屬性的預設值。 您可以在 IDE 內建立其他組態。

  根據預設，`Debug` 組態會導致 `Install` 和 `SignAndroidPackage` 目標建立容量較小、需要有其他檔案和套件才能運作的 Android 套件。

  預設的 `Release` 設定會導致 `Install` 和 `SignAndroidPackage` 目標建立獨立式 Android 套件，而且不必安裝任何其他套件或檔案即可使用。

- **DebugSymbols** &ndash; 搭配 `$(DebugType)` 屬性來決定 Android 套件是否可偵錯的布林值。 可偵錯的套件會包含偵錯符號、將 `//application/@android:debuggable` 屬性設定為 `true`，並且會自動新增 `INTERNET` 權限讓偵錯工具可以附加至程序。 如果 `DebugSymbols` 是 `True` 且`DebugType` 為空字串或 `Full`，則應用程式是可偵錯的。

- **DebugType** &ndash; 指定要在建置過程中產生的[偵錯符號類型](https://docs.microsoft.com/visualstudio/msbuild/csc-task)，此值也會影響應用程式是否可偵錯。 可能的值包括：

  - **Full**：產生完整符號。 如果 `DebugSymbols` MSBuild 屬性也是 `True`，則應用程式套件是可偵錯的。

  - **PdbOnly**：產生 "PDB" 符號。 應用程式套件無法偵錯。

  如果 `DebugType` 未設定或者是空字串，則 `DebugSymbols` 屬性會控制應用程式是否可偵錯。

  - **AndroidGenerateLayoutBindings** &ndash; 若設定為 `true`即可產生[配置程式碼後置](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md)；若設定為 `false`，則會完全予以停用。 預設值為 `false`。

### <a name="install-properties"></a>安裝屬性

安裝屬性可控制 `Install` 和 `Uninstall` 目標的行為。

- **AdbTarget** &ndash; 指定可供安裝或移除 Android 套件的 Android 目標裝置。 這個屬性的值和 [`adb` 目標裝置選項](https://developer.android.com/tools/help/adb.html#issuingcommands)相同：

  ```bash
  # Install package onto emulator via -e
  # Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
  MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
  ```

### <a name="packaging-properties"></a>封裝屬性

封裝屬性可控制 Android 套件的建立，並可供 `Install` 和 `SignAndroidPackage` 目標使用。
在封裝發行應用程式時，也會與[簽署屬性](#Signing_Properties)關。

- **AndroidApkDigestAlgorithm** &ndash; 此字串值指定要與 `jarsigner -digestalg` 一起使用的摘要演算法。

  APK 的預設值為 `SHA1`，應用程式套件組合的預設值為 `SHA-256`。

  Xamarin.Android 9.4 已新增此支援。

- **AndroidApkSignerAdditionalArguments** &ndash; 字串屬性，可允許開發人員將額外引數提供給 `apksigner` 工具。

  已在 Xamarin.Android 8.2 中新增。

- **AndroidApkSigningAlgorithm** &ndash;字串值，指定要搭配 `jarsigner -sigalg` 使用的簽署演算法。

  APK 的預設值為 `md5withRSA`，應用程式套件組合的預設值為 `SHA256withRSA`。

  已在 Xamarin.Android 8.2 中新增。

- **AndroidApplication** &ndash; 指出專案適用於 Android 應用程式 (`True`) 還是 Android 程式庫專案 (`False` 或不存在) 的布林值。

  Android 套件內只能有一個具有 `<AndroidApplication>True</AndroidApplication>` 的專案。 (遺憾的是，此限制尚未經過驗證，因而可能造成 Android 資源發生些微異常錯誤。)

- **AndroidApplicationJavaClass** &ndash; 當類別是繼承自 [Android.App.Application](xref:Android.App.Application) 時，要用來取代 `android.app.Application` 的完整 Java 類別名稱。

  這個屬性通常由其他屬性來設定，例如 `$(AndroidEnableMultiDex)` MSBuild 屬性。

  已在 Xamarin.Android 6.1 中新增。

- **AndroidBuildApplicationPackage** &ndash; 指出是否要建立和簽署套件 (.apk) 的布林值。 將此值設定為 `True` 就等於使用 [SignAndroidPackage](#Build_Targets) 建置目標。

  在 Xamarin.Android 7.1 之後已新增這個屬性的支援。

  這個屬性的預設值是 `False`。

- **AndroidDexTool** &ndash; 有效值為 `dx` 或 `d8` 的列舉樣式屬性。 指出在 Xamarin.Android 建置程序期間使用哪一個 Android [dex][dex] 編譯器。
  目前預設為 `dx`。 如需進一步的詳細資訊，請參閱有關 [D8 和 R8][d8-r8] 的文件。

  [dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidEnableDesugar** &ndash; 用來決定是否啟用 `desugar` 的布林屬性值。 Android 目前不支援所有 Java 8 功能，預設工具鏈會藉由在 `javac` 編譯器的輸出上執行位元組程式碼轉換 (稱為 `desugar`)，以實作新的語言功能。 若使用 `AndroidDexTool=dx`，預設即為 `False`；若使用 `AndroidDexTool=d8`，預設則為 `True`。

- **AndroidEnableGooglePlayStoreChecks** &ndash; 此集區屬性允許開發人員停用下列 Google Play 商店檢查：XA1004、XA1005 及 XA1006。 這適用於不是以 Google Play 商店為目標，所以不想要執行這些檢查的開發人員。

  Xamarin.Android 9.4 已新增此支援。

- **AndroidEnableMultiDex** &ndash; 決定是否會在最終的 `.apk` 內使用 Multi-Dex 支援的布林值屬性。

  在 Xamarin.Android 5.1 中已新增這個屬性的支援。

  這個屬性的預設值是 `False`。

- **AndroidEnablePreloadAssemblies** &ndash; 布林值屬性，可控制是否要在流程啟動期間載入應用程式套件內隨附的所有受控組件。

  設定為 `True` 時，就會在流程啟動期間先載入應用程式套件內隨附的所有組件，然後再叫用任何應用程式程式碼。
  這與 Xamarin.Android 9.2 之前版本的做法一致。

  設定為 `False` 時，組件將只會在需要時載入。
  這可加快應用程式啟動速度，同時也能夠與傳統型 .NET 語意更加一致。  若要查看省下的時間，請設定 `debug.mono.log` 系統屬性以包括 `timing`，並在 `adb logcat` 內尋找 `Finished loading assemblies: preloaded` 訊息。

  如果使用相依性插入的應用程式或程式庫要求 `AppDomain.CurrentDomain.GetAssemblies()` 傳回應用程式組合內的所有組件 (即使還不需要那些組件)，它們可能就會*要求*此屬性為 `True`。

  此值預設為 `True`。

  已在 Xamarin.Android 9.2 中新增。

- **AndroidEnableProfiledAot** &ndash; 此布林值屬性決定預先編譯時，是否要使用 AOT 設定檔。

  這些設定檔會列在 `AndroidAotProfile` 項目群組中。 此 ItemGroup 包含預設設定檔。 只要移除現有的設定檔，然後新增您的 AOT 設定檔，就能加以覆寫。

  Xamarin.Android 9.4 已新增此屬性支援。

  這個屬性的預設值是 `False`。

- **AndroidEnableSGenConcurrent** &ndash; 決定是否會使用 Mono [並行 GC 收集器](https://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen)的布林值屬性。

  在 Xamarin.Android 7.2 中已新增這個屬性的支援。

  這個屬性的預設值是 `False`。

- **AndroidErrorOnCustomJavaObject** &ndash;布林屬性，決定型別是否可能實作 `Android.Runtime.IJavaObject`
   而不繼承自 `Java.Lang.Object` 或 `Java.Lang.Throwable`：

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

  若為 True，這種型別會產生 XA4212 錯誤，否則會產生 XA4212 警告。

  在 Xamarin.Android 8.1 中已新增這個屬性的支援。

  這個屬性的預設值是 `True`。

- **AndroidFastDeploymentType** &ndash; 當 `$(EmbedAssembliesIntoApk)` MSBuild 屬性是 `False` 時，用來控制哪些類型可以部署到目標裝置上[快速部署目錄](#Fast_Deployment)的 `:` (冒號) 分隔值清單。 資源如果使用快速部署，則不會內嵌到所產生的 `.apk`，從而可加快部署時間 (使用快速部署的資源越多，必須重建 `.apk` 的頻率就會越低，並讓安裝程序變快)。有效值包括：

  - `Assemblies`：部署應用程式組件。

  - `Dexes`：部署 `.dex` 檔案、Android 資源和 Android 資產。 **執行 Android 4.4 或更新版本 (API-19) 的裝置上才能使用這個值。**

  預設值為 `Assemblies`。

  **實驗**。 已在 Xamarin.Android 6.1 中新增。

- **AndroidGenerateJniMarshalMethods** &ndash; 可在建置流程期間產生 JNI 封送處理方法的布林屬性。 這可大幅減少 System.Reflection 在繫結協助程式碼中的使用量。

  根據預設，這會設定為 False。 如果開發人員想要使用新的 JNI 封送處理方法功能，他們可以在其 csproj 中設定下列程式碼：

  ```xml
  <AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
  ```

  。 或者，透過下列參數在命令列上提供屬性：

  ```
  /p:AndroidGenerateJniMarshalMethods=True
  ```

  **實驗**。 已在 Xamarin.Android 9.2 中新增。
  預設值為 False。

- **AndroidGenerateJniMarshalMethodsAdditionalArguments** &ndash; 可用來將額外的參數新增至 `jnimarshalmethod-gen.exe` 引動過程的字串屬性。  這適用於偵錯，因此可以使用 `-v`、`-d` 或 `--keeptemp` 等選項。

  預設值為空字串。 它可以在 csproj 檔案或在命令列中設定。 例如：

  ```xml
  <AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
  ```

  或：

  ```
  /p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"
  ```

  已在 Xamarin.Android 9.2 中新增。

- **AndroidHttpClientHandlerType** &ndash;控制預設 `System.Net.Http.HttpMessageHandler` 實作，該實作將由 `System.Net.Http.HttpClient` 預設建構函式使用。 該值為 `HttpMessageHandler` 子類別的組件限定類型名稱，適合搭配 [`System.Type.GetType(string)`](https://docs.microsoft.com/dotnet/api/system.type.gettype?view=netcore-2.0#System_Type_GetType_System_String_) 使用。
  此屬性最常見的值：

  - `Xamarin.Android.Net.AndroidClientHandler`：使用 Android JAVA API 執行網路要求。 這允許在基礎 Android 版本支援 TLS 1.2 時存取 TLS 1.2 URL。 只有 Android 5.0 及更新版本能夠可靠地透過 Java 提供 TLS 1.2 支援。

    這對應到 Visual Studio 屬性頁中的 [Android] 選項及 Visual Studio for Mac 屬性頁中的 [AndroidClientHandler] 選項。

    當 [Android 最低版本] 的設定為 [Android 5.0 (Lollipop)]，或當 Visual Studio for Mac 中之 [目標平台] 的設定為 [最新及最高] 時，[新增專案] 精靈會為新專案選取此選項。

  - 取消設定/空字串：此值等同於 `System.Net.Http.HttpClientHandler, System.Net.Http`

    這對應到 Visual Studio屬性頁中的 [預設] 選項。

    當 Visual Studio 中之 [Android 最低版本] 的設定為 [Android 4.4.87] 或更低，或當 Visual Studio for Mac 中之 [目標平台] 的設定為[新式開發] 或 [相容性上限] 時，[新增專案] 精靈會為新專案選取此選項。

  - `System.Net.Http.HttpClientHandler, System.Net.Http`：使用受控 `HttpMessageHandler`。

    這對應到 Visual Studio屬性頁中的 [受控] 選項。

  > [!NOTE]
  > 若舊於 5.0 版的 Android 需要 TLS 1.2 支援，或者`System.Net.WebClient``$(AndroidTlsProvider)`  和相關 API 需要 TLS 1.2 支援，則應使用 。

  > [!NOTE]
  > 透過設定 ](~/android/deploy-test/environment.md)`XA_HTTP_CLIENT_HANDLER_TYPE` 環境變數以支援此屬性。
  > 在使用 `@(AndroidEnvironment)` 建置動作的檔案中找到的 `$XA_HTTP_CLIENT_HANDLER_TYPE` 值會有更高的優先順序。

  已在 Xamarin.Android 6.1 中新增。

- **AndroidLinkMode** &ndash; 指定該對 Android 套件內所包含的組件執行哪一種[連結](~/android/deploy-test/linker.md)。 僅限用於 Android 應用程式專案內。 預設值是 SdkOnly。 有效值為：

  - **None**：不會嘗試任何連結。

  - **SdkOnly**：只會對基底類別庫執行連結，而不會對使用者的組件執行。

  - **Full**：連結的執行對象包含基底類別庫和使用者組件。

    > [!NOTE]
    > 若使用的 `AndroidLinkMode` 值為 *Full*，通常會導致應用程式損壞，特別是在使用了「反映」時。 除非您確實了解您正在執行的動作，否則請避免使用此值。

  ```xml
  <AndroidLinkMode>SdkOnly</AndroidLinkMode>
  ```

- **AndroidLinkSkip** &ndash; 指定不該連結之組件的組件名稱清單 (不含副檔名，並以分號分隔 (`;`))。 僅限用於 Android 應用程式專案內。

  ```xml
  <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
  ```

- **AndroidLinkTool** &ndash; 有效值為 `proguard` 或 `r8` 的列舉樣式屬性。 指出哪一個程式碼壓縮工具用於 Java 程式碼。 目前預設為空字串，如果 `$(AndroidEnableProguard)` 為 `True`，則預設為 `proguard`。 如需進一步的詳細資訊，請參閱有關 [D8 和 R8][d8-r8] 的文件。

  [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

- **AndroidLintEnabled** &ndash; 布林屬性，可允許開發人員在封裝流程期間執行 Android `lint` 工具。

  - **AndroidLintEnabledIssues** &ndash; 要啟用的 Lint 問題清單 (以逗號分隔)。

  - **AndroidLintDisabledIssues** &ndash; 要啟用的 Lint 問題清單 (以逗號分隔)。

  - **AndroidLintCheckIssues** &ndash; 要檢查的 Lint 問題清單 (以逗號分隔)。
    注意：只會檢查這些問題。

  - **AndroidLintConfig** &ndash; 這是 Lint 樣式組態檔的建置動作。 這可用來啟用/停用要檢查的問題。 多個檔案可以使用此建置動作，因為其內容將會合併。

  如需 Android `lint` 工具的詳細資訊，請參閱 [Lint 說明](https://developer.android.com/studio/write/lint)。

- **AndroidManagedSymbols** &ndash; 布林值屬性，可控制是否產生序列點以便可從 `Release` 堆疊追蹤擷取檔案名稱和行號資訊。

  已在 Xamarin.Android 6.1 中新增。

- **AndroidManifest** &ndash; 指定要作為應用程式之 [`AndroidManifest.xml`](~/android/platform/android-manifest.md) 範本的檔案名稱。
  建置期間會合併任何其他必要值以產生實際的 `AndroidManifest.xml`。
  `$(AndroidManifest)` 必須在 `/manifest/@package` 屬性中包含套件名稱。

- **AndroidMultiDexClassListExtraArgs** &ndash; 可讓開發人員在產生 `multidex.keep` 檔案時，將額外的引數傳遞給 `com.android.multidex.MainDexListBuilder` 的字串屬性。

  其中一個特定情況是您在 `dx` 編譯期間收到下列錯誤時。

  ```
  com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded
  ```

  如果您收到這個錯誤，則可以將下列程式碼新增至 .csproj。

  ```xml
  <DxExtraArguments>--force-jumbo </DxExtraArguments>
  <AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
  ```

  此舉應該可讓 `dx` 步驟成功完成。

  已在 Xamarin.Android 8.3 中新增。

- **AndroidPackageFormat** &ndash; 此 enum-style 屬性具有有效值 `apk` 或 `aab`。 這會指出您要將 Android 應用程式封裝為 [ APK 檔案][apk]或[ Android 應用程式套件組合][bundle]。 應用程式套件組合是 `Release` 的新格式，專供 Google Play 中的提交之用。 此值目前的預設值為 `apk`。

  當 `$(AndroidPackageFormat)` 的設定為 `aab` 時，將會一併設定 Android 應用程式套件組合所需的其他 MSBuild 屬性：

  - `$(AndroidUseAapt2)` 為 `True`。
  - `$(AndroidUseApkSigner)` 為 `False`。
  - `$(AndroidCreatePackagePerAbi)` 為 `False`。

  [apk]: https://en.wikipedia.org/wiki/Android_application_package
  [bundle]: https://developer.android.com/platform/technology/app-bundle

- **AndroidR8JarPath** &ndash; `r8.jar` 的路徑，用於 R8 Dex 編譯器和壓縮工具。 預設為 Xamarin.Android 安裝中的路徑。 如需進一步的詳細資訊，請參閱有關 [D8 和 R8][d8-r8] 的文件。

- **AndroidSdkBuildToolsVersion** &ndash; Android SDK 建置工具套件提供 **aapt** 和 **zipalign** 等工具。 您可以同時安裝建置工具套件的多個不同版本。 如果「慣用的」建置工具版本存在，可藉由檢查及使用該版本來完成選擇要封裝的建置工具套件，如果「慣用的」版本不存在，則會使用已安裝的建置工具套件中版本最高者。

  `$(AndroidSdkBuildToolsVersion)` MSBuild 屬性包含慣用的建置工具版本。 Xamarin.Android 建置系統會在 `Xamarin.Android.Common.targets` 中提供預設值，而如果 (舉例來說) 最新的 aapt 損毀但您知道有舊版 aapt 能夠正常運作，則可以在專案檔內複寫此預設值，以選擇其他建置工具版本。

- **AndroidSupportedAbis** &ndash; 字串屬性，內含應納入 `.apk` 之 ABI 的分號 (`;`) 分隔清單。

  支援的值包括：

  - `armeabi-v7a`
  - `x86`
  - `arm64-v8a`：需要 Xamarin.Android 5.1 和更新版本。
  - `x86_64`：需要 Xamarin.Android 5.1 和更新版本。

- **AndroidTlsProvider** &ndash; 指定應用程式中應該使用哪一個 TLS 提供者的字串值。 可能的值為：

  - 取消設定/空字串：在 Xamarin. Android 7.3 及更新版本中，此值等同於 `btls`。

    在 Xamarin.Android 7.1 中，這相當於 `legacy`。

    這對應到 Visual Studio屬性頁中的 [預設] 設定。

  - `btls`：使用 [Boring SSL](https://boringssl.googlesource.com/boringssl) 以利用 [HttpWebRequest](xref:System.Net.HttpWebRequest) 進行 TLS 通訊。

    這允許在所有的 Android 版本上使用 TLS 1.2。

    這對應到 Visual Studio 屬性頁中的 [原生 TLS 1.2+] 設定。

  - `legacy`：在網路互動中使用過往的受控 SSL 實作。 這不支援 TLS 1.2。

    這對應到 Visual Studio 屬性頁中的 [原生 TLS 1.0] 設定。

  - `default`：此值無法用於 Xamarin. Android 專案。 建議改用對應到 Visual Studio 屬性頁中之 [預設] 設定的空字串值。

    Visual Studio 屬性頁不提供 `default` 值。

    目前此值等同於 `legacy`。

  在 Xamarin.Android 7.1 中已新增。

- **AndroidUseApkSigner** &ndash; 布林值屬性，可讓開發人員使用 `apksigner` 工具而非 `jarsigner`。

    已在 Xamarin.Android 8.2 中新增。

- **AndroidUseLegacyVersionCode** &ndash; 布林值屬性允許開發人員將 versionCode 計算還原回 Xamarin.Android 8.2 之前的舊行為。 這應該「僅」針對 Google Play 商店中擁有現有應用程式的開發人員使用。 強烈建議使用新的 `$(AndroidVersionCodePattern)` 屬性。

  已在 Xamarin.Android 8.2 中新增。

- **AndroidUseManagedDesignTimeResourceGenerator** &ndash; 布林屬性，可切換設計階段組建以使用受控資源剖析器而非 `aapt`。

  已在 Xamarin.Android 8.1 中新增。

- **AndroidUseSharedRuntime** &ndash; 布林值屬性，可決定是否需要有共用執行階段套件才能在目標裝置上執行應用程式。 依賴共用執行階段套件可讓應用程式套件小一些、加快套件的建立和部署程序速度，進而提升建置/部署/偵錯往返循環速度。

  如果是偵錯組建，這個屬性應該是 `True`，如果是發行專案，則應該是 `False`。

- **AndroidVersionCodePattern** &ndash; 字串屬性，可讓開發人員在資訊清單中自訂 `versionCode`。
  如需有關決定 `versionCode` 的資訊，請參閱[為 APK 建立版本代碼](~/android/deploy-test/building-apps/abi-specific-apks.md)。

  一些範例，如果 `abi` 是 `armeabi` 且資訊清單中的 `versionCode` 是 `123`，則在 `$(AndroidCreatePackagePerAbi)` 為 True 時，`{abi}{versionCode}` 會產生 `1123` 的 versionCode，否則會產生 123 的值。
  如果 `abi` 是 `x86_64` 且資訊清單中的 `versionCode` 是 `44`。 當 `$(AndroidCreatePackagePerAbi)` 為 True 時，這會產生 `544`，否則會產生 `44` 的值。

  如果我們納入左側填補格式字串 `{abi}{versionCode:0000}`，它會產生 `50044`，因為我們會在 `versionCode` 左側填補 `0`。 或者，您也可以使用小數點填補 (例如 `{abi}{versionCode:D4}`)，
  其作用與上一個範例相同。

  值必須是整數，因此僅支援 '0' 和 'Dx' 填補格式字串。

  預先定義的索引鍵項目

  - **abi** &ndash; 為應用程式插入設為目標的 abi
    - 2 &ndash; `armeabi-v7a`
    - 3 &ndash; `x86`
    - 4 &ndash; `arm64-v8a`
    - 5 &ndash; `x86_64`

  - **minSDK** &ndash; 若未定義任何項目，則插入所支援的最小 Sdk 值 (`AndroidManifest.xml` 或 `11`)。

  - **versionCode** &ndash; 直接使用 `Properties\AndroidManifest.xml` 中的版本代碼。

  您可使用 `$(AndroidVersionCodeProperties)` 屬性 (於下一步定義) 定義自訂項目。

  根據預設，值會設定為 `{abi}{versionCode:D6}`。 如果開發人員想要保留舊的行為，則您可以將 `$(AndroidUseLegacyVersionCode)` 屬性設定為 `true` 來覆寫預設值

  已在 Xamarin.Android 7.2 中新增。

- **AndroidVersionCodeProperties** &ndash; 字串屬性可讓開發人員定義要與 `AndroidVersionCodePattern` 搭配使用的自訂項目。 其格式為 `key=value` 組。 `value` 中的所有項目都應該是整數值。 例如：`screen=23;target=$(_AndroidApiLevel)`。 如您所見，您可以在字串中利用現有或自訂的 MSBuild 屬性。

  已在 Xamarin.Android 7.2 中新增。

- **AotAssemblies** &ndash; 布林值屬性，可決定組件是否會預先編譯至機器碼，並納入 `.apk` 中。

  在 Xamarin.Android 5.1 中已新增這個屬性的支援。

  這個屬性的預設值是 `False`。

- **EmbedAssembliesIntoApk** &ndash; 布林值屬性，可決定應用程式的組件是否應內嵌到應用程式套件中。

  如果是發行組建，這個屬性應該是 `True`，如果是偵錯組建，則應該是 `False`。 如果快速部署不支援目標裝置，則在偵錯組建中，這個屬性可能需為 `True`。

  當這個屬性是 `False` 時，`$(AndroidFastDeploymentType)` MSBuild 屬性也可控制所要內嵌到 `.apk` 的項目，而這可能會影響部署和重建時間。

- **EnableLLVM** &ndash; 布林值屬性，可決定在將組件預先編譯至機器碼時，是否會使用 LLVM。

  在 Xamarin.Android 5.1 中已新增這個屬性的支援。

  這個屬性的預設值是 `False`。

  除非 `$(AotAssemblies)` MSBuild 屬性為 `True`，否則會忽略這個屬性。

- **EnableProguard** &ndash; 布林值屬性，可決定 [proguard](https://developer.android.com/tools/help/proguard.html) 是否會在封裝程序進行期間執行以便連結 Java 程式碼。

  在 Xamarin.Android 5.1 中已新增這個屬性的支援。

  這個屬性的預設值是 `False`。

  若為 `True`，則會使用 [ProguardConfiguration](#ProguardConfiguration) 檔案來控制 `proguard` 執行。

- **JavaMaximumHeapSize** &ndash; 指定在封裝程序進行期間建置 `.dex` 檔案時，所要使用之 **java**
  `-Xmx` 參數值的值。 如果未指定，則 `-Xmx` 選項會為 **java** 提供值 `1G`。 相較於其他平台，這在 Windows 上通常是必要項目。

  如果 [`_CompileDex` 目標擲回 `java.lang.OutOfMemoryError`](https://bugzilla.xamarin.com/show_bug.cgi?id=18327)，則必須指定這個屬性。

  藉由變更下列項目來自訂值：

  ```xml
  <JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
  ```

- **JavaOptions** &ndash; 指定要在建置 `.dex` 檔案時傳遞給 **java** 的其他命令列選項。

- **LinkerDumpDependencies** &ndash; 可產生連結器相依性檔案的布林屬性。 此檔案可作為 [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md) 工具的輸入使用。

  預設值為 False。

- **MandroidI18n** &ndash; 指定應用程式隨附的國際化支援，例如定序和排序資料表。 此值是下列一或多個不區分大小寫之值的逗號或分號分隔清單：

  - **None**：未包含其他編碼。

  - **All**：包含所有可用的編碼。

  - **CJK**：包含中文、日文和韓文編碼，例如日文 (EUC) \[enc-jp, CP51932\]、日文 (Shift-JIS) \[iso-2022-jp, shift\_jis, CP932\]、日文 (JIS) \[CP50220\]、簡體中文 (GB2312) \[gb2312, CP936\]、韓文 (UHC) \[ks\_c\_5601-1987, CP949\]、韓文 (EUC) \[euc-kr, CP51949\]、繁體中文(Big5) \[big5, CP950\] 和簡體中文 (GB18030) \[GB18030, CP54936\]。

  - **MidEast**：包含中東編碼，例如土耳其文 (Windows) \[iso-8859-9, CP1254\]、希伯來文 (Windows) \[windows-1255, CP1255\]、阿拉伯文 (Windows) \[windows-1256, CP1256\]、阿拉伯文 (ISO) \[iso-8859-6, CP28596\]、希伯來文 (ISO) \[iso-8859-8, CP28598\]、拉丁文 5 (ISO) \[iso-8859-9, CP28599\] 和希伯來文 (Iso Alternative) \[iso-8859-8, CP38598\]。

  - **Other**：包含其他編碼，例如斯拉夫文 (Windows) \[CP1251\]、波羅的海文 (Windows) \[iso-8859-4, CP1257\]、越南文 (Windows) \[CP1258\]、斯拉夫文 (KOI8-R) \[koi8-r, CP1251\]、烏克蘭文 (KOI8-U) \[koi8-u, CP1251\]、波羅的海文 (ISO) \[iso-8859-4, CP1257\]、斯拉夫文 (ISO) \[iso-8859-5, CP1251\]、ISCII 梵文字母 \[x-iscii-de, CP57002\]、ISCII 孟加拉文 \[x-iscii-be, CP57003\]、ISCII 坦米爾文 \[x-iscii-ta, CP57004\]、ISCII 泰盧固文 \[x-iscii-te, CP57005\]、ISCII 阿薩姆文 \[x-iscii-as, CP57006\]、ISCII 歐迪亞文 \[x-iscii-or, CP57007\]、ISCII 坎那達文 \[x-iscii-ka, CP57008\]、ISCII 馬來亞拉姆文 \[x-iscii-ma, CP57009\]、ISCII 古吉拉特文 \[x-iscii-gu, CP57010\]、ISCII 旁遮普文 \[x-iscii-pa, CP57011\] 和泰文 (Windows) \[CP874\]。

  - **Rare**：包含罕見的編碼，例如 IBM EBCDIC (土耳其文) \[CP1026\]、IBM EBCDIC (開啟系統拉丁文 1) \[CP1047\]、IBM EBCDIC (美國-加拿大與歐元區) \[CP1140\]、IBM EBCDIC (德國與歐元區) \[CP1141\]、IBM EBCDIC (丹麥/挪威與歐元區) \[CP1142\]、IBM EBCDIC (芬蘭/瑞典與歐元區) \[CP1143\]、IBM EBCDIC (義大利與歐元區) \[CP1144\]、IBM EBCDIC (拉丁美洲/西班牙與歐元區) \[CP1145\]、IBM EBCDIC (英國與歐元區) \[CP1146\]、IBM EBCDIC (法國與歐元區) \[CP1147\]、IBM EBCDIC (國際與歐元區) \[CP1148\]、IBM EBCDIC (冰島與歐元區) \[CP1149\]、IBM EBCDIC (德國) \[CP20273\]、IBM EBCDIC (丹麥/挪威) \[CP20277\]、IBM EBCDIC (芬蘭/瑞典) \[CP20278\]、IBM EBCDIC (義大利) \[CP20280\]、IBM EBCDIC (拉丁美洲/西班牙) \[CP20284\]、IBM EBCDIC (英國) \[CP20285\]、IBM EBCDIC (日文片假名延伸) \[CP20290\]、IBM EBCDIC (法國) \[CP20297\]、IBM EBCDIC (阿拉伯文) \[CP20420\]、IBM EBCDIC (希伯來文) \[CP20424\]、IBM EBCDIC (冰島文) \[CP20871\]、IBM EBCDIC (斯拉夫文-塞爾維亞文、保加利亞文) \[CP21025\]、IBM EBCDIC (美國-加拿大) \[CP37\]、IBM EBCDIC (國際) \[CP500\]、阿拉伯文 (ASMO 708) \[CP708\]、中歐語系 (DOS) \[CP852\]、斯拉夫文 (DOS) \[CP855\]、土耳其文 (DOS) \[CP857\]、西歐語系 (DOS 與歐元區) \[CP858\]、希伯來文 (DOS) \[CP862\]、阿拉伯文 (DOS) \[CP864\]、俄文 (DOS) \[CP866\]、希臘文 (DOS) \[CP869\]、IBM EBCDIC (拉丁文 2) \[CP870\] 和 IBM EBCDIC (希臘文) \[CP875\]。

  - **West**：包含西方編碼，例如西歐語系 (Mac) \[macintosh, CP10000\]、冰島文 (Mac) \[x-mac-icelandic, CP10079\]、中歐語系 (Windows) \[iso-8859-2, CP1250\]、西歐語系 (Windows) \[iso-8859-1, CP1252\]、希臘文 (Windows) \[iso-8859-7, CP1253\]、中歐語系 (ISO) \[iso-8859-2, CP28592\]、拉丁文 3 (ISO) \[iso-8859-3, CP28593\]、希臘文 (ISO) \[iso-8859-7, CP28597\]、拉丁文 9 (ISO) \[iso-8859-15, CP28605\]、OEM 美國 \[CP437\]、西歐語系 (DOS) \[CP850\]、葡萄牙文 (DOS) \[CP860\]、冰島文 (DOS) \[CP861\]、加拿大法文 (DOS) \[CP863\] 和北歐語言 (DOS) \[CP865\]。

  ```xml
  <MandroidI18n>West</MandroidI18n>
  ```

- **MonoSymbolArchive** &ndash; 布林值屬性，可控制是否建立 `.mSYM` 以供稍後與 `mono-symbolicate` 搭配使用，以便從發行堆疊追蹤擷取&ldquo;真實的&rdquo;檔案名稱和行號資訊。

  若為已啟用偵錯符號的&ldquo;發行&rdquo;應用程式，此值預設是 True：`$(EmbedAssembliesIntoApk)` 為 True、`$(DebugSymbols)` 為 True，且 `$(Optimize)` 為 True。

  在 Xamarin.Android 7.1 中已新增。

### <a name="binding-project-build-properties"></a>繫結專案組建屬性

下列 MSBuild 屬性可與[繫結專案](~/android/platform/binding-java-library/index.md)搭配使用：

- **AndroidClassParser** &ndash; 可控制 `.jar` 檔案剖析方式的字串屬性。 可能的值包括：

  - **class-parse**：使用 `class-parse.exe` 來直接剖析 Java 位元組程式碼，且不需要 JVM 的協助。 這是實驗值。

  - **jar2xml**:使用 `jar2xml.jar` 以使用 Java 反映來從 `.jar` 檔案擷取類型和成員。

  `class-parse` 相對於 `jar2xml` 的優勢在於：

  - `class-parse` 可以從包含偵錯符號 (例如，使用 `javac -g` 所編譯的位元組程式碼) 的 Java 位元組程式碼中擷取參數名稱。

  - `class-parse` 不會「略過」繼承自或包含無法解析類型成員的類別。

  **實驗**。 已在 Xamarin.Android 6.0 中新增。

  預設值為 `jar2xml`。

  未來版本的預設值會變更。

- **AndroidCodegenTarget** &ndash; 可控制程式碼產生目標 ABI 的字串屬性。 可能的值包括：

  - **XamarinAndroid**：使用自 Mono for Android 1.0 起便存在的 JNI 繫結 API。 使用 Xamarin.Android 5.0 或更新版本所建置的繫結組件只可在 Xamarin.Android 5.0 或更新版本 (API/ABI 新增項目) 上執行，但「來源」與舊版產品相容。

  - **XAJavaInterop1**：在 JNI 引動過程使用 Java.Interop。 使用 `XAJavaInterop1` 的繫結組件只可使用 Xamarin.Android 6.1 或更新版本來建置及執行。 Xamarin.Android 6.1 及更新版本使用此值繫結 `Mono.Android.dll`。

    `XAJavaInterop1` 的優點包括：

    - 組件較小。

    - 以 `jmethodID` 快取 `base` 方法引動過程，但繼承階層中所有其他繫結類型皆必須使用 `XAJavaInterop1` 或更新版本來建置。

    - 以 `jmethodID` 快取受控子類別的 Java 可呼叫包裝函式建構函式。

    預設值為 `XAJavaInterop1`。

### <a name="resource-properties"></a>資源屬性

資源屬性可控制 `Resource.designer.cs` 檔案的產生，而此檔案可提供 Android 資源的存取權。

- **AndroidAapt2CompileExtraArgs** &ndash; 指定要在處理 Android 資產和資源時，傳遞給 **aapt2 compile** 命令的其他命令列選項。

  已在 Xamarin.Android 9.1 中新增。

- **AndroidAapt2LinkExtraArgs** &ndash; 指定要在處理 Android 資產和資源時，傳遞給 **aapt2 link** 命令的其他命令列選項。

  已在 Xamarin.Android 9.1 中新增。

- **AndroidExplicitCrunch** &ndash; 如果您要使用非常大量的本機可繪製資源來建置應用程式，則初始建置 (或重建) 可能需要幾分鐘的時間才能完成。 若要加快建置程序，請嘗試納入此屬性並將它設定為 `True`。 若設定了這個屬性，建置程序就會預先處理 .png 檔案。

  注意:此選項與 `$(AndroidUseAapt2)` 選項不相容。 如果啟用 `$(AndroidUseAapt2)`，將會停用這項功能。 如果您想要繼續使用此功能，請將 `$(AndroidUseAapt2)` 設定為 `False`。

  **實驗**。 已在 Xamarin.Android 7.0 中新增。

- **AndroidResgenExtraArgs** &ndash; 指定要在處理 Android 資產和資源時傳遞給 **aapt** 命令的其他命令列選項。

- **AndroidResgenFile** &ndash; 指定要產生之資源檔的名稱。 預設範本會將此值設定為 `Resource.designer.cs`。

- **AndroidUseAapt2** &ndash; 可讓開發人員控制 `aapt2` 工具的使用以進行封裝的布林屬性。
  根據預設，這會設定為 False，而我們將使用 `aapt`。
  如果開發人員想要使用新的 `aapt2`，他們可以在其 csproj 中設定下列程式碼：

  ```xml
  <AndroidUseAapt2>True</AndroidUseAapt2>
  ```

  。 或者，透過下列參數在命令列上提供屬性：

  ```
  /p:AndroidUseAapt2=True
  ```

  已在 Xamarin.Android 8.3 中新增。

- **MonoAndroidResourcePrefix** &ndash; 指定要使用 `AndroidResource` 的建置動作從檔案名稱開頭移除的路徑前置詞。 這是為了允許變更資源所在位置。

  預設值為 `Resources`。 若為 Java 專案結構，請將此值變更為 `res`。

<a name="Signing_Properties" />

### <a name="signing-properties"></a>簽署屬性

簽署屬性可控制應用程式套件的簽署方式，以便能夠將它安裝到 Android 裝置。 為了加快建置反覆運算，Xamarin.Android 工作不會在建置程序進行期間簽署套件，因為簽署作業很慢。 相反地，會在安裝前或匯出期間由 IDE 或安裝建置目標簽署套件 (如有必要)。 叫用 SignAndroidPackage 目標會在輸出目錄中產生後置詞為 `-Signed.apk` 的套件。

根據預設，簽署目標會產生新的偵錯簽署金鑰 (如有必要)。 如果您想要使用特定索引鍵，例如在建置伺服器上，則可以使用下列 MSBuild 屬性：

- **AndroidDebugKeyAlgorithm** &ndash;指定要針對 `debug.keystore` 使用的預設演算法。 預設值為 `RSA`。

- **AndroidDebugKeyValidity** &ndash;指定要針對 `debug.keystore` 使用的預設有效性。 預設值為 `10950`、`30 * 365` 或 `30 years`。

- **AndroidKeyStore** &ndash; 指出是否應該使用自訂簽署資訊的布林值。 預設值是 `False`，也就是會使用預設的偵錯簽署金鑰來簽署套件。

- **AndroidSigningKeyAlias** &ndash; 指定金鑰儲存區中之金鑰的別名。 這是在建立金鑰儲存區時所使用的 **keytool -alias** 值。

- **AndroidSigningKeyPass** &ndash; 指定金鑰儲存區檔案內之金鑰的密碼。 這是在 `keytool` 要求**輸入 $(AndroidSigningKeyAlias) 的金鑰密碼**時所輸入的值。

- **AndroidSigningKeyStore** &ndash; 指定 `keytool` 所建立之金鑰儲存區檔案的檔案名稱。 這會對應至提供給 **keytool -keystore** 選項的值。

- **AndroidSigningStorePass** &ndash; 指定 `$(AndroidSigningKeyStore)` 的密碼。 這是在建立金鑰儲存區檔案時提供給 `keytool`，並且會要求**輸入金鑰儲存區密碼:** 的值。

例如，請考慮下列 `keytool` 引動過程：

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

<a name="Build_Actions" />

## <a name="build-actions"></a>建置動作

建置動作會[套用至專案內的檔案](https://docs.microsoft.com/visualstudio/msbuild/common-msbuild-project-items)並控制檔案的處理方式。

### <a name="androidaarlibrary"></a>AndroidAarLibrary

`AndroidAarLibrary` 的建置動作應用來直接參考 .aar 檔案。 Xamarin 元件最常使用此建置動作。 也就是要包含 .aar 檔案的參考，這些檔案是讓 Google Play 和其他服務運作的必要項目。

針對使用此建置動作的檔案，其處理方式類似於在程式庫專案中找到的內嵌資源。 .aar 將擷取到中繼目錄中。 然後，所有資產、資源和 .jar 檔案將會包含在適當的項目群組中。

### <a name="androidboundlayout"></a>AndroidBoundLayout

表示在 `AndroidGenerateLayoutBindings` 屬性設定為 `false` 的情況下，配置檔案將為其產生程式碼後置。 在所有其他層面上，它與上述的 `AndroidResource` 相同。 此動作**只**能與配置檔案搭配使用：

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

<a name="AndroidEnvironment" />

### <a name="androidenvironment"></a>AndroidEnvironment

建置動作為 `AndroidEnvironment` 的檔案可用來[在程序啟動期間初始化環境變數和系統屬性](~/android/deploy-test/environment.md)。
`AndroidEnvironment` 建置動作可套用到多個檔案，這些檔案並不會依特定順序來進行評估 (因此，請勿在多個檔案中指定相同的環境變數或系統屬性)。

### <a name="androidfragmenttype"></a>AndroidFragmentType

指定在產生配置繫結程式碼時，要用於所有 `<fragment>`配置元素的預設完整類型。 此屬性預設為標準 Android `Android.App.Fragment` 類型。

### <a name="androidjavalibrary"></a>AndroidJavaLibrary

建置動作為 `AndroidJavaLibrary` 的檔案是會納入到最終版 Android 套件的 Java 封存 (`.jar` 檔案)。

### <a name="androidjavasource"></a>AndroidJavaSource

建置動作為 `AndroidJavaSource` 的檔案是會納入到最終版 Android 套件的 Java 原始程式碼。

### <a name="androidlintconfig"></a>AndroidLintConfig

建置動作 'AndroidLintConfig' 應該與 `AndroidLintEnabled` 建置屬性一起使用。 具有此建置動作的檔案會合併在一起，並傳遞給 Android `lint` 工具。 它們應該是 XML 檔案，其中包含要啟用及停用哪些測試的相關資訊。

如需詳細資訊，請參閱 [Lint 文件](https://developer.android.com/studio/write/lint)。

### <a name="androidnativelibrary"></a>AndroidNativeLibrary

[原生程式庫](~/android/platform/native-libraries.md)可藉由將其建置動作設定為 `AndroidNativeLibrary` 來新增至組建中。

請注意，由於 Android 支援多個應用程式二進位介面 (ABI)，所以建置系統必須知道所建置的原生程式庫適用於哪個 ABI。 方法有二：

1. 路徑「探查」。
2. 使用 `Abi` 項目屬性。

路徑探查會使用原生程式庫的父目錄名稱來指定程式庫的目標 ABI。 因此，如果您將 `lib/armeabi-v7a/libfoo.so` 新增至組建，則會以 `armeabi-v7a` 的形式來「探查」ABI。

#### <a name="item-attribute-name"></a>項目屬性名稱

**Abi** &ndash; 指定原生程式庫的 ABI。

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```

### <a name="androidresource"></a>AndroidResource

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

### <a name="content"></a>內容

不支援一般的 `Content` 建置動作 (因為我們還沒想出該如何提供支援，而又不會讓首次執行步驟的成本太高)。

從 Xamarin.Android 5.1 開始，嘗試使用 `@(Content)` 建置動作將會導致 `XA0101` 警告。

### <a name="linkdescription"></a>LinkDescription

具有 LinkDescription 建置動作的檔案可用來[控制連結器行為](~/cross-platform/deploy-test/linker.md)。

<a name="ProguardConfiguration" />

### <a name="proguardconfiguration"></a>ProguardConfiguration

具有 ProguardConfiguration 建置動作的檔案包含可用來控制 `proguard` 行為的選項。 如需此建置動作的詳細資訊，請參閱 [ProGuard](~/android/deploy-test/release-prep/proguard.md)。

除非 `$(EnableProguard)` MSBuild 屬性為 `True`，否則會忽略這些檔案。

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
