---
title: .NET 內嵌
description: .Net 內嵌可讓您現有的 .netC#程式F#代碼（、和其他專案）可供以其他程式設計語言撰寫的程式碼使用。
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: af068e5a09cc11eec33508a4f2eb33186168aae6
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290218"
---
# <a name="net-embedding"></a>.NET 內嵌

![預覽](~/media/shared/preview.png)

.Net 內嵌可讓您從其他程式C#設計F#語言和各種不同的環境中使用現有的 .net 程式碼（、和其他專案）。

這表示如果您有想要從現有 iOS 應用程式使用的 .NET 程式庫，您可以這麼做。   或者，如果您想要將它與原C++生程式庫連結，您也可以這麼做。   或使用 JAVA 的 .NET 程式碼。

.NET 內嵌以[Embeddinator-4000](https://github.com/mono/Embeddinator-4000)開放原始碼專案為基礎。

## <a name="environments-and-languages"></a>環境和語言

此工具同時察覺它將使用的環境，以及將取用它的語言。   例如，iOS 平臺不允許即時（JIT）編譯，因此 .NET 內嵌會以靜態方式將您的 .NET 程式碼編譯成可在 iOS 中使用的機器碼。  其他環境則允許 JIT 編譯，而在這些環境中，我們選擇進行 JIT 編譯。

它支援各種語言取用者，因此會以目的語言呈現 .NET 程式碼為慣用程式碼。   這是目前支援的語言清單：

- [**目標-c**](objective-c/index.md) -將 .net 對應至慣用的目標-c api
- [**JAVA**](android/index.md) –對應 .net 以慣用 JAVA api
- [**C**](get-started/c.md) –將 .net 對應至物件導向，例如 C api

稍後會提供更多語言。

## <a name="getting-started"></a>快速入門

若要開始使用，請參閱我們的其中一個指南，以取得目前支援的每一種語言：

- [**目標-C**](get-started/objective-c/index.md) –涵蓋 MacOS 和 iOS
- [**JAVA**](get-started/java/index.md) –涵蓋 MacOS 和 Android
- [**C**](get-started/c.md) –涵蓋桌面平臺上的 c 語言

## <a name="related-links"></a>相關連結

- [GitHub 上的 Embeddinator-4000](https://github.com/mono/Embeddinator-4000)
