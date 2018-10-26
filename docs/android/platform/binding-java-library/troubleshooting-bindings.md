---
title: 疑難排解的繫結
description: 本文摘要說明幾種常見的錯誤時產生繫結，以及可能的原因和解決這些問題的建議的方式可能會發生。
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f54da980834b44bbca7dc8619943769f8f429a7a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115285"
---
# <a name="troubleshooting-bindings"></a>疑難排解的繫結

_本文摘要說明幾種常見的錯誤時產生繫結，以及可能的原因和解決這些問題的建議的方式可能會發生。_


## <a name="overview"></a>總覽

繫結 Android 程式庫 ( **.aar**或是 **.jar**) 檔案很少是簡單的老頭子; 它通常需要額外的工作，以減輕問題所導致的 Java 和.NET 之間的差異。
這些問題會防止 Xamarin.Android 繫結 Android 程式庫，並將自己當成組建記錄檔中的錯誤訊息。 本指南會提供用來疑難排解問題的一些秘訣，列出一些較常見的問題/案例，並提供可能的解決方案成功繫結 Android 程式庫。

繫結現有 Android 程式庫時，必須記住下列幾點：

- **程式庫的外部相依性**&ndash;做為 Xamarin.Android 專案中必須包含 Android 程式庫所需的任何 Java 相依性**ReferenceJar**或**EmbeddedReferenceJar**。

- **Android 程式庫為目標的 Android API 層級**&ndash;它無法 「 降級 」 的 Android API 層級，確保 Xamarin.Android 繫結專案的目標相同的 API 層級 （或更新版本） 為 Android 程式庫。

- **用來封裝 Android 程式庫的 Android jdk 版本**&ndash;如果 Android 程式庫所建置使用不同版本的 JDK 比使用 Xamarin.Android 時將繫結錯誤可能會發生。 可能的話，請重新編譯使用相同版本的 JDK，以供您的 Xamarin.Android 安裝的 Android 程式庫。

