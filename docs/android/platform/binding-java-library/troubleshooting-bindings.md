---
title: 對繫結進行疑難排解
description: 本文總結了生成綁定時可能發生的伺服器常見錯誤,以及可能的原因和解決這些問題的建議方法。
ms.prod: xamarin
ms.assetid: BB81FCCF-F7BF-4C78-884E-F02C49AA819A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 0c273797d7512f062260e49e0f71fdd1132f037b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78293029"
---
# <a name="troubleshooting-bindings"></a>對繫結進行疑難排解

_本文總結了生成綁定時可能發生的伺服器常見錯誤,以及可能的原因和解決這些問題的建議方法。_

## <a name="overview"></a>概觀

綁定 Android 庫 **(.aar**或 **.jar)** 檔很少是一件簡單的事情;它通常需要額外的努力來緩解由於 JAVA 和 .NET 之間的差異而導致的問題。
這些問題將阻止 Xamarin.Android 綁定 Android 庫,並在生成日誌中將自己作為錯誤消息呈現。 本指南將提供一些解決問題的提示,列出一些更常見的問題/方案,並提供成功綁定 Android 庫的可能解決方案。

綁定現有 Android 庫時,需要牢記以下幾點:

- &ndash;**庫的外部依賴項**Android 庫所需的任何 JAva 依賴項都必須作為**參考Jar**或**嵌入式參考Jar**包含在 Xamarin.Android 專案中。

- **Android 庫所針對**&ndash;的 Android API 級別不可能「降級」Android API 級別;確保 Xamarin.Android 綁定專案的目標與 Android 庫相同的 API 級別(或更高)。

- 如果 Android 庫的 JDK 版本與 Xamarin.Android 正在使用的 JDK 版本不同,則**用於打包 Android 庫**&ndash;綁定錯誤的 Android JDK 版本可能會發生。 如果可能,請使用安裝 Xamarin.Android 時使用的 JDK 版本重新編譯 Android 庫。

使用繫結 Xamarin.Android 函式庫解決問題的第一步是啟用[診斷 MSBuild 輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)。
啟用診斷輸出後,重建 Xamarin.Android 綁定專案並檢查生成日誌,以查找問題原因的線索。

反編譯 Android 庫並檢查 Xamarin.Android 試圖綁定的類型和方法也很有説明。 本指南稍後將詳細介紹這一點。

## <a name="decompiling-an-android-library"></a>正在停用 Android 函式庫

