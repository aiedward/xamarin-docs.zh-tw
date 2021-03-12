---
title: Hello, iOS 多重畫面 – 深度剖析
description: 此文件會深入探討擴展的 Phoneword 應用程式、進一步考量模型-檢視-控制器、iOS 巡覽和其他 iOS 開發概念。
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: c866e5f4-8154-4342-876e-efa0693d66f5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 8e7035d586871f8faf9a49823cdc7030dd144fc0
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602758"
---
# <a name="hello-ios-multiscreen--deep-dive"></a>Hello, iOS 多重畫面 – 深度剖析
> [!WARNING]
> IOS 設計工具在 Visual Studio 2019 16.8 版和 Visual Studio 2019 for Mac 版本8.8 中已淘汰，並已在 Visual Studio 2019 版本16.9 和 Visual Studio for Mac 版本8.9 中移除。
> 建立 iOS 使用者介面的建議方式是直接在執行 Xcode 介面產生器的 Mac 上。 如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](~/ios/user-interface/storyboards/index.md)。 

在＜快速入門＞逐步解說中，我們已建置並執行第一個多重畫面的 Xamarin.iOS 應用程式。 現在是時候更深入了解 iOS 瀏覽和架構。

在本指南中，我們將介紹「模型、檢視、控制器 (MVC)」模式及其在 iOS 架構與瀏覽中所扮演的角色。
接著會深入剖析瀏覽控制器，並了解如何使用它，在 iOS 中提供熟悉的瀏覽體驗。

## <a name="model-view-controller-mvc"></a>模型檢視控制器 (MVC)

