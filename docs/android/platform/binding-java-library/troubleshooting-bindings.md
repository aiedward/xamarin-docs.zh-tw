---
title: 對繫結進行疑難排解
description: 本文摘要說明產生系結時可能會發生的多種常見錯誤，以及可能的原因和解決這些問題的建議方法。
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: dfbcb1a6f502d6d7a5b03dc03278fc21e57806bf
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756619"
---
# <a name="troubleshooting-bindings"></a>對繫結進行疑難排解

_本文摘要說明產生系結時可能會發生的多種常見錯誤，以及可能的原因和解決這些問題的建議方法。_

## <a name="overview"></a>總覽

系結 Android 程式庫（ **aar**或 **.jar**）檔案不太容易開一整天;通常需要額外的工作來減輕 JAVA 和 .NET 之間的差異所造成的問題。
這些問題將導致 Xamarin 無法系結 Android 程式庫，並將其本身呈現為組建記錄檔中的錯誤訊息。 本指南將提供一些疑難排解問題的秘訣、列出一些較常見的問題/案例，並提供成功系結 Android 程式庫的可能解決方案。

系結現有的 Android 程式庫時，必須記住下列幾點：

- 連結**庫的外部**相依性Android 程式庫所需的任何 JAVA 相依性都必須以 ReferenceJar 或**EmbeddedReferenceJar**的形式包含在 Xamarin android 專案中。 &ndash;

- **瞄準 android 程式庫的 ANDROID API 層級**&ndash;不可能「降級」 Android API 層級; 請確定您的 Xamarin android 系結專案的目標是與 Android 程式庫相同的 API 層級（或更高版本）。

- **用來封裝 android 程式庫的 ANDROID JDK 版本**&ndash;如果 Android 程式庫是以不同于 Xamarin 所使用的 JDK 版本所建立，則可能會發生系結錯誤。 可能的話，請使用您的 Xamarin 安裝所使用的相同 JDK 版本來重新編譯 Android 程式庫。

