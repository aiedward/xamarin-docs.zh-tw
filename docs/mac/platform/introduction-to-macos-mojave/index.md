---
title: macOS Mojave 簡介
description: 本檔提供 macOS Mojave 中新增和更新功能的概要說明。
ms.prod: xamarin
ms.assetid: 4A41CD85-C807-44C9-85AB-B5441B145A73
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: dc3def5d5c709515574a72cf9f61d71d67690d87
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429703"
---
# <a name="introduction-to-macos-mojave"></a>macOS Mojave 簡介

本檔提供 macOS Mojave 中新增和更新功能的概要說明。

若要開始使用 Xamarin 建立 macOS Mojave 應用程式，請參閱[xamarin 5.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/mac/xamarin.mac_5/xamarin.mac_5.0.md)的使用者[入門指南](~/mac/platform/introduction-to-macos-mojave/get-started.md)。

## <a name="dark-mode"></a>深色模式

深色模式是 macOS Mojave 中的全系統深色主題，使用動態、暗灰色的色彩配置來顯示使用者介面元素。 它也引進了新的輔色、色彩效果和內容色調色彩，讓協力廠商應用程式看起來很不錯，無論使用者的色彩設定為何。

## <a name="user-notifications-framework"></a>使用者通知架構

使用者通知架構包含在 macOS Mojave 中，變更 Mac 應用程式用來處理使用者通知的 Api。

## <a name="natural-language-framework"></a>自然語言架構

自然語言架構可讓應用程式執行各種類型的語言分析。 例如，它可以用來識別語音的各部分，並決定以文字塊表示的語言。

## <a name="vision-framework"></a>視覺架構

視覺架構包含改良的臉部偵測器，可偵測各種方向的臉部。 此外，您現在可以使用要求修訂來選取特定的視覺架構演算法修訂。

## <a name="network-framework"></a>網路架構

網路架構 `URLSession` 是 iOS 應用程式中常用的 api 基礎，現在以獨立架構的形式提供，讓您更輕鬆地使用 TCP、UDP、TLS、IPv4/IPv6 等等。

## <a name="deprecations"></a>棄用功能

透過 macOS Mojave，Apple 已淘汰 OpenGL ES 和 OpenCL， [鼓勵開發人員](https://developer.apple.com/macos/whats-new/) 採用金屬和金屬效能著色器。

## <a name="related-links"></a>相關連結

- [Xamarin.Mac 範例](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [macOS – Apple Developer](https://developer.apple.com/macos/)
- [Xamarin. Mac 5.0 版本資訊](/xamarin/mac/release-notes/5/5.0/)