---
title: watchOS 平臺功能
description: 本檔連結到描述 watchOS 平臺功能（例如 Apple Pay、通知、複雜、主動式建議、健身應用程式等）的各種指南。
ms.prod: xamarin
ms.assetid: 13F23E01-BAED-43EB-A70E-3B30EF53D379
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: 2b987992bcb3dd4d2575a46e21a2302ed78d8d70
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282293"
---
# <a name="watchos-platform-features"></a>watchOS 平臺功能

## <a name="introduction-to-watchos-5introduction-to-watchos5indexmd"></a>[watchOS 5 簡介](introduction-to-watchos5/index.md)

本檔提供 watchOS 5 中新增和更新功能的高階總覽，可供在使用 Xamarin 建立 watchOS 應用程式時使用。

## <a name="introduction-to-watchos-4introduction-to-watchos4md"></a>[watchOS 4 簡介](introduction-to-watchos4.md)

本檔提供 watchOS 4 中新增和更新功能的高階總覽。

## <a name="introduction-to-watchos-3introduction-to-watchos3indexmd"></a>[watchOS 3 簡介](introduction-to-watchos3/index.md)

本文說明 watchOS 3 中新的和更新的 Api。

## <a name="apple-pay-enhancementsioswatchosplatformapple-paymd"></a>[Apple Pay 增強功能](~/ios/watchos/platform/apple-pay.md)

在 watchOS 3 中，已擴充 PassKit 架構，以允許在 Apple Watch 上執行的應用程式支援安全的應用程式內付款（這兩者都是實體貨物和服務）。

## <a name="background-tasksioswatchosplatformbackground-tasksmd"></a>[背景工作](~/ios/watchos/platform/background-tasks.md)

watchOS 3 引進數個背景工作，應用程式可以使用這些工作來更新其資訊，確保其在開啟它之前，擁有使用者所需的內容。

## <a name="notificationsnotificationsmd"></a>[通知](notifications.md)

瞭解如何在您的監看式應用程式中提供自訂通知處理。

## <a name="complicationscomplicationsmd"></a>[複雜功能](complications.md)

新增複雜的支援，以在監看表面上顯示最新的資料。

## <a name="proactive-suggestionsioswatchosplatformproactive-suggestionsmd"></a>[主動式建議](~/ios/watchos/platform/proactive-suggestions.md)

watchOS 3 允許應用程式在指定的內容中主動向使用者呈現資訊。 為了支援這項功能， [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity)現在包含`MapItem`屬性，可讓應用程式提供位置資訊供其他應用程式稍後使用。

## <a name="quick-interaction-techniquesioswatchosplatformquick-interaction-techniquesmd"></a>[快速互動技術](~/ios/watchos/platform/quick-interaction-techniques.md)

提供快速的使用者互動，是建立引人注目的 Apple Watch 應用程式和複雜的必備要素。 WatchOS 3 的新手新增了對手勢辨識器的支援、Digital Crown 的存取權，以及新的使用者通知和流覽技術。 這同時提供 SceneKit 和 SpriteKit 的支援，可讓開發人員輕鬆地建立既快速又迅速的豐富 glanceable 介面。

## <a name="workout-app-enhancementsioswatchosplatformworkout-appsmd"></a>[健身應用程式增強功能](~/ios/watchos/platform/workout-apps.md)

WatchOS 3 的新功能：健身相關應用程式可在 Apple Watch 的背景中執行。 若要啟用這項功能（並取得 HealthKit 資料的存取權），應用程式`WKBackgroundModes`必須`Info.plist`在檔案中包含具有值`workout-processing`的金鑰。
