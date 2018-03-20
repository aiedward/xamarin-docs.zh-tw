---
title: ".xib 程式碼產生"
ms.topic: article
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 7d320b3fc40c852c337e5fd1e9bda4e90920cf70
ms.sourcegitcommit: cc38757f56aab53bce200e40f873eb8d0e5393c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2018
---
# <a name="xib-code-generation"></a>.xib 程式碼產生

> [!IMPORTANT]
>  本文說明 Visual Studio Mac 的整合，使用 Xcode 的介面產生器，因為動作插座，不會使用和 Xamarin 設計工具中適用於 iOS。 如需 iOS 設計工具的詳細資訊，請檢閱[iOS 設計工具](~/ios/user-interface/designer/index.md)文件。

Apple 介面產生器工具 ("IB 」) 可以用來以視覺化方式設計使用者介面。 IB 所建立的介面定義會儲存在**.xib**檔案。 Widget 和中的其他物件**.xib**檔案指定 「 類別識別碼 」，它可以是自訂的使用者定義型別。 這可讓您自訂的小工具的行為，以及寫入自訂的 widget。

這些使用者類別通常是 UI 在控制器類別的子類別。 它們有*插座*（類似屬性） 和*動作*（類似於事件），其可以連線到介面物件。 在執行階段，當載入 IB 檔案時，建立物件，而且插座和動作會連接到各種 UI 物件動態。 在定義這些受管理的類別時，您必須定義所有動作和插座，以符合 IB 預期的項目。 Visual Studio for Mac 使用類似的程式碼後置模型，以簡化這行。 這類似於 Xcode 處理 OBJECTIVE-C，但程式碼產生模型和慣例強制更熟悉的.NET 開發人員。

使用**.xib**檔案目前不支援在 Xamarin.iOS for Visual Studio。

## <a name="xib-files-and-custom-classes"></a>.xib 檔案和自訂類別

也可使用現有的型別從 Cocoa 觸控，就可以定義自訂類型中的**.xib**檔案。 它也可使用其他定義的型別**.xib**檔案，或單純地在 C# 程式碼中定義。 目前並不知道目前外定義之類型的詳細資料的介面產生器**.xib**檔案，因此無法予以列出，或顯示其自訂插座和動作。 移除這項限制是計劃在未來一段時間。

中可以定義自訂類別**.xib**檔案介面產生器的 「 類別 」 索引標籤中使用 「 新增子類別 」 命令。 我們將為 「 程式碼後置 」 類別。 如果**.xib**檔案具有 「。 xib.designer.cs 」 專案，然後 Visual Studio for Mac 中的對應檔案會自動填入部分類別定義中的所有自訂類別**.xib**。 我們將這些部分的類別稱為 「 設計工具類別 」。

## <a name="generating-code"></a>產生程式碼

針對任何**{0}.xib**檔案的建置動作*頁面*，如果**{0}.xib.designer.cs**檔案也存在專案中，適用於 Mac 的 Visual Studio 會產生部分類別中的設計工具中可找到的使用者類別的所有檔案**.xib**檔案，請到商店的內容和部分方法的所有動作。 這個檔案的存在只會啟用產生程式碼。

設計工具的檔案，則會自動更新時**.xib**檔案變更及 Visual Studio for Mac 重新取得焦點。 設計工具的檔案應該無法手動修改，因為變更會覆寫下一次 Visual Studio for Mac 的更新檔案。

## <a name="registration-and-namespaces"></a>註冊和命名空間

適用於 Mac 的 visual Studio 會產生使用專案的預設命名空間設計工具的檔案位置，讓它與一般的.NET 專案 namespacing 一致的設計工具類別。 設計工具的檔案命名空間是由所驅動專案的 「 預設命名空間 」 和 「.NET 命名原則 」 設定。 請注意，是否變更專案的預設命名空間，MD 會重新產生新的命名空間中的類別，您可能會發現您的部分類別不再符合。

若要使該類別成為可探索 Objective C 執行階段，Visual Studio for Mac 適用於`[Register (name)]`屬性加入該類別。 雖然會自動註冊 Xamarin.iOS `NSObject`-衍生的類別，它會使用完整的.NET 名稱。 由 Visual Studio for Mac 覆寫以確保每個類別所套用的屬性註冊中使用的名稱與**.xib**檔案。 如果您使用自訂類別 IB 中而不需使用 Visual Studio for Mac 以產生設計工具的檔案，您可能要套用此選項以手動方式可讓您比對預期 OBJECTIVE-C 類別名稱的 managed 的類別。

類別不能定義在一個以上的**.xib**，或會在發生衝突。

## <a name="non-designer-class-parts"></a>非設計工具類別的組件

設計工具的部分類別不打算做為是。 插座都是私用，且指定沒有基底類別。 預期時，每個類別必須有對應的 「 非設計工具 」 類別組件中另一個檔案，其設定的基底類別、 使用或公開插座，及定義建構函式所需時要執行個體化的類別從原生程式碼載入**.xib**。 預設值**.xib**範本這樣做，但任何其他的自訂類別定義中**.xib**，您必須手動新增非設計工具組件。

這是需要的彈性。 例如，多個程式碼後置類別可以共同管理的抽象類別的子類別要由 IB 子類別化之類別的子類別。

它是傳統置於這些**{0}.xib.cs**檔案旁邊**{0}.xib.designer.cs**設計工具檔案。

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>產生的動作和出口

在部分的設計工具類別，適用於 Mac 的 Visual Studio 會產生對應至任何已連線的插座 IB，與部分方法對應至任何已連線的動作中定義的屬性。

### <a name="outlet-properties"></a>輸出屬性

設計工具類別包含屬性對應至自訂類別上定義的所有插座。 這些是屬性的事實是到 Objective C 橋接器 Xamarin.iOS，以啟用延遲繫結的實作詳細資料。 您應該將它們視為相當於適用於只會從程式碼後置類別的私用欄位。 如果您想要將它們設為公用，存取子將屬性加入至非設計工具類別組件中，如同任何其他的私用欄位。

如果輸出屬性定義為具有一種`id`(相當於`NSObject`) 然後目前設計工具程式碼產生器會決定最強的可能類型連線到該插座，以方便使用的物件為基礎。
不過，這可能不支援在未來版本中，所以我們建議您定義自訂類別時，明確強鍵入插座。

### <a name="action-properties"></a>動作屬性

設計工具類別包含部分對應至自訂類別上定義的所有動作的方法。 這些是沒有實作的方法。 部分方法的目的有兩個：

1.  如果您輸入`partial`在類別主體中的非設計工具類別的組件，Visual Studio for Mac 會提供自動完成的所有非實作部分方法簽章。
2.  部分方法簽章有套用公開這些屬性的 OBJECTIVE-C 世界中，因此可以做為對應的動作會處理的屬性。


如果您希望，您可能會略過部分的方法，和實作的動作，透過將屬性套用至不同的方法，或讓它落入基底類別。

如果動作定義為具有傳送者類型的`id`(相當於`NSObject`)，則目前的設計工具程式碼產生器會決定最強的可能類型連線到該動作的物件為基礎。 不過，這可能不支援在未來版本中，所以我們建議您定義自訂類別時，明確強類型的動作。

請注意，這些部分方法建立僅針對 C# 中，因為 CodeDOM 不支援部分方法，因此不會產生其他語言。

## <a name="cross-xib-class-usage"></a>跨 XIB 類別使用量

某些情況下，使用者想要從多個參考相同的類別**.xib**檔案，例如使用索引標籤上控制站。 這可藉由明確參考從另一個類別定義來**.xib**檔案，或藉由定義相同的類別名稱一次在第二個**.xib**。

後者的情況下可能會因為 Visual Studio for Mac 處理發生問題**.xib**個別檔案。 無法自動偵測並合併重複的定義，因此您可能會得到相同的部分類別定義多個設計工具的檔案中的使用者註冊屬性套用多次的衝突。 最新版本的 Visual Studio for Mac 嘗試解決此問題，但它可能不一定如預期運作。 這是可能會變成不受支援，在未來，而且適用於 Mac 的 Visual Studio 會改為進行中所有定義的所有型別**.xib**檔案和所有直接顯示專案中的 managed 程式碼**.xib**檔案。

## <a name="type-resolution"></a>型別解析

IB 中使用的類型是 Objective C 類型名稱。 這些元素對應至 CLR 型別，透過暫存器屬性使用。 當產生輸出和動作的程式碼，Visual Studio for Mac 將解析 Xamarin.iOS 核心所包裝的所有 Objective C 類型對應的 CLR 型別，並完整限定類型名稱。

不過，程式碼產生器目前無法解析 Objective C 類型名稱，在使用者程式碼或程式庫中的 CLR 類型，因此它會在此情況下輸出逐字的型別名稱。 這表示對應的 CLR 類型必須有相同名稱做為 Objective C 類型，而且是正在使用它的程式碼相同的命名空間。 這會考慮將專案中的所有 Objective C 類型程式碼產生期間在未來一段時間修正計劃。
