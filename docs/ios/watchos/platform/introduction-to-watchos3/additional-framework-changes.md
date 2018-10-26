---
title: 其他 watchOS 3 架構變更
description: 本文件說明各種不同的架構變更引進 watchOS 3，以及如何在 Xamarin 中使用它們。 討論核心資料、 Core 影片、 Foundation、 HealthKit、 HomeKit、 PassKit 和 UIKit。
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 745c39dab1f73870ce036791434ed9a0b05d681b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122617"
---
# <a name="additional-watchos-3-frameworks-changes"></a>其他 watchOS 3 架構變更

_本文章涵蓋其他、 次要的變更或增強功能到現有的架構，針對 watchos 3 多。_

IOS 的主要變更，除了 Apple 方面所做的修改以及數個現有的架構的改善 watchOS 3。


## <a name="core-data"></a>核心資料

下列增強功能會對監看式 3 作業系統的核心資料架構：

- 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援並行的判定為失敗，並擷取沒有序列化。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別維護的 SQLite 資料存放區集區。
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件與 SQLite WAL 日誌模式支援新的查詢產生的資料存放區功能，受管理物件內容 （模式） 可以釘選到未來擷取特定的資料庫版本和失敗的交易。
- 使用高階`NSPersistenceContainer`參考`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心資料設定資源。
- 已新增數個新的便利方法`NSManagedObject`更容易執行提取，並建立子類別。

如需詳細資訊，請參閱 Apple [Core Data Framework 參考](https://developer.apple.com/reference/coredata)。


## <a name="core-motion"></a>Core 動畫

下列增強功能會對監看式 3 作業系統的核心移動架構：

- 新的裝置動作事件會提供動作和方向的更新使用加速計和陀螺儀。 此更新 （按最多 100 Hz 的費率），可以註冊應用程式的應用程式。
- 新的 Pedometer 事件可讓快速、 即時的通知，當使用者暫停和繼續執行。 使用[CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer)前景或背景 pedometer 事件註冊。


## <a name="foundation"></a>Foundation

下列增強功能會對監看式 3 作業系統的 Foundation 架構：

- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別，例如持續時間，來比較的間隔，並測試間隔交集的日期和時間間隔計算。
- 已新增數個新屬性[NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別來取得本機資訊和可用的顯示格式。
- 使用新[NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement)類別來轉換之間不同單位的量值 (UOM)，或在不同的 UOMs 中的值上執行計算。
- 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)格式化當地語系化的度量單位，顯示給使用者的類別。
- 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)並[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)類別，表示特定 UOMs。


## <a name="healthkit"></a>HealthKit

下列增強功能所做的監看式作業系統 3 HealthKit framework:

- 使用新[HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration)類別，以指定`ActivityType`和`LocationType`的了。
- 新[HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject)並`WheelchairUse`方法[HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)類別已新增用於殘障人士相關健全狀況資料。
- 天氣類型已新增新的中繼資料索引鍵 (例如`HKWeatherConditionClear`並`HKWeatherConditionCloudy`) 和健身類型 (例如`HKWorkoutActivityTypeFlexibility`和`HKWorkoutActivityTypeWheelchairRunPace`) 已新增。


## <a name="homekit"></a>HomeKit

下列增強功能所做的監看式作業系統 3 HomeKit 架構：

- 新增檢視並與其互動 HomeKit 能夠連線 IP 相機。
- 加入數個新的服務和特性。
- 新增更多的內容和組態的主要服務和連結服務的附屬應用程式。


## <a name="passkit"></a>PassKit

下列增強功能所做的監看式作業系統 3 PassKit 架構：

- 擴充架構，以支援在實體產品和服務的 Apple Watch 上的安全、 應用程式內的付款。
- 現已提供下列類別： [PKPayment](https://developer.apple.com/reference/passkit/pkpayment)， [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod)， [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest)並[PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

下列增強功能會對監看式作業系統 3 的 UIKit 架構：

- 若要支援動態型別在標籤中，文字欄位和文字方塊使用新`PreferredFontForTextStyle`方法的`UIFont`類別。
- `ColorWithDisplayP3`方法已加入來支援各種色彩。


## <a name="related-links"></a>相關連結

- [開始使用 （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [WatchOS 3 中最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
