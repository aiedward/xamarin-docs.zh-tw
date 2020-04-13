---
title: 綁定安卓科特林庫
description: 本文件介紹如何創建到 Kotlin 代碼的 C# 綁定,從而可以在 Xamarin.Android 應用程式中使用本機庫。
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: ec7d154b0d7fcb055bd398089e142fe8b1d9f60e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292885"
---
# <a name="bind-android-kotlin-libraries"></a>綁定安卓科特林庫

Android 平臺及其母語和工具不斷發展,並且有許多使用最新產品開發的第三方庫。 最大化代碼和元件重用是跨平台開發的主要目標之一。 隨著 Xamarin 開發人員在開發人員中的受歡迎程度不斷提高,重用使用 Kotlin 構建的元件的能力變得越來越重要。 您可能已經熟悉綁定常規[Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)庫的過程。 現在提供了其他文檔來描述[裝訂科特林庫](walkthrough.md)的過程,因此 Xamarin 應用程式也以同樣的方式使用它們。 本文檔的目的是描述為 Xamarin 創建科特林綁定的高級方法。

## <a name="high-level-approach"></a>進階方法

使用 Xamarin,您可以將任何第三方本機庫綁定為 Xamarin 應用程式可消耗的庫。 Kotlin 是新語言,為此語言構建的庫創建綁定需要一些額外的步驟和工具。 此方法涉及以下四個步驟:

1. 編譯本機庫
1. 準備 Xamarin 中繼資料,讓 Xamarin 工具能夠產生 C# 類別
1. 使用本機庫和中繼資料建構 Xamarin 綁定庫
1. 在 Xamarin 應用程式中使用 Xamarin 綁定庫

以下各節概述了這些步驟,並提供了其他詳細資訊。

### <a name="build-the-native-library"></a>編譯本機庫

第一步是獲取本機科特林庫(AAR 包,這是一個 Android 存檔)。 您可以直接從供應商處請求,也可以自己構建它。

### <a name="prepare-the-xamarin-metadata"></a>準備 Xamarin 中繼資料

第二步是準備元資料轉換檔,Xamarin 工具將使用該檔生成相應的 C# 類。 在最佳情況下,此檔可能為空,其中 Xamarin 工具發現並生成了所有類。 在某些情況下,必須應用元數據轉換來生成正確的和/或所需的C# 程式碼。 在許多情況下,必須使用 AAR 拆解器(如[JAva De 編譯器 (JD))](http://java-decompiler.github.io/)來標識您希望從要生成的 C# 類的最終清單中排除的隱藏依賴項和不需要的類。 最終元數據應表示引用 Xamarin.Android 應用程式將在其中與之交互的公共介面。

### <a name="build-a-xamarinandroid-binding-library"></a>構建 Xamarin.安卓綁定庫

第三步是創建一個特殊專案 - Xamarin.Android 綁定庫。 它包括科特林庫作為本機引用和前一步中定義的元數據轉換。 在編寫本文時,每個被引用的 AAR 包都需要單獨的 Android 綁定庫專案。 綁定庫必須添加[Xamarin.Kotlin.StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)套件才能支援象牙林標準庫。

### <a name="consume-the-xamarin-binding-library"></a>使用 Xamarin 繫結庫

第四步也是最後一步是引用 Xamarin.Android 應用程式中的綁定庫。 添加對 Xamarin.Android 綁定庫的引用使 Xamarin 應用程式能夠使用該包內公開的 Kotlin 類。

## <a name="walkthrough"></a>逐步介紹

上述方法概述了為 Xamarin 創建 Kotlin 綁定所需的進階步驟。 在準備這些綁定時,需要考慮許多較低級別的步驟和進一步的詳細資訊,包括適應本機工具和語言的變化。 目的是説明您更深入地瞭解此概念以及此過程所涉及的高級步驟。 有關詳細的分步指南,請參閱[Xamarin Kotlin 綁定演練](walkthrough.md)文檔。

## <a name="related-links"></a>相關連結

- [Android Studio](https://developer.android.com/studio)
- [格拉德爾安裝](https://gradle.org/install/)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Java 解編譯](http://java-decompiler.github.io/)
- [泡泡採摘者科特林圖書館](https://github.com/igalata/Bubble-Picker)
- [繫結 Java 函式庫](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Java 繫結中繼資料](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [薩馬林.科特林.斯特利布·努Get](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [範例項目儲存庫](https://github.com/xamcat/xamarin-binding-kotlin-framework)
