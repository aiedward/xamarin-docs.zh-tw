---
title: TvOS 12 簡介
description: 本文件提供高層次概觀中的 Xamarin 預覽版本的 tvOS 12 的全新和更新功能目前提供的 C# 繫結。
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: e45d9944a2f4fc392b5a78efb4a7751d19641c73
ms.sourcegitcommit: cfb72be633e335147d156af3ef9527151b9e31d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39030661"
---
# <a name="introduction-to-tvos-12"></a>TvOS 12 簡介

![預覽](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 的 tvOS 12 支援目前為預覽狀態，它可能包含錯誤，這表示不具備完整功能，而且可能變更。 您可以使用它僅用於進行測試。

> [!NOTE]
> - 檢閱[開始使用](~/ios/platform/introduction-to-ios12/get-started.md)指南的指示，有關如何開始建置 iOS 12 和使用 Xamarin 的 tvOS 12 應用程式。
> - 如需詳細資訊，請閱讀 Xamarin 預覽版[釋放部落格文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)。

本文件提供的全新和更新的 tvOS 概觀 12 的 Xamarin 預覽版本目前提供 C# 繫結的功能。

## <a name="password-autofill"></a>密碼自動填入

使用 tvOS 12，使用者可以使用他們的 iOS 裝置，只要點選一下登入的 tvOS 應用程式。 啟用此選項結合`UITextContentType`使用量，以指定使用者名稱和密碼欄位，以建立 iOS 應用程式和 tvOS 應用程式，與慣用的焦點的環境，以選取要在使用者接收焦點的項目之間的關聯性相關聯的網域提供使用者名稱和密碼。

## <a name="focus-engine-enhancements"></a>焦點搜尋引擎的增強功能

tvOS 12 可讓所有的應用程式，無論它們呈現的方式，與焦點引擎互動。 透過使用 Siri 遠端使用者的互動，焦點引擎可用在任何應用程式選取項目、 可能的焦點變更的提示和自然更新焦點。 這透過 UIKit 的自訂應用程式中啟用`IUIFocusItemContainer`介面，`UIFocusMovementHint`類別，`IUIFocusItemScrollableContainer`介面，以及其他相關的類別和方法。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS – Apple 開發人員 (Apple)](https://developer.apple.com/tvos/)
- [TvOS 12 (Apple) （影片） 中最新消息](https://developer.apple.com/videos/play/wwdc2018/208/)
- [電視節目 (Apple)](https://www.apple.com/tv/)
- Xamarin 預覽[版本部落格文章](https://releases.xamarin.com/preview-release-xcode-10-beta-3/)
