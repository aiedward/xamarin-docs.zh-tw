---
title: Xamarin 中的 .NET Standard 2.0 支援
description: 本文說明如何轉換 Xamarin Forms 應用程式，以使用 .NET Standard 2.0。 .NET Standard 是 .NET Api 的規格，其目標是要提供給所有 .NET 的執行。
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: eaef607e3e6095ccc7dfb1f5831d2384d11cab5f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760053"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>Xamarin 中的 .NET Standard 2.0 支援

_本文說明如何轉換 Xamarin Forms 應用程式，以使用 .NET Standard 2.0。_

.NET Standard 是 .NET Api 的規格，其目標是要提供給所有 .NET 的執行。 藉由將相同的 Api 帶入不同的平臺，可讓您更輕鬆地跨桌面應用程式、行動應用程式和遊戲和雲端服務共用程式碼。 如需 .NET Standard 支援之平臺的詳細資訊，請參閱[.net 部署支援](/dotnet/standard/net-standard#net-implementation-support)。

.NET Standard 程式庫是可移植類別庫（PCL）的取代。 不過，以 .NET Standard 為目標的程式庫仍然是 PCL，而是以 .NET Standard 為基礎的 PCL。 某些 PCL 設定檔會對應至 .NET Standard 版本，而對於具有對應的設定檔，這兩個程式庫類型將能夠彼此參考。 如需詳細資訊，請參閱[PCL 相容性](/dotnet/standard/net-standard#pcl-compatibility)。

Xamarin 2.4 藉由以 .NET Standard 2.0 程式庫取代 PCL，讓 Xamarin. Forms 應用程式以 .NET Standard 2.0 為目標。 這可以透過下列方式來達成：

- 請確定已安裝[.Net Core 2.0](https://www.microsoft.com/net/download/core) 。
- 將 [Xamarin] 方案更新為使用2.4 或更高版本。
- 將 .NET Standard 程式庫新增至解決方案，以 .NET Standard 2.0 為目標。
- 刪除新增至 .NET Standard 程式庫的類別。
- 將 [Xamarin 2.4 （或更新版本）] NuGet 套件新增至 .NET Standard 程式庫。
- 在平臺專案中，新增 .NET Standard 程式庫的參考，並移除包含 Xamarin 使用者介面邏輯之 PCL 專案的參考。
- 將檔案從 PCL 專案複製到 .NET Standard 程式庫。
- 移除包含 Xamarin 使用者介面邏輯的 PCL 專案。

## <a name="related-links"></a>相關連結

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [程式碼共用選項](~/cross-platform/app-fundamentals/code-sharing.md)
