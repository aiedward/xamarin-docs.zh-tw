---
title: 。在 Xamarin 中產生 xib 程式碼
description: '本檔說明 Xamarin 如何產生程式碼以將 xib 檔案對應至 c #，讓視覺控制項可透過程式設計方式存取。'
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 21085e534cee4010e79b76e39b11e03e6fb2580b
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84565027"
---
# <a name="xib-code-generation-in-xamarinios"></a>。在 Xamarin 中產生 xib 程式碼

> [!IMPORTANT]
> 本檔說明 Visual Studio for Mac 與 Xcode 的 Interface Builder 的整合，因為 Xamarin Designer for iOS 中不會使用動作和輸出。 如需 iOS 設計工具的詳細資訊，請參閱[Ios 設計](~/ios/user-interface/designer/index.md)工具檔。

Apple Interface Builder 工具（「IB」）可以用來以視覺化方式設計使用者介面。 IB 所建立的介面定義會儲存在**xib**檔案中。 **Xib**檔案中的 widget 和其他物件可能會被授與「類別識別」，這可以是自訂的使用者定義型別。 這可讓您自訂小工具的行為，以及撰寫自訂 widget。

這些使用者類別通常是 UI 控制器類別的子類別。 它們具有可以連接到介面物件的*輸出*（類似屬性）和*動作*（類似事件）。 在執行時間，載入 IB 檔案時，會建立物件，而輸出和動作會以動態方式連接到各種 UI 物件。 定義這些 managed 類別時，您必須定義所有動作和輸出，以符合 IB 所預期的。 Visual Studio for Mac 使用類似後置模式的模型來簡化這種情況。 這與 Xcode 針對目標-C 的用途類似，但程式碼產生模型和慣例已調整為更熟悉 .NET 開發人員。

Visual Studio 的 Xamarin 目前不支援使用**xib**檔案。

## <a name="xib-files-and-custom-classes"></a>xib 檔案和自訂類別

此外，您也可以在**xib**檔案中定義自訂類型，以及從 Cocoa Touch 使用現有的類型。 您也可以使用在其他**xib**檔中定義的類型，或純粹以 c # 程式碼定義的類型。 目前，Interface Builder 不知道在**xib**檔案外部定義的類型詳細資料，因此不會列出它們或顯示其自訂的輸出和動作。 未來已規劃移除這項限制。

您可以使用 Interface Builder 的 [類別] 索引標籤中的 [新增子類別] 命令，在**xib**檔案中定義自訂類別。 我們稱之為「後置」類別。 如果**xib**檔案的專案中有 "xib.designer.cs" 對應檔案，Visual Studio for Mac 會自動為**xib**中的所有自訂類別填入部分類別定義。 我們將這些部分類別稱為「設計工具類別」。

## <a name="generating-code"></a>產生程式碼

針對具有 [建立] 動作為 [*頁面*] 的任何** {0} xib**檔案，如果專案中也有** {0} xib.designer.cs**檔案，Visual Studio for Mac 將會在設計工具檔案中，為它在**xib**檔案中找到的所有使用者類別產生部分類別，其中包含輸出的屬性，以及所有動作的部分方法。 只要此檔案存在，就會啟用程式碼產生。

當**xib**檔案變更時，會自動更新設計工具檔案，並 Visual Studio for Mac 重新取得焦點。 設計工具檔案不應手動修改，因為下次 Visual Studio for Mac 更新檔案時，將會覆寫變更。

## <a name="registration-and-namespaces"></a>註冊和命名空間

Visual Studio for Mac 會使用設計工具檔案位置的專案預設命名空間來產生設計工具類別，使其與一般 .NET 專案命名空間如此一致。 設計工具檔案的命名空間是由專案的「預設命名空間」及其「.NET 命名原則」設定所驅動。 請注意，如果您專案的預設命名空間變更，MD 會重新產生新命名空間中的類別，因此您可能會發現您的部分類別已不再相符。

為了讓目標-C 執行時間可以探索類別，Visual Studio for Mac 會將 `[Register (name)]` 屬性套用至類別。 雖然 Xamarin 會自動註冊 `NSObject` 衍生的類別，但它會使用完整的 .net 名稱。 Visual Studio for Mac 所套用的屬性會覆寫這個，以確保每個類別都是使用**xib**檔案中所使用的名稱來註冊。 如果您使用 IB 中的自訂類別，而不使用 Visual Studio for Mac 來產生設計工具檔案，您可能必須手動套用此功能，讓您的 managed 類別符合預期的目標-C 類別名稱。

無法在一個以上**的 xib**中定義類別，否則將會衝突。

