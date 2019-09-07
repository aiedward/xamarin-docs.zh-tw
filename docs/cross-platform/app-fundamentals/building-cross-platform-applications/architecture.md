---
title: 第 2 部分 - 架構
description: 本檔說明可用於建立跨平臺應用程式的架構模式。 它討論一般的應用層（資料層、資料存取層等）和常見的行動軟體模式（MVVM、MVC 等等）
ms.prod: xamarin
ms.assetid: 2176DB2D-E84A-3757-CFAB-04A586068D50
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: e1b1a98bf06bbd03b382f0b7263e6965d4efad15
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762110"
---
# <a name="part-2---architecture"></a>第 2 部分 - 架構

建立跨平臺應用程式的主要原則是建立一個架構，讓自己能夠極大化跨平臺的程式碼共用。 遵守下列物件導向程式設計原則有助於建立架構良好的應用程式：

- **封裝**–確保類別（甚至是架構層）只會公開執行其必要功能的最小 API，並隱藏執行詳細資料。 在類別層級，這表示物件的行為會是「黑色方塊」，而使用程式碼則不需要知道它們如何完成其工作。 在架構層級中，這表示執行外觀之類的模式，以鼓勵簡化的 API 代表更抽象層中的程式碼來協調較複雜的互動。 這表示 UI 程式碼（例如）應該只負責顯示畫面並接受使用者輸入;而且永遠不會直接與資料庫互動。 同樣地，資料存取程式碼應該唯讀寫資料庫，而不是直接與按鈕或標籤互動。
- **責任分隔**–確保每個元件（在架構和類別層級）都具有清楚且妥善定義的目的。 每個元件都應該只執行其定義的工作，並透過可供其他需要使用它的類別存取的 API 來公開該功能。
- **多**型-對支援多個執行的介面（或抽象類別）進行程式設計，表示可以在平臺上撰寫和共用核心程式代碼，同時仍然與平臺特定功能互動。

自然結果是在真實世界或具有個別邏輯層的抽象實體之後模型化的應用程式。 將程式碼分成各層，可讓應用程式更容易瞭解、測試和維護。 建議每個圖層中的程式碼實際分開（在目錄中，或甚至是針對非常大型應用程式的個別專案中），並以邏輯方式分隔（使用命名空間）。

 <a name="Typical_Application_Layers" />

## <a name="typical-application-layers"></a>一般應用層

在整份檔和個案研究中，我們指的是下列六個應用層：

- **資料層**–非動態資料持續性，可能是 SQLite 資料庫，但可以使用 XML 檔案或任何其他適當的機制來執行。
- **資料存取層**–資料層的包裝函式，可提供資料的建立、讀取、更新、刪除（CRUD）存取權，而不會向呼叫者公開執行細節。 例如，DAL 可能包含用來查詢或更新資料的 SQL 語句，但是參考的程式碼不需要知道這個。
- **商務層**–（有時稱為商務邏輯層或 BLL）包含商業實體定義（模型）和商務邏輯。 商業面板模式的候選。
- **服務存取層**-用來存取雲端中的服務：從複雜的 web 服務（REST、JSON、WCF），到簡單地從遠端伺服器抓取資料和影像。 封裝網路行為，並提供應用程式和 UI 層所要使用的簡單 API。
- **應用層**–通常是平臺特定的程式碼（通常不會跨平臺共用），或應用程式特定的程式碼（通常不可重複使用）。 是否要將程式碼放在應用層和 UI 層中的良好測試是（a），用來判斷類別是否有任何實際的顯示控制項，或（b）是否可在多個螢幕或裝置之間共用（例如 iPhone 和 iPad）。
- **使用者介面（UI）層**–使用者面向圖層，包含螢幕、widget 和管理它們的控制器。

應用程式可能不一定包含所有層級–例如，服務存取層不會存在於不會存取網路資源的應用程式中。 非常簡單的應用程式可能會合並資料層和資料存取層，因為作業非常基本。

 <a name="Common_Mobile_Software_Patterns" />

## <a name="common-mobile-software-patterns"></a>常見的行動軟體模式

模式是為常見問題捕捉週期性解決方案的既定方式。 有幾個重要的模式有助於瞭解如何建立可維護/易懂的行動應用程式。

- **Model、View、ViewModel （MVVM）** –模型視圖-ViewModel 模式常用於支援資料系結的架構，例如 Xamarin. Forms。 它是由啟用 XAML 的 Sdk 普及化，例如 Windows Presentation Foundation （WPF）和 Silverlight;其中，ViewModel 是透過資料系結和命令，做為資料（模型）和使用者介面（View）之間的一開始。
- **Model、View、Controller （MVC）** –常見且經常被誤解的模式，MVC 最常用於建立使用者介面，並提供 UI 螢幕（View）的實際定義、處理互動之引擎背後的分隔（控制器）和填入資料（模型）的資料。 模型實際上是完全選擇性的部分，因此瞭解此模式的核心是在 View 和 Controller 中。 MVC 是適用于 iOS 應用程式的常用方法。
- **商務外觀**–也就是管理員模式，為複雜的工作提供簡化的進入點。 例如，在工作追蹤應用程式中，您可能會有`TaskManager`一個類別`GetAllTasks()` ，其中包含、 `GetTask(taskID)` 、 `SaveTask (task)`等方法。`TaskManager`類別提供實際儲存/抓取工作物件之內部運作的外觀。
- **Singleton** –單一模式提供一種方式，讓特定物件的單一實例只能存在。 例如，在行動應用程式中使用 SQLite 時，您只需要一個資料庫實例。 使用單一模式是確保這種情況的簡單方式。
- **提供者**– Microsoft 所 alistair 創造的模式（可與策略或基本相依性插入類似），鼓勵跨 SILVERLIGHT、WPF 和 WinForms 應用程式重複使用程式碼。 共用程式碼可以針對介面或抽象類別來撰寫，並在使用程式碼時，撰寫和傳入平臺特定的實作為。
- **非同步**–不會與 async 關鍵字混淆，非同步模式是在需要執行長時間執行的工作，而不需按住 UI 或目前的處理時使用。 在最簡單的形式中，非同步模式只會描述長時間執行的工作應該在另一個執行緒中啟動（或類似的執行緒抽象，例如工作），而目前的執行緒會繼續處理並接聽來自背景進程的回應，然後在傳回資料和或狀態時，更新 UI。

我們會更詳細地檢查每個模式，因為案例研究中說明了其實際用法。 維琪百科具有[MVVM](https://en.wikipedia.org/wiki/Model–view–viewmodel)、 [MVC](https://en.wikipedia.org/wiki/Model–view–controller)、[外觀](https://en.wikipedia.org/wiki/Facade_pattern)、 [Singleton](https://en.wikipedia.org/wiki/Singleton_pattern)、[策略](https://en.wikipedia.org/wiki/Strategy_pattern)和[提供者](https://en.wikipedia.org/wiki/Provider_model)模式（以及[設計模式](https://en.wikipedia.org/wiki/Design_Patterns)的一般）的詳細描述。
