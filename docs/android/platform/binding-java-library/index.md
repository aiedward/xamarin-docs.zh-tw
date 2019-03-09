---
title: 繫結 Java 程式庫
description: Android 的社群有許多您可能想要在您的應用程式; 中的 Java 程式庫本指南說明如何藉由建立繫結程式庫併入您的 Xamarin.Android 應用程式的 Java 程式庫。
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
---

# <a name="binding-a-java-library"></a>繫結 Java 程式庫

_Android 的社群有許多您可能想要在您的應用程式; 中的 Java 程式庫本指南說明如何藉由建立繫結程式庫併入您的 Xamarin.Android 應用程式的 Java 程式庫。_

## <a name="overview"></a>總覽

適用於 Android 的協力廠商程式庫生態系統將會很可觀。 因此，經常合理使用現有的 Android 程式庫，而不是建立一個新。 Xamarin.Android 提供兩種方式使用這些程式庫：

-   建立*繫結程式庫*會自動換行的程式庫C#包裝函式，因此您可以叫用 Java 程式碼透過C#的呼叫。

-   使用*Java Native Interface* (*JNI*) 來直接叫用在 Java 程式庫程式碼的呼叫。 JNI 是一個可讓 Java 程式碼呼叫，並可由原生應用程式或程式庫呼叫的程式設計架構。

本指南將說明第一個選項： 如何建立*繫結程式庫*包裝一或多個現有的 Java 程式庫至組件，您可以連結至您的應用程式中。 如需使用 JNI 的詳細資訊，請參閱 <<c0> [ 使用 JNI](~/android/platform/java-integration/working-with-jni.md)。

Xamarin.Android 會藉由使用實作繫結*受管理的可呼叫包裝函式*(*MCW*)。 MCW 是 managed 程式碼需要叫用 Java 程式碼時，會使用 JNI 橋接器。 子類別化的 Java 類型及覆寫虛擬方法，在 Java 型別上的受管理的可呼叫包裝函式也會提供支援。 同樣地，每當 Android 執行階段 （藝術） 程式碼想要叫用 managed 程式碼，它會透過已知為 Android 的可呼叫包裝函式 (ACW) 的另一個 JNI 橋接器。 這[架構](~/android/internals/architecture.md)如下圖所示：

