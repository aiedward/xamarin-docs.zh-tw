---
title: 其他 watchOS 3 架構變更
description: 本檔說明 watchOS 3 引進的各種架構變更, 以及如何在 Xamarin 中使用它們。 我們會討論核心資料、核心動作、基礎、HealthKit、HomeKit、PassKit 和 UIKit。
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: d8c1ace9972f15c3f068bda88bc21e7cb5990f25
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655561"
---
# <a name="additional-watchos-3-frameworks-changes"></a>其他 watchOS 3 架構變更

_本文涵蓋 watchOS 3 的其他、次要變更或現有架構的增強功能。_

除了 iOS 的主要變更之外, Apple 也已對 watchOS 3 中的數個現有架構進行修改和改進。


## <a name="core-data"></a>核心資料

以下是針對 watch OS 3 的核心資料架構所做的增強功能:

- 根[NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援並行錯誤和未序列化的提取。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別會維護 SQLite 資料存放區的集區。
- WAL 記錄模式中具有 SQLite 資料存放區的[NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援新的查詢產生功能, 其中 Managed 物件內容 (MOC) 可以釘選到特定的資料庫版本, 以供未來提取和錯誤交易使用。
- 使用高階`NSPersistenceContainer`來`NSPersistentStoreCoordinator`參考、 [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心資料設定資源。
- 已新增`NSManagedObject`數個新的便利方法, 讓您更輕鬆地執行提取和建立子類別。

如需詳細資訊, 請參閱 Apple 的[核心資料架構參考](https://developer.apple.com/reference/coredata)。


## <a name="core-motion"></a>核心動作

觀看 OS 3 的核心動作架構已進行下列增強功能:

- 新的裝置動作事件會使用加速計和陀螺儀來提供動作和方向更新。 應用程式可以註冊這項更新 (以最多100Hz 的速率)。
- 當使用者暫停並繼續執行時, 新的計步器事件會啟用快速的即時通知。 使用[CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer)來註冊前景或背景計步器事件。


## <a name="foundation"></a>打下

適用于 watch OS 3 的基礎架構已進行下列增強功能:

- 使用新的[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別來進行日期和時間間隔計算 (例如持續期間), 以比較間隔和測試間隔交集。
- 已將數個新的屬性新增至[NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別, 以取得本機資訊和可用的顯示格式。
- 使用新的[NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement)類別, 在不同的測量單位 (UOM) 之間進行轉換, 或在不同 UOMs 的值上執行計算。
- 使用新的[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)類別來格式化當地語系化的量測測量, 以顯示給終端使用者。
- 使用新的[NSUnit](https://developer.apple.com/reference/foundation/nsunit)和[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)類別來代表特定 UOMs。


## <a name="healthkit"></a>HealthKit

Watch OS 3 的 HealthKit 架構有下列增強功能:

- 使用新的[HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration)類別來指定健身`ActivityType`的`LocationType`和。
- 已新增[HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)類別的`WheelchairUse`新[HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject)和方法, 以使用輪椅相關的健全狀況資料。
- 已新增新的中繼資料`HKWeatherConditionClear`索引鍵 (例如和`HKWeatherConditionCloudy`), 以及已新增的`HKWorkoutActivityTypeFlexibility`健身類型 (例如`HKWorkoutActivityTypeWheelchairRunPace`和)。


## <a name="homekit"></a>HomeKit

Watch OS 3 的 HomeKit 架構有下列增強功能:

- 已新增 HomeKit 連線 IP 攝影機的觀看和互動功能。
- 已新增數個新的服務和特性。
- 新增更多主要服務和連結服務的附屬應用程式內容與設定。


## <a name="passkit"></a>PassKit

Watch OS 3 的 PassKit 架構有下列增強功能:

- 擴充架構, 以支援實體貨物和服務之 Apple Watch 的安全應用程式內付款。
- 現在可以使用下列類別:[PKPayment](https://developer.apple.com/reference/passkit/pkpayment)、 [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod)、 [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest)和[PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

Watch OS 3 的 UIKit 架構有下列增強功能:

- 若要在標籤中支援動態類型, 文字欄位和文字方塊會`PreferredFontForTextStyle`使用`UIFont`類別的新方法。
- 已`ColorWithDisplayP3`新增方法以支援寬色彩。


## <a name="related-links"></a>相關連結

- [watchOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS%20watchos)
- [WatchOS 3 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
