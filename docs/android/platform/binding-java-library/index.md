---
title: 繫結的 Java 程式庫
description: Android 的社群有許多您可能想要使用您的應用程式; 中的 Java 程式庫本指南說明如何建立繫結的程式庫併入 Xamarin.Android 應用程式的 Java 程式庫。
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/01/2017
ms.openlocfilehash: 3c2ed92da07519516db94697bbeaac9f328baa22
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="binding-a-java-library"></a>繫結的 Java 程式庫

_Android 的社群有許多您可能想要使用您的應用程式; 中的 Java 程式庫本指南說明如何建立繫結的程式庫併入 Xamarin.Android 應用程式的 Java 程式庫。_

## <a name="overview"></a>總覽

適用於 Android 的協力廠商程式庫生態系統是大量。 因為這個緣故，它經常使用合理比建立一個新的現有 Android 程式庫。 Xamarin.Android 提供兩種方式使用這些程式庫：

-   建立*繫結的程式庫*可自動包裝程式庫的 C# 的包裝函式，因此您可以叫用 C# 呼叫透過 Java 程式碼。

-   使用*Java 原生介面*(*JNI*) 直接叫用在 Java 程式庫程式碼中的呼叫。 JNI 是在 Java 程式碼呼叫，並由原生應用程式或程式庫呼叫的程式設計架構。

本指南說明第一個選項： 如何建立*繫結的程式庫*包裝一或多個現有的 Java 程式庫可以連結至您的應用程式中的組件。 如需使用 JNI 的詳細資訊，請參閱[使用 JNI](~/android/platform/java-integration/working-with-jni.md)。

Xamarin.Android 實作繫結使用*管理可呼叫包裝函式*(*MCW*)。 MCW 是 JNI 橋接器時，managed 程式碼可以叫用的 Java 程式碼使用。 子類別化 Java 類型及覆寫虛擬方法，在 Java 型別上的受管理的可呼叫包裝函式也會提供支援。 同樣地，每當 Android 的執行階段 （圖案） 程式碼想要叫用 managed 程式碼，它會以透過另一個 JNI 橋接器已知為 Android 的可呼叫包裝函式 (ACW)。 這[架構](~/android/internals/architecture.md)如下圖所示：

