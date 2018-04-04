---
title: .NET 標準 2.0 的支援 Xamarin.Forms 中
description: 本文說明如何將轉換為使用.NET 標準 2.0 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 8685f1e10b5094e6f58e8efea51e6dd216dfa000
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="net-standard-20-support-in-xamarinforms"></a>.NET 標準 2.0 的支援 Xamarin.Forms 中

_本文說明如何將轉換為使用.NET 標準 2.0 Xamarin.Forms 應用程式。_

.NET 標準是一種規格的.NET Api，要用於所有.NET 實作。 它可讓您更輕鬆地跨桌面應用程式、 行動裝置應用程式與遊戲、 共用程式碼和雲端服務，將相同應用程式開發介面結合以不同的平台。 如需支援的.NET 標準平台資訊，請參閱[.NET 實作支援](/dotnet/standard/net-standard#net-implementation-support/)。

標準.NET 程式庫會取代可攜式類別程式庫 (PCL)。 不過，為目標的.NET 標準程式庫仍在 PCL 中，而且指.NET 標準為基礎的 PCL。 某些 PCL 設定檔會對應至.NET 標準版本中，並針對沒有對應的設定檔，兩個程式庫類型將能夠互相參考。 如需詳細資訊，請參閱[PCL 相容性](/dotnet/standard/net-standard#pcl-compatibility)。

Xamarin.Forms 2.4 允許 Xamarin.Forms 應用程式為目標的.NET 標準 2.0 PCL 取代.NET 標準 2.0 程式庫。 這可以如下所示：

- 請確定[.NET Core 2.0](https://www.microsoft.com/net/download/core)安裝。
- 更新 Xamarin.Forms 方案，以使用 Xamarin.Forms 2.4，或更新版本。
- 加入至方案，以.NET 標準 2.0 為目標的.NET 標準程式庫。
- 刪除類別加入至.NET 標準程式庫。
- Xamarin.Forms 2.4 （或以上） NuGet 封裝加入標準.NET 程式庫。
- 在平台專案中，加入.NET 標準程式庫的參考和移除包含 Xamarin.Forms 使用者介面邏輯 PCL 專案的參考。
- PCL 專案中的檔案複製.NET 標準程式庫。
- 移除 PCL 專案，其中包含 Xamarin.Forms 使用者介面邏輯。


## <a name="related-links"></a>相關連結

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [程式碼共用選項](~/cross-platform/app-fundamentals/code-sharing.md)