繫結的 Xamarin.Android 程式庫的問題疑難排解的第一個步驟是啟用[診斷 MSBuild 輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。
啟用診斷的輸出之後, 重建 Xamarin.Android 繫結專案，並檢查組建記錄檔，找出問題的原因是什麼的線索。

它也可以證明進行 Android 程式庫，並檢查的類型和 Xamarin.Android 器嘗試繫結的方法很有幫助。 這會涵蓋更多詳細資料，以便稍後在本指南中。


## <a name="decompiling-an-android-library"></a>反向組譯 Android 程式庫

檢查類別，以及 Java 類別的方法，可以提供寶貴的資訊協助您繫結程式庫。
[JD GUI](http://jd.benow.ca/)是圖形化公用程式，可顯示從 Java 原始程式碼**類別**包含 JAR 檔案。 它可以執行為獨立應用程式或外掛程式 IntelliJ 或 Eclipse。

反編譯的 Android 程式庫開啟 **。JAR** Java 解編程式檔案。 如果程式庫是 **。AAR**檔，它就必須將檔案解壓縮**classes.jar**從封存檔案。 以下是範例螢幕擷取畫面來分析使用 JD GUI[畢加索](http://square.github.io/picasso/)JAR:

![使用 Java 解編程式來分析畢加索 2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

一旦您有反向組譯 Android 程式庫，檢查原始程式碼。 一般而言，尋找：

- **類別具有模糊化的特性**&ndash;模糊化類別的特性包括：

    - 類別名稱會包含**$**，也就是 **$.class**
    - 類別名稱完全洩露的小寫字元，也就是**a.class**      

- **`import` 陳述式未參考的程式庫**&ndash;未參考的文件庫識別，並將這些相依性新增至 Xamarin.Android 繫結專案**建置動作**的**ReferenceJar**或是**EmbedddedReferenceJar**。

> [!NOTE]
> 反向組譯 Java 程式庫可能會禁止或受制於法律限制根據 用戶所在地法律或在其下的 Java 程式庫已發佈的授權。 如有必要，請再嘗試進行 Java 程式庫，並檢查原始碼登錄法律專業人員的服務。


## <a name="inspect-apixml"></a>檢查 API。XML

建置一個繫結專案時，Xamarin.Android 會產生一個 XML 檔名**obj/Debug/api.xml**:

![產生的 api.xml 下 obj/偵錯](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

此檔案會提供一份所有 Java Api Xamarin.Android 嘗試繫結。 此檔案的內容可協助識別任何遺漏的類型或方法，重複的繫結。 雖然檢查這個檔案是既繁瑣又費時，它可以提供在什麼可能會造成任何繫結問題的線索。 例如， **api.xml**屬性會傳回不適當的類型，或有兩個類型共用相同的 managed 名稱可能會顯示。


## <a name="known-issues"></a>已知問題

本節會列出一些常見的錯誤訊息或徵狀，我嘗試繫結 Android 程式庫時，會發生。


### <a name="problem-java-version-mismatch"></a>問題： Java 版本不符

有時不會產生型別，或因為您使用的較新或較舊的 Java 版本相較於程式庫在編譯了，可能會發生未預期的當機。 重新編譯您的 Xamarin.Android 專案使用的 jdk 的相同版本 Android 程式庫。


### <a name="problem-at-least-one-java-library-is-required"></a>至少一個 Java 程式庫是必要問題：

您會收到錯誤 「 至少一個 Java 程式庫 」，即使。已新增 JAR。

#### <a name="possible-causes"></a>可能的原因：

請確定建置動作設定為`EmbeddedJar`。 由於有多個建置動作。JAR 檔案 (例如`InputJar`， `EmbeddedJar`，`ReferenceJar`和`EmbeddedReferenceJar`)，繫結產生器無法自動猜出要依預設會使用哪一個。 如需有關建置動作的詳細資訊，請參閱[建置動作](~/android/platform/binding-java-library/index.md)。


### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>問題： 繫結工具無法載入。JAR 程式庫

繫結程式庫產生器無法載入。JAR 程式庫。

#### <a name="possible-causes"></a>可能原因

部分。無法載入的 Java 工具使用 （透過 [工具]，像是 Proguard 等） 的程式碼混淆的 JAR 程式庫。 因為我們的工具會利用 Java 反射和工程文件庫的 ASM 位元組程式碼，這些相依的工具可能會拒絕混亂的程式庫，雖然可能會通過 Android 執行階段工具。 因應措施是手動繫結這些程式庫，而不是使用繫結產生器。



### <a name="problem-missing-c-types-in-generated-output"></a>問題： 遺漏的C#中產生的輸出型別。

繫結 **.dll**所建置，但遺漏一些 Java 類型，或產生C#不會建立來源，這是因為發生錯誤，指出有遺漏的類型。

#### <a name="possible-causes"></a>可能的原因：

如下所示，可能會發生此錯誤，由多種原因造成：

-   要繫結的程式庫可能會參考第二個的 Java 程式庫。 如果繫結的程式庫的公用 API 會使用型別，從第二個程式庫，您必須參考第二個程式庫的受管理繫結。

-   可以插入文件庫時，是因為 Java 反射，類似於上述造成未預期的中繼資料載入程式庫載入錯誤的原因。 Xamarin.Android 的工具目前無法解決這種情況。 在此情況下，程式庫必須以手動方式繫結。

-   無法載入組件時應該有的.NET 4.0 執行階段時發生錯誤。 已修正此問題在.NET 4.5 執行階段。

-   Java 可讓衍生自非公用類別的公用類別，但這不支援在.NET 中。 因為繫結產生器不會產生非公用類別的繫結，例如無法正確地產生這些衍生的類別。 若要修正此問題，請移除這些衍生的類別，使用中的移除節點的中繼資料項目**Metadata.xml**，或修正正在非公用類別公開的中繼資料。 雖然第二種解決方案將會建立繫結，讓C#來源會建置，不應該使用非公用類別。

    例如: 

    ```xml
    <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
        name="visibility">public</attr>
    ```

-   混淆 Java 程式庫的工具可能會干擾 Xamarin.Android 繫結產生器，而且它能夠產生C#包裝函式類別。 下列程式碼片段示範如何更新**Metadata.xml**至 unobfuscate 類別名稱：

    ```xml
    <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
        name="obfuscated">false</attr>
    ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>問題： 產生的C#不會建立來源，這是因為參數類型不符

產生C#來源不會建置。 覆寫方法的參數類型不相符。

#### <a name="possible-causes"></a>可能的原因：

Xamarin.Android 包含各種不同的對應至列舉中的 Java 欄位C#繫結。 在產生的繫結，這些會造成類型不相容。 若要解決此問題，從繫結產生器建立的方法簽章需要修改成使用列舉。 如需 imformation，請參閱[更正列舉](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)。

### <a name="problem-noclassdeffounderror-in-packaging"></a>在封裝中的問題： NoClassDefFoundError

`java.lang.NoClassDefFoundError` 在封裝步驟會擲回。

#### <a name="possible-causes"></a>可能的原因：

此錯誤最可能的原因是必要的 Java 程式庫必須新增至應用程式專案 (**.csproj**)。 .JAR 檔案不會自動解析。 Java 程式庫繫結永遠不會產生針對不存在於目標裝置或模擬器的使用者組件 (例如 Google Maps **maps.jar**)。 這不是如需 Android 程式庫專案的支援，作為程式庫。JAR 會內嵌在程式庫 dll。 例如： [Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>問題： 重複的自訂 EventArgs 型別

建置失敗，因為重複的自訂 EventArgs 類型。 會發生錯誤，就像這樣：

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>可能的原因：

這是因為來自共用具有相同名稱的方法的多個介面 「 接聽程式 」 類型的事件型別之間有一些衝突。 例如，如果在下列範例所示，有兩個 Java 介面，產生器會建立`DismissScreenEventArgs`兩者`MediationBannerListener`和`MediationInterstitialListener`，產生的錯誤。

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

這是預設行為，如此可避免長時間之事件引數類型的名稱。 若要避免這些衝突，某些中繼資料轉換則是必要項目。 編輯[ **Transforms\Metadata.xml** ](https://github.com/xamarin/monodroid-samples/blob/master/AdMob/AdMob/Transforms/Metadata.xml) ，並新增`argsType`屬性上的介面 （或介面方法上）：

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

會產生錯誤訊息，指出產生的類別未實作所產生的類別會實作的介面的方法。 不過，從產生的程式碼，您可以看出方法實作。

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

這是具有 covariant 傳回型別與繫結 Java 方法，就會發生問題。 在此範例中，此方法`Oauth.Signpost.Http.IHttpRequest.UnWrap()`需要傳回`Java.Lang.Object`。 不過，此方法`Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()`的傳回類型為`HttpURLConnection`。 有兩種方式可以修正此問題：

-   加入部分類別宣告`HttpURLConnectionRequestAdapter`，並明確地實作`IHttpRequest.Unwrap()`:

    ```csharp
    namespace Oauth.Signpost.Basic {
        partial class HttpURLConnectionRequestAdapter {
            Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
                return Unwrap();
            }
        }
    }
    ```

-   從產生移除共變數C#程式碼。 這牽涉到新增下列轉換**Transforms\Metadata.xml**這會產生C#的程式碼擁有傳回型別`Java.Lang.Object`:

    ```xml
    <attr
        path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
        name="managedReturn">Java.Lang.Object
    </attr>
    ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>問題： 命名衝突，在內部的類別 / 屬性

衝突的可見性繼承的物件。

在 Java 中，您不需要在衍生的類別都有相同的可視性，做為其父系。 Java 會只是修正這個問題，您。 在C#，具有明確，因此您必須先確定階層中的所有類別都有適當的可見性。 下列範例示範如何將 Java 套件名稱，從`com.evernote.android.job`至`Evernote.AndroidJob`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>問題： A **.so**繫結所需的程式庫是未載入

某些繫結專案也會取決於中的功能 **.so**程式庫。 您可 Xamarin.Android 不會自動載入 **.so**程式庫。 若要讓 JNI 呼叫和錯誤訊息的已包裝的 Java 程式碼執行時，將會失敗 Xamarin.Android _java.lang.UnsatisfiedLinkError： 找不到的原生方法：_ 會出現在出 logcat，應用程式。

此修正方法是手動載入 **.so**藉由呼叫的程式庫`Java.Lang.JavaSystem.LoadLibrary`。 例如假設 Xamarin.Android 專案具有共用媒體櫃**libpocketsphinx_jni.so**包含在繫結專案的建置動作**EmbeddedNativeLibrary**，下列程式碼片段（執行之前使用共用的程式庫） 會載入 **.so**程式庫：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>總結

在本文中，我們會列出與 Java 繫結相關聯的常見疑難排解問題，並說明如何解決這些問題。


## <a name="related-links"></a>相關連結

- [程式庫專案](http://developer.android.com/tools/projects/index.html#LibraryProjects)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [啟用診斷輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [適用於 Android 開發人員的 Xamarin](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
