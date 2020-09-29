---
title: 其他 watchOS 3 架構變更
description: 本檔說明 watchOS 3 引進的各種架構變更，以及如何在 Xamarin 中使用這些變更。 我們會討論核心資料、核心動作、Foundation、HealthKit、HomeKit、PassKit 和 UIKit。
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 50f671febdc698454dd5bc7e00d040672c7a79af
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436532"
---
# <a name="additional-watchos-3-frameworks-changes"></a>其他 watchOS 3 架構變更

_本文涵蓋 watchOS 3 現有架構的其他、次要變更或增強功能。_

除了 iOS 的重大變更之外，Apple 還對 watchOS 3 中的數個現有架構進行了修改和改進。

## <a name="core-data"></a>核心資料

適用于監看作業系統3的核心資料架構已進行下列增強功能：

- 根 [NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) 物件支援並行錯誤和未序列化的提取。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別會維護 SQLite 資料存放區的集區。
- 在 WAL 記錄模式中具有 SQLite 資料存放區的 [NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) 物件，可支援新的查詢產生功能，其中的 Managed 物件內容 (MOC) 可以釘選到特定的資料庫版本，以供未來提取和錯誤交易之用。
- 使用高階 `NSPersistenceContainer` 參考 `NSPersistentStoreCoordinator` 、 [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) 和其他核心資料設定資源。
- 已加入數個新的便利方法，可 `NSManagedObject` 讓您更輕鬆地執行提取和建立子類別。

如需詳細資訊，請參閱 Apple 的 [核心資料架構參考](https://developer.apple.com/reference/coredata)。

## <a name="core-motion"></a>核心動作

觀看 OS 3 的核心動作架構已進行下列增強功能：

- 新的裝置動作事件會使用加速計和陀螺儀來提供動作和方向更新。 應用程式可以在最多 100Hz) 的速率下註冊此更新 (。
- 新的 Pedometer 事件可在使用者暫停和繼續執行時，提供快速、即時的通知。 使用 [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) 來註冊前景或背景 pedometer 事件。

## <a name="foundation"></a>Foundation

以下是對 watch OS 3 的基礎架構進行的增強：

- 使用新的 [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) 類別來進行日期和時間間隔計算（例如持續時間），以比較間隔和測試間隔交集。
- [NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別已新增幾個新的屬性，以取得本機資訊和可用的顯示格式。
- 使用新的 [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) 類別，在不同的測量單位之間進行轉換 (UOM) 或在不同 UOMs 的值上執行計算。
- 使用新的 [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) 類別來格式化當地語系化的量值，以顯示給終端使用者。
- 使用新的 [NSUnit](https://developer.apple.com/reference/foundation/nsunit) 和 [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) 類別來代表特定的 UOMs。

## <a name="healthkit"></a>HealthKit

HealthKit framework for watch OS 3 已進行下列增強功能：

- 使用新的 [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) 類別來指定 `ActivityType` 測驗的和 `LocationType` 。
- 已新增 [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) 和 `WheelchairUse` [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) 類別的方法，以使用輪椅相關的健康情況資料。
- 已新增新的中繼資料金鑰給氣象型別 (例如 `HKWeatherConditionClear` 和 `HKWeatherConditionCloudy`) 和測驗類型 (例如 `HKWorkoutActivityTypeFlexibility` 和 `HKWorkoutActivityTypeWheelchairRunPace`) 已加入。

## <a name="homekit"></a>HomeKit

HomeKit framework for watch OS 3 已進行下列增強功能：

- 新增了 HomeKit 連線 IP 攝影機的查看和互動功能。
- 新增了數個新的服務和特性。
- 針對主要服務和連結服務的附屬應用程式新增更多的內容和設定。

## <a name="passkit"></a>PassKit

PassKit framework for watch OS 3 已進行下列增強功能：

- 擴充架構，以支援實體貨物和服務之 Apple Watch 的安全、應用程式內付款。
- 現在提供下列類別： [PKPayment](https://developer.apple.com/reference/passkit/pkpayment)、 [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod)、 [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) 和 [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)

## <a name="uikit"></a>UIKit

UIKit framework for watch OS 3 已進行下列增強功能：

- 若要在標籤中支援動態類型，文字欄位和文字方塊會使用類別的新 `PreferredFontForTextStyle` 方法 `UIFont` 。
- `ColorWithDisplayP3`已新增方法來支援寬色彩。

## <a name="related-links"></a>相關連結

- [watchOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2bwatchos)
- [WatchOS 3 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)