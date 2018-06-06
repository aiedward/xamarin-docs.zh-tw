---
title: 第 3-部分設定 Xamarin 的跨平台解決方案
description: 本文件說明如何設定 Xamarin 的跨平台解決方案。 它 discuses 各種程式碼，例如共用策略共用專案和.NET 標準。
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: f802e31d851915d33cb6dbf5866f8cba3ab90303
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34781268"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>第 3-部分設定 Xamarin 的跨平台解決方案

不論使用何種平台，所有的 Xamarin 專案都使用相同的方案檔案格式 (在 Visual Studio **.sln**檔案格式)。 方案可以共用的開發環境，即使在個別的專案無法載入 （例如適用於 Mac 的 Visual Studio 中的 Windows 專案）。



在建立新的跨平台應用程式時，第一個步驟是建立空白方案。 此區段會發生什麼情況下一步： 設定建置跨平台行動應用程式的專案。

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>共用程式碼

請參閱[程式碼共用選項](~/cross-platform/app-fundamentals/code-sharing.md)詳細說明如何實作跨平台的程式碼共用的文件。

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>共用的專案

共用程式碼檔案最簡單的方式是使用[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)。

這個方法可讓您跨不同的平台專案中，共用相同的程式碼，並使用編譯器指示詞，包括不同的平台專屬程式碼的路徑。

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>可攜式類別庫 (PCL)

在過去.NET 專案檔 （和產生的組件） 其目標為特定的 framework 版本。 這可防止由不同架構所共用的組件或專案。

可攜式類別程式庫 (PCL) 是專案的一種特殊型別的可以跨不同的 CLI 平台，例如 Xamarin.iOS 和 Xamarin.Android，WPF、 通用 Windows 平台，以及 Xbox 使用。 文件庫只可以利用做為目標的平台受限於完整.NET framework 的子集。

閱讀更多有關 Xamarin 的[之可攜式類別庫支援](~/cross-platform/app-fundamentals/pcl.md)並遵循此處的指示以查看如何[TaskyPortable 範例](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)的運作方式。


### <a name="net-standard"></a>.NET Standard

在 2016 中，導入[.NET 標準](~/cross-platform/app-fundamentals/net-standard.md)專案可讓您輕鬆共用程式碼跨平台，產生可用於 Windows 的組件、 Xamarin 平台 (iOS、 Android、 Mac) 和 Linux。

可建立及使用 PCLs，類似，不同之處在於會更輕鬆地探索每個版本 （從至 1.6 1.0) 中所提供的 Api，且每個版本回溯相容.NET 標準程式庫具有較低版本號碼。



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>填入方案

無論哪一種方法使用共用的程式碼，整體解決方案結構應該實作鼓勵共用程式碼的多層式的架構。
Xamarin 方法是群組到兩個專案類型的程式碼：

-   **核心專案**– 撰寫可重複使用的程式碼中各種不同平台共用的一個位置。 若要隱藏實作詳細資料，可能的情況下，利用封裝 （encapsulation） 的原則。
-   **平台專屬的應用程式專案**– 使用儘可能少結合可重複使用的程式碼。 加入平台專屬功能，在此層級，內建於核心專案中公開的元件。


 <a name="Core_Project" />


### <a name="core-project"></a>核心專案

共用程式碼專案應僅參考會提供所有平台上 – ie 的組件。常見的 framework 命名空間類似`System`，`System.Core`和`System.Xml`。

共用的專案應實作一樣多的非 UI 功能，有可能，可能包括下列層級：

-   **資料層**– 例如會負責實體資料存放區的程式碼。  [SQLite NET](https://github.com/praeclarum/sqlite-net)，替代的資料庫類似[Realm.io](https://realm.io/products/realm-mobile-database/)或甚至 XML 檔案。 通常是資料層的類別只供資料存取層。
-   **資料存取層**– 定義支援應用程式的功能所需的資料作業，例如方法來存取資料，個別資料項目的清單，並建立、 編輯和刪除這些應用程式開發介面。
-   **服務的存取層**– 選擇性的層，來提供雲端服務應用程式。 包含會存取遠端網路資源 （web 服務、 映像下載等等） 的程式碼，可能快取的結果。
-   **商業層**– 模型類別和功能公開給特定平台應用程式的外觀或管理員類別的定義。


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>平台專屬的應用程式專案

平台專屬專案必須參考繫結至每個平台 SDK （Xamarin.iOS、 Xamarin.Android、 Xamarin.Mac 或 Windows） 以及核心共用程式碼專案所需的組件。

平台專屬專案應實作：

-   **應用程式層**– 平台特定功能和商務層物件和使用者介面之間的繫結/轉換。
-   **使用者介面層**– 螢幕、 自訂使用者介面控制項、 驗證邏輯的呈現方式。


<a name="Example" />


### <a name="example"></a>範例

在此圖說明將應用程式架構：

 [ ![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "在此圖說明將應用程式架構")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

這個螢幕擷取畫面顯示共用的核心專案、 iOS 和 Android 的應用程式專案與方案安裝程式。 共用專案會包含每個架構圖層 （商務、 服務、 資料和資料存取程式碼） 相關的程式碼：

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "共用專案中包含每個架構圖層 （商務、 服務、 資料和資料存取程式碼） 相關的程式碼")


 <a name="Project_References" />


## <a name="project-references"></a>專案參考

專案參考會反映專案的相依性。 Core 專案限制其常見的組件的參考，讓您輕鬆地共用程式碼。
平台專屬的應用程式專案會參考共用程式碼中，加上它們需要利用目標平台的任何其他平台專屬組件。

應用程式專案每個參考共用專案，而且包含呈現給使用者，功能所需的使用者介面程式碼，這些螢幕擷取畫面所示：

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "應用程式專案中 Shared 專案的每個參考") ![ ](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "應用程式專案中每個參考共用專案")


在這些案例研究會提供特定的專案結構的方式的範例。

 <a name="Adding_Files" />


## <a name="adding-files"></a>新增檔案

 <a name="Build_Action" />


### <a name="build-action"></a>建置動作

請務必設定為特定檔案類型正確的建置動作。 此清單會顯示一些常見的檔案類型的建置動作：

-  **所有 C# 檔案**– 建置動作： 編譯
-   **Xamarin.iOS & Windows 映像**– 建置動作： 內容
-   **Xamarin.iOS XIB 和分鏡腳本檔案**– 建置動作： InterfaceDefinition
-   **映像和 Android AXML 配置**– 建置動作： AndroidResource
-  **Windows 專案中的 XAML 檔案**– 建置動作： 頁面
-  **Xamarin.Forms XAML 檔案**– 建置動作： EmbeddedResource


通常在 IDE 會偵測到的檔案類型，並建議的正確建置動作。

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>區分大小寫

最後，請記住，某些平台都有區分大小寫的檔案系統 （例如。
iOS 和 Android)，請務必使用一致的檔案命名標準，並請確定您在程式碼中使用的檔案名稱完全相符的檔案系統。 這是特別重要，而影像和其他資源，您在程式碼中參考的項目。
