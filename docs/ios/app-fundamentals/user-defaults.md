---
title: 使用 Xamarin.iOS 中的使用者預設值
description: 這篇文章涵蓋使用 「 使用 NSUserDefaults 來儲存 Xamarin iOS 應用程式或擴充中的預設設定。 它說明使用 NSUserDefaults 概括而言，並討論如何讀取和寫入值。
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 688db534d6c99a8fadb7535f0532f9c1e9564707
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153382"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>使用 Xamarin.iOS 中的使用者預設值

_這篇文章涵蓋使用 NSUserDefault 儲存 Xamarin.iOS 應用程式或擴充中的預設設定。_


`NSUserDefaults`類別會提供適用於 iOS 的應用程式和延伸模組，以程式設計方式與全系統的預設系統互動的方式。 使用預設的系統，使用者可以設定應用程式的行為或設定樣式來符合他們的喜好設定 （根據應用程式的設計而定）。 例如，如果 Imperial 度量呈現計量 vs 中的資料，或選取特定的 UI 佈景主題。

當搭配應用程式群組`NSUserDefaults`也提供給定群組內的 應用程式 （或延伸模組） 之間進行通訊的方式。

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>關於使用者預設值

如上所述，使用者預設值 (`NSUserDefaults`) 可以加入應用程式 （或延伸模組） 在程式碼中，用來提供可設定的選項，使用者可以修改調整外觀或在執行階段的應用程式的作業。

當您的應用程式第一次執行時，`NSUserDefaults`讀取應用程式的使用者預設資料庫中的索引鍵和值，並會加以快取記憶體，以避免開啟並讀取資料庫每次是必要的值。 

> [!IMPORTANT]
> Apple 不會再建議開發人員呼叫`Synchronize`直接同步處理與資料庫的記憶體中快取的方法。 相反地，它就會自動呼叫保留於記憶體中快取與使用者的預設資料庫的同步處理的定期間隔。

`NSUserDefaults`類別包含數個便利的方法，用於讀取和寫入喜好設定的值一般資料型別，例如： 字串、 整數、 浮點、 布林值和 Url。 其他類型的資料可以使用封存`NSData`，然後讀取或寫入使用者預設資料庫。 如需詳細資訊，請參閱 Apple[喜好設定和設定程式設計指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)。

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>存取共用的 NSUserDefaults 執行個體 

共用使用者預設執行個體提供存取使用者的預設值為目前使用者的裝置。 如果共用的預設值的物件不存在，則會建立第一次存取及初始化下列資訊：

- `NSArgumentDomain`剖析從目前的應用程式的預設值所組成。
- 應用程式的套件組合識別碼的網域。
- `NSGlobalDomain`共用的所有應用程式的預設值所組成。
- 針對每個使用者的慣用的語言不同的網域。
- `NSRegistrationDomain`與一組暫存的預設值，就可以修改的應用程式，以確保搜尋一律會成功。

若要存取共用使用者預設執行個體，請使用下列程式碼：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>存取應用程式群組 NSUserDefaults 執行個體

如上所述，使用應用程式群組`NSUserDefaults`可用來溝通應用程式 （或延伸模組） 內指定的群組。 首先，您必須確定該應用程式群組和必要的應用程式識別碼已正確設定在**憑證、 識別碼與設定檔**區段[iOS 開發人員中心](https://developer.apple.com/devcenter/ios/)且已安裝在開發環境。

接下來，您的應用程式和/或延伸模組專案需要有的上方，建立有效的應用程式識別碼和`Entitlements.plist`檔案必須包含在應用程式套件組合，與已啟用，而且指定的應用程式群組。

一切就緒，共用應用程式群組使用者預設可以存取使用下列程式碼：

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

何處`group.com.xamarin.todaysharing`應用程式群組中建立**憑證、 識別碼與設定檔**您想要存取。 如需詳細資訊，請參閱[應用程式群組功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文件。

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>讀取預設值

您已存取所需的使用者預設資料庫之後，您可以使用索引鍵/值配對和數個便利的方法，根據所讀取的資料類型的預設值來讀取值：

- `ArrayForKey` -傳回的陣列`NSObjects`給定索引鍵的值。
- `BoolForKey` -傳回指定的索引鍵的布林值。
- `DataForKey` -傳回`NSData`指定索引鍵的物件。
- `DictionaryForKey` -傳回`NSDictionary`指定索引鍵。
- `DoubleForKey` -傳回指定索引鍵的雙精度浮點數值。
- `FloatForKey` -傳回浮點值，指定索引鍵。
- `IntForKey` -傳回整數值，以指定的索引鍵。
- `StringArrayForKey` -傳回的陣列`String`從指定的索引鍵值的物件。
- `StringForKey` -傳回指定的索引鍵的字串值。
- `URLForKey` -傳回`NSUrl`指定索引鍵的值。

例如，下列程式碼會讀取布林值，從使用者的預設值：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>正在寫入預設值

如同讀取上述的值，您已存取所需的使用者預設的資料庫之後, 您可以撰寫值為使用索引鍵/值配對和數個便利的方法，取決於要寫入的資料類型的預設值：

- `SetBool` -將指定的布林值寫入指定的索引鍵。
- `SetDouble` -將指定的雙精度浮點數值寫入指定的索引鍵。
- `SetFloat` -將給定的浮點值寫入指定的索引鍵。
- `SetString` -將指定的字串值寫入指定的索引鍵。
- `SetURL` 寫入指定的 URL (`NSUrl`) 指定的索引鍵的值。

比方說，下列程式碼會寫入的布林值，使用者的預設值：

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> 當您的應用程式第一次執行時，`NSUserDefaults`讀取應用程式的使用者預設資料庫中的索引鍵和值，並會加以快取記憶體，以避免開啟並讀取資料庫每次是必要的值。



<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已涵蓋`NSUserDefaults`類別，以及如何使用它來提供一組使用者可用來設定您的 Xamarin.iOS 應用程式的選項。 此外，它涵蓋了使用應用程式群組來擴充功能和其父應用程式之間或群組中的應用程式之間進行通訊。


## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [偏好選項及設定程式設計指南](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
