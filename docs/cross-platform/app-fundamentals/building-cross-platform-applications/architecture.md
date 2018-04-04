---
title: 第 2 部-架構
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/27/2017
ms.openlocfilehash: d8da218e081b0c3da7a7590f232c8e604b98a790
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="part-2---architecture"></a>第 2 部-架構

建置跨平台應用程式的主要宗旨是建立借用本身進行極大化跨平台共用的程式碼的架構。 遵循下列的物件導向程式設計原則可協助建置架構良好的應用程式：

-   **封裝**– 確保類別和甚至架構圖層只公開執行其必要的最小 API 函式，並隱藏實作詳細資料。 在類別層級中，這表示物件的行為視為 '黑箱' 和，使用程式碼不需要知道他們完成其工作的方式。 在架構層級，則表示實作類似外觀鼓勵協調代表更抽象層程式碼更複雜的互動的簡化的 API 的模式。 這表示，UI 程式碼 （例如） 只應負責顯示螢幕，並接受使用者輸入。而且永遠不會與資料庫直接互動。 同樣地的資料存取程式碼應該只能讀取和寫入資料庫，但永遠不會直接互動按鈕或標籤。
-   **責任區分**-請確認每個元件 (在架構和類別層級) 有清除並妥善定義的用途。 每個元件應該只能執行其定義的工作，並公開 （expose） 透過 API 存取需要使用它的類別，該功能。
-   **多型**– 程式設計介面 （或抽象類別） 可支援多個核心程式碼可以寫入與共用跨平台，而仍與平台專屬功能互動的實作方式。


自然的結果會是應用程式模擬真實世界或個別的邏輯層的抽象實體之後。 將程式碼分隔成容易了解、 測試和維護的層級讓應用程式。 建議每個圖層中的程式碼是實體上不同 （是目錄或甚至不同的專案，非常大的應用程式），以及邏輯方式分隔 （使用命名空間）。

 <a name="Typical_Application_Layers" />


## <a name="typical-application-layers"></a>一般應用程式層級

透過這份文件和案例研究我們將下列六個應用程式層級：

-   **資料層**– 靜態資料持續性，可能是 SQLite 資料庫，但無法實作與 XML 檔案或任何其他適當的機制。
-   **資料存取層**– 包裝函式，提供建立、 讀取、 更新、 刪除 (CRUD) 存取的資料，而不會暴露給呼叫者的實作詳細資料的資料層。 例如，DAL 可能包含 SQL 陳述式來查詢或更新的資料，但參考的程式碼不需要知道這項資訊。
-   **商業層**– （有時稱為商務邏輯層或 BLL） 包含商務實體定義 （模型） 和商務邏輯。 商務外觀模式的候選項目。
-   **服務的存取層**– 用來存取雲端中的服務： 從複雜的 web 服務 (REST，JSON，WCF) 的資料與映像的簡單擷取從遠端伺服器。 封裝的網路功能的行為，並提供一個簡單的 API 應用程式和 UI 層使用。
-   **應用程式層**– 通常是平台特定 （通常不是跨平台共用） 或程式碼的程式碼是應用程式特定的 （不通常可重複使用）。 （a） 來判斷此類別是否具有任何實際顯示控制項，或 （b） 是否它無法在之間共用多個螢幕或裝置 （例如是很好的測試要將程式碼放在應用程式層與 UI 層 iPhone 和 iPad）。
-   **使用者介面 (UI) 層級**– 面對使用者的圖層，包含小工具的螢幕和控制器可用於管理它們。


應用程式不一定要包含所有圖層，例如服務的存取層就不存在的應用程式，不會存取網路資源。 因為這些作業有非常基本，非常簡單的應用程式可能會合併資料層和資料存取層。

 <a name="Common_Mobile_Software_Patterns" />


## <a name="common-mobile-software-patterns"></a>常見的行動軟體模式

所擷取週期性常見問題的解決方案的建立的方式的模式。 有幾個重要的模式，有助於您了解建置易於維護/容易了解行動應用程式。

-   **模型中，檢視中，ViewModel (MVVM)** – 模型-檢視-ViewModel 模式是最受歡迎，以支援資料繫結，例如 Xamarin.Forms 的架構。 它是由 XAML 啟用 Sdk 類似 Windows Presentation Foundation (WPF) 和 Silverlight; popularized其中 ViewModel 會做為做為中間人資料 （模型） 與透過資料繫結和命令的使用者介面 （檢視） 之間。
-   **模型檢視控制器 (MVC)** – 一般和通常誤解模式中，MVC 建置使用者介面時最常使用，並提供 UI 畫面 （檢視），負責處理的引擎背後的實際定義之間的區隔互動 （控制站），並於其中填入它 （模型） 的資料。 模型是實際完全選擇性片段，因此，了解此模式的核心在於檢視和控制器。 MVC 是 iOS 應用程式常用的方法。
-   **商務外觀**– 複雜的工作也稱為管理員模式中，提供簡化的進入點。 例如，在追蹤工作的應用程式，您可能必須`TaskManager`類別與方法如`GetAllTasks()`， `GetTask(taskID)` ，`SaveTask (task)`等等。`TaskManager`類別會提供實際儲存/擷取的工作物件的內部運作的外觀。
-   **單一**– 的方法不會存在特定物件的單一執行個體可以提供單一子句模式。 比方說，當使用 SQLite 行動應用程式中，永遠只想資料庫的一個執行個體。 使用單一子句模式是簡單的方式，以確保這。
-   **提供者**– 模式產生由 Microsoft （說是類似於策略或基本的相依性插入），鼓勵跨 Silverlight、 WPF 和 WinForms 應用程式的程式碼重複使用。 針對介面或抽象類別，可寫入共用的程式碼和特定平台的具象實作會撰寫，並使用程式碼時，會傳遞。
-   **非同步**– 不適用於與 Async 關鍵字，長時間執行的工作需要執行，而妨礙 UI 或目前的處理時，會使用模式的非同步混淆。 最簡單的形式，非同步模式只是說明長時間執行的工作應該會開始在另一個執行緒 （或類似的執行緒抽象概念，例如工作） 時的目前的執行緒會繼續處理並監聽背景處理序的回應然後傳回資料，或狀態時，更新 UI。


每個模式會檢查其實際使用如下所示的案例研究的詳細。 維基百科有更詳細的說明[MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel)， [MVC](https://en.wikipedia.org/wiki/Model–view–controller)，[外觀](http://en.wikipedia.org/wiki/Facade_pattern)，[單一](http://en.wikipedia.org/wiki/Singleton_pattern)，[策略](http://en.wikipedia.org/wiki/Strategy_pattern)和[提供者](http://en.wikipedia.org/wiki/Provider_model)模式 (以及[設計模式](http://en.wikipedia.org/wiki/Design_Patterns)通常)。
