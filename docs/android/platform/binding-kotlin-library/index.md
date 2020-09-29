---
title: 系結 Android Kotlin 程式庫
description: '本檔說明如何建立 c # 系結以 Kotlin 程式碼，讓您可以在 Xamarin. Android 應用程式中取用原生程式庫。'
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 4fcb36f76ff2714a7a783b716abfe44de51b2855
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454139"
---
# <a name="bind-android-kotlin-libraries"></a>系結 Android Kotlin 程式庫

> [!IMPORTANT]
> 我們目前正在調查 Xamarin 平臺上的自訂系結使用方式。 請填寫 [**這份問卷**](https://www.surveymonkey.com/r/KKBHNLT) ，以通知未來的開發工作。

Android 平臺（以及其原生語言和工具）不斷演進，而且有許多協力廠商程式庫是使用最新的供應專案所開發。 將程式碼和元件重複使用最大化是跨平臺開發的主要目標之一。 當 Xamarin 開發人員在開發人員不斷成長時，對 Xamarin 開發人員來說，重複使用以 Kotlin 建立的元件日益重要。 您可能已經熟悉系結一般 [JAVA](../binding-java-library/index.md) 程式庫的程式。 現在提供其他檔，描述系結 [Kotlin 程式庫](walkthrough.md)的程式，因此 Xamarin 應用程式可以使用相同的方式來取用它們。 本檔的目的是要說明建立 Xamarin Kotlin 系結的高階方法。

## <a name="high-level-approach"></a>高層級的方法

透過 Xamarin，您可以系結 Xamarin 應用程式可取用的任何協力廠商原生程式庫。 Kotlin 是新的語言，並為使用此語言建立的程式庫建立系結需要一些額外的步驟和工具。 此方法牽涉到下列四個步驟：

1. 建立原生程式庫
1. 準備 Xamarin 中繼資料，此中繼資料可讓 Xamarin 工具產生 c # 類別
1. 使用原生程式庫和中繼資料來建立 Xamarin 系結程式庫
1. 在 Xamarin 應用程式中使用 Xamarin 系結程式庫

下列各節將概述這些步驟，並提供其他詳細資料。

### <a name="build-the-native-library"></a>建立原生程式庫

第一個步驟是取得原生 Kotlin 程式庫 (AAR 套件，這是 Android 封存) 。 您可以直接向廠商要求，或自行建立。

### <a name="prepare-the-xamarin-metadata"></a>準備 Xamarin 中繼資料

第二個步驟是準備中繼資料轉換檔案，Xamarin 工具會使用此檔案來產生個別的 c # 類別。 在最佳情況下，這個檔案可能是空的，其中所有類別都是由 Xamarin 工具探索和產生。 在某些情況下，必須套用中繼資料轉換，以產生正確和/或所需的 c # 程式碼。 在許多情況下，您必須使用 AAR 解譯器（例如 [JAVA 解編程式 (JD) ](http://java-decompiler.github.io/)）來識別隱藏的相依性，以及您想要從產生的 c # 類別的最終清單中排除的不必要類別。 最終的中繼資料應代表參考 Xamarin. Android 應用程式將與之互動的公用介面。

### <a name="build-a-xamarinandroid-binding-library"></a>建立 Xamarin. Android 系結程式庫

第三個步驟是建立特殊專案： Xamarin. Android 系結程式庫。 它包含 Kotlin 程式庫做為原生參考，以及在上一個步驟中定義的中繼資料轉換。 在撰寫本文時，所參考的每個 AAR 封裝都需要個別的 Android 系結程式庫專案。 系結程式庫必須新增 [Kotlin stdlib.h](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) 套件，才能支援 Kotlin 標準程式庫。

### <a name="consume-the-xamarin-binding-library"></a>使用 Xamarin 系結程式庫

第四個和最後一個步驟是在 Xamarin Android 應用程式中參考系結程式庫。 新增 Xamarin. Android 系結程式庫的參考，可讓您的 Xamarin 應用程式使用該套件內公開的 Kotlin 類別。

## <a name="walkthrough"></a>逐步解說

上述方法會概述為 Xamarin 建立 Kotlin 系結所需的高階步驟。 當您準備這些系結時，需要考慮許多較低層級的步驟和進一步的詳細資料，包括對原生工具和語言的變更進行調整。 其目的是協助您深入瞭解此概念，以及此程式所牽涉的高階步驟。 如需詳細的逐步指南，請參閱 [Xamarin Kotlin Binding](walkthrough.md) 逐步解說檔。

## <a name="related-links"></a>相關連結

- [Android Studio](https://developer.android.com/studio)
- [Gradle 安裝](https://gradle.org/install/)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [JAVA 解編程式](http://java-decompiler.github.io/)
- [BubblePicker Kotlin 程式庫](https://github.com/igalata/Bubble-Picker)
- [系結 JAVA 程式庫](../binding-java-library/index.md)
- [XPath](https://www.w3.org/TR/xpath/)
- [JAVA 系結中繼資料](../binding-java-library/customizing-bindings/java-bindings-metadata.md)
- [Kotlin. Stdlib.h NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [範例專案儲存機制](https://github.com/xamcat/xamarin-binding-kotlin-framework)