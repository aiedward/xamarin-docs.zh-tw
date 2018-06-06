---
title: 使用 Xamarin.iOS 中的使用者預設值
description: 本文件涵蓋使用 NSUserDefaults 儲存 Xamarin iOS 應用程式或擴充功能中的預設設定。 它說明 NSUserDefaults 以高層級，並討論如何讀取和寫入的值。
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 3befe77e990887773d408a3b9b6ec2e8cd371540
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784606"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>使用 Xamarin.iOS 中的使用者預設值

_本文件涵蓋使用 NSUserDefault 儲存 Xamarin.iOS 應用程式或擴充功能中的預設設定。_


`NSUserDefaults`類別會提供適用於 iOS 的應用程式和擴充功能，以程式設計的方式與全系統預設系統互動的方式。 使用預設的系統，使用者可以設定應用程式的行為或樣式以符合他們的喜好設定 （根據應用程式的設計）。 例如，如果帝國度量呈現度量 vs 中的資料，或選取指定的 UI 佈景主題。

應用程式群組搭配使用時`NSUserDefaults`也提供給定群組內的 應用程式 （或擴充功能） 之間進行通訊的方式。

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>關於使用者預設值

如上所述，使用者預設值 (`NSUserDefaults`) 可以加入應用程式 （或擴充），並且用來提供終端使用者可以進行修改以調整外觀或作業的執行階段應用程式的可設定選項。

當您的應用程式第一次執行時，`NSUserDefaults`讀取應用程式的使用者預設資料庫中的索引鍵和值，並快取這些必要的值為要避免開啟和讀取資料庫每次記憶體。 

> [!IMPORTANT]
> Apple 不再建議開發人員呼叫`Synchronize`直接同步處理資料庫的記憶體中快取的方法。 相反地，它會自動呼叫要保留在記憶體中快取與使用者的預設資料庫的同步處理的定期間隔。

`NSUserDefaults`類別包含數個便利的方法來讀取和寫入喜好設定值的一般資料類型，例如： 字串、 整數、 浮點、 布林值和 Url。 其他類型的資料可以使用封存`NSData`，然後讀取或寫入使用者預設資料庫。 如需詳細資訊，請參閱 Apple[喜好設定和設定程式設計指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)。

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>存取共用的 NSUserDefaults 執行個體 

共用使用者預設執行個體提供存取使用者的預設值為目前使用者的裝置。 如果共用的預設值的物件不存在，則會建立第一次存取及初始化下列資訊：

- `NSArgumentDomain`剖析從目前的應用程式的預設值所組成。
- 應用程式的配套識別碼的網域。
- `NSGlobalDomain`共用的所有應用程式的預設值所組成。
- 每個使用者的慣用語言不同的網域。
- `NSRegistrationDomain`與一組可以由應用程式，以確保搜尋永遠都能成功進行修改的暫存預設值。

若要存取共用使用者預設執行個體，請使用下列程式碼：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>存取應用程式群組 NSUserDefaults 執行個體

如上所述，方法是使用應用程式群組`NSUserDefaults`可以用於應用程式 （或擴充功能） 之間的通訊給定群組內。 首先，您必須確定應用程式群組和必要的應用程式識別碼有已正確設定在**憑證、 識別項與設定檔**區段[iOS 開發人員中心](https://developer.apple.com/devcenter/ios/)並已安裝在開發環境。

接下來，必須有一個有效的應用程式識別碼上方，建立您的應用程式和/或擴充功能專案和`Entitlements.plist`有包含在與應用程式群組啟用，並指定應用程式套件組合的檔案。

與就地這一切，共用應用程式群組使用者預設可以存取使用下列程式碼：

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

其中`group.com.xamarin.todaysharing`應用程式群組中建立**憑證、 識別項與設定檔**您想要存取。 如需詳細資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文件。

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>讀取預設值

您已存取所需的使用者預設資料庫之後，您可以從使用索引鍵/值組，以及幾個便利的方法，根據正在讀取的資料類型的預設值來讀取值：

- `ArrayForKey` -傳回陣列`NSObjects`針對給定索引鍵的值。
- `BoolForKey` -傳回布林值，指定索引鍵。
- `DataForKey` -傳回`NSData`指定索引鍵的物件。
- `DictionaryForKey` -傳回`NSDictionary`指定索引鍵。
- `DoubleForKey` -傳回指定索引鍵的雙精度浮點數值。
- `FloatForKey` -傳回指定索引鍵的浮點值。
- `IntForKey` -傳回指定索引鍵的整數值。
- `StringArrayForKey` -傳回陣列`String`物件指定索引鍵的值。
- `StringForKey` -傳回指定的索引鍵的字串值。
- `URLForKey` -傳回`NSUrl`指定索引鍵的值。

例如，下列程式碼會從使用者的預設值讀取布林值：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>寫入預設值

就像讀取上述的值，您已存取所需的使用者預設的資料庫之後, 您可以撰寫值為使用索引鍵/值組，以及幾個便利的方法，根據正在寫入的資料類型的預設值：

- `SetBool` -將指定的布林值寫入指定的索引鍵。
- `SetDouble` -指定的雙精度浮點數值只寫入指定的索引鍵。
- `SetFloat` -將給定的浮點值寫入指定的索引鍵。
- `SetString` -將指定的字串值寫入指定的索引鍵。
- `SetURL` 寫入指定的 URL (`NSUrl`) 指定的索引鍵的值。

例如，下列程式碼會寫入布林值，使用者的預設值：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> 當您的應用程式第一次執行時，`NSUserDefaults`讀取應用程式的使用者預設資料庫中的索引鍵和值，並快取這些必要的值為要避免開啟和讀取資料庫每次記憶體。



<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋`NSUserDefaults`類別，以及如何使用它來提供一組使用者可用來設定 Xamarin.iOS 應用程式的選項。 此外，它涵蓋使用延伸模組和其父應用程式之間或群組中的應用程式之間通訊的應用程式群組。


## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [喜好設定和設定程式設計指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
