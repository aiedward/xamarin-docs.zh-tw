---
title: 第3部分-設定 Xamarin 跨平臺解決方案
description: 本檔說明如何在 Xamarin 中設定跨平臺解決方案。 它討論各種程式碼共用策略，例如共用專案和 .NET Standard。
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: davidortinau
ms.author: daortin
ms.date: 03/27/2017
ms.openlocfilehash: 843887282c9a5af671d46699ae2f601fd32902e0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016877"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>第3部分-設定 Xamarin 跨平臺解決方案

不論使用的平臺為何，Xamarin 專案都使用相同的方案檔格式（Visual Studio **.sln**檔案格式）。 解決方案可以在開發環境之間共用，即使無法載入個別專案（例如 Visual Studio for Mac 中的 Windows 專案）。

建立新的跨平臺應用程式時，第一個步驟是建立空白的解決方案。 本節說明接下來會發生的情況：設定專案以建立跨平臺行動應用程式。

 <a name="Sharing_Code" />

## <a name="sharing-code"></a>共用程式碼

如需如何跨平臺執行程式碼共用的詳細說明，請參閱程式[代碼共用選項](~/cross-platform/app-fundamentals/code-sharing.md)檔。

 <a name="Shared_Asset_Projects" />

### <a name="shared-projects"></a>共用的專案

共用程式碼檔案最簡單的方法是使用[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)。

這個方法可讓您在不同的平臺專案之間共用相同的程式碼，並使用編譯器指示詞來包含不同的平臺特定程式碼路徑。

 <a name="Portable_Class_Libraries" />

### <a name="portable-class-libraries-pcl"></a>可攜式類別庫 (PCL)

在過去，.NET 專案檔（和產生的元件）都是以特定的 framework 版本為目標。 這會防止專案或元件由不同的架構所共用。

可移植的類別庫（PCL）是一種特殊類型的專案，可跨不同的 CLI 平臺使用，例如 Xamarin 和 Xamarin，以及 WPF、通用 Windows 平臺和 Xbox。 程式庫只能使用完整 .NET framework 的子集，受限於目標平臺。

您可以閱讀更多有關 Xamarin 的[可攜性類別庫支援](~/cross-platform/app-fundamentals/pcl.md)的資訊，並遵循其中的指示來查看[TaskyPortable 範例](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)的運作方式。

### <a name="net-standard"></a>.NET Standard

[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)專案在2016中引進，可讓您輕鬆地跨平臺共用程式碼，產生可跨 Windows、Xamarin 平臺（IOS、Android、Mac）和 Linux 使用的元件。

.NET Standard 程式庫可建立及使用，就像 Pcl 一樣，不同的是，每個版本（1.0 到1.6）中可用的 Api 更容易探索，而且每個版本都與較低版本號碼回溯相容。

 <a name="Populating_the_Solution" />

## <a name="populating-the-solution"></a>填入解決方案

不論使用哪一個方法來共用程式碼，整體的解決方案結構都應該執行一個可鼓勵程式碼共用的多層式架構。
Xamarin 的方法是將程式碼分組為兩種專案類型：

- **核心專案**–在一個位置撰寫可重複使用的程式碼，以在不同的平臺之間共用。 使用封裝的原則，盡可能隱藏執行詳細資料。
- **平臺專屬的應用程式專案**-使用可重複使用的程式碼，盡可能不結合。 平臺特定功能會在此層級新增，並以核心專案中公開的元件為基礎。

 <a name="Core_Project" />

### <a name="core-project"></a>核心專案

共用的程式碼專案應該只參考所有平臺都能使用的元件– ie。通用架構命名空間，例如 `System`、`System.Core` 和 `System.Xml`。

共用的專案應該盡可能地執行非 UI 功能，其中可能包含下列層級：

- **資料層**-負責處理實體資料儲存的程式碼，例如  [SQLite-NET](https://github.com/praeclarum/sqlite-net)，這是一種替代的資料庫，例如[Realm.io](https://realm.io/products/realm-mobile-database/)或甚至是 XML 檔案。 資料層類別通常僅供資料存取層使用。
- **資料存取層**-定義支援應用程式功能所需資料作業的 API，例如存取資料清單的方法、個別資料項目，以及建立、編輯和刪除這些專案。
- **服務存取層**–提供雲端服務給應用程式的選擇性層。 包含的程式碼可存取遠端網路資源（web 服務、影像下載等），以及可能的結果快取。
- **商務層**–模型類別的定義，以及對平臺特定應用程式公開功能的外觀或管理員類別。

 <a name="Platform-Specific_Application_Projects" />

### <a name="platform-specific-application-projects"></a>平臺特定應用程式專案

平臺特定專案必須參考系結至每個平臺的 SDK （Xamarin. iOS、Xamarin、Xamarin 或 Windows），以及核心共用程式碼專案所需的元件。

平臺特定專案應該會執行：

- **應用層**-商務層物件與使用者介面之間的平臺特定功能和系結/轉換。
- **使用者介面層**–螢幕、自訂使用者介面控制項、驗證邏輯的呈現方式。

<a name="Example" />

### <a name="example"></a>範例

此圖說明應用程式架構：

 [![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "The application architecture is illustrated in this diagram")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

這個螢幕擷取畫面顯示了使用共用核心專案、iOS 和 Android 應用程式專案設定的解決方案。 共用專案包含與每個架構層（商務、服務、資料和資料存取程式碼）相關的程式碼：

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "The Shared Project contains code relating to each of the architectural layers (Business, Service, Data and Data Access code)")

 <a name="Project_References" />

## <a name="project-references"></a>專案參考

專案參考會反映專案的相依性。 核心專案會限制其對一般元件的參考，讓程式碼易於共用。
平臺專屬的應用程式專案會參考共用程式碼，再加上所需的任何其他平臺特定元件，以利用目標平臺。

應用程式會投射每個參考共用專案，並包含將功能呈現給使用者所需的使用者介面程式碼，如下列螢幕擷取畫面所示：

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "應用程式專案每個參考共用專案") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "應用程式專案每個參考共用專案")

案例研究中提供如何結構化專案的特定範例。

 <a name="Adding_Files" />

## <a name="adding-files"></a>新增檔案

 <a name="Build_Action" />

### <a name="build-action"></a>建置動作

請務必為特定檔案類型設定正確的組建動作。 此清單會顯示一些常見檔案類型的 [建立] 動作：

- **所有C#** 檔案–組建動作：編譯
- **Xamarin 中的影像 & Windows** –組建動作：內容
- **Xamarin 中的 XIB 和**分鏡腳本檔案–組建動作： InterfaceDefinition
- **Android 中的影像和 AXML 版面**配置–組建動作： AndroidResource
- **Windows 專案中的 XAML**檔案–組建動作：頁面
- **Xamarin. 表單 XAML**檔案–組建動作： EmbeddedResource

通常，IDE 會偵測檔案類型，並建議正確的組建動作。

 <a name="Case_Sensitivity" />

### <a name="case-sensitivity"></a>區分大小寫

最後請記住，有些平臺有區分大小寫的檔案系統（例如，
iOS 和 Android），因此請務必使用一致的檔案命名標準，並確定您在程式碼中使用的檔案名與檔案系統完全相符。 對於您在程式碼中參考的映射和其他資源而言，這一點特別重要。
