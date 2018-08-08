---
title: TvOS 12 簡介
description: 本文件提供高層次概觀中的 Xamarin 預覽版本的 tvOS 12 的全新和更新功能目前提供的 C# 繫結。
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 5cbec23aa81a4637a18f83d9955a78183dadaa21
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615195"
---
# <a name="introduction-to-tvos-12"></a>TvOS 12 簡介

![預覽](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 的 tvOS 12 支援目前為預覽狀態，它可能包含錯誤，這表示不具備完整功能，而且可能變更。 您可以使用它僅用於進行測試。

本文件提供的全新和更新的 tvOS 概觀 12 的 Xamarin 預覽版本目前提供 C# 繫結的功能。

若要開始建置使用 Xamarin 的 tvOS 12 應用程式，看看：

- [入門指南](~/ios/platform/introduction-to-ios12/get-started.md)
- Xamarin 預覽[版本部落格文章](https://releases.xamarin.com/preview-release-xcode-10-beta-5/)

## <a name="tvuikit"></a>TVUIKit

tvOS 12 包含 TVUIKit，一組 Api，以便讓 tvOS 開發人員能夠使用常見的 tvOS 控制項，例如海報檢視、 標題按鈕、 卡檢視和 monogram 檢視。 tvOS 12 也導入了允許捲動太長而無法完全顯示的文字標籤的屬性。

## <a name="password-autofill"></a>密碼自動填入

使用 tvOS 12，使用者可以使用他們的 iOS 裝置，只要點選一下登入的 tvOS 應用程式。 啟用此選項結合`UITextContentType`使用量，以指定使用者名稱和密碼欄位，以建立 iOS 應用程式和 tvOS 應用程式，與慣用的焦點的環境，以選取要在使用者接收焦點的項目之間的關聯性相關聯的網域提供使用者名稱和密碼。

## <a name="focus-engine-enhancements"></a>焦點搜尋引擎的增強功能

tvOS 12 可讓所有的應用程式，無論它們呈現的方式，與焦點引擎互動。 透過使用 Siri 遠端使用者的互動，焦點引擎可用在任何應用程式選取項目、 可能的焦點變更的提示和自然更新焦點。 這透過 UIKit 的自訂應用程式中啟用`IUIFocusItemContainer`介面，`UIFocusMovementHint`類別，`IUIFocusItemScrollableContainer`介面，以及其他相關的類別和方法。

## <a name="vision-framework"></a>視覺架構

視覺架構包含可以在不同的方向，偵測臉改良的臉部偵測器。 此外，要求修訂現在可用來選取特定的目標 framework 演算法修訂。

## <a name="natural-language-framework"></a>自然語言架構

自然語言架構可讓應用程式，以執行各種類型的語言分析。 比方說，它可以用來識別組件的語音，並判斷由文字區塊的語言。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS – Apple 開發人員 (Apple)](https://developer.apple.com/tvos/)
- [TvOS 12 (Apple) （影片） 中最新消息](https://developer.apple.com/videos/play/wwdc2018/208/)
- [電視節目 (Apple)](https://www.apple.com/tv/)
- Xamarin 預覽[版本部落格文章](https://releases.xamarin.com/preview-release-xcode-10-beta-5/)
