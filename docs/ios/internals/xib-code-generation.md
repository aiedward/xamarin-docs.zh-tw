---
title: xib 在 Xamarin 中產生程式碼
description: "本檔說明 Xamarin 如何產生程式碼，以將 '. xib ' 檔案對應至 c #，讓視覺控制項以程式設計方式存取。"
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 7e6f0cdab812c65dde1305dbb3d17a7ccdc5c2e1
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603096"
---
# <a name="xib-code-generation-in-xamarinios"></a>xib 在 Xamarin 中產生程式碼

Apple Interface Builder 工具 ( 「IB」 ) 可以用視覺化方式設計使用者介面。 由 IB 建立的介面定義會儲存在 **xib** 檔案中。 Widget 和 **xib** 檔案中的其他物件可能會被授與「類別身分識別」，這可以是自訂的使用者定義型別。 使用自訂類型可讓您自訂 widget 的行為，並撰寫自訂 widget。

這些使用者類別通常是 UI 控制器類別的子類別。 它們具有 (屬性的 *輸出*) 和可連接到介面物件) 的 *動作* (事件。
在執行時間，會載入 IB。 屆時會建立物件，而輸出和動作會動態連接到各種 UI 物件。 定義這些受管理的類別時，您必須定義所有的動作和輸出，以符合 IB 預期的動作和輸出。 Visual Studio for Mac 使用類似程式碼的模型來簡化程式碼。 Xcode 具有類似的目標 C 模型。 但是，我們已調整了 Xamarin 的程式碼產生模型和慣例，讓 .NET 開發人員更熟悉。

## <a name="xib-files-and-custom-classes"></a>xib 檔案和自訂類別

除了使用 Cocoa 觸控的現有類型之外，您還可以在 **xib** 檔案中定義自訂類型。 您也可以使用其他 **xib** 檔中定義的類型，或純粹以 c # 程式碼定義的類型。 目前，介面產生器不會察覺目前 **xib** 檔之外定義之類型的詳細資料，因此不會列出它們或顯示其自訂的輸出和動作。 未來的某個時間會規劃移除這項限制。

您可以使用 Interface Builder 之 [類別] 索引標籤中的 [新增子類別] 命令，在 **xib** 檔案中定義自訂類別。 我們將這些類別稱為「後置」類別。 如果 **xib** 檔案在專案中有一個 ". xib.designer.cs" 對應檔案，則 Visual Studio for Mac 會自動為 **xib** 中的所有自訂類別填入部分類別定義。 我們將這些部分類別稱為「設計工具類別」。

## <a name="generating-code"></a>產生程式碼

如果有 **{0} xib.designer.cs** 檔案，則會啟用程式碼產生， **{0} xib** 檔案中的 [建立] 動作為 [*頁面*]。
Visual Studio for Mac 會在設計工具檔案中，針對可在 **xib** 檔案中找到的所有使用者類別產生部分類別。 Visual Studio for Mac 會產生輸出的屬性，以及動作的部分方法。 

當 **xib** 檔案變更且 Visual Studio for Mac 重新取得焦點時，會自動更新設計工具檔案。 由於下次 Visual Studio for Mac 更新檔案時，將會覆寫變更，因此不建議對設計工具檔案進行變更。

## <a name="registration-and-namespaces"></a>註冊和命名空間

Visual Studio for Mac 會使用專案的預設命名空間，為設計工具檔案位置產生設計工具類別。
此行為與一般 .NET 專案命名空間產生一致。
設計工具檔案的命名空間會使用專案的「預設命名空間」和其「.NET 命名原則」設定。 如果您專案的預設命名空間變更，重新產生的類別將會使用新的命名空間。
重新產生之後，您可能會發現部分類別不再相符。

Visual Studio for Mac 會將屬性套用至類別，以使類別可供目標 C 執行時間探索 `[Register (name)]` 。 雖然 Xamarin 會自動註冊 `NSObject` 衍生的類別，但它會使用完整的 .net 名稱。 Visual Studio for Mac 所套用的屬性會覆寫 **xib** 檔案中使用的名稱，以確保每個類別都註冊。 使用 IB 以手動方式為定義的所有自訂類別加入屬性，而不使用 Visual Studio for Mac 來產生設計工具檔案。 這麼做會讓您的 managed 類別符合預期的目標 C 類別名稱。

無法在一個以上的 **. xib** 中定義類別，否則就會發生衝突。

## <a name="non-designer-class-parts"></a>非設計師類別元件

設計工具部分類別不適合依原樣使用。 輸出是私用的，且未指定任何基類。 預期每個類別都會在另一個檔案中有對應的「非設計師」類別部分。 「非設計師」檔案會設定基類、操作輸出，然後定義從機器碼具現化類別所需的函式。 **Xib** 範本具有「非設計師」類別的部分，但針對您在 **xib** 中定義的任何其他自訂類別，您必須手動加入非設計師元件。

需要使用部分類別分隔，以提供彈性。 例如，多個程式碼程式碼類別可以子類別化常見的 managed 抽象類別，此類別會子類別要由 IB 子類別的類別。

傳統上，會將程式碼後置類別放在 **{0} xib.designer.cs** 設計工具檔案旁的 **{0} xib.cs** 檔案中。

<a name="generated"></a>

## <a name="generated-actions-and-outlets"></a>產生的動作和輸出

在部分設計工具類別中，Visual Studio for Mac 會產生對應于 IB 中所定義之任何連接的輸出的屬性，以及對應至任何已連接動作的部分方法。

### <a name="outlet-properties"></a>輸出屬性

設計工具類別包含的屬性會對應至自訂類別上定義的所有輸出。 這些屬性會啟用延遲系結。 它們是 Xamarin 至目標 C 橋接器的實作為詳細資料。 您可以將它們視為相當於私用欄位，僅供從程式碼後置類別使用。 將公用存取子新增至非設計師類別部分中的欄位，使欄位成為公用。

如果將「輸出」屬性定義為具有 `id` 相當於) 的 (類型 `NSObject` ，則設計工具程式碼產生器目前會根據連接到該輸出的物件來判斷最強的可能類型，以方便使用。
不過，未來的版本可能不支援這種行為。 建議您在定義自訂類別時明確地輸入輸出。

### <a name="action-properties"></a>動作屬性

設計工具類別包含部分方法，對應至自訂類別上定義的所有動作。 這些方法沒有執行。 部分方法的目的是雙重：

1. 如果您輸入  `partial` 非設計師類別元件的類別主體，Visual Studio For Mac 將會提供自動完成所有未執行部分方法的簽章。
2. 部分方法簽章已套用可將其公開至目標 C 世界的屬性，因此可作為對應的動作來處理。

您可以忽略部分方法，並將屬性套用至不同的方法來執行動作。 或讓它落到基類。

如果動作定義為具有等同于) 的寄件者類型 `id` (，則設計工具程式 `NSObject` 代碼產生器目前會根據連接到該動作的物件，判斷最強的可能類型。 不過，未來的版本可能不支援這種行為。 建議您在定義自訂類別時，明確地強型別動作。

這些部分方法只會針對 c # 建立，因為 CodeDOM 不支援部分方法。 它們不會針對其他語言產生。

## <a name="cross-xib-class-usage"></a>跨 XIB 類別使用方式

有時候，使用者想要從多個 **xib** 檔案（例如，使用索引標籤控制器）參考相同的類別。 您可以從另一個 **xib** 檔明確參考類別定義，或在第二個 **. xib** 中再次定義相同的類別名稱。

第二種情況可能會造成問題，因為 Visual Studio for Mac 處理 **xib** 檔案。 Visual Studio for Mac 無法偵測及合併重複的定義。 當多個設計工具檔案中定義了相同的部分類別時，您可能會發生衝突，並多次套用 Register 屬性。 最新版本的 Visual Studio for Mac 會嘗試解決衝突，但不一定會如預期般運作。 未來，此行為可能會變成不受支援，而 Visual Studio for Mac 會讓所有 **xib** 檔案中定義的所有類型和專案中的 managed 程式碼，直接顯示在所有 **xib** 檔案中。

## <a name="type-resolution"></a>類型解析

在 IB 中使用的類型是使用 Register 屬性對應至 CLR 類型的目標 C 型別名稱。 當產生程式碼時，Visual Studio for Mac 將會解析 CLR 型別，將型別名稱完整限定為目標 C 類型。 這些目標 C 類型是由 Xamarin 核心包裝。

程式碼產生器目前無法從使用者程式碼或程式庫中的目標 C 類型名稱解析 CLR 類型。 在這種情況下，它會逐字輸出型別名稱。 它必須與目標 C 類型具有相同的名稱，才能正確地解析 CLR 型別。
CLR 型別必須與使用它的程式碼位於相同的命名空間中。 程式碼產生器的未來版本將會考慮專案中的所有目標 C 類型。
