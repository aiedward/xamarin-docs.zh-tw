---
title: .NET standard 2.0 的支援在 Xamarin.Forms 中
description: 這篇文章說明如何使用.NET Standard 2.0 將 Xamarin.Forms 應用程式。 .NET standard 是主要做為在所有.NET 實作上提供的.NET Api 規格。
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: c3e46592bb8760ff85eaeb5dce119897a97dfe89
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667850"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>.NET standard 2.0 的支援在 Xamarin.Forms 中

_這篇文章說明如何使用.NET Standard 2.0 將 Xamarin.Forms 應用程式。_

.NET standard 是主要做為在所有.NET 實作上提供的.NET Api 規格。 它可讓您更輕鬆地跨桌面應用程式、 行動裝置應用程式與遊戲、 共用程式碼部署和雲端服務，讓不同的平台相同的 Api。 如需支援的.NET Standard 平台的資訊，請參閱[.NET 實作支援](/dotnet/standard/net-standard#net-implementation-support)。

.NET standard 程式庫會取代為可攜式類別庫 (PCL)。 不過，.NET Standard 為目標的程式庫仍在 PCL 中，而且指.NET Standard 型 PCL。 某些 PCL 設定檔會對應至.NET Standard 版本，並針對沒有對應的設定檔，兩個程式庫類型都能夠彼此參考。 如需詳細資訊，請參閱 < [PCL 相容性](/dotnet/standard/net-standard#pcl-compatibility)。

Xamarin.Forms 2.4 可取代.NET Standard 2.0 程式庫的 PCL，讓目標為.NET Standard 2.0 的 Xamarin.Forms 應用程式。 達成，如下所示：

- 請確定[.NET Core 2.0](https://www.microsoft.com/net/download/core)安裝。
- 更新 Xamarin.Forms 方案，以使用 Xamarin.Forms 2.4，或更新版本。
- 加入方案，以.NET Standard 2.0 為目標的.NET Standard 程式庫。
- 刪除類別新增至.NET Standard 程式庫。
- 加入.NET Standard 程式庫中的 Xamarin.Forms 2.4 （或以上） NuGet 套件。
- 在平台專案中，加入.NET Standard 程式庫的參考，並移除包含 Xamarin.Forms 使用者介面邏輯的 PCL 專案的參考。
- PCL 專案檔案複製到.NET Standard 程式庫。
- 移除的 PCL 專案，其中包含 Xamarin.Forms 使用者介面邏輯。


## <a name="related-links"></a>相關連結

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [程式碼共用選項](~/cross-platform/app-fundamentals/code-sharing.md)
