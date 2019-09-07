---
title: 繫結 Java 程式庫
description: Android 社區有許多您可能想要在應用程式中使用的 JAVA 程式庫;本指南說明如何藉由建立系結程式庫，將 JAVA 程式庫整合到您的 Xamarin Android 應用程式中。
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: e829c953278d8edeb697d27da8e3707ee1c91784
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757590"
---
# <a name="binding-a-java-library"></a>繫結 Java 程式庫

_Android 社區有許多您可能想要在應用程式中使用的 JAVA 程式庫;本指南說明如何藉由建立系結程式庫，將 JAVA 程式庫整合到您的 Xamarin Android 應用程式中。_

## <a name="overview"></a>總覽

適用于 Android 的協力廠商程式庫生態系統很龐大。 因此，使用現有的 Android 程式庫通常是合理的，而不是建立新的。 Xamarin 提供兩種使用這些程式庫的方式：

- 建立系結連結*庫*，以使用包裝函C#式自動包裝程式庫，讓您C#可以透過呼叫叫用 JAVA 程式碼。

- 使用*JAVA 原生介面*（*JNI*）直接叫用 java 程式庫程式碼中的呼叫。 JNI 是一種程式設計架構，可讓 JAVA 程式碼呼叫並由原生應用程式或程式庫呼叫。

本指南說明第一個選項：如何建立系結連結*庫*，將一或多個現有的 JAVA 程式庫包裝到您的應用程式中可以連結的元件。 如需使用 JNI 的詳細資訊，請參閱使用[JNI](~/android/platform/java-integration/working-with-jni.md)。

Xamarin 會使用 Managed 可呼叫*包裝*函式（*MCW*）來實行系結。 MCW 是一種 JNI 橋接器，當 managed 程式碼需要叫用 JAVA 程式碼時使用。 Managed 可呼叫包裝函式也提供子類別化 JAVA 類型的支援，以及覆寫 JAVA 類型上的虛擬方法。 同樣地，每當 Android 執行時間（美工）程式碼想要叫用 managed 程式碼時，它會透過另一個 JNI 橋接器來執行，稱為 Android 可呼叫包裝函式（ACW）。 下圖說明此[架構](~/android/internals/architecture.md)：

