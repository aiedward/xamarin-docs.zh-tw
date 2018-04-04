---
title: 其他 watchOS 3 架構變更
description: 本文涵蓋其他次要變更或增強的 watchOS 3 的現有架構。
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 572aaff9d010ec1ec1f48db2878859e445e2fb20
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="additional-watchos-3-frameworks-changes"></a>其他 watchOS 3 架構變更

_本文涵蓋其他次要變更或增強的 watchOS 3 的現有架構。_

IOS 的重大變更，除了 Apple 已進行修改，以及數個現有架構的增強功能中 watchOS 3。


## <a name="core-data"></a>核心資料

下列增強功能已對監看式 3 OS 的核心資料架構：

- 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援並行判定為失敗，和擷取沒有序列化。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別會維護 SQLite 資料存放區集區。
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件與 SQLite WAL 日誌模式支援新的查詢產生的資料存放區功能在受管理物件內容 （木） 可以固定到未來擷取特定的資料庫版本和判定為失敗的交易。
- 使用高階`NSPersistenceContainer`參考`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他資源的核心資料設定。
- 已加入數個新的便利方法`NSManagedObject`以便更容易執行提取及建立子類別。

如需詳細資訊，請參閱 Apple[核心資料架構參考](https://developer.apple.com/reference/coredata)。


## <a name="core-motion"></a>核心影片

下列增強功能已對監看式 3 OS 的核心移動架構：

- 新的裝置移動事件使用加速計和迴轉儀提供影片和方向的更新。 應用程式的應用程式可以註冊這個更新 （速率的最多 100 Hz）。
- 新的 Pedometer 事件可讓快速、 即時的通知，當使用者暫停和繼續執行。 使用[CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer)以註冊前景或背景 pedometer 事件。


## <a name="foundation"></a>Foundation

下列增強功能已對基礎架構的監看式作業系統 3:

- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別，例如持續時間，來比較間隔及測試間隔交集的日期和時間間隔計算。
- 已加入數個新屬性[NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別取得本機資訊以及可用的顯示格式。
- 使用新[NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement)轉換之間不同單位的量值 (UOM)，或在不同的 UOMs 值上執行計算的類別。
- 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)類別來格式化終端使用者顯示當地語系化的度量單位。
- 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)和[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)類別，表示特定 UOMs。


## <a name="healthkit"></a>HealthKit

下列增強功能已對監看式作業系統 3 HealthKit 架構：

- 使用新[HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration)類別，以指定`ActivityType`和`LocationType`健身。
- 新[HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject)和`WheelchairUse`方法[HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)類別已新增了殘障人士使用相關的健全狀況資料。
- 新的中繼資料金鑰已經加入的天氣類型 (例如`HKWeatherConditionClear`和`HKWeatherConditionCloudy`) 和健身類型 (例如`HKWorkoutActivityTypeFlexibility`和`HKWorkoutActivityTypeWheelchairRunPace`) 已加入。


## <a name="homekit"></a>HomeKit

下列增強功能已對監看式作業系統 3 HomeKit 架構：

- 加入檢視並與其互動 HomeKit 能夠連線 IP 相機。
- 加入數個新的服務和特性。
- 加入更多的內容和組態的主要服務和連結服務的附屬應用程式。


## <a name="passkit"></a>PassKit

下列增強功能已對監看式作業系統 3 PassKit 架構：

- 展開要在 Apple Watch 的實體貨物與服務上支援安全、 在應用程式付款架構。
- 下列類別現在都： [PKPayment](https://developer.apple.com/reference/passkit/pkpayment)， [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod)， [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest)和[PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

下列增強功能已對監看式作業系統 3 UIKit 架構：

- 若要支援動態類型標籤中，文字欄位與文字方塊使用新`PreferredFontForTextStyle`方法`UIFont`類別。
- `ColorWithDisplayP3`方法已加入來支援廣泛的色彩。


## <a name="related-links"></a>相關連結

- [使用者入門 （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [什麼是 watchOS 3 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