疑難排解 Xamarin. Android 程式庫的問題的第一個步驟是啟用[診斷 MSBuild 輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。
啟用診斷輸出之後，請重建 Xamarin Android 系結專案，並檢查組建記錄檔，以找出問題原因的線索。

它也有助於將 Android 程式庫反編譯，並檢查 Xamarin 嘗試系結的類型和方法。 本指南稍後會詳細說明這一點。

## <a name="decompiling-an-android-library"></a>反向組譯 Android 程式庫

檢查 JAVA 類別的類別和方法可以提供有用的資訊，協助系結程式庫。
[JD-GUI](http://jd.benow.ca/)是一個圖形化公用程式，可從 JAR 所包含的**類別**檔案中顯示 JAVA 原始碼。 它可以做為獨立應用程式或 IntelliJ 或 Eclipse 的外掛程式來執行。

若要將 Android 程式庫反編譯，請開啟 **。** 具有 JAVA 解編程式的 JAR 檔案。 如果程式庫是 **。AAR**檔案，則必須從封存檔案中解壓縮檔案**類別 .jar** 。 以下是使用 JD （GUI）分析[Picasso](http://square.github.io/picasso/) JAR 的範例螢幕擷取畫面：

![使用 JAVA 解編程式來分析 picasso-2.5.2 .jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

反向組譯 Android 程式庫之後，請檢查原始程式碼。 一般來說，請尋找：

- **具有混淆特性的類別**&ndash;模糊類別的特性包括：

  - 類別名稱會包含 **$** ，也就是 **$.class**
  - 類別名稱完全洩露小寫字元，亦即**類別**      

- 未參考連結&ndash; **庫的語句會識別未參考的程式庫，並將這些相依性新增至 ReferenceJar 的組建動作或的 Xamarin 繫結項目`import`**  **EmbedddedReferenceJar**。

> [!NOTE]
> 反向組譯 JAVA 程式庫可能會被禁止，或根據當地法律或用來發行 JAVA 程式庫的授權而受到法律限制。 如有必要，請在嘗試將 JAVA 程式庫進行反編譯並檢查原始程式碼之前，先登記合法專業人員的服務。

## <a name="inspect-apixml"></a>檢查 API。STL

在建立系結專案的過程中，Xamarin 會產生 XML 檔案名**obj/Debug/api .xml**：

![在 obj/Debug 之下產生的 api](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

此檔案提供 Xamarin 正嘗試系結之所有 JAVA Api 的清單。 此檔案的內容有助於識別任何遺漏的類型或方法、重複的系結。 雖然此檔案的檢查既繁瑣又費時，但它可以提供可能造成任何系結問題之原因的線索。 例如， **config.xml**可能會顯示某個屬性傳回不適當的類型，或有兩個類型共用相同的受控名稱。

## <a name="known-issues"></a>已知問題

本節將列出一些在嘗試系結 Android 程式庫時所發生的常見錯誤訊息或徵兆。

### <a name="problem-java-version-mismatch"></a>問題：JAVA 版本不符

有時不會產生類型或未預期的當機，因為您使用的是較新或較舊版本的 JAVA，而不是與用來編譯程式庫的內容相比較。 使用您的 Xamarin Android 專案所使用的相同 JDK 版本來重新編譯 Android 程式庫。

### <a name="problem-at-least-one-java-library-is-required"></a>問題：至少需要一個 JAVA 程式庫

您會收到錯誤「需要至少一個 JAVA 程式庫」，即使是也一樣。已新增 JAR。

#### <a name="possible-causes"></a>可能的原因：

請確定 [組建] 動作已設定`EmbeddedJar`為。 因為有多個組建動作。`InputJar`JAR 檔案（例如`EmbeddedJar` `ReferenceJar` 、和`EmbeddedReferenceJar`），系結產生器無法自動猜測要使用哪一個預設值。 如需組建動作的詳細資訊，請參閱[組建動作](~/android/platform/binding-java-library/index.md)。

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>問題：系結工具無法載入。JAR 程式庫

系結程式庫產生器無法載入。JAR 程式庫。

#### <a name="possible-causes"></a>可能原因

部分.JAVA 工具無法載入使用程式碼混淆的 JAR 程式庫（透過 Proguard 之類的工具）。 因為我們的工具會使用 JAVA 反映和 ASM 位元組程式碼工程程式庫，所以當 Android 執行時間工具可能通過時，這些相依的工具可能會拒絕模糊的程式庫。 這個方法的因應措施是手動系結這些程式庫，而不是使用系結產生器。

### <a name="problem-missing-c-types-in-generated-output"></a>問題：產生C#的輸出中遺漏類型。

此系結 **.dll**建立但遺漏一些 JAVA 類型，或產生C#的來源因為錯誤而無法建立，這表示有遺失的類型。

#### <a name="possible-causes"></a>可能的原因：

發生此錯誤的原因有好幾個，如下所示：

- 所系結的程式庫可能會參考第二個 JAVA 程式庫。 如果系結程式庫的公用 API 使用第二個程式庫中的類型，您也必須參考第二個程式庫的 managed 系結。

- 程式庫可能是因為 JAVA 反映而插入的，這與上述程式庫載入錯誤的原因類似，因而導致未預期的中繼資料載入。 Xamarin. Android 的工具目前無法解決這種情況。 在這種情況下，程式庫必須以手動方式系結。

- .NET 4.0 執行時間中發生錯誤，無法載入元件（如果有的話）。 此問題已在 .NET 4.5 執行時間中修正。

- JAVA 允許從非公用類別衍生公用類別，但 .NET 不支援這種方式。 由於系結產生器不會針對非公用類別產生系結，因此無法正確產生衍生類別（例如）。 若要修正此問題，請使用**metadata**中的 remove 節點移除這些衍生類別的中繼資料專案，或修正使非公用類別成為公用的中繼資料。 雖然後者的解決方案將會建立系結，以便C#建立來源，但不應使用非公用類別。

  例如：

  ```xml
  <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
      name="visibility">public</attr>
  ```

- 模糊 JAVA 程式庫的工具可能會干擾 Xamarin 的系結產生器，以及它產生C#包裝函式類別的能力。 下列程式碼片段顯示如何更新**Metadata** ，以 unobfuscate 類別名稱：

  ```xml
  <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
      name="obfuscated">false</attr>
  ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>問題：產生C#的來源因參數類型不符而無法建立

產生C#的來源不會建立。 覆寫方法的參數類型不相符。

#### <a name="possible-causes"></a>可能的原因：

Xamarin 包含各種對應至系結中C#列舉的 JAVA 欄位。 這些可能會導致產生的系結中出現型別不相容的情況。 若要解決此問題，必須修改從系結產生器建立的方法簽章，以使用列舉。 如需詳細資訊，請參閱[更正](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)列舉。

### <a name="problem-noclassdeffounderror-in-packaging"></a>問題：封裝中的 JAVA.lang.noclassdeffounderror

`java.lang.NoClassDefFoundError`封裝步驟中擲回。

#### <a name="possible-causes"></a>可能的原因：

此錯誤最有可能的原因是必須將必要的 JAVA 程式庫加入應用程式專案（ **.csproj**）中。 .JAR 檔案不會自動解析。 JAVA 程式庫系結不一定會針對不存在於目標裝置或模擬器中的使用者元件（例如 Google Maps**對應 .jar**）產生。 這不是 Android 程式庫專案支援的情況，而是程式庫。JAR 內嵌于程式庫 dll 中。 例如：[Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>問題：重複的自訂 EventArgs 類型

組建因重複的自訂 EventArgs 類型而失敗。 發生類似的錯誤：

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>可能的原因：

這是因為來自多個介面「接聽程式」類型的事件種類，與共享相同名稱的方法之間有一些衝突。 例如，如果有兩個 JAVA 介面如下列範例所示，產生器會同時`DismissScreenEventArgs` `MediationBannerListener`建立和`MediationInterstitialListener`，因而導致錯誤。

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

這是設計的，因此會避免事件引數類型上冗長的名稱。 為了避免這些衝突，需要進行一些中繼資料轉換。 編輯[**Transforms\Metadata.xml**](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) ，並在`argsType`其中一個介面上加入屬性（或在介面方法上）：

```xml
<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationBannerListener']/method[@name='onDismissScreen']"
        name="argsType">BannerDismissScreenEventArgs</attr>

<attr path="/api/package[@name='com.google.ads.mediation']/
        interface[@name='MediationInterstitialListener']/method[@name='onDismissScreen']"
        name="argsType">IntersitionalDismissScreenEventArgs</attr>

<attr path="/api/package[@name='android.content']/
        interface[@name='DialogInterface.OnClickListener']"
        name="argsType">DialogClickEventArgs</attr>
```

### <a name="problem-class-does-not-implement-interface-method"></a>問題：類別未執行介面方法

系統會產生錯誤訊息，指出產生的類別不會針對所產生之類別所執行的介面，進行必要的方法。 不過，查看所產生的程式碼，您可以看到方法已實作為。

以下是錯誤的範例：

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>可能的原因：

這是使用具有協變數傳回型別的系結 JAVA 方法時所發生的問題。 在此範例中，方法`Oauth.Signpost.Http.IHttpRequest.UnWrap()`必須`Java.Lang.Object`傳回。 不過，方法`Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()`的傳回`HttpURLConnection`型別為。 有兩種方式可以修正此問題：

- 新增的部分類別`HttpURLConnectionRequestAdapter`宣告，並明確地執行： `IHttpRequest.Unwrap()`

  ```csharp
  namespace Oauth.Signpost.Basic {
      partial class HttpURLConnectionRequestAdapter {
          Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
              return Unwrap();
          }
      }
  }
  ```

- 從產生C#的程式碼中移除共變數。 這牽涉到將下列轉換新增至**Transforms\Metadata.xml** ，這會導致C#產生的程式碼具有的`Java.Lang.Object`傳回類型：

  ```xml
  <attr
      path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
      name="managedReturn">Java.Lang.Object
  </attr>
  ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>問題：內部類別/屬性的名稱衝突

繼承物件的可見度衝突。

在 JAVA 中，衍生類別不一定要有與其父系相同的可見度。 JAVA 只會為您修正此問題。 在C#中，這必須是明確的，因此您必須確定階層中的所有類別都有適當的可見度。 下列範例顯示如何將 JAVA 封裝名稱從`com.evernote.android.job`變更為： `Evernote.AndroidJob`

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>問題：答 **：** 無法載入系結所需的程式庫

某些系結專案也可能相依于中的功能 **。因此，** 請參閱程式庫。 Xamarin 可能不會自動載入 **。因此**，請看程式庫。 當包裝的 JAVA 程式碼執行時，Xamarin 將無法進行 JNI 呼叫，並會 UnsatisfiedLinkError 錯誤訊息 _：找不到原生_方法：將會出現在應用程式的 logcat 中。

修正此問題的方法是以手動方式**載入，並**呼叫來`Java.Lang.JavaSystem.LoadLibrary`進行程式庫。 例如，假設 Xamarin 專案有共用程式庫**libpocketsphinx_jni。因此**包含在系結專案中，且組建動作為**EmbeddedNativeLibrary**，下列程式碼片段（在使用共用程式庫之前執行）會載入 **。因此，** 程式庫：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>總結

在本文中，我們列出了與 JAVA 系結相關的常見疑難排解問題，並說明如何解決它們。

## <a name="related-links"></a>相關連結

- [程式庫專案](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [啟用診斷輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [適用于 Android 開發人員的 Xamarin](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