[![Android JNI 橋接器架構](images/architecture.png)](images/architecture.png#lightbox)

包含受管理的可呼叫包裝函式的 Java 類型的組件繫結程式庫。 例如，以下是 Java 型別`MyClass`，我們想要繫結的文件庫中自動換行：

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

我們會產生一個繫結文件庫之後**d**包含`MyClass`，我們可以將它執行個體化，並在其上呼叫方法，從 C#:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

若要建立此繫結文件庫，您可以使用 Xamarin.Android *Java 繫結的程式庫*範本。 產生的繫結專案 MCW 類別中，建立.NET 組件**d**檔案，並內嵌在其中的 Android 程式庫專案的資源。 您也可以為 Android 封存建立繫結的程式庫 (。AAR) 檔案和 Eclipse Android 程式庫專案。 藉由參考產生繫結的程式庫 DLL 的組件，您可以在 Xamarin.Android 專案中重複使用現有的 Java 程式庫。

當您在繫結程式庫參考類型時，您必須使用繫結文件庫的命名空間。 一般來說，您會加入`using`指示詞上方的 C# 來源檔案即 Java 封裝名稱的.NET 命名空間版本。 例如，如果您的繫結名稱 Java 封裝**d**如下所示：

```csharp
com.company.package
```

然後您會將下列`using`中繫結類型在 C# 原始程式檔的存取最上方的陳述式**d**檔案：

```csharp
using Com.Company.Package;
```


在繫結現有的 Android 程式庫，請務必牢記下列要點：

* **是否有任何文件庫的外部相依性？** &ndash; Android 程式庫所需的任何 Java 相依性必須包含在 Xamarin.Android 專案**ReferenceJar**或**EmbeddedReferenceJar**。 任何原生組件必須新增至繫結專案**EmbeddedNativeLibrary**。  

* **哪個版本的 Android 應用程式開發介面嗎？ Android 程式庫目標** &ndash; 不可能 「 降級 」 的 Android API 層級;請確定繫結 Xamarin.Android 專案的目標相同的 API 層級 （或更高） 為 Android 程式庫。

* **用來編譯程式庫的 JDK 什麼版本？** &ndash; 如果 Android 程式庫所建置 Xamarin.Android JDK 比使用中的不同版本，可能發生繫結錯誤。 可能的話，請重新編譯使用相同版本的 JDK，以供您安裝的 Xamarin.Android Android 程式庫。


## <a name="build-actions"></a>建置動作

當您建立的繫結的程式庫時，您將設定*建置動作*上**d**或。AAR 檔案，您將加入到繫結的程式庫專案&ndash;每個建置動作會決定如何**d**或。AAR 檔案將會是內嵌至 （或所參考） 您繫結的程式庫。 下列清單摘要說明這些建置動作：

* `EmbeddedJar` &ndash; 將內嵌**d**到產生的繫結程式庫 DLL，做為內嵌資源。 這是最簡單，最常使用的建置動作。 當您想要使用這個選項**d**自動編譯成位元組程式碼，並封裝到繫結的程式庫。

* `InputJar` &ndash; 不會內嵌**d**至產生的繫結程式庫。DLL。 您的繫結的程式庫。DLL 將會有相依性這樣**d**在執行階段。 使用此選項，當您不想要加入**d**您繫結文件庫中 （例如，授權理由）。 如果您使用此選項時，您必須確定輸入**d**執行您的應用程式在裝置上使用。

* `LibraryProjectZip` &ndash; 將內嵌。AAR 檔案產生的繫結程式庫。DLL。 這是 EmbeddedJar，類似，不同之處在於您可以在繫結中存取資源 （以及程式碼）。AAR 檔案。 使用此選項，當您想要將內嵌。AAR 到您的繫結程式庫。

* `ReferenceJar` &ndash; 指定的參考**d**： 參考**d**是**d**您繫結的其中一種**d**或。AAR 檔案而定。 此參考**d**只能用來滿足編譯階段相依性。 當您使用此建置動作時，C# 繫結不會建立參考**d** ，它將不會內嵌在產生的繫結程式庫。DLL。 使用此選項，當您將繫結的程式庫參考**d**但未如此做尚未。 此建置動作可用於封裝的多個**d**s （和/或。AARs) 成多個具有相依性的繫結程式庫。

* `EmbeddedReferenceJar` &ndash; 將內嵌參考**d**至產生的繫結程式庫。DLL。 當您想要建立 C# 這兩個輸入的繫結時使用此組建動作**d** （或。AAR) 和所有其參考**d**(s) 您繫結文件庫中。

* `EmbeddedNativeLibrary` &ndash; 嵌入原生**.so**繫結。 用於此組建動作**.so**檔案所需的**d**檔案所要繫結。 可能需要手動載入**.so**之前執行的 Java 程式庫的程式碼程式庫。 說明如下。

動作中的下列指南中詳細說明這些組建。

此外，下列的建置動作使用，以匯入 Java 應用程式開發介面文件，並將其轉換成 C# XML 文件：

* `JavaDocJar` 用來指向 Java 程式庫，並符合 Maven 封裝樣式的 Javadoc 封存 Jar (通常`FOOBAR-javadoc**.jar**`)。
* `JavaDocIndex` 用來指向`index.html`中的應用程式開發介面參考文件 HTML 檔案。
* `JavaSourceJar` 用來補充`JavaDocJar`以第一次從來源中產生 JavaDoc，然後再將結果視為`JavaDocIndex`，Java 文件庫，並符合 Maven 封裝樣式 (通常`FOOBAR-sources**.jar**`)。

應用程式開發介面文件應該是從 Java8、 Java7 或 Java6 SDK （它們是完全不同的格式），預設 doclet 或 DroidDoc 樣式。

## <a name="including-a-native-library-in-a-binding"></a>包含繫結中的原生程式庫

可能需要包含**.so** Xamarin.Android 繫結專案一部分的繫結 Java 文件庫中的程式庫。 若要讓 JNI 呼叫和錯誤訊息的已包裝的 Java 程式碼執行時，將會失敗 Xamarin.Android _java.lang.UnsatisfiedLinkError： 找不到原生方法：_會出現在出 logcat，應用程式。

此修正方法是手動載入**.so**呼叫的程式庫`Java.Lang.JavaSystem.LoadLibrary`。 如需範例假設 Xamarin.Android 專案已共用程式庫**libpocketsphinx_jni.so**包含在繫結專案的建置動作**EmbeddedNativeLibrary**、 下（使用共用媒體櫃之前執行） 的程式碼片段會載入**.so**程式庫：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>調整為 C 的 Java 應用程式開發介面&eparsl;

一些 Java 語言和模式來對應至.NET 模式，將會變更 Xamarin.Android 繫結產生器。 下列清單將描述如何對應至 C# 的 Java /.NET:

-   _Setter/Getter 方法_在 Java_屬性_.NET 中。

-   _欄位_在 Java_屬性_.NET 中。

-   _接聽程式/接聽程式介面_在 Java_事件_.NET 中。 回呼介面中方法的參數會由`EventArgs`子類別。

-   A_靜態巢狀類別_在 Java 中為_巢狀類別_.NET 中。

-   _內部類別_在 Java 中為_巢狀類別_與 C# 中的執行個體建構函式。



## <a name="binding-scenarios"></a>繫結案例

下列繫結案例的指引可協助您將 Java 程式庫 （或程式庫） 納入到應用程式的繫結：

-   [繫結。JAR](~/android/platform/binding-java-library/binding-a-jar.md)是建立繫結的程式庫的逐步解說**d**檔案。

-   [繫結。AAR](~/android/platform/binding-java-library/binding-an-aar.md)是建立繫結的程式庫的逐步解說。AAR 檔案。 讀取此逐步解說來了解如何將繫結 Android Studio 文件庫。

-   [繫結的 Eclipse 程式庫專案](~/android/platform/binding-java-library/binding-a-library-project.md)為 Android 程式庫專案，建立繫結的程式庫的逐步解說。 讀取此逐步解說來了解如何將繫結 Eclipse Android 程式庫專案。

-   [自訂繫結](~/android/platform/binding-java-library/customizing-bindings/index.md)說明如何進行繫結，以解決建置錯誤和圖形產生應用程式開發介面，使其更多的手動修改"C#-例如"。

-   [疑難排解繫結](~/android/platform/binding-java-library/troubleshooting-bindings.md)列出常見的繫結錯誤狀況，說明可能的原因，並建議解決這些錯誤。


## <a name="related-links"></a>相關連結

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [GAPI 中繼資料](http://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [使用原生程式庫](~/android/platform/native-libraries.md)
