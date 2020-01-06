---
title: 第3部分-設定 Xamarin 跨平臺解決方案
description: 本檔說明如何在 Xamarin 中設定跨平臺解決方案。 它討論各種程式碼共用策略，例如共用專案和 .NET Standard。
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: e7cde22115830a845ed82aa907195521f36b6866
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663269"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>第3部分-設定 Xamarin 跨平臺解決方案

不論使用的平臺為何，Xamarin 專案都使用相同的方案檔格式（Visual Studio **.sln**檔案格式）。 解決方案可以在開發環境之間共用，即使無法載入個別專案（例如 Visual Studio for Mac 中的 Windows 專案）。

建立新的跨平臺應用程式時，第一個步驟是建立空白的解決方案。 本節說明接下來會發生的情況：設定專案以建立跨平臺行動應用程式。

## <a name="sharing-code"></a>共用程式碼

如需如何跨平臺執行程式碼共用的詳細說明，請參閱程式[代碼共用選項](~/cross-platform/app-fundamentals/code-sharing.md)檔。

### <a name="net-standard"></a>.NET 標準

[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)專案提供一個簡單的方式，跨平臺共用程式碼，產生可跨 Windows、Xamarin 平臺（IOS、Android、Mac）和 Linux 使用的元件。
這是為 Xamarin 解決方案共用程式碼的建議方式。

### <a name="other-options"></a>其他選項

過去 Xamarin 使用的是[可移植的類別庫（pcl）](~/cross-platform/app-fundamentals/pcl.md)] 和[共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)。 這兩者都不建議用於新的專案;而且，您應該考慮將現有的應用程式遷移到使用 .NET Standard。

## <a name="populating-the-solution"></a>填入解決方案

不論使用哪一個方法來共用程式碼，整體的解決方案結構都應該執行一個可鼓勵程式碼共用的多層式架構。
Xamarin 的方法是將程式碼分組為兩種專案類型：

- **核心（或「共用」）專案**–在一個位置撰寫可重複使用的程式碼，以在不同的平臺之間共用。 使用封裝的原則，盡可能隱藏執行詳細資料。
- **平臺專屬的應用程式專案**-使用可重複使用的程式碼，盡可能不結合。 平臺特定功能會在此層級新增，並以核心專案中公開的元件為基礎。

### <a name="core-project"></a>核心專案

共用程式碼的核心專案應該 .NET Standard，而且只可在所有平臺上使用的參考元件– ie。通用架構命名空間，例如 `System`、`System.Core` 和 `System.Xml`。

核心專案應該盡可能地執行非 UI 功能，其中可能包含下列層級：

- **資料層**-負責處理實體資料儲存的程式碼，例如 [SQLite-NET](https://www.nuget.org/packages/sqlite-net-pcl/)或甚至是 XML 檔案。 資料層類別通常僅供資料存取層使用。
- **資料存取層**-定義支援應用程式功能所需資料作業的 API，例如存取資料清單的方法、個別資料項目，以及建立、編輯和刪除這些專案。
- **服務存取層**–提供雲端服務給應用程式的選擇性層。 包含的程式碼可存取遠端網路資源（web 服務、影像下載等），以及可能的結果快取。
- **商務層**–模型類別的定義，以及對平臺特定應用程式公開功能的外觀或管理員類別。

### <a name="platform-specific-application-projects"></a>平臺特定應用程式專案

平臺特定專案必須參考系結至每個平臺的 SDK （如 [Xamarin]、[Xamarin. Android]、[xamarin] 或 [Windows]）以及 [.NET Standard] 專案所需的元件。

平臺特定專案應該會執行：

- **應用層**-商務層物件與使用者介面之間的平臺特定功能和系結/轉換。
- **使用者介面層**–螢幕、自訂使用者介面控制項、驗證邏輯的呈現方式。

## <a name="project-references"></a>專案參考

專案參考會反映專案的相依性。 核心專案會限制其對一般元件的參考，讓程式碼易於共用。
平臺特定的應用程式專案會參考 .NET Standard 專案，加上所需的任何其他平臺特定元件，以利用目標平臺。

案例研究中提供如何結構化專案的特定範例。

## <a name="adding-files"></a>新增檔案

### <a name="build-action"></a>建置動作

請務必為特定檔案類型設定正確的組建動作。 此清單會顯示一些常見檔案類型的 [建立] 動作：

- **所有C#** 檔案–組建動作：編譯
- **Xamarin 中的影像 & Windows** –組建動作：內容
- **Xamarin 中的 XIB 和**分鏡腳本檔案–組建動作： InterfaceDefinition
- **Android 中的影像和 XML 版面**配置–組建動作： AndroidResource
- **Windows 專案中的 XAML**檔案–組建動作：頁面
- **Xamarin. 表單 XAML**檔案–組建動作： EmbeddedResource

通常，IDE 會偵測檔案類型，並建議正確的組建動作。

### <a name="case-sensitivity"></a>區分大小寫

最後請記住，有些平臺有區分大小寫的檔案系統（例如，
iOS 和 Android），因此請務必使用一致的檔案命名標準，並確定您在程式碼中使用的檔案名與檔案系統完全相符。 對於您在程式碼中參考的映射和其他資源而言，這一點特別重要。
