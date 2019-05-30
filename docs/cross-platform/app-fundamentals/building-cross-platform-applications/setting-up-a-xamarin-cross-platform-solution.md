---
title: 第 3 部分-設定 Xamarin 跨平台解決方案
description: 本文件說明如何設定 Xamarin 的跨平台解決方案。 其中討論各種的程式碼共用策略，例如共用的專案和.NET Standard。
ms.prod: xamarin
ms.assetid: 4139A6C2-D477-C563-C1AB-98CCD0D10A93
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: d20275bab4e4ce90f902a5e72321701d94b1d416
ms.sourcegitcommit: 4a1520dee7759f8355ea65c8bb3d1bac8ba58122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2019
ms.locfileid: "66354063"
---
# <a name="part-3---setting-up-a-xamarin-cross-platform-solution"></a>第 3 部分-設定 Xamarin 跨平台解決方案

不論使用何種平台，Xamarin 專案都會使用相同的方案檔案格式 (Visual Studio **.sln**檔案格式)。 即使在個別的專案無法載入 （例如 Visual Studio for Mac 中的 Windows 專案），可以在開發環境，共用解決方案。



在建立新的跨平台應用程式時，第一個步驟是建立空白方案。 本章節將說明接下來的情況： 設定建置跨平台行動應用程式的專案。

 <a name="Sharing_Code" />


## <a name="sharing-code"></a>共用程式碼

請參閱[程式碼共用選項](~/cross-platform/app-fundamentals/code-sharing.md)詳細說明如何實作跨平台的 程式碼共用的文件。

 <a name="Shared_Asset_Projects" />


### <a name="shared-projects"></a>共用的專案

共用程式碼檔案最簡單的方式使用[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)。

這個方法可讓您跨不同的平台專案中，共用相同的程式碼，並使用編譯器指示詞來包含不同的平台特定程式碼的路徑。

 <a name="Portable_Class_Libraries" />


### <a name="portable-class-libraries-pcl"></a>可攜式類別庫 (PCL)

在過去的.NET 專案檔案 （和產生的組件） 其目標為特定的架構版本。 這可防止專案或不同的架構所共用的組件。

可攜式類別庫 (PCL) 是專案的一種特殊的可使用於不同的 CLI 平台，例如 Xamarin.iOS 和 Xamarin.Android 中，以及 WPF、 通用 Windows 平台和 Xbox。 程式庫只可以利用完整的.NET framework，受到 目標平台的子集。

您可以深入了解 Xamarin 的[支援可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)並遵循此處的指示以查看如何[TaskyPortable 範例](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)的運作方式。


### <a name="net-standard"></a>.NET Standard

在 2016 年引進[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)專案提供簡單的方式跨平台，產生可用於 Windows 的組件、 Xamarin 平台 (iOS、 Android、 Mac) 和 Linux 共用程式碼。

.NET standard 程式庫可以建立和使用 Pcl，例如，不同之處在於更輕鬆地探索每個版本 （從 1.0 為 1.6) 中提供的 Api，而且每個版本與舊版相容以較低的版本號碼。



 <a name="Populating_the_Solution" />


## <a name="populating-the-solution"></a>填入解決方案

不論哪一種方法來共用程式碼，整體的方案結構應該實作鼓勵共用程式碼的多層式的架構。
群組至兩個專案類型的程式碼是 Xamarin 方法：

-   **Core 專案**-撰寫可重複使用的程式碼，在一個地方，跨不同平台共用。 若要隱藏實作詳細資料，盡可能使用封裝的原則。
-   **特定平台應用程式專案**– 使用可重複使用程式碼為盡可能的小結合程度。 在此層級，根據在 Core 專案中公開的元件已加入平台專屬功能。


 <a name="Core_Project" />


### <a name="core-project"></a>Core 專案

共用程式碼專案只應該參考組件，可跨所有平台 – ie。常見的 framework 命名空間想`System`，`System.Core`和`System.Xml`。

共用的專案應該實作更多的非 UI 功能，有可能，可能包括下列各層：

