---
title: 繫結 Java 程式庫
description: Android 社區有許多 JAVA 庫,您可能需要在應用中使用;本指南說明如何透過建立綁定庫將 Java 庫合併到 Xamarin.Android 應用程式中。
ms.prod: xamarin
ms.assetid: B39FF1D5-69C3-8A76-D268-C227A23C9485
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: d40a23076ec8f405e57ec40de47ec9ad2261d85d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027611"
---
# <a name="binding-a-java-library"></a>繫結 Java 程式庫

_Android 社區有許多 JAVA 庫,您可能需要在應用中使用;本指南說明如何透過建立綁定庫將 Java 庫合併到 Xamarin.Android 應用程式中。_

## <a name="overview"></a>概觀

Android 的第三方庫生態系統是巨大的。 因此,使用現有的 Android 庫通常比創建新的 Android 庫更有意義。 Xamarin.Android 提供了兩種使用這些庫的方法:

- 建立*綁定庫*,該庫使用 C# 包裝器自動包裝庫,以便可以通過 C# 調用呼叫 Java 代碼。

- 使用*JAVA 本機介面*(*JNI*) 直接呼叫 Java 庫代碼中的呼叫。 JNI 是一個程式設計框架,使 Java 代碼能夠調用本機應用程式或庫來調用和調用。

本指南介紹了第一個選項:如何創建*綁定庫,該綁定庫*將一個或多個現有 JAVA 庫包裝到可在應用程式中連結到的程式集中。 有關使用 JNI 的詳細資訊,請參閱[使用 JNI](~/android/platform/java-integration/working-with-jni.md)。

