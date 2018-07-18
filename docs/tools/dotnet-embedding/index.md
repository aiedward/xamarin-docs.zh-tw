---
title: .NET 內嵌
description: '.NET 內嵌可讓您現有.NET 程式碼 (C#、 F # 和其他人） 若要以其他程式設計語言撰寫的程式碼可使用。'
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 16f59498a49d10a43e04989136d8835bf78bd89d
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38830396"
---
# <a name="net-embedding"></a>.NET 內嵌

![預覽](~/media/shared/preview.png)

.NET 內嵌可讓您現有.NET 程式碼 (C#、 F # 和其他人） 從其他程式設計語言，且在各種不同的環境中取用。

這表示，如果您有想要使用從您現有的 iOS 應用程式的.NET 程式庫，您可以這麼做。   或者，如果您想要將它連結的原生的 c + + 程式庫，您也可以執行的。   或使用 Java 的.NET 程式碼。

.NET 內嵌根據[Embeddinator 4000](https://github.com/mono/Embeddinator-4000)開放原始碼專案。

## <a name="environments-and-languages"></a>環境和語言

此工具是同時留意它會使用時，環境，以及會取用它的語言。   比方說，iOS 平台不允許在 just-in-time (JIT) 編譯，因此.NET 內嵌將會以靜態方式會編譯成可用於 iOS 的原生程式碼的.NET 程式碼。  其他環境，並允許 JIT 編譯，並在這些環境中，我們選擇 JIT 編譯。

支援各種語言的取用者，因此它會呈現為慣用的程式碼的目標語言的.NET 程式碼。   這是目前的支援語言的清單：

- [**OBJECTIVE-C** ](objective-c/index.md) – 對應至慣用的 Objective C Api 的.NET
- [**Java** ](android/index.md) – 對應至慣用的 Java Api 的.NET
- [**C** ](get-started/c.md) – 對應.NET 物件導向，如 C Api

稍後將會推出更多語言。

## <a name="getting-started"></a>快速入門

若要開始，請檢查我們的指南，為每個目前支援的語言：

- [**OBJECTIVE-C** ](get-started/objective-c/index.md) – 涵蓋 macOS 和 iOS
- [**Java** ](get-started/java/index.md) – 涵蓋 macOS 和 Android
- [**C** ](get-started/c.md) – 涵蓋在桌面平台上的 C 語言

## <a name="related-links"></a>相關連結

- [在 GitHub 上的 Embeddinator 4000](https://github.com/mono/Embeddinator-4000)