## <a name="non-designer-class-parts"></a>非設計工具類別元件

設計工具部分類別不適合用來依原本使用。 輸出為私用，且未指定任何基底類別。 預期每個類別在另一個檔案中將會有對應的「非設計工具」類別部分，這會設定基類、使用或公開輸出，以及定義在載入 **. xib**時，從機器碼具現化類別所需的函式。 **Xib**範本會執行這項操作，但針對您在**xib**中定義的任何其他自訂類別，您必須手動新增非設計工具元件。

這是需要彈性的原因。 例如，多個程式碼後置類別可以子類別化通用的 managed 抽象類別，這會將類別分類為由 IB 進行子類別化。

傳統的方式是將這些檔案放在** {0} xib.designer.cs**設計工具檔案旁邊的** {0} xib.cs**檔案中。

<a name="generated"></a>

## <a name="generated-actions-and-outlets"></a>產生的動作和輸出

在部分設計工具類別中，Visual Studio for Mac 會產生對應于 IB 中所定義之任何連接的輸出的屬性，以及對應于任何已連接動作的部分方法。

### <a name="outlet-properties"></a>插座屬性

設計工具類別包含的屬性會對應至自訂類別上定義的所有輸出。 這些是屬性的事實，就是要啟用延遲系結的 Xamarin. iOS 到目標 C 橋接器的執行詳細資料。 您應該將它們視為與私用欄位相等，其目的只是要從程式碼後置類別使用。 如果您想要將其設為公用，請將存取子屬性加入非設計工具類別部分，如同其他任何私用欄位一樣。

如果將 [輸出] 屬性定義為具有類型 `id` （相當於 `NSObject` ），則設計工具程式碼產生器目前會根據連接到該輸出的物件來決定最強的可能類型，以方便使用。
不過，未來的版本可能不支援此功能，因此建議您在定義自訂類別時明確地輸入輸出。

### <a name="action-properties"></a>動作屬性

設計工具類別包含與自訂類別上定義的所有動作對應的部分方法。 這些是沒有執行的方法。 部分方法的目的是雙重：

1. 如果您輸入 `partial` 非設計工具類別部分的類別主體，Visual Studio for Mac 將會提供自動完成所有未實作為部分方法的簽章。
2. 部分方法簽章具有套用的屬性，可將其公開至目標-C 世界，以便將其視為對應的動作來處理。

如果您想要的話，可以忽略部分方法，並將屬性套用至不同的方法來執行動作，或讓它通過基類。

如果將動作定義為具有的傳送者類型 `id` （相當於 `NSObject` ），則設計工具程式碼產生器目前會根據連接到該動作的物件，決定最強的可能類型。 不過，未來的版本可能不支援此功能，因此建議您在定義自訂類別時明確地輸入動作。

請注意，這些部分方法只會針對 c # 建立，因為 CodeDOM 不支援部分方法，因此不會針對其他語言產生。

## <a name="cross-xib-class-usage"></a>跨 XIB 類別使用方式

有時候，使用者想要從多個**xib**檔案參考相同的類別，例如使用索引標籤控制器。 這可以藉由從另一個**xib**檔案明確參考類別定義，或在第二個**xib**中再次定義相同的類別名稱來完成。

第二種情況可能會造成問題，因為 Visual Studio for Mac 個別處理**xib**檔案。 它無法自動偵測及合併重複的定義，因此當您在多個設計工具檔案中定義相同的部分類別時，您可能會發現多次套用 Register 屬性的衝突。 最新版本的 Visual Studio for Mac 嘗試解決此問題，但可能不一定會如預期般運作。 在未來，這可能會變得不受支援，而是 Visual Studio for Mac 會讓專案中所有**xib**檔案和 managed 程式碼中定義的所有型別直接顯示在所有**xib**檔中。

## <a name="type-resolution"></a>類型解析

在 IB 中使用的類型是目標-C 類型名稱。 這些會透過使用 Register 屬性來對應至 CLR 類型。 產生輸出和動作程式碼時，Visual Studio for Mac 將會針對由 Xamarin. iOS core 包裝的所有目標-C 類型解析對應的 CLR 類型，並完整限定其類型名稱。

不過，程式碼產生器目前無法從使用者程式碼或程式庫中的目標 C 類型名稱解析 CLR 類型，因此在這種情況下，它會逐字輸出類型名稱。 這表示對應的 CLR 型別必須與目標-C 型別具有相同的名稱，而且在與使用它的程式碼相同的命名空間中。 在程式碼產生期間，考慮專案中的所有目標 C 類型，計畫在未來的某個時間修正此問題。
