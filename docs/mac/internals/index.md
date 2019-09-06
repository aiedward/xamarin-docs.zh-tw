---
title: 在 Xamarin 中的幕後
description: 本檔會連結至描述 Xamarin 之內部運作的各種指南。 連結的檔會在編譯、Xamarin、Mac 架構和 Xamarin 註冊機構之前進行討論。
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 51cf479ba07a769f5d7a875bb3f1203caef2ad0b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290101"
---
# <a name="under-the-hood-in-xamarinmac"></a>在 Xamarin 中的幕後

## <a name="ahead-of-time-compilation-aotaotmd"></a>[早于時間編譯（AOT）](aot.md)

早于時間（AOT）編譯是一種強大的優化技術，可改善啟動效能。 不過，它也會影響您的組建時間、應用程式大小和程式執行，以有意義的方式來瞭解其運作方式。

## <a name="mac-architecturearchitecturemd"></a>[Mac 架構](architecture.md)

Xamarin 與目標的關係-C，包括編譯、選取器、註冊機構、應用程式啟動和產生器等概念。

## <a name="xamarinmac-registrarregistrarmd"></a>[Xamarin. Mac 註冊機構](registrar.md)

Xamarin 會橋接受控世界與 Cocoa 的執行時間之間的差距，讓 managed 類別能夠呼叫非受控的目標 C 類別，並在發生事件時回呼。 執行此「魔術」所需的工作是由註冊機構所處理，但是瞭解「幕後」的事情有時可能會有説明。
