---
title: "疑難排解繫結"
description: "本文摘要說明許多常見的錯誤時產生繫結，以及可能的原因和建議的方法來解決它們可能會發生。"
ms.topic: article
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 84ef87f5ed84fcd0a9aa2504c52a0fec17404e1f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting-bindings"></a>疑難排解繫結

_本文摘要說明許多常見的錯誤時產生繫結，以及可能的原因和建議的方法來解決它們可能會發生。_

<a name="OVERVIEW" />

## <a name="overview"></a>總覽

繫結 Android 程式庫 ( **.aar**或**d**) 檔案很少是簡單容易出錯，通常需要額外的工作，以降低問題所導致的 Java 和.NET 的差異。
這些問題會防止 Xamarin.Android 繫結 Android 程式庫，並呈現本身為組建記錄檔中的錯誤訊息。 本指南會提供用來疑難排解問題的一些祕訣，列出了一些較常見的問題/案例，並提供可能的解決方案成功繫結 Android 程式庫。

在繫結現有的 Android 程式庫，則需要記住以下要點：

- **程式庫的外部相依性** &ndash; Android 程式庫所需的任何 Java 相依性必須包含在 Xamarin.Android 專案**ReferenceJar**或**EmbeddedReferenceJar**。

- **Android 程式庫是以 Android API 層級**&ndash;它不可能 「 降級 」 的 Android API 層級; 請確定繫結 Xamarin.Android 專案的目標相同的 API 層級 （或更高） 為 Android 程式庫。

- **用來封裝 Android 程式庫的 Android jdk 版本**&ndash;如果 Android 程式庫所建置 Xamarin.Android 具有比使用不同版本的 JDK 時將繫結錯誤可能會發生。 可能的話，請重新編譯使用相同版本的 JDK，以供您安裝的 Xamarin.Android Android 程式庫。