Xamarin.Android 使用*託管可調用包裝器*實現綁定 (*MCW*)。 MCW 是一個 JNI 網路,當託管代碼需要調用 Java 代碼時使用。 託管可調用包裝器還支援子類 Java 類型和在 Java 類型上重寫虛擬方法。 同樣,每當 Android 運行時 (ART) 代碼希望調用託管代碼時,它都會通過另一個 JNI 橋(稱為 Android 可呼叫包裝器 (ACW) 來調用。 下圖說明此[系統結構](~/android/internals/architecture.md):

[![安卓 JNI 橋架構](images/architecture.png)](images/architecture.png#lightbox)

綁定庫是包含 JAVA 類型的託管可調用包裝器的程式集。 例如,下面是一個 JAVA`MyClass`類型 ,我們希望在綁定庫中包裝:

```java
package com.xamarin.mycode;

public class MyClass
{
    public String myMethod (int i) { ... }
}
```

為 包含**的 .jar**生成綁`MyClass`定庫後 ,我們可以實例化它並從 C# 呼叫它的方法:

```csharp
var instance = new MyClass ();

string result = instance.MyMethod (42);
```

要創建此綁定庫,請使用 Xamarin.Android *Java 綁定庫*範本。 生成的綁定項目創建一個 .NET 程式集,其中嵌入了 MCW 類 **、.jar**檔和資源,用於嵌入 Android 庫專案。 您還可以為 Android 存檔創建綁定庫(。AAR) 檔案和 Eclipse Android 庫專案。 通過引用生成的綁定庫 DLL 程式集,可以在 Xamarin.Android 專案中重用現有的 JAVA 庫。

在綁定庫中引用類型時,必須使用綁定庫的命名空間。 通常,在 C# 源檔的頂部添加`using`一個指令,該指令是 Java 包名稱的 .NET 命名空間版本。 例如,如果綁定 **.jar**的 JAVA 包名稱如下:

```csharp
com.company.package
```

然後,您將以下`using`語句放在 C# 源檔案的頂部,以造訪結合 **.jar**檔中的類型:

```csharp
using Com.Company.Package;
```

綁定現有 Android 庫時,需要牢記以下幾點:

- **庫是否有任何外部依賴關係?** &ndash;Android 庫要求的任何 JAVA 依賴項都必須作為**參考 Jar**或**嵌入式參考Jar**包含在 Xamarin.Android 專案中。 任何本機程式集都必須作為**嵌入式本機庫**添加到綁定專案中。  

- **Android 庫的目標是最新版本的 Android API?** &ndash;無法「降級」Android API 級別;因此,無法"降級"Android API 級別。確保 Xamarin.Android 綁定專案的目標與 Android 庫相同的 API 級別(或更高)。

- **用於編譯庫的 JDK 版本是什麼版本?** &ndash;如果 Android 庫的 JDK 版本與 Xamarin.Android 使用的版本不同,則可能發生綁定錯誤。 如果可能,請使用安裝 Xamarin.Android 時使用的 JDK 版本重新編譯 Android 庫。

## <a name="build-actions"></a>建置動作

建立繫結庫時,將*產生操作*設定在 **.jar**或 。您合併到繫結庫專案中&ndash;的 AAR 檔,每個產生操作確定 **.jar**或 。AAR 檔將嵌入(或引用)綁定庫。 下面的清單總結了這些產生操作:

- `EmbeddedJar`&ndash;將 **.jar**作為嵌入資源嵌入到生成的綁定庫 DLL 中。 這是最簡單和最常用的生成操作。 如果您希望 **.jar**自動編譯為位元組碼並打包到綁定庫中,請使用此選項。

- `InputJar`&ndash;不將 **.jar**嵌入到生成的綁定庫中。Dll。 您的繫結庫 。DLL 在運行時將依賴於此 **.jar。** 當您不想在綁定庫中包括 **.jar**時(例如,出於許可原因),請使用此選項。 如果使用此選項,必須確保輸入 **.jar**在運行應用的設備上可用。

- `LibraryProjectZip`&ndash;內嵌AAR 檔進入生成的綁定庫。Dll。 這與 EmbeddedJar 類似,只不過可以訪問綁定 中的資源(以及代碼)。AAR 檔。 如果要嵌入 ,請使用此選項。AAR 進入綁定庫。

- `ReferenceJar`&ndash;指定引用 **.jar**: 引用 **.jar**是綁定 **.jar**或 的 一個 **.jar。** AAR 文件取決於。 此引用 **.jar**僅用於滿足編譯時的依賴項。 使用此生成操作時,不會為引用 **.jar**創建 C# 綁定,也不會嵌入到生成的綁定庫中。Dll。 當您為引用 **.jar**創建綁定庫但尚未創建綁定庫時,請使用此選項。 此生成操作對於打包多個 **.jars(** 和/或 )非常有用。AARs) 到多個相互依賴的綁定庫。

- `EmbeddedReferenceJar`&ndash;將引用 **.jar**嵌入到生成的綁定庫中。Dll。 如果要為輸入 **.jar(** 或 )創建 C# 繫結,請使用此產生操作。AAR)及其綁定庫中的所有引用 **.jar**(s)。

- `EmbeddedNativeLibrary`&ndash;將本機 **.so**嵌入到綁定中。 此產生操作用於綁定 **.jar**檔所需的 **.so**檔案。 在從 Java 庫執行代碼之前,可能需要手動載入 **.so**庫。 如下所述。

以下指南將更詳細地介紹這些生成操作。

此外,以下產生操作用於幫助匯入 Java API 文件並將其轉換為 C# XML 文件:

- `JavaDocJar`用於指向符合 Maven 包樣式的 Java 庫的 Javadoc`FOOBAR-javadoc**.jar**`存檔 Jar(通常為 )。
- `JavaDocIndex`用於在 API`index.html`參考文件 HTML 中指向檔。
- `JavaSourceJar`用於補充`JavaDocJar`,首先從源生成 JavaDoc,然後將`JavaDocIndex`結果視為 符合 Maven 包樣式的 Java 庫(通常)。 `FOOBAR-sources**.jar**`

API 文件應該是來自 JAVA8、JAva7 或 JAva6 SDK 的預設文件(它們都是不同的格式)或 DroidDoc 樣式。

## <a name="including-a-native-library-in-a-binding"></a>在繫結中包括本機庫

可能需要在 Xamarin.Android 綁定專案中包括 **.so**庫,作為綁定 Java 庫的一部分。 執行包裝的 Java 代碼時,Xamarin.Android 將無法進行 JNI 呼叫和錯誤訊息_java.lang.UnsinLinkError:未找到本機方法:_ 將顯示在應用程式的註銷中。

為此,修復程式是手動載入 **.so**庫,並`Java.Lang.JavaSystem.LoadLibrary`呼叫 。 例如,假設 Xamarin.Android 專案具有共用庫**libpocketsphinx_jni.因此**包含在綁定專案中,具有**嵌入式本機庫**的生成操作,以下代碼段(在使用共用庫之前執行)將載入 **.so**庫:

```csharp
Java.Lang.JavaSystem.LoadLibrary("pocketsphinx_jni");
```

## <a name="adapting-java-apis-to-ceparsl"></a>將 Java API 調整到 C&eparsl;

Xamarin.Android 綁定生成器將更改一些 JAva 習語和模式,以對應於 .NET 模式。 下面的清單描述了如何將 Java 映射到 C#/.NET:

- Java 中的_Setter/Getter 方法_是 .NET 中_的屬性_。

- Java 中的_欄位_是 .NET 中_的屬性_。

- Java 中的_偵聽器/偵聽器介面_是 .NET 中的_事件_。 回調介面中方法的參數將由`EventArgs`子類表示。

- Java 中的_靜態嵌套類別_是 .NET 中的_嵌取類別_。

- Java_中的內部類別_是具有 C# 中的實體建構函式的_嵌入類別 。_

## <a name="binding-scenarios"></a>繫結方案

以下繫結方案指南可以協助您綁定 Java 函式庫(或庫)以納入應用:

- [繫結 a 。JAR](~/android/platform/binding-java-library/binding-a-jar.md)是為 **.jar**檔案創建綁定庫的演練。

- [繫結 。AAR](~/android/platform/binding-java-library/binding-an-aar.md)是為創建綁定庫的演練。AAR 檔。 閱讀本演練,瞭解如何綁定 Android Studio 庫。

- [綁定 Eclipse 庫專案](~/android/platform/binding-java-library/binding-a-library-project.md)是從 Android 庫專案創建綁定庫的演練。 閱讀本演練,瞭解如何綁定 Eclipse Android 庫專案。

- [自定義綁定](~/android/platform/binding-java-library/customizing-bindings/index.md)說明如何手動修改綁定以解決生成錯誤並塑造生成的 API,使其更像"C#」。。

- [故障排除綁定](~/android/platform/binding-java-library/troubleshooting-bindings.md)列出了常見的綁定錯誤方案,解釋了可能的原因,並提供了解決這些錯誤的建議。

## <a name="related-links"></a>相關連結

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [GAPI 中繼資料](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
- [使用原生程式庫](~/android/platform/native-libraries.md)
