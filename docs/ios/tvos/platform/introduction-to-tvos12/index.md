---
title: tvOS 12 簡介
description: 本檔提供 tvOS 12 中，Xamarin 預覽版本目前提供C#系結的全新和更新功能的高階總覽。
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: f5028fe6ae7ee726ffd94d7908a089ce3bdcb385
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287943"
---
# <a name="introduction-to-tvos-12"></a>tvOS 12 簡介

本檔提供新的和更新的 tvOS 12 的高階總覽。

若要開始使用 Xamarin 建立 tvOS 12 應用程式，請參閱快速入門[指南](~/ios/platform/introduction-to-ios12/get-started.md)。

## <a name="tvuikit"></a>TVUIKit

tvOS 12 包含 TVUIKit，這是一組 Api，可讓 tvOS 開發人員使用常用的 tvOS 控制項，例如海報視圖、標題按鈕、卡片視圖和 monogram views。 tvOS 12 也引進了一個屬性，可讓標籤滾動太長而無法完全顯示的文字。

## <a name="password-autofill"></a>自動填滿密碼

在 tvOS 12 中，使用者可以使用其 iOS 裝置，透過單鍵來登入 tvOS 應用程式。 這會透過使用方式的組合`UITextContentType`來指定使用者名稱和密碼欄位、相關聯的網域以建立 iOS 應用程式與 tvOS 應用程式之間的關聯性，以及慣用的焦點環境，以選取要在使用者之後接收焦點的專案提供使用者名稱和密碼。

## <a name="focus-engine-enhancements"></a>焦點引擎增強功能

tvOS 12 允許所有應用程式與焦點引擎互動，不論其轉譯方式為何。 透過使用者與 Siri Remote 的互動，焦點引擎可以與任何應用程式搭配使用，以選取專案、可能的焦點變更提示，以及自然更新焦點。 在自訂應用程式中`IUIFocusItemContainer` `UIFocusMovementHint` ，會透過 UIKit 的介面、類別、 `IUIFocusItemScrollableContainer`介面和其他相關類別和方法來啟用此功能。

## <a name="vision-framework"></a>願景架構

視覺架構包含改良的臉部偵測器，可偵測各種方向的臉部。 此外，您現在可以使用要求修訂來選取特定的視覺架構演算法修訂。

## <a name="natural-language-framework"></a>自然語言架構

自然語言架構可讓應用程式執行各種類型的語言分析。 例如，它可以用來識別語音的各個部分，並判斷以文字塊表示的語言。

## <a name="deprecations"></a>棄用功能

在 tvOS 12 中，Apple 已淘汰 OpenGL ES，[鼓勵開發人員](https://developer.apple.com/tvos/whats-new/)採用裸機。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS – Apple Developer （Apple）](https://developer.apple.com/tvos/)
- [TvOS 12 （Apple）的新功能（影片）](https://developer.apple.com/videos/play/wwdc2018/208/)
- [電視（Apple）](https://www.apple.com/tv/)
