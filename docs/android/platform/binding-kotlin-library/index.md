---
title: 系結 Android Kotlin 程式庫
description: 本檔說明如何建立C# Kotlin 程式碼的系結，讓您可以在 Xamarin Android 應用程式中使用原生程式庫。
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: ec7d154b0d7fcb055bd398089e142fe8b1d9f60e
ms.sourcegitcommit: b751605179bef8eee2df92cb484011a7dceb6fda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "78292885"
---
# <a name="bind-android-kotlin-libraries"></a>系結 Android Kotlin 程式庫

Android 平臺以及其原生語言和工具會不斷演進，而且有許多協力廠商程式庫是使用最新的供應專案開發的。 最大化程式碼和元件重複使用是跨平臺開發的主要目標之一。 對 Xamarin 開發人員而言，重複使用以 Kotlin 建立的元件，對於開發人員的熱門程度越來越重要。 您可能已經熟悉將一般[JAVA](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)程式庫系結的程式。 其他檔現已提供描述系結[Kotlin 程式庫](walkthrough.md)的程式，因此 Xamarin 應用程式可以用相同的方式取用它們。 本檔的目的是要說明為 Xamarin 建立 Kotlin 系結的高階方法。

## <a name="high-level-approach"></a>高階方法

透過 Xamarin，您可以系結任何協力廠商原生程式庫，以供 Xamarin 應用程式取用。 Kotlin 是新語言，而建立使用此語言之程式庫的系結需要一些額外的步驟和工具。 此方法包含下列四個步驟：

1. 建立原生程式庫
1. 準備 Xamarin 中繼資料，讓 Xamarin 工具能夠產生C#類別
1. 使用原生程式庫和中繼資料來建立 Xamarin 系結程式庫
1. 在 Xamarin 應用程式中使用 Xamarin 系結程式庫

下列各節將概述這些步驟和其他詳細資料。

### <a name="build-the-native-library"></a>建立原生程式庫

第一個步驟是取得原生 Kotlin 程式庫（AAR 套件，也就是 Android 封存）。 您可以直接向廠商要求，或自行建立。

### <a name="prepare-the-xamarin-metadata"></a>準備 Xamarin 中繼資料

第二個步驟是準備中繼資料轉換檔案，Xamarin 工具會使用此檔案來產生個別C#的類別。 在最佳情況下，這個檔案可能是空的，而 Xamarin 工具會探索並產生所有類別。 在某些情況下，必須套用中繼資料轉換來產生正確和/或C#所需的程式碼。 在許多情況下，AAR 解譯器（例如[JAVA 解編程式（JD））](http://java-decompiler.github.io/)必須用來識別隱藏的相依性，以及您想要從產生的最終C#類別清單中排除的不想要的類別。 最終的中繼資料應該代表參考的 Xamarin Android 應用程式將與之互動的公用介面。

### <a name="build-a-xamarinandroid-binding-library"></a>建立 Xamarin Android 系結程式庫

第三個步驟是建立一個特殊專案-一個 Xamarin. Android 系結程式庫。 其中包含 Kotlin 程式庫做為原生參考和上一個步驟中定義的中繼資料轉換。 在撰寫本文時，每個參考的 AAR 套件都需要個別的 Android 系結程式庫專案。 系結程式庫必須新增[stdlib.h>](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)封裝，才能支援 Kotlin 標準程式庫。

### <a name="consume-the-xamarin-binding-library"></a>使用 Xamarin 系結程式庫

第四個和最後一個步驟是參考 Xamarin Android 應用程式中的系結程式庫。 將參考加入至 Xamarin 的程式庫，可讓您的 Xamarin 應用程式使用該封裝內公開的 Kotlin 類別。

## <a name="walkthrough"></a>逐步介紹

上述方法概述為 Xamarin 建立 Kotlin 系結所需的高階步驟。 在實務上準備這些系結時，需要考慮許多較低層級的步驟，還有進一步的詳細資料，包括適應原生工具和語言的變更。 其目的是協助您更深入瞭解此概念，以及此程式所牽涉的高階步驟。 如需詳細的逐步指南，請參閱[Xamarin Kotlin](walkthrough.md)系結逐步解說檔。

## <a name="related-links"></a>相關連結

- [Android Studio](https://developer.android.com/studio)
- [Gradle 安裝](https://gradle.org/install/)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [JAVA 解編程式](http://java-decompiler.github.io/)
- [BubblePicker Kotlin 程式庫](https://github.com/igalata/Bubble-Picker)
- [系結 JAVA 程式庫](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [JAVA 系結中繼資料](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Kotlin. Stdlib.h> NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [範例專案存放庫](https://github.com/xamcat/xamarin-binding-kotlin-framework)