檢查 JAVA 類的類和方法可以提供有價值的資訊,有助於綁定庫。
[JD-GUI](http://jd.benow.ca/)是一個圖形實用程式,可以從 JAR 中包含的**CLASS**檔中顯示 Java 原始程式碼。 它可以作為獨立應用程式運行,也可以作為 IntelliJ 或 Eclipse 的外掛程式運行。

要取消編譯 Android 函式庫,將開啟 **。JAR**檔與 JAVA 解編譯。 如果函式庫為 **。AAR**檔,有必要從存檔檔中提取檔案**類.jar。** 以下是使用 JD-GUI 分析[畢卡索](https://square.github.io/picasso/)JAR 的範例螢幕截圖:

![使用 JAVA 編譯分析畢卡索-2.5.2.jar](troubleshooting-bindings-images/troubleshoot-bindings-01.png)

取消編譯 Android 庫後,請檢查原始程式碼。 一般來說,尋找 :

- **具有**&ndash;混淆類混淆特徵的類包括:

  - 類別名稱包括**$**,**$.類別**
  - 類別名稱完全受小寫字元(即**a.類別**)的損壞      

- **ReferenceJar****未參考函式庫的語句 識別未引用的庫,並將這些依賴項新增到 Xamarin.Android 綁定專案中,並帶有引用Jar的生成操作或 嵌入的參考Jar 。 `import`****EmbedddedReferenceJar** &ndash; ** **

> [!NOTE]
> 可能禁止取消編譯 Java 庫,或受基於當地法律或 JAVA 庫發佈許可的法律限制。 如有必要,在嘗試取消編譯 Java 庫並檢查原始程式碼之前,應徵到法律專業人員的服務。

## <a name="inspect-apixml"></a>檢查 API。Xml

作為建構結合的連線項目的一部份,Xamarin.Android 將產生 XML 檔名**obj/Debug/api.xml**:

![在 obj/除錯下產生的 api.xml](troubleshooting-bindings-images/troubleshoot-bindings-02.png)

此檔提供了 Xamarin.Android 正在嘗試綁定的所有 Java API 的清單。 此文件的內容可幫助識別任何缺少的類型或方法,重複綁定。 儘管檢查此檔既繁瑣又耗時,但它可以提供導致任何綁定問題的線索。 例如 **,api.xml**可能會顯示屬性正在返回不適當的類型,或者有兩種類型共用相同的託管名稱。

## <a name="known-issues"></a>已知問題

本節將列出嘗試綁定 Android 庫時發生的一些常見錯誤消息或癥狀。

### <a name="problem-java-version-mismatch"></a>問題:JAVA 版本不匹配

有時不會生成類型,或者可能會發生意外崩潰,因為與編譯庫相比,您使用的是較新的或較舊版本的 JAVA。 使用 Xamarin.Android 專案使用的 JDK 版本重新編譯 Android 庫。

### <a name="problem-at-least-one-java-library-is-required"></a>問題:至少需要一個 JAVA 庫

即使 收到錯誤"至少需要一個 Java 庫",即使 .已添加 JAR。

#### <a name="possible-causes"></a>可能的原因:

確保產生操作設定為`EmbeddedJar`。 由於有多個生成操作。JAR 檔(`InputJar``EmbeddedJar`如`ReferenceJar``EmbeddedReferenceJar`, 和 ),綁定產生器無法自動猜測預設情況下要使用的檔案。 有關產生操作的詳細資訊,請參閱[產生操作](~/android/platform/binding-java-library/index.md)。

### <a name="problem-binding-tools-cannot-load-the-jar-library"></a>問題:繫結工具無法載入 。JAR 函式庫

繫結庫產生器無法載入 。JAR 庫。

#### <a name="possible-causes"></a>可能的原因

一些。使用代碼混淆(透過 Proguard 等工具)的 JAR 庫不能由 Java 工具載入。 由於我們的工具使用 JAVA 反射和 ASM 位元組碼工程庫,因此這些依賴工具可能會拒絕模糊化庫,而 Android 運行時工具可能會通過。 解決方法是手動綁定這些庫,而不是使用綁定生成器。

### <a name="problem-missing-c-types-in-generated-output"></a>問題:生成的輸出中缺少 C# 類型。

綁定 **.dll**生成,但遺漏了某些 Java 類型,或者生成的 C# 源由於錯誤導致缺少類型而未生成。

#### <a name="possible-causes"></a>可能的原因:

由於以下幾個原因,可能會出現此錯誤:

- 綁定的庫可能引用第二個 JAVA 庫。 如果綁定庫的公共 API 使用第二個庫中的類型,則還必須引用第二個庫的託管綁定。

- 由於 Java 反射,可能會注入庫,這與上述庫載入錯誤的原因類似,從而導致元數據意外載入。 Xamarin.安卓的工具目前無法解決這種情況。 在這種情況下,必須手動綁定庫。

- .NET 4.0 運行時中有一個 Bug,在應該載入程式集時無法載入它。 此問題已在 .NET 4.5 運行時中修復。

- Java 允許從非公共類派生公共類,但在 .NET 中不支援此類。 由於綁定生成器不為非公共類生成綁定,因此無法正確生成此類派生類。 要解決此問題,請使用**Metadata.xml**中的 remove-node 刪除派生類的元數據條目,或者修復使非公共類成為公共類的元數據。 儘管後一個解決方案將創建綁定,以便生成 C# 源,但不應使用非公共類。

  例如：

  ```xml
  <attr path="/api/package[@name='com.some.package']/class[@name='SomeClass']"
      name="visibility">public</attr>
  ```

- 混淆 Java 庫的工具可能會干擾 Xamarin.Android 綁定生成器及其生成 C# 包裝類的能力。 以下代碼片段如何更新**Metadata.xml**以取消繫結類別名稱:

  ```xml
  <attr path="/api/package[@name='{package_name}']/class[@name='{name}']"
      name="obfuscated">false</attr>
  ```

### <a name="problem-generated-c-source-does-not-build-due-to-parameter-type-mismatch"></a>問題:由於參數類型不匹配,生成的 C# 無法產生

生成的 C# 源不會生成。 重寫方法的參數類型不匹配。

#### <a name="possible-causes"></a>可能的原因:

Xamarin.Android 包括映射到 C# 綁定中枚舉的各種 Java 欄位。 這可能導致生成的綁定中的類型不相容。 要解決此問題,需要修改從綁定生成器創建的方法簽名才能使用枚舉。 有關詳細資訊,請參閱[更正枚舉](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md)。

### <a name="problem-noclassdeffounderror-in-packaging"></a>問題:包裝中無類DefFound錯誤

`java.lang.NoClassDefFoundError`被扔進包裝步驟。

#### <a name="possible-causes"></a>可能的原因:

出現此錯誤的最可能原因是需要將強制 JAVA 庫添加到應用程式專案 **(.csproj**)。 .JAR 檔不會自動解析。 Java 庫綁定並不總是針對目標設備或模擬器中不存在的使用者程式集(如 Google**地圖.jar)** 生成。 Android 庫項目支持情況並非如此,因為該庫。JAR 嵌入在庫 dll 中。 例如[:Bug 4288](https://bugzilla.xamarin.com/show_bug.cgi?id=4288)

### <a name="problem-duplicate-custom-eventargs-types"></a>問題:重複的自訂事件阿格型態

由於重複的自定義事件阿格類型,生成失敗。 發生錯誤:

```shell
error CS0102: The type `Com.Google.Ads.Mediation.DismissScreenEventArgs' already contains a definition for `p0'
```

#### <a name="possible-causes"></a>可能的原因:

這是因為來自多個共用具有相同名稱的方法的介面"偵聽器"類型的事件類型之間存在一些衝突。 例如,如果以下示例中所示有兩個 Java 介面,則生成器將`DismissScreenEventArgs``MediationBannerListener`同時`MediationInterstitialListener`為和創建,從而導致錯誤。

```java
// Java:
public interface MediationBannerListener {
    void onDismissScreen(MediationBannerAdapter p0);
}
public interface MediationInterstitialListener {
    void onDismissScreen(MediationInterstitialAdapter p0);
}
```

這是設計,以避免事件參數類型的冗長名稱。 為了避免這些衝突,需要進行一些元數據轉換。 編輯**\中繼資料.xml**並在任`argsType`一介面(或介面方法上)上新增屬性:

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

### <a name="problem-class-does-not-implement-interface-method"></a>問題:類不實現介面方法

生成一條錯誤消息,指示生成的類不實現生成類實現的介面所需的方法。 但是,查看生成的代碼,您可以看到該方法已實現。

下面是錯誤的範例:

```shell
obj\Debug\generated\src\Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.cs(8,23):
error CS0738: 'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter' does not
implement interface member 'Oauth.Signpost.Http.IHttpRequest.Unwrap()'.
'Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.Unwrap()' cannot implement
'Oauth.Signpost.Http.IHttpRequest.Unwrap()' because it does not have the matching
return type of 'Java.Lang.Object'
```

#### <a name="possible-causes"></a>可能的原因:

這是具有共變數返回類型的綁定 Java 方法時出現的問題。 這個方法`Oauth.Signpost.Http.IHttpRequest.UnWrap()`需要傳回`Java.Lang.Object`。 但是,該方法`Oauth.Signpost.Basic.HttpURLConnectionRequestAdapter.UnWrap()`的傳回類型`HttpURLConnection`為 。 有兩種方法可以解決此問題:

- 新增`HttpURLConnectionRequestAdapter`部分類別宣告 並顯示`IHttpRequest.Unwrap()`式 :

  ```csharp
  namespace Oauth.Signpost.Basic {
      partial class HttpURLConnectionRequestAdapter {
          Java.Lang.Object OauthSignpost.Http.IHttpRequest.Unwrap() {
              return Unwrap();
          }
      }
  }
  ```

- 從生成的 C# 代碼中刪除協方差。 這一個包含到以下轉換到**transform_Metadata.xml,** 這會造成產生的 C# 程式碼有`Java.Lang.Object`傳回類型:

  ```xml
  <attr
      path="/api/package[@name='oauth.signpost.basic']/class[@name='HttpURLConnectionRequestAdapter']/method[@name='unwrap']"
      name="managedReturn">Java.Lang.Object
  </attr>
  ```

### <a name="problem-name-collisions-on-inner-classes--properties"></a>問題:內部類別/屬性上的名稱衝突

繼承物件上的可見性衝突。

在 Java 中,派生類的可見性與其父類的可見性不同,則不需要它。 Java 將只為您修復它。 在 C# 中,這必須是顯式的,因此您需要確保層次結構中的所有類具有適當的可見性。 下面的範例展示如何將 Java 套件`com.evernote.android.job`名稱從`Evernote.AndroidJob`變更為 :

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

### <a name="problem-a-so-library-required-by-the-binding-is-not-loading"></a>問題:繫結所需的 **.so**函式庫未載入

某些綁定專案也可能依賴於 **.so**庫中的功能。 Xamarin.Android 可能不會自動載入 **.so**庫。 執行包裝的 Java 代碼時,Xamarin.Android 將無法進行 JNI 呼叫和錯誤訊息_java.lang.UnsinLinkError:未找到本機方法:_ 將顯示在應用程式的註銷中。

為此,修復程式是手動載入 **.so**庫,並`Java.Lang.JavaSystem.LoadLibrary`呼叫 。 例如,假設 Xamarin.Android 專案具有共用庫**libpocketsphinx_jni.因此**包含在綁定專案中,具有**嵌入式本機庫**的生成操作,以下代碼段(在使用共用庫之前執行)將載入 **.so**庫:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="summary"></a>總結

在本文中,我們列出了與 JAVA 綁定相關的常見疑難解答問題,並解釋了如何解決這些問題。

## <a name="related-links"></a>相關連結

- [圖書館專案](https://developer.android.com/tools/projects/index.html#LibraryProjects)
- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [啟用診斷輸出](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output)
- [Xamarin 為 Android 開發人員](~/android/get-started/java-developers.md)
- [JD-GUI](http://jd.benow.ca/)
