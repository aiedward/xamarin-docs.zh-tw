---
title: TvOS 12 簡介
description: 本文件提供高層級的 tvOS 12 的 Xamarin 的預覽版本中新增和更新功能的概觀的目前提供的 C# 繫結。
ms.prod: xamarin
ms.assetid: 037F7FFF-2155-4017-B99A-839CE7EC5C9C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 03841306ba54e511dbf2f2b86a7c17e9f4669bcd
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067284"
---
# <a name="introduction-to-tvos-12"></a>TvOS 12 簡介

![預覽](~/media/shared/preview.png)

> [!WARNING]
> Xamarin 的 tvOS 12 支援，是目前在預覽中，也就是說，它可能包含 bug，不是功能完成，而且可能會變更。 您可以將它用於只試驗。

> [!NOTE]
> - 檢閱[入門](~/ios/platform/introduction-to-ios12/get-started.md)指示如何開始建置 iOS 12 和使用 Xamarin tvOS 12 應用程式的指南。
> - 如需詳細資訊，請參閱[版本資訊](https://releases.xamarin.com/preview-release-xcode-10-beta/)xamarin 預覽版本。

本文件提供概要新增和更新 tvOS 12 的 Xamarin 的預覽版本目前提供的 C# 繫結的功能。

## <a name="password-autofill"></a>密碼自動填入

與 tvOS 12，使用者可以點選登入 tvOS 應用程式使用其 iOS 裝置。 啟用此選項的組合`UITextContentType`欄位來指定使用者名稱和密碼的使用方式，來建立 iOS 應用程式和 tvOS 應用程式，以及慣用的焦點環境，以選取使用者之後接收焦點的項目之間的關聯性相關聯的網域提供使用者名稱和密碼。

## <a name="focus-engine-enhancements"></a>焦點引擎增強功能

tvOS 12 允許所有的應用程式，不論它們呈現方式，以便與焦點引擎互動。 透過使用 Siri 遠端使用者的互動，焦點引擎可用在任何應用程式選取項目，在可能的焦點變更及自然更新焦點。 這透過 UIKit 的自訂應用程式中啟用`IUIFocusItemContainer`介面，`UIFocusMovementHint`類別`IUIFocusItemScrollableContainer`介面，和其他相關的類別和方法。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS – Apple 開發人員 (Apple)](https://developer.apple.com/tvos/)
- [新功能 tvOS 12 (Apple) （影片）](https://developer.apple.com/videos/play/wwdc2018/208/)
- [電視 (Apple)](https://www.apple.com/tv/)
- Xamarin 預覽[版本資訊](https://releases.xamarin.com/preview-release-xcode-10-beta/)
