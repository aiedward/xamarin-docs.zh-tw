---
title: watchOS 平台功能
description: 本文件所連結到各種描述 watchOS 平台功能，例如 Apple Pay、 通知、 複雜性、 主動式建議、 健身應用程式，和更多功能的輔助線。
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: 09200ba5968838edf829b30a50a8ad0f4a3ab3aa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224556"
---
# <a name="watchos-platform-features"></a>watchOS 平台功能

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[watchOS 5 簡介](introduction-to-watchos5/index.md)

本文件提供在 watchOS 5 建置使用 Xamarin 的 watchOS 應用程式時可供使用的全新和更新功能的高階概觀。

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[watchOS 4 簡介](introduction-to-watchos4.md)

本文件提供新增和更新在 watchOS 4 中的功能的高階概觀。

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[watchOS 3 簡介](introduction-to-watchos3/index.md)

本文說明在 watchOS 3 中的新增和更新 Api。

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Apple Pay 的增強功能](~/ios/watchos/platform/apple-pay.md)

WatchOS 3 中 「 PassKit 架構已經過擴充，可允許 Apple Watch 上執行的應用程式 （實體產品及服務） 的安全、 應用程式內付款的支援。

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[背景工作](~/ios/watchos/platform/background-tasks.md)

watchOS 3 引進了數個應用程式可用來更新它的資訊確保其內容，使用者必須先開啟它的背景工作。

## <a name="notificationsnotificationsmd"></a>[通知](notifications.md)

了解如何提供自訂的通知，在您的監看式應用程式中處理。

## <a name="complicationscomplicationsmd"></a>[複雜功能](complications.md)

複雜功能的支援新增至錶面上顯示最新的資料。

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[主動式建議](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 可讓應用程式，以主動將資訊呈現給使用者，在指定內容。 若要支援此功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)現在包含`MapItem`屬性，可讓應用程式的其他應用程式提供位置資訊供日後使用。

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md)

提供快速的使用者互動所必要建立吸引人的 Apple Watch 應用程式和複雜性。 新增到 watchOS 3，Apple 已新增支援筆勢辨識器，存取數位皇冠和新的使用者通知，並瀏覽技術。 這項目，以及新增 SceneKit 和 SpriteKit，支援可讓開發人員輕鬆地建立豐富、 glanceable 是快速且回應迅速的介面。

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[健身應用程式增強功能](~/ios/watchos/platform/workout-apps.md)

新 watchOS 3 健身相關應用程式都將能夠在 Apple Watch 上的 在背景執行。 若要啟用這項功能 （和 HealthKit 資料存取），應用程式必須包含`WKBackgroundModes`中的索引鍵`Info.plist`具有值檔`workout-processing`。