-   **資料層**– 例如會處理實體的資料儲存體的程式碼。  [SQLite NET](https://github.com/praeclarum/sqlite-net)，等替代的資料庫[Realm.io](https://realm.io/products/realm-mobile-database/)或甚至是 XML 檔案。 資料層的類別通常是僅供資料存取層。
-   **資料存取層**– 定義支援應用程式的功能的所需的資料作業，例如方法來存取資料，個別資料項目的清單和也建立、 編輯和刪除它們的 API。
-   **服務的存取層**– 選擇性的層，來提供雲端服務應用程式。 包含存取遠端網路資源 （web 服務，影像下載等等） 的程式碼，而且可能快取的結果。
-   **商務層**– 模型類別和功能公開給特定平台應用程式的外觀或管理員類別的定義。


 <a name="Platform-Specific_Application_Projects" />


### <a name="platform-specific-application-projects"></a>特定平台應用程式專案

平台專屬專案必須參考繫結至每個平台 SDK （Xamarin.iOS、 Xamarin.Android、 Xamarin.Mac、 或 Windows） 以及核心共用的程式碼專案所需的組件。

應該實作平台專屬專案：

-   **應用程式層**– 平台特定功能和商務層物件和使用者介面之間的繫結/轉換。
-   **使用者介面層**– 畫面、 自訂的使用者介面控制項、 驗證邏輯的呈現方式。


<a name="Example" />


### <a name="example"></a>範例

應用程式架構是由本圖所示：

 [ ![](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png "此圖說明應用程式架構")](setting-up-a-xamarin-cross-platform-solution-images/conceptualarchitecture.png#lightbox)

此螢幕擷取畫面會顯示與共用的核心的專案、 iOS 和 Android 應用程式專案的方案設定。 共用專案中包含每個架構層 （商務、 服務、 資料和資料存取程式碼） 相關的程式碼：

 ![](setting-up-a-xamarin-cross-platform-solution-images/core-solution-example.png "共用專案中包含每個架構層 （商務、 服務、 資料和資料存取程式碼） 相關的程式碼")


 <a name="Project_References" />


## <a name="project-references"></a>專案參考

專案參考會反映專案的相依性。 Core 專案會限制其常見的組件的參考，讓您輕鬆地共用程式碼。
平台專屬的應用程式專案參考的共用程式碼，以及他們需要利用目標平台的任何其他平台特定組件。

應用程式專案每個參考共用專案，並包含給使用者，有的功能所需的使用者介面程式碼，如下列螢幕擷取畫面所示：

![](setting-up-a-xamarin-cross-platform-solution-images/solution-android.png "應用程式專案中 Shared 專案的每個參考") ![](setting-up-a-xamarin-cross-platform-solution-images/solution-ios.png "應用程式專案中每個參考共用專案")


案例研究中提供的專案結構的方式的特定範例。

 <a name="Adding_Files" />


## <a name="adding-files"></a>新增檔案

 <a name="Build_Action" />


### <a name="build-action"></a>建置動作

請務必設定正確的建置動作為特定檔案類型。 此清單會顯示一些常見的檔案類型的建置動作：

-  **所有C#檔案**– 建置動作：編譯
-   **Xamarin.iOS Windows 中的映像**– 建置動作：內容
-   **在 Xamarin.iOS 中的 XIB 和分鏡腳本檔案**– 建置動作：InterfaceDefinition
-   **映像和 AXML 版面配置，在 Android 中的**– 建置動作：AndroidResource
-  **Windows 專案中的 XAML 檔案**– 建置動作：頁面
-  **Xamarin.Forms XAML 檔案**– 建置動作：內嵌資源


通常在 IDE 會偵測檔案類型，並建議的正確建置動作。

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>區分大小寫

最後，請記住，某些平台有區分大小寫的檔案系統 （例如。
iOS 和 Android) 因此請務必使用一致的檔案命名標準，並確定您在程式碼中使用的檔案名稱完全相符的檔案系統。 這是特別重要，而影像和其他資源，您在程式碼中參考的項目。
