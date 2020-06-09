---
title: 在 Xamarin 中使用使用者預設值
description: 本文說明如何使用使用 nsuserdefaults 來儲存 Xamarin iOS 應用程式或延伸模組中的預設設定。 它會描述高階使用 nsuserdefaults，並討論如何讀取和寫入值。
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: c65a06b8f2a04eda669b2d741135538fa8a583f2
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571723"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>在 Xamarin 中使用使用者預設值

_本文涵蓋如何使用 NSUserDefault 來儲存 Xamarin iOS 應用程式或延伸模組中的預設設定。_

`NSUserDefaults`類別可讓 IOS 應用程式和延伸模組以程式設計方式與全系統的預設系統互動。 藉由使用預設系統，使用者可以設定應用程式的行為或樣式，以符合其喜好設定（根據應用程式的設計）。 例如，若要呈現度量與英制測量的資料，或選取特定的 UI 主題。

與應用程式群組搭配使用時， `NSUserDefaults` 也會提供在指定群組內的應用程式（或擴充功能）之間進行通訊的方式。

<a name="About-User-Defaults"></a>

## <a name="about-user-defaults"></a>關於使用者預設值

如上所述，可以將使用者預設值（ `NSUserDefaults` ）新增至應用程式（或擴充功能），並用來提供可設定的選項，讓使用者可以修改以在執行時間調整應用程式的外觀或操作。

當您的應用程式第一次執行時， `NSUserDefaults` 會從應用程式的使用者預設資料庫讀取金鑰和值，並將其快取到記憶體，以避免在每次需要值時開啟和讀取資料庫。 

> [!IMPORTANT]
> Apple 不再建議開發人員呼叫 `Synchronize` 方法，直接將記憶體中的快取與資料庫同步。 相反地，它會以定期間隔自動呼叫，讓記憶體內部快取與使用者的預設資料庫保持同步。

`NSUserDefaults`類別包含數個便利的方法，可用於讀取和寫入常見資料類型的喜好設定值，例如：字串、整數、浮點數、布林值和 url。 您可以使用來封存其他類型的資料 `NSData` ，然後讀取或寫入使用者預設資料庫。 如需詳細資訊，請參閱 Apple 的[喜好設定和設定程式設計指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)。

<a name="Accessing-the-Shared-NSUserDefaults-Instance"></a>

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>存取共用的使用 nsuserdefaults 實例 

[共用使用者預設值] 實例可讓您存取裝置目前使用者的使用者預設值。 如果共用預設物件不存在，則會在第一次存取時建立，並以下列資訊初始化：

- `NSArgumentDomain`，包含從目前應用程式剖析的預設值。
- 應用程式的套件組合識別碼網域。
- ， `NSGlobalDomain` 包含所有應用程式共用的預設值。
- 每個使用者慣用語言的個別網域。
- ， `NSRegistrationDomain` 具有一組可由應用程式修改的暫時預設值，以確保搜尋一律成功。

若要存取共用使用者預設實例，請使用下列程式碼：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance"></a>

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>存取應用程式群組使用 nsuserdefaults 實例

如上所述，藉由使用應用程式群組， `NSUserDefaults` 可以用來在指定群組內的應用程式（或擴充功能）之間進行通訊。 首先，您必須確定已在[IOS 開發人員中心](https://developer.apple.com/devcenter/ios/)的 [**憑證]、[識別碼 & 設定檔**] 區段中正確設定應用程式群組和所需的應用程式識別碼，並已安裝在開發環境中。

接下來，您的應用程式和/或延伸模組專案必須具有上述所建立的其中一個有效應用程式識別碼，而且該檔案必須 `Entitlements.plist` 包含在應用程式群組中，並已啟用並指定應用程式群組。

這麼做之後，就可以使用下列程式碼來存取共用應用程式群組使用者預設值：

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

其中 `group.com.xamarin.todaysharing` 是在憑證中建立的應用程式群組 **，識別碼 &** 您要存取的設定檔。 如需詳細資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)檔。

<a name="Reading-Default-Values"></a>

## <a name="reading-default-values"></a>讀取預設值

存取所需的使用者預設資料庫之後，您可以使用索引鍵/值組來讀取預設值，並根據所要讀取的資料類型，以數個便利的方法來閱讀：

- `ArrayForKey`-傳回指定之索引 `NSObjects` 鍵值的陣列。
- `BoolForKey`-傳回指定索引鍵的布林值。
- `DataForKey`- `NSData` 傳回指定索引鍵的物件。
- `DictionaryForKey`- `NSDictionary` 傳回指定索引鍵的。
- `DoubleForKey`-傳回指定索引鍵的雙精度浮點數。
- `FloatForKey`-傳回指定索引鍵的浮點值。
- `IntForKey`-傳回指定索引鍵的整數值。
- `StringArrayForKey`- `String` 從指定的索引鍵值傳回物件的陣列。
- `StringForKey`-傳回指定索引鍵的字串值。
- `URLForKey`- `NSUrl` 傳回指定索引鍵的值。

例如，下列程式碼會從使用者預設值讀取布林值：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values"></a>

## <a name="writing-default-values"></a>寫入預設值

就像讀取上述的值，在您存取所需的使用者預設資料庫之後，您可以使用索引鍵/值組，並根據所要寫入的資料類型，將值寫入預設值，以及數個便利的方法：

- `SetBool`-將指定的布林值寫入指定的索引鍵。
- `SetDouble`-將指定的 double 值寫入指定的索引鍵。
- `SetFloat`-將指定的 float 值寫入指定的索引鍵。
- `SetString`-將指定的字串值寫入指定的索引鍵。
- `SetURL`-將指定的 URL （ `NSUrl` ）值寫入指定的索引鍵。

例如，下列程式碼會將布林值寫入至使用者預設值：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> 當您的應用程式第一次執行時， `NSUserDefaults` 會從應用程式的使用者預設資料庫讀取金鑰和值，並將其快取到記憶體，以避免在每次需要值時開啟和讀取資料庫。

<a name="Summary"></a>

## <a name="summary"></a>總結

本文涵蓋 `NSUserDefaults` 類別，以及如何使用它來提供一組可供使用者用來設定您的 Xamarin IOS 應用程式的選項。 此外，它涵蓋了如何使用應用程式群組，在擴充功能和其父應用程式之間或群組中的應用程式之間進行通訊。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [喜好設定與設定程式設計指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [使用 nsuserdefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