在 [Hello，ios](~/ios/get-started/hello-ios/index.md) 教學課程中，我們瞭解到 ios 應用程式只有一個 *視窗* ，可以讓控制器將其 *內容視圖* 階層載入至視窗。 在第二個 Phoneword 逐步解說中，我們已將第二個畫面新增到應用程式，並在這兩個畫面之間傳遞一些資料 (電話號碼清單)，如下圖所示：

 [![此圖說明如何在兩個畫面之間傳遞資料](hello-ios-multiscreen-deepdive-images/08.png)](hello-ios-multiscreen-deepdive-images/08.png#lightbox)

在我們的範例中，資料是在第一個畫面中收集、從第一個檢視控制器傳遞到第二個，並由第二個畫面顯示。 這種畫面、視圖控制器和資料的分隔會遵循 *Model、view、Controller (MVC)* 模式。 在後續幾節中，我們將討論此模式的優點、它的元件，以及如何在 Phoneword 應用程式中使用它。

### <a name="benefits-of-the-mvc-pattern"></a>MVC 模式的優點

「模型-檢視-控制器」是一個「設計模式」，一種可在程式碼中針對常見問題或使用案例重複使用的架構方案。 MVC 是適用於含「圖形化使用者介面 (GUI)」之應用程式的架構。 它會為應用程式中的物件指派下列三個角色之一：「模型」(資料或應用程式邏輯)、「檢視」(使用者介面)，以及「控制器」(程式碼後置)。 下圖說明 MVC 模式的三個部分與使用者之間的關聯性：

 [![下圖說明 MVC 模式的三個部分與使用者之間的關聯性](hello-ios-multiscreen-deepdive-images/00.png)](hello-ios-multiscreen-deepdive-images/00.png#lightbox)

MVC 模式很實用，因為它提供 GUI 應用程式之不同組件間的邏輯分隔，讓我們能夠更輕鬆地重複使用程式碼與檢視。 讓我們開始更詳盡地查看這每一個角色。

> [!NOTE]
> MVC 模式大致上類似於 ASP.NET 網頁或 WPF 應用程式的結構。 在這些範例中，檢視是實際上負責描述 UI 的元件，而且對應至 ASP.NET 中的 ASPX (HTML) 網頁，或對應至 WPF 應用程式中的 XAML。 控制器是負責管理檢視的元件，對應至 ASP.NET 或 WPF 中的程式碼後置。

### <a name="model"></a>型號

模型物件通常是要顯示於檢視或輸入到檢視之資料的應用程式專用表示法。 模型通常是鬆散定義的，例如，在 **Phoneword_iOS** 應用程式中，電話號碼的清單 (顯示為字串清單) 即為模型。 如果我們正在建置跨平台應用程式，則可選擇在 iOS 和 Android 應用程式之間共用 **PhonewordTranslator** 程式碼。 我們也可以將該共用程式碼視為模型。

MVC 完全不知道模型的「資料持續性」和「存取」。 換句話說，MVC 不在意資料看起來像什麼或它的儲存方式，只關心資料的「呈現」方式。 例如，我們可以選擇將資料儲存於 SQL 資料庫，或將它保存於某些雲端儲存機制中，或者只使用 `List<string>`。 基於 MVC 用途，模式中只會包含資料表示法本身。

在某些情況下，MVC 的模型部分可能是空的。 例如，我們可能選擇將一些靜態網頁新增至應用程式，以說明電話翻譯工具的運作方式、如何建置它，以及如何與我們聯繫來回報錯誤。 這些應用程式畫面仍會使用檢視和控制器來建立，但它們不會有任何實際的模型資料。

> [!NOTE]
> 在某些文獻中，MVC 模式的模型部分可以參考整個應用程式後端，而不只是顯示於 UI 的資料。 在本指南中，我們會使用模型的新詮釋，但差別不是特別重要。

### <a name="view"></a>檢視

檢視是負責轉譯使用者介面的元件。 幾乎在使用 MVC 模式的所有平台中，使用者介面都是由檢視階層所組成。 我們可以將 MVC 中的檢視想像為位於階層頂端且具有單一檢視的檢視階層 (又稱為根檢視)，而在其下方有任意數目的子項檢視 (又稱為子檢視)。 在 iOS 中，畫面的內容檢視階層會對應到 MVC 中的檢視元件。

### <a name="controller"></a>控制器

控制器物件是將一切連接在一起的元件，在 iOS 中透過 `UIViewController` 來呈現。 我們可以將控制器想像為一個畫面或一組檢視的支援程式碼。 控制器負責接聽來自使用者的要求，並傳回適當的檢視階層。 它會接聽來自檢視 (按鈕按一下、文字輸入等) 的要求，並執行適當的處理、檢視修改，以及重新載入檢視。 控制器也會負責從支援的資料存放區存在於應用程式的地方建立或擷取模型，並使用它的資料填入檢視。

控制器也可以管理其他控制器。 例如，如果一個控制器需要顯示不同畫面，則它可能會載入另一個控制器，或者管理控制器的堆疊來監視它們的順序及其之間的轉換。 在下一節中，我們會看到控制器的範例，此控制器會在我們引進一種特殊類型的 iOS view controller （稱為 *流覽控制器*）時管理其他控制器。

## <a name="navigation-controller"></a>瀏覽控制器

在 Phoneword 應用程式中，我們使用「瀏覽控制器」來協助管理多個畫面之間的瀏覽。 瀏覽控制器是由 `UINavigationController` 類別所表示的特製化 `UIViewController`。 瀏覽控制器不會管理單一內容檢視階層，而是會管理其他檢視控制器，並以瀏覽工具列形式來管理它自己特殊的內容檢視階層，工具列中包括標題、[上一頁] 按鈕及其他選擇性功能。

流覽控制器在 iOS 應用程式中很常見，而且會提供如以下螢幕擷取畫面 **所示** 的 ios 應用程式裝訂導覽：

 [![流覽控制器提供 iOS 應用程式的流覽，例如此處顯示的設定應用程式](hello-ios-multiscreen-deepdive-images/01.png)](hello-ios-multiscreen-deepdive-images/01.png#lightbox)

瀏覽控制器提供三個主要功能：

- **提供向前流覽的** 勾點–流覽控制器會使用階層式流覽比喻，將內容視圖階層  *推送* 至  *導覽堆疊* 上。 您可以將瀏覽堆疊想像為一疊撲克牌，只能看見最上方的牌，如下圖所示：  

    [![此圖表說明如何以一堆牌的形式導覽](hello-ios-multiscreen-deepdive-images/02.png)](hello-ios-multiscreen-deepdive-images/02.png#lightbox)

- **選擇性地提供 [上一頁] 按鈕**：當我們將新的項目推送至瀏覽堆疊時，標題列可以自動顯示「[上一頁] 按鈕」，讓使用者能夠向後瀏覽。 按 [上一步  *] 按鈕會將目前的 view* 控制器從流覽堆疊中取出，並將先前的內容視圖階層載入至視窗：  

    [![此圖說明如何從堆疊中取出卡片](hello-ios-multiscreen-deepdive-images/03.png)](hello-ios-multiscreen-deepdive-images/03.png#lightbox)

- **提供標題列** –流覽控制器的上半部稱為  *標題列* 。 它負責顯示檢視控制器標題，如下圖所示：  

    [![標題列負責顯示視圖控制器標題](hello-ios-multiscreen-deepdive-images/04.png)](hello-ios-multiscreen-deepdive-images/04.png#lightbox)

### <a name="root-view-controller"></a>根檢視控制器

瀏覽控制器不會管理內容檢視階層；因此，其本身沒有什麼可以顯示。
相反地，流覽控制器會與 *根視圖控制器* 配對：

 [![流覽控制器會與根視圖控制器配對](hello-ios-multiscreen-deepdive-images/05.png)](hello-ios-multiscreen-deepdive-images/05.png#lightbox)

根檢視控制器代表瀏覽控制器堆疊中的第一個檢視控制器，而根檢視控制器的內容檢視階層則是第一個要載入到視窗的內容檢視階層。 如果想要將整個應用程式放置於瀏覽控制器的堆疊上，可將無來源的 Segue 移到瀏覽控制器，並將第一個畫面的檢視控制器設定為根檢視控制器，如同我們在 Phoneword 應用程式中所做：

 [![Segue Segue 會將第一個螢幕視圖控制器設定為根視圖控制器](hello-ios-multiscreen-deepdive-images/06.png)](hello-ios-multiscreen-deepdive-images/06.png#lightbox)

### <a name="additional-navigation-options"></a>其他瀏覽選項

瀏覽控制器是在 iOS 中處理瀏覽的常見方式，但不是唯一選項。 例如，索引標籤列 [控制器](~/ios/user-interface/controls/creating-tabbed-applications.md) 可以將應用程式分割成不同的功能區域，而 [分割視圖控制器](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/split_view/use_split_view_to_show_two_controllers) 可用來建立主要/詳細資料檢視。 將瀏覽控制器與這些其他的瀏覽架構相結合，便可以彈性地在 iOS 中呈現和巡覽內容。

## <a name="handling-transitions"></a>處理轉換

在 Phoneword 逐步解說中，我們以兩種不同方式處理這兩個檢視控制器之間的轉換：先使用分鏡腳本 Segue，然後以程式設計方式。 讓我們更詳細地探索這兩個選項。

### <a name="prepareforsegue"></a>PrepareForSegue

當我們將具有 **Show** 動作的 Segue 新增至分鏡腳本時，會指示 iOS 將第二個 view 控制器推送至流覽控制器的堆疊：

 [![從下拉式清單設定 segue 類型](hello-ios-multiscreen-deepdive-images/09.png)](hello-ios-multiscreen-deepdive-images/09.png#lightbox)

將 Segue 新增到分鏡腳本，就足以建立畫面之間的簡單轉換。 如果想要在檢視控制器之間傳遞資料，就必須覆寫 `PrepareForSegue` 方法並自行處理資料：

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);
    ...
}
```

iOS 會在轉換發生之前先呼叫 `PrepareForSegue`，並將我們在分鏡腳本中建立的 Segue 傳遞給該方法。
此時，必須手動設定 Segue 的目的地檢視控制器。 下列程式碼會取得目的地檢視控制器的控制代碼，並轉換成適當的類別 (在此案例中為 CallHistoryController)：

```csharp
CallHistoryController callHistoryController = segue.DestinationViewController as CallHistoryController;
```

最後，透過將 `CallHistoryController` 的 `PhoneHistory` 屬性設定為已撥號電話號碼清單，將電話號碼清單 (模型) 從 `ViewController` 傳遞到 `CallHistoryController`：

```csharp
callHistoryController.PhoneNumbers = PhoneNumbers;
```

使用 Segue 傳遞資料的完整程式碼如下所示：

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController as CallHistoryController;

    if (callHistoryController != null) {
         callHistoryController.PhoneNumbers = PhoneNumbers;
    }
 }
```

### <a name="navigation-without-segues"></a>不使用 Segue 進行瀏覽

在程式碼中從第一個檢視控制器轉換到第二個的程序，與使用 Segue 的程序相同，但必須手動完成幾個步驟。
首先，使用 `this.NavigationController` 來取得目前所在堆疊的瀏覽控制器參考。 然後，使用瀏覽控制器的 `PushViewController` 方法，將下一個檢視控制器手動推送至堆疊，傳入檢視控制器並選擇以動畫顯示轉換 (我們將此項目設定為 `true`)。

下列程式碼會處理從 Phoneword 畫面到通話記錄畫面的轉換：

```csharp
this.NavigationController.PushViewController (callHistory, true);
```

在可以轉換到下一個檢視控制器之前，必須先透過呼叫 `this.Storyboard.InstantiateViewController` 並傳入 `CallHistoryController` 的分鏡腳本識別碼，從分鏡腳本手動將它具現化：

```csharp
CallHistoryController callHistory =
this.Storyboard.InstantiateViewController
("CallHistoryController") as CallHistoryController;
```

最後，透過將 `CallHistoryController` 的 `PhoneHistory` 屬性設定為已撥號電話號碼清單，將電話號碼清單 (模型) 從 `ViewController` 傳遞到 `CallHistoryController`，就像我們使用 Segue 處理轉換時所做的：

```csharp
callHistory.PhoneNumbers = PhoneNumbers;
```

以程式設計方式進行轉換的完整程式碼如下所示：

```csharp
CallHistoryButton.TouchUpInside += (object sender, EventArgs e) => {
    // Launches a new instance of CallHistoryController
    CallHistoryController callHistory = this.Storyboard.InstantiateViewController ("CallHistoryController") as CallHistoryController;
    if (callHistory != null) {
     callHistory.PhoneNumbers = PhoneNumbers;
     this.NavigationController.PushViewController (callHistory, true);
    }
};
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Phoneword 中引入的其他概念

Phoneword 應用程式引入本指南未涵蓋的數個概念。 這些概念包括：

- **自動建立視圖控制器** ：當我們在  **Properties Pad** 中輸入視圖控制器的類別名稱時，iOS 設計工具會檢查該類別是否存在，然後為我們產生 view controller 支援類別。 如需此功能與其他 iOS 設計工具功能的詳細資訊，請參閱 [iOS 設計工具簡介](~/ios/user-interface/designer/introduction.md)指南。
- **資料表視圖控制器** –  `CallHistoryController` 是資料表視圖控制器。 資料表檢視控制器包含資料表檢視，這是 iOS 中最常見的版面配置與資料顯示工具。 資料表已超出本指南的範圍。 如需資料表視圖控制器的詳細資訊，請參閱使用  [資料表和資料格](~/ios/user-interface/controls/tables/index.md) 指南。
- 分鏡腳本 **識別碼**：設定分鏡腳本識別碼會在目標 C 中建立視圖控制器類別，其中包含分鏡腳本中視圖控制器的程式碼後端。 我們使用分鏡腳本識別碼來尋找 Objective-C 類別，並將分鏡腳本中的檢視控制器具現化。 如需有關分鏡腳本識別碼的詳細資訊，請參閱[分鏡腳本簡介](~/ios/user-interface/storyboards/index.md)指南。

## <a name="summary"></a>摘要

恭喜您完成第一個多重畫面的 iOS 應用程式！

在本指南中，我們導入了 MVC 模式，並使用它來建立一個多重畫面的應用程式。 我們也探索了瀏覽控制器，以及它們在提供 iOS 瀏覽功能中所扮演的角色。 現在您已經具備可開始開發自己的 Xamarin.iOS 應用程式的穩固基礎。

接下來，讓我們利用[行動應用程式開發簡介](~/cross-platform/get-started/introduction-to-mobile-development.md)和[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)指南，以了解如何使用 Xamarin 來建置跨平台應用程式。

## <a name="related-links"></a>相關連結

- [Hello, iOS (範例)](/samples/xamarin/ios-samples/hello-ios) \(英文\)
- [iOS 人性化介面指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/Introduction/Introduction.html)
- [iOS 佈建入口網站](https://developer.apple.com/ios/manage/overview/index.action)