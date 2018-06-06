---
title: .NET 內嵌
description: '內嵌.NET 可讓現有.NET 程式碼 （C#、 F # 中，以及其他） 以供其他程式設計語言撰寫的程式碼。'
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 16f59498a49d10a43e04989136d8835bf78bd89d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793113"
---
# <a name="net-embedding"></a>.NET 內嵌

![預覽](~/media/shared/preview.png)

內嵌.NET 可讓現有.NET 程式碼 （C#、 F # 中，以及其他） 從其他程式設計語言，且在各種不同的環境中取用。

這表示如果您有想要使用從您現有的 iOS 應用程式的.NET 程式庫，您可以的。   或者，如果您想要將它連結的原生的 c + + 程式庫，您也可以執行的。   或使用來自 Java 的.NET 程式碼。

內嵌.NET 為基礎[Embeddinator 4000](https://github.com/mono/Embeddinator-4000)開放原始碼專案。

## <a name="environments-and-languages"></a>環境和語言

此工具可以同時留意環境，它會使用，以及使用它的語言。   例如，iOS 平台不允許在 just-in-time (JIT) 編譯，讓.NET 內嵌將會以靜態方式會編譯成原生程式碼可以在 iOS 中使用的.NET 程式碼。  其他環境，並允許 JIT 編譯，並在這些環境中，我們選擇 JIT 編譯。

支援各種語言的取用者，因此它會呈現的.NET 程式碼做為慣用的程式碼的目標語言。   這是支援的語言清單在存在：

- [**Objective C** ](objective-c/index.md) – 對應慣用語 OBJECTIVE-C api 的.NET
- [**Java** ](android/index.md) – 對應至慣用的 Java 應用程式開發介面的.NET
- [**C** ](get-started/c.md) – 對應至物件導向像 C 應用程式開發介面的.NET

稍後將更多語言。

## <a name="getting-started"></a>快速入門

若要開始，請檢查我們的指南，每個目前支援的語言：

- [**Objective C** ](get-started/objective-c/index.md) – 涵蓋 macOS 和 iOS
- [**Java** ](get-started/java/index.md) – 涵蓋 macOS 和 Android
- [**C** ](get-started/c.md) – 涵蓋在桌面的平台上的 C 語言

## <a name="related-links"></a>相關連結

- [GitHub 上 Embeddinator-4000。](https://github.com/mono/Embeddinator-4000)