[![Android JNI bridge 架構](images/architecture.png)](images/architecture.png#lightbox)

系結程式庫是包含適用于 JAVA 類型之受控可呼叫包裝函式的元件。 例如，以下是我們想要在系`MyClass`結程式庫中包裝的 JAVA 類型：

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

在為包含`MyClass`的 **.jar**產生系結程式庫之後，我們可以將它具現化，並從下列C#來源呼叫方法：

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

若要建立此系結程式庫，您可以使用 [Xamarin JAVA 系結連結*庫*] 範本。 產生的系結專案會建立 .NET 元件，其中包含內嵌在其中的 MCW 類別、 **.jar**檔案和 Android 程式庫專案資源。 您也可以建立適用于 Android Archive 的系結程式庫（。AAR）檔案和 Eclipse Android 程式庫專案。 藉由參考產生的系結程式庫 DLL 元件，您可以在您的 Xamarin Android 專案中重複使用現有 JAVA 程式庫。

當您參考系結程式庫中的類型時，您必須使用系結程式庫的命名空間。 一般來說，您會在`using` C#原始程式檔的頂端新增指示詞，也就是 JAVA 封裝名稱的 .net 命名空間版本。 例如，如果系結的 **.jar**的 JAVA 封裝名稱如下：

```csharp
com.company.package
```

接著，您會將下列`using`語句放在原始程式C#檔的頂端，以存取系結的 **.jar**檔案中的類型：

```csharp
using Com.Company.Package;
```

系結現有的 Android 程式庫時，必須記住下列幾點：

- **程式庫是否有任何外部相依性？** &ndash;Android 程式庫所需的任何 JAVA 相依性都必須以**ReferenceJar**或**EmbeddedReferenceJar**的形式包含在 Xamarin android 專案中。 任何原生元件都必須加入至系結專案做為**EmbeddedNativeLibrary**。  

- **Android 程式庫的目標是哪個版本的 Android API？** &ndash;不可能「降級」 Android API 層級;確定 [Xamarin] 系結專案的目標是與 Android 程式庫相同的 API 層級（或更高版本）。

- **用來編譯程式庫的 JDK 版本為何？** &ndash;如果 Android 程式庫是使用不同于 Xamarin 的 JDK 版本所建立，則可能會發生系結錯誤。 可能的話，請使用您的 Xamarin 安裝所使用的相同 JDK 版本來重新編譯 Android 程式庫。

## <a name="build-actions"></a>建置動作

當您建立系結程式庫時，您會在 **.jar**或上設定*組建動作*。您併入系結程式庫專案&ndash;中的 AAR 檔案：每個組建動作都會決定 **.jar**或的方式。AAR 檔案將內嵌至您的系結程式庫（或由其參考）。 下列清單摘要列出這些組建動作：

- `EmbeddedJar`將 .jar 內嵌至產生的系結程式庫 DLL，做為內嵌資源。 &ndash; 這是最簡單且最常使用的組建動作。 當您想要將 **.jar**自動編譯成位元組程式碼並封裝到系結程式庫時，請使用此選項。

- `InputJar`不會將 .jar 內嵌到產生的系結程式庫中。 &ndash;URLMON.DLL. 您的系結程式庫。DLL 在執行時間會相依于這個 **.jar** 。 當您不想要在系結程式庫（例如，基於授權原因）中包含 **.jar**時，請使用此選項。 如果您使用此選項，您必須確定在執行應用程式的裝置上可以使用輸入 **.jar** 。

- `LibraryProjectZip`&ndash;內嵌。AAR 檔案放入產生的系結程式庫中。URLMON.DLL. 這類似于 EmbeddedJar，不同之處在于您可以在系結中存取資源（以及程式碼）。AAR 檔案。 當您想要內嵌時，請使用此選項。AAR 至您的系結程式庫。

- `ReferenceJar`指定一個參考 .jar：一個參考 .jar 是您所系結的 .jar 或的 .jar。 &ndash;AAR 檔案視而定。 這個參考 **.jar**僅用於滿足編譯時間相依性。 當您使用這個組建動作時C# ，不會為參考建立系**結，而且**它不會內嵌在產生的系結程式庫中。URLMON.DLL. 當您要建立參考 **.jar**的系結程式庫，但尚未完成時，請使用此選項。 這個組建動作適用于封裝多個 **.jar**（和/或）。AARs）分成多個相互相依的系結程式庫。

- `EmbeddedReferenceJar`將參考 .jar 內嵌到產生的系結程式庫中。 &ndash;URLMON.DLL. 當您想要為輸入 **.jar** （或C# ）建立系結時，請使用此組建動作。AAR）及其所有在您的系結程式庫中的參考 **。**

- `EmbeddedNativeLibrary`將原生. 內嵌到系結中。 &ndash; 這個組建動作用於 **。因此**，會系結 **.jar**檔所需的檔案。 在從 JAVA 程式庫執行程式碼之前，可能需要手動載入 **。** 如下所述。

這些組建動作會在下列指南中詳細說明。

此外，下列組建動作是用來協助匯入 JAVA API 檔，並將其C#轉換成 XML 檔：

- `JavaDocJar`是用來指向符合 Maven 封裝樣式之 JAVA 程式庫的 JAVAdoc 封存 Jar （通常`FOOBAR-javadoc**.jar**`是）。
- `JavaDocIndex`是用來在 API `index.html`參考檔 HTML 中指向檔案。
- `JavaSourceJar`是用來補充`JavaDocJar`，以便先從來源產生 JAVADoc，然後將`JavaDocIndex`結果視為，針對符合 Maven 封裝樣式的 JAVA 程式庫（通常`FOOBAR-sources**.jar**`是）。

API 檔應該是來自 JAVA8、JAVA7 或 JAVA6 SDK 的預設 doclet （兩者都是不同的格式），或 DroidDoc 樣式。

## <a name="including-a-native-library-in-a-binding"></a>在系結中包含原生程式庫

在系結 JAVA 程式庫的過程中，可能需要在 Xamarin. Android 系結專案中包含 **。** 當包裝的 JAVA 程式碼執行時，Xamarin 將無法進行 JNI 呼叫，並會 UnsatisfiedLinkError 錯誤訊息 _：找不到原生_方法：將會出現在應用程式的 logcat 中。

修正此問題的方法是以手動方式**載入，並**呼叫來`Java.Lang.JavaSystem.LoadLibrary`進行程式庫。 例如，假設 Xamarin 專案有共用程式庫**libpocketsphinx_jni。因此**包含在系結專案中，且組建動作為**EmbeddedNativeLibrary**，下列程式碼片段（在使用共用程式庫之前執行）會載入 **。因此，** 程式庫：

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>將 JAVA Api 調整成 C&eparsl;

Xamarin 的系結產生器會變更一些 JAVA 慣用語和模式，以對應至 .NET 模式。 下列清單說明如何將 JAVA 對應至C#/.NET：

- JAVA 中的_Setter/Getter 方法_是 .net 中的_屬性_。

- JAVA 中的_欄位_是 .net 中的_屬性_。

- JAVA 中的接聽程式/接聽程式_介面_是 .net 中的_事件_。 回呼介面中方法的參數會以子`EventArgs`類別表示。

- JAVA 中的_靜態嵌套類別_是 .net 中的一個_嵌套類別_。

- JAVA 中的_內部類別_是在中C#具有實例建立程式的_嵌套類別_。

## <a name="binding-scenarios"></a>系結案例

下列系結案例指南可協助您系結 JAVA 程式庫（或程式庫），以合併至您的應用程式：

- 系結[。JAR](~/android/platform/binding-java-library/binding-a-jar.md)是針對 **.jar**檔案建立系結程式庫的逐步解說。

- 系結[。AAR](~/android/platform/binding-java-library/binding-an-aar.md)是建立系結程式庫的逐步解說。AAR 檔案。 閱讀本逐步解說，瞭解如何系結 Android Studio 程式庫。

- 系結[Eclipse 程式庫專案](~/android/platform/binding-java-library/binding-a-library-project.md)是從 Android 程式庫專案建立系結程式庫的逐步解說。 閱讀本逐步解說，以瞭解如何系結 Eclipse Android 程式庫專案。

- [自訂](~/android/platform/binding-java-library/customizing-bindings/index.md)系結說明如何對系結進行手動修改以解決組建錯誤，並塑造產生的 API，使其更C#「類似」。

- [疑難排解](~/android/platform/binding-java-library/troubleshooting-bindings.md)系結會列出常見的系結錯誤案例、說明可能的原因，並提供解決這些錯誤的建議。

## <a name="related-links"></a>相關連結

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [GAPI 中繼資料](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [使用原生程式庫](~/android/platform/native-libraries.md)