繫結 Xamarin.Android 文件庫的問題疑難排解的第一個步驟是啟用[診斷 MSBuild 輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。
啟用診斷的輸出之後, 重新建置 Xamarin.Android 繫結專案，並檢查組建記錄檔，找出新功能的相關問題的原因的線索。

它也可以證明有助於反編譯 Android 程式庫，並檢查的類型和 Xamarin.Android 嘗試繫結的方法。 這是以便稍後在本指南的更詳細地討論。

<a name="DECOMPILING_AN_ANDROID_LIBRARY" />

## <a name="decompiling-an-android-library"></a>Decompiling Android 程式庫

檢查的類別和方法的 Java 類別可以提供寶貴的資訊可協助繫結的程式庫。
[JD GUI](http://jd.benow.ca/)圖形公用程式，可顯示從 Java 原始碼**類別**包含 JAR 檔案。 它可以當做獨立應用程式或外掛程式針對 IntelliJ 或執行 Eclipse。

反編譯 Android 程式庫開啟**。JAR** Java 解編程式檔案。 如果程式庫是**。AAR**檔案，就必須將檔案解壓縮**classes.jar**保存檔案。 以下是範例螢幕擷取畫面的分析中使用 JD GUI[畢加索](http://square.github.io/picasso/)JAR:

![使用 Java 解編程式分析畢加索 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

一旦您已解編 Android 程式庫，檢查原始碼。 一般而言，尋找：

- **具有模糊化的特性類別**&ndash;模糊化類別的特性包括：

    - 類別名稱包含 **$** ，也就是**$.class**
    - 類別名稱完全受到個小寫字元，也就是**a.class**      

- **`import` 陳述式未參考的程式庫**&ndash;未參考的文件庫識別，並將這些相依性加入至具有的 Xamarin.Android 繫結專案**建置動作**的**ReferenceJar**或**EmbedddedReferenceJar**。

> [!NOTE]
> **注意：** Decompiling Java 程式庫可能會禁止或合法限制根據用戶所在地法律或發行 Java 程式庫時的授權。 必要時，編列合法 professional 的服務之前嘗試反編譯 Java 文件庫，並檢查來源的程式碼。

<a name="INSPECTING_API_XML" />

## <a name="inspect-apixml"></a>檢查應用程式開發介面。XML

建立繫結專案的一部分 Xamarin.Android 會產生 XML 檔案名稱**obj/Debug/api.xml**:

![產生的 api.xml 下 obj/偵錯](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

此檔案會提供一份所有 Java Api Xamarin.Android 正嘗試繫結。 此檔案的內容可協助找出遺漏的類型或方法，重複的繫結。 雖然檢查這個檔案是冗長且耗時，但它可以提供什麼可能會造成任何繫結問題的線索。 例如， **api.xml**屬性所傳回的是不適當的類型，或有兩個型別共用相同的 managed 名稱可能會顯示。

<a name="KNOWN_ISSUES" />

## <a name="known-issues"></a>已知問題

本節會列出一些常見的錯誤訊息或徵兆是，我嘗試繫結 Android 程式庫時，會發生。

<a name="PROBLEM_JAVA_VERSION_MISMATCH" />

### <a name="problem-java-version-mismatch"></a>問題： Java 版本不符

有時不會產生型別，或因為您使用的新或舊的 Java 版本相較於程式庫已編譯的可能會發生未預期的當機。 重新編譯版本的 Xamarin.Android 專案使用的 jdk 版本相同的 Android 程式庫。

<a name="PROBLEM_AT_LEAST_ONE_JAVA_LIBRARY_IS_REQUIRED" />

### <a name="problem-at-least-one-java-library-is-required"></a>問題： 至少一個 Java 文件庫無須

您會收到錯誤 「 至少一個 Java 文件庫 」，即使。已加入 JAR。

#### <a name="possible-causes"></a>可能的原因：

請確認建置動作設定為`EmbeddedJar`。 因為有多個組建動作。JAR 檔案 (例如`InputJar`， `EmbeddedJar`，`ReferenceJar`和`EmbeddedReferenceJar`)，繫結產生器無法自動猜測要依預設會使用哪一個。 如需建置動作的詳細資訊，請參閱[建置動作](~/android/platform/binding-java-library/index.md)。

<a name="PROBLEM_BINDING_TOOLS_CANNOT_LOAD_THE_JAR_LIBRARY" />

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>問題： 繫結工具無法載入。JAR 程式庫

繫結的程式庫產生器無法載入。JAR 程式庫。

#### <a name="possible-causes"></a>可能原因

部分。無法載入 JAR 程式庫 （透過 [工具]，例如 Proguard） 的程式碼混淆的 Java 工具。 使用 Java 反映和 ASM 位元組碼工程文件庫，可讓我們的工具，因為這些相依工具可能會在 Android 的執行階段工具，可以傳遞時拒絕混亂的程式庫。 因應措施是手動繫結這些程式庫，而不是使用繫結產生器。


<a name="PROBLEM_MISSING_C_TYPES_IN_GENERATED_OUTPUT_" />

### <a name="problem-missing-c-types-in-generated-output"></a>問題： 遺漏 C# 中產生的輸出型別。

繫結**.dll**組建但遺漏某些 Java 資料類型，或因為發生錯誤，指出有遺失的類型未建置產生的 C# 原始程式碼。

#### <a name="possible-causes"></a>可能的原因：

這個錯誤可能由多種原因造成，如下所示：

-   要繫結的程式庫可能會參考第二個 Java 文件庫。 如果繫結的程式庫的公用 API 會使用型別，從第二個程式庫，您必須參考第二個程式庫的受管理的繫結。

-   很可能文件庫所插入由於 Java 反映，類似於上述造成未預期的中繼資料載入程式庫載入錯誤的原因。 Xamarin.Android 的工具，目前無法解析這種情況。 在這種情況下，程式庫必須以手動方式繫結。

-   無法載入組件時就不應有的.NET 4.0 執行階段時發生錯誤。 .NET 4.5 執行階段中已修正此問題。

-   Java 允許公用類別衍生自非公用類別，但這不支援的.NET 中。 繫結產生器不會產生非公用類別的繫結，因為衍生類別，例如這些無法正確產生。 若要修正此問題，請移除這些衍生的類別使用中的移除節點的中繼資料項目**Metadata.xml**，或修復會使非公用類別公開的中繼資料。 雖然第二種解決方案將會建立繫結，好讓 C# 原始程式檔會建置，但應該不使用非公用類別。

    例如: 

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   模糊化 Java 文件庫的工具可能會干擾 Xamarin.Android 繫結產生器，以及其能力產生 C# 包裝函式類別。 下列程式碼片段示範如何更新**Metadata.xml**至 unobfuscate 類別名稱：

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>問題： 產生 C# 原始程式檔未建置因為參數類型不符

尚未建置產生的 C# 原始程式碼。 覆寫方法的參數類型不相符。

#### <a name="possible-causes"></a>可能的原因：

Xamarin.Android 包含各種不同的對應至列舉中的 C# 繫結的 Java 欄位。 在產生的繫結，這些會造成型別不相容。 若要解決此問題，需要修改成使用列舉方法簽章所建立的繫結產生器。 如需 imformation，請參閱[更正列舉](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)。

### <a name="problem-noclassdeffounderror-in-packaging"></a>在封裝中的問題： NoClassDefFoundError

`java.lang.NoClassDefFoundError` 在封裝步驟會擲回。

#### <a name="possible-causes"></a>可能的原因：

此錯誤最可能的原因是強制性的 Java 程式庫必須加入至應用程式專案 (**.csproj**)。 .JAR 檔案不會自動解決。 Java 文件庫繫結不一定會產生對不存在於目標裝置或模擬器的使用者組件 (例如 Google 地圖**maps.jar**)。 這不是程式庫為 Android 程式庫專案支援的情況。JAR 內嵌於程式庫 dll。 例如： [Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>問題： 重複的 EventArgs 的自訂型別

建置失敗，因為重複自訂 EventArgs 類型。 會發生錯誤，就像這樣：

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>可能的原因：

這是因為來自共用具有相同名稱的方法的多個介面 「 接聽程式 」 類型的事件類型之間有一些衝突。 例如，如果有兩個 Java 介面，如下列範例所示，產生器建立`DismissScreenEventArgs`兩者`MediationBannerListener`和`MediationInterstitialListener`，並產生錯誤。

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

這是依設計會避免事件引數型別上的冗長名稱。 若要避免這些衝突，某些中繼資料不需要轉換。 編輯[ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml)並加入`argsType`屬性上的介面 （或介面方法上）：

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

### <a name="problem-class-does-not-implement-interface-method"></a>問題： 類別未實作介面方法

會產生錯誤訊息，指出產生的類別未實作所產生的類別會實作介面的方法。 不過，查看產生的程式碼，您可以看到方法實作。

錯誤的範例如下：

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>可能的原因：

這是具有 covariant 傳回型別與繫結 Java 方法，就會發生問題。 在此範例中，此方法`Oauth.Signpost.Http.IHttpRequest.UnWrap()`需要傳回`Java.Lang.Object`。 不過，此方法`Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()`具有傳回型別的`HttpURLConnection`。 有兩種方式可以修正此問題：

-   加入部分類別宣告`HttpURLConnectionRequestAdapter`和明確實作`IHttpRequest.Unwrap()`:

    ```csharp
    namespace Oauth.Signpost.Basic {
        partial class HttpURLConnectionRequestAdapter {
            Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
                return Unwrap();
            }
        }
    }
    ```

-   移除所產生的 C# 程式碼中的共變數。 這項作業包括加入下列的轉換來**Transforms\Metadata.xml**這會導致產生的 C# 程式碼具有傳回型別的`Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>問題： 將名稱衝突上的內部類別 / 屬性

繼承的物件上發生衝突的可見性。

在 Java 中，您不需要在衍生的類別具有與其父系相同的可見性。 Java 將只會為您解決問題。 在 C# 中，具有明確宣告，因此您必須確定階層中的所有類別都有適當的可見度。 下列範例示範如何在 Java 封裝名稱，從`com.evernote.android.job`至`Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>問題： A **.so**繫結所需的程式庫是未載入

某些繫結專案也會取決於功能中**.so**程式庫。 很可能 Xamarin.Android 不會自動載入**.so**程式庫。 若要讓 JNI 呼叫和錯誤訊息的已包裝的 Java 程式碼執行時，將會失敗 Xamarin.Android _java.lang.UnsatisfiedLinkError： 找不到原生方法：_會出現在出 logcat，應用程式。

此修正方法是手動載入**.so**呼叫的程式庫`Java.Lang.JavaSystem.LoadLibrary`。 如需範例假設 Xamarin.Android 專案已共用程式庫**libpocketsphinx_jni.so**包含在繫結專案的建置動作**EmbeddedNativeLibrary**、 下（使用共用媒體櫃之前執行） 的程式碼片段會載入**.so**程式庫：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

<a name=summary />

## <a name="summary"></a>總結

在本文中，我們會列出與 Java 繫結相關聯的常見疑難排解問題，並說明如何解決這些問題。


## <a name="related-links"></a>相關連結

- [程式庫專案](http://developer.android.com/tools/projects/index.html#LibraryProjects)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [啟用診斷輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin for Android 開發人員](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