[![Android 的 JNI 橋接器架構](images/architecture.png)](images/architecture.png#lightbox)

繫結程式庫是包含受管理的可呼叫包裝函式的 Java 類型的組件。 例如，以下是 Java 型別`MyClass`，我們想要將包裝在繫結程式庫：

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

我們產生的繫結程式庫之後 **.jar** ，其中包含`MyClass`，我們可以加以具現化，並呼叫方法，從C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

若要建立此繫結程式庫，您可以使用 Xamarin.Android *Java 繫結程式庫*範本。 產生的繫結專案 MCW 類別中，建立.NET 組件 **.jar**檔案，並內嵌在其中的 Android 程式庫專案的資源。 您也可以建立 Android 封存 」 繫結程式庫 (。AAR) 檔案，而且 Eclipse Android 程式庫專案。 藉由參考所產生的繫結程式庫 DLL 組件，您可以在您的 Xamarin.Android 專案中重複使用現有的 Java 程式庫。

當您在您的繫結程式庫參考類型時，您必須使用您的繫結程式庫的命名空間。 一般而言，您將新增`using`頂端的指示詞您C#的來源檔案也就是.NET 命名空間名稱的版本 Java 套件。 例如，如果您繫結名稱的 Java 封裝 **.jar**所示：

```csharp
com.company.package
```

然後您會將下列資料放`using`頂端的陳述式您C#原始程式檔來存取繫結中的型別 **.jar**檔案：

```csharp
using Com.Company.Package;
```


在繫結現有 Android 程式庫，就必須牢記下列要點：

* **是否有任何文件庫中的外部相依性？** &ndash; Android 程式庫所需的任何 Java 相依性必須納入為 Xamarin.Android 專案**ReferenceJar**或是**EmbeddedReferenceJar**。 任何原生組件必須加入至與繫結專案**EmbeddedNativeLibrary**。  

* **Android API 的什麼版本並 Android 程式庫目標嗎？** &ndash; 就無法 「 降級 」 的 Android API 層級;請確定 Xamarin.Android 繫結專案的目標相同的 API 層級 （或更新版本） 為 Android 程式庫。

* **用來編譯程式庫的 JDK 哪些版本？** &ndash; 如果 Android 程式庫所建置使用不同版本的 JDK，比使用 Xamarin.Android，可能會繫結錯誤。 可能的話，請重新編譯使用相同版本的 JDK，以供您的 Xamarin.Android 安裝的 Android 程式庫。


## <a name="build-actions"></a>建置動作

當您建立繫結程式庫時，您會設定*建置動作*上 **.jar**或。AAR 檔案合併到您的繫結程式庫專案&ndash;每個組建動作會決定如何 **.jar**或。AAR 檔案將會內嵌到 （或所參考） 您繫結程式庫。 下列清單摘要說明這些建置動作：

* `EmbeddedJar` &ndash; 內嵌 **.jar**做為內嵌資源產生的繫結程式庫 dll。 這是最簡單且最常用的建置動作。 如果您想要使用此選項 **.jar**自動編譯為位元組程式碼，並封裝成繫結程式庫。

* `InputJar` &ndash; 不會內嵌 **.jar**到產生的繫結程式庫。DLL。 您的繫結程式庫。DLL 將會有相依性這 **.jar**在執行階段。 使用此選項，當您不想要包含 **.jar**在您的繫結程式庫 （例如，基於授權理由）。 如果您使用此選項時，您必須確定輸入 **.jar**可在執行您的應用程式的裝置上取得。

* `LibraryProjectZip` &ndash; 內嵌。AAR 檔案產生的繫結程式庫。DLL。 這是 EmbeddedJar，類似，不同之處在於您可以在繫結中存取資源 （以及程式碼）。AAR 檔案。 使用此選項，當您想要內嵌。AAR 到您的繫結程式庫。

* `ReferenceJar` &ndash; 指定的參考 **.jar**： 參考 **.jar**會 **.jar**繫結的其中一個 **.jar**或。AAR 檔案而定。 此參考 **.jar**只能用來滿足編譯時間相依性。 當您使用此建置動作，C#繫結不會建立參考 **.jar** ，它將不會內嵌在產生的繫結程式庫。DLL。 使用此選項，當您將繫結程式庫參考 **.jar**但未完成此動作尚未。 此建置動作可用於封裝多個 **.jar**s （和/或。AARs) 成多個互相依存的繫結程式庫。

* `EmbeddedReferenceJar` &ndash; 將內嵌參考 **.jar**到產生的繫結程式庫。DLL。 使用此建置動作，當您想要建立C#這兩個輸入的繫結 **.jar** （或）。AAR) 以及其參考的所有 **.jar**(s) 在您的繫結程式庫中。

* `EmbeddedNativeLibrary` &ndash; 嵌入原生 **.so**繫結。 此建置動作適用於 **.so**所需的檔案 **.jar**檔案繫結。 您可能必須手動載入 **.so**之前執行程式碼從 Java 程式庫的程式庫。 如下所述。

這些動作會說明下列指南中的更詳細的組建。

此外，使用以下的建置動作說明匯入 Java API 文件，並轉換到C#XML 文件：

* `JavaDocJar` 用來指向 Javadoc 封存檔 Jar 符合 Maven 套件樣式之 Java 程式庫 (通常`FOOBAR-javadoc**.jar**`)。
* `JavaDocIndex` 用來指向`index.html`內的 API 參考文件 HTML 檔案。
* `JavaSourceJar` 用來補充`JavaDocJar`，以第一次從來源中產生 JavaDoc 並再將結果視為`JavaDocIndex`，符合 Maven Java 程式庫封裝樣式 (通常`FOOBAR-sources**.jar**`)。

API 文件應該是從 Java8、 Java7 或 Java6 SDK （它們是完全不同的格式），預設 doclet 或 DroidDoc 樣式。

## <a name="including-a-native-library-in-a-binding"></a>繫結中包含原生程式庫

您可能必須包含 **.so** Xamarin.Android 繫結專案一部分的繫結 Java 程式庫中的程式庫。 若要讓 JNI 呼叫和錯誤訊息的已包裝的 Java 程式碼執行時，將會失敗 Xamarin.Android _java.lang.UnsatisfiedLinkError:找不到的原生方法：_ 會出現在出 logcat，應用程式。

此修正方法是手動載入 **.so**藉由呼叫的程式庫`Java.Lang.JavaSystem.LoadLibrary`。 例如假設 Xamarin.Android 專案具有共用媒體櫃**libpocketsphinx_jni.so**包含在繫結專案的建置動作**EmbeddedNativeLibrary**，下列程式碼片段（執行之前使用共用的程式庫） 會載入 **.so**程式庫：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>調整到 C 的 Java Api&eparsl;

Xamarin.Android 繫結產生器將會變更一些 Java 慣用語和模式，以對應至.NET 模式。 下列清單將描述如何將 Java 對應到C#/.NET:

-   _Setter/Getter 方法_在 Java 中所_屬性_在.NET 中。

-   _欄位_在 Java 中所_屬性_在.NET 中。

-   _接聽程式/接聽程式介面_在 Java 中所_事件_在.NET 中。 將所代表的回呼介面中的方法參數`EventArgs`子類別。

-   A_靜態巢狀類別_在 Java 中是_巢狀類別_在.NET 中。

-   _內部類別_在 Java 中是_巢狀類別_使用中的執行個體建構函式C#。



## <a name="binding-scenarios"></a>繫結案例

下列繫結案例的指引可協助您將 Java 程式庫 （或程式庫） 的整合到您的應用程式繫結：

-   [繫結。JAR](~/android/platform/binding-java-library/binding-a-jar.md)是用於建立適用於繫結程式庫的逐步解說 **.jar**檔案。

-   [繫結。AAR](~/android/platform/binding-java-library/binding-an-aar.md)是用於建立適用於繫結程式庫的逐步解說。AAR 檔案。 閱讀本逐步解說，了解如何將繫結 Android Studio 中的程式庫。

-   [繫結 Eclipse 程式庫專案](~/android/platform/binding-java-library/binding-a-library-project.md)是 Android 程式庫專案，建立繫結程式庫的逐步解說。 閱讀本逐步解說，了解如何將繫結 Eclipse Android 程式庫專案。

-   [自訂繫結](~/android/platform/binding-java-library/customizing-bindings/index.md)說明如何進行手動修改，以解決建置錯誤和圖形產生的 API，使其更多的繫結 」C#-例如"。

-   [疑難排解繫結](~/android/platform/binding-java-library/troubleshooting-bindings.md)列出常見的繫結錯誤狀況，說明可能的原因，並提供解決這些錯誤的建議。


## <a name="related-links"></a>相關連結

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [GAPI 中繼資料](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [使用原生程式庫](~/android/platform/native-libraries.md)
