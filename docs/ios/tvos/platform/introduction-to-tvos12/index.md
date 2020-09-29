---
title: tvOS 12 簡介
description: '本檔概要說明 tvOS 12 中的新功能和更新功能，Xamarin 的預覽版本目前提供 c # 系結。'
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 2ea9ddcf5f8a2412502c1133fdc9f87474bdd830
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434750"
---
# <a name="introduction-to-tvos-12"></a>tvOS 12 簡介

本檔提供新的和更新的 tvOS 12 的概要說明。

若要開始使用 Xamarin 建立 tvOS 12 應用程式，請參閱 [快速入門手冊](~/ios/platform/introduction-to-ios12/get-started.md)。

## <a name="tvuikit"></a>TVUIKit

tvOS 12 包含 TVUIKit 這組 Api，可讓 tvOS 開發人員使用常見的 tvOS 控制項，例如海報視圖、標題按鈕、卡片視圖和 monogram 視圖。 tvOS 12 也引進了一個屬性，可讓標籤滾動太長而無法完全顯示的文字。

## <a name="password-autofill"></a>自動填入密碼

使用 tvOS 12 時，使用者可以使用其 iOS 裝置，以單鍵登入 tvOS 應用程式。 這可透過使用方式的組合 `UITextContentType` 來啟用，以指定使用者名稱和密碼欄位、相關聯的網域來建立 iOS 應用程式與 tvOS 應用程式之間的關聯性，以及在使用者提供使用者名稱和密碼之後，選擇要接收焦點之專案的慣用焦點環境。

## <a name="focus-engine-enhancements"></a>焦點引擎增強功能

tvOS 12 允許所有的應用程式都能與焦點引擎互動，而不論其呈現方式為何。 透過使用者與 Siri 遠端的互動，焦點引擎可以與任何應用程式搭配使用，以選取專案、提示可能的焦點變更，以及自然更新焦點。 這會透過 UIKit 的 `IUIFocusItemContainer` 介面、 `UIFocusMovementHint` 類別、 `IUIFocusItemScrollableContainer` 介面，以及其他相關類別和方法，在自訂應用程式中啟用。

## <a name="vision-framework"></a>視覺架構

視覺架構包含改良的臉部偵測器，可偵測各種方向的臉部。 此外，您現在可以使用要求修訂來選取特定的視覺架構演算法修訂。

## <a name="natural-language-framework"></a>自然語言架構

自然語言架構可讓應用程式執行各種類型的語言分析。 例如，它可以用來識別語音的各部分，並決定以文字塊表示的語言。

## <a name="deprecations"></a>棄用功能

使用 tvOS 12，Apple 已淘汰 OpenGL ES， [鼓勵開發人員](https://developer.apple.com/tvos/whats-new/) 採用裸機。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS – Apple Developer (Apple) ](https://developer.apple.com/tvos/)
- [TvOS 12 (Apple) 的新功能 (影片) ](https://developer.apple.com/videos/play/wwdc2018/208/)
- [電視 (Apple) ](https://www.apple.com/tv/)