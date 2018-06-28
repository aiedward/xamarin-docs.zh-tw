---
title: watchOS 平台功能
description: 描述 watchOS 平台功能，例如 Apple Pay、 通知、 複雜性、 主動式建議、 健身應用程式和更多的各種指南的這個文件連結。
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/25/2018
ms.openlocfilehash: 16d10dd69223f404aac7c933302992a1544461e9
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066610"
---
# <a name="watchos-platform-features"></a>watchOS 平台功能

描述 watchOS 平台功能，例如 Apple Pay、 通知、 複雜性、 主動式建議、 健身應用程式和更多的各種指南的這個文件連結。

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[watchOS 4 簡介](introduction-to-watchos4.md)

本文件提供功能新增及更新 watchOS 4 中的高階的概觀。

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[watchOS 3 簡介](introduction-to-watchos3/index.md)

本文說明 watchOS 3 中的新增和更新 Api。

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Apple Pay 增強功能](~/ios/watchos/platform/apple-pay.md)

在 watchOS 3，已展開 PassKit 架構以支援安全、 在應用程式付款 （實體貨物與服務） 的 Apple Watch 上執行的應用程式。

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[背景工作](~/ios/watchos/platform/background-tasks.md)

watchOS 3 會介紹幾個背景工作的應用程式可以用來更新他們開啟之前，使用者需要其資訊確保它包含的內容。

## <a name="notificationsnotificationsmd"></a>[通知](notifications.md)

了解如何提供自訂監看式應用程式中處理的通知。

## <a name="complicationscomplicationsmd"></a>[複雜功能](complications.md)

加入複雜功能支援 watch 錶面上顯示最新的資料。

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[主動式建議](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 可讓應用程式，以主動呈現資訊中的使用者提供內容。 若要支援這項功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)現在包含`MapItem`屬性，可讓應用程式的其他應用程式提供位置資訊供日後使用。

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md)

提供快速的使用者互動，是必要條件建立吸引人的 Apple Watch 應用程式和複雜性。 新 watchOS 3，Apple 已加入的筆勢辨識器，存取數位皇冠和新的使用者通知與瀏覽技術支援。 這項目，以及已新增支援 SceneKit 並且 SpriteKit，可讓開發人員輕鬆地建立豐富、 一目了然會快速且回應迅速的介面。

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[健身應用程式增強功能](~/ios/watchos/platform/workout-apps.md)

新增至 watchOS 3，健身相關的應用程式具有 Apple Watch 在背景中執行的能力。 若要啟用這項功能 （和存取 HealthKit 資料），應用程式必須包含`WKBackgroundModes`中的索引鍵`Info.plist`檔案具有值`workout-processing`。
