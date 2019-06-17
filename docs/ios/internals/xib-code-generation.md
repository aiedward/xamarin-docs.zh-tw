---
title: .xib 在 Xamarin.iOS 中的程式碼產生
description: 本文件說明如何為 Xamarin.iOS 產生對應到.xib 檔案的程式碼C#，以程式設計方式進行視覺控制項可供存取。
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 57988374b4383f5659e29edff3834958b8f99f1b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035906"
---
# <a name="xib-code-generation-in-xamarinios"></a>.xib 在 Xamarin.iOS 中的程式碼產生

> [!IMPORTANT]
>  本文件說明 Visual Studio for Mac 的整合，使用 Xcode 的 Interface Builder，因為動作與出口不會用於 Xamarin 的設計工具適用於 iOS。 如需有關 iOS 設計工具的詳細資訊，請檢閱[iOS 設計工具](~/ios/user-interface/designer/index.md)文件。

Apple Interface Builder 工具 ("IB 」) 可用來以視覺化方式設計使用者介面。 由 IB 介面定義會儲存在 **.xib**檔案。 Widget 和中的其他物件 **.xib**檔案提供 「 類別身分識別 」，它可以是自訂的使用者定義型別。 這可讓您自訂的小工具的行為，以及寫入自訂小工具。

這些使用者類別會正常 UI 控制器類別的子類別。 還*插座*（類似屬性） 和*動作*（類似於事件），可以連線到介面的物件。 在執行階段，載入 IB 檔案時，會建立物件，並輸出和動作連線到不同的 UI 物件以動態方式。 在定義這些受管理的類別時，您必須定義的所有動作和以符合 IB 預期的輸出。 Visual Studio for Mac 會使用類似程式碼後置的模型，為了簡化此作業。 這類似於 Xcode 功能適用於 OBJECTIVE-C，但程式碼產生模型和慣例調整以.NET 開發人員比較熟悉。

使用 **.xib**檔案目前不支援在 Xamarin.iOS for Visual Studio。

## <a name="xib-files-and-custom-classes"></a>.xib 檔案和自訂類別

以及使用現有的類型，從 Cocoa Touch 時，就可以定義中的自訂型別 **.xib**檔案。 您也可使用其他定義的類型 **.xib**檔案，或單純中定義C#程式碼。 目前並不知道外目前定義之類型的詳細資料的 Interface Builder **.xib**檔案，因此它不會列出這些或顯示其自訂的輸出和動作。 移除這項限制是計劃在未來一段時間。

中可以定義自訂類別 **.xib**檔案中，使用 Interface Builder 的 「 類別 」 索引標籤中的 「 新增子類別 」 命令。 我們將這些稱為 「 程式碼後置 」 類別。 如果 **.xib**檔案具有 「。 xib.designer.cs 」 專案，然後 Visual Studio for Mac 中的對應檔案會自動填入部分類別定義中的所有自訂類別 **.xib**。 我們將這些部分的類別稱為 「 設計工具類別 」。

## <a name="generating-code"></a>產生程式碼

針對任何 **{0}.xib**檔案的建置動作*頁面*時，如果 **{0}。 xib.designer.cs**檔案也存在於專案中，Visual Studio for Mac將會產生部分類別的設計工具檔案中的所有使用者類別中可找到 **.xib**檔案，請為輸出的屬性和部分方法的所有動作。 此檔案只會啟用程式碼產生。

設計工具檔案會自動更新的時機 **.xib**檔案變更和 Visual Studio for Mac 重新取得焦點。 設計工具的檔案應該無法手動修改，變更將會覆寫下一次 Visual Studio for Mac 更新檔案。

## <a name="registration-and-namespaces"></a>註冊和命名空間

Visual Studio for Mac 會產生使用專案的預設命名空間，設計工具的檔案位置，將一般的.NET 專案命名空間與一致的設計工具類別。 設計工具檔案的命名空間會隨專案的 「 預設命名空間 」 及 「.NET 命名原則 」 設定。 請注意，是否您的專案預設命名空間變更，MD 會重新產生新的命名空間中的類別讓您可能會發現您的部分類別不再符合。

若要讓類別可探索 Objective C 執行階段，Visual Studio for Mac 會套用`[Register (name)]`屬性加入該類別。 雖然會自動註冊 Xamarin.iOS `NSObject`-衍生的類別，它會使用完整的.NET 名稱。 Visual studio for Mac 會覆寫此選項以確保每個類別所套用的屬性註冊中所使用的名稱 **.xib**檔案。 如果您使用自訂類別 IB 中不包含 Visual Studio for Mac 來產生設計工具檔案，您可能要套用此選項以手動方式可讓您比對必須是 OBJECTIVE-C 類別名稱的 managed 的類別。

類別不能定義於多部 **.xib**，或它們將會發生衝突。

## <a name="non-designer-class-parts"></a>非設計工具類別的組件

設計工具的部分類別不可做為是。 輸出是私用，並指定沒有基底類別。 每個類別會具有對應的 「 非設計工具 」 類別組件，在另一個檔案中，可設定的基底類別、 使用或公開 （expose） 的輸出，並定義才能載入時，具現化原生程式碼類別的建構函式預期 **.xib**。 預設值 **.xib**範本執行這項操作，但定義中的任何額外的自訂類別 **.xib**，您必須手動新增非設計工具的一部分。

這是彈性的需求。 例如，多個程式碼後置類別可以共同管理的抽象類別的子類別要衍生子類別透過 IB 類別的子類別。

它會將這些放傳統 **{0}。 xib.cs**檔案旁邊 **{0}。 xib.designer.cs**設計工具檔案。

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>產生的動作與出口

在部分的設計工具類別中，Visual Studio for Mac 會產生對應至任何已連線的輸出，IB，與對應至任何已連線的動作的部分方法中定義的屬性。

### <a name="outlet-properties"></a>輸出屬性

設計工具類別會包含對應至自訂類別上定義的所有輸出的屬性。 事實上，這些屬性是實作細節的 Objective C bridge Xamarin.iOS，以啟用消極式繫結。 您應該考慮它們等同於適用於只能從程式碼後置類別的私用欄位。 如果您想要將它們設為公用，加入存取子屬性的非設計工具類別部分中，如同任何其他私用欄位。

若要將輸出屬性定義為具有型別`id`(相當於`NSObject`) 則設計工具的程式碼產生器目前決定的最強的可能型別，以根據物件連接至該插座，為了方便起見。
不過，這可能不被支援在未來版本中，所以我們建議您定義自訂類別時，明確強類型的輸出。

### <a name="action-properties"></a>動作屬性

設計工具類別包含部分方法對應至自訂類別上定義的所有動作。 這些是沒有實作的方法。 部分方法的目的有兩個：

1.  如果您輸入`partial`在類別主體中非 designer 類別組件，Visual Studio for Mac 將會提供自動完成命令的所有非實作部分方法簽章。
2.  部分方法簽章有套用公開這些屬性的 Objective C 的世界中，因此它們可以取得處理做為對應的動作的屬性。


如果您想，可能會略過部分的方法，和實作的動作，藉由將屬性套用至不同的方法，或讓它落入的基底類別。

若要將動作定義為具有寄件者型別`id`(相當於`NSObject`)，則目前的設計工具程式碼產生器會決定最強的可能類型，以連接到該動作的物件為基礎。 不過，這可能不被支援在未來版本中，所以我們建議您定義自訂類別時，明確強類型的動作。

請注意，這些部分方法只會針對建立C#，因為 CodeDOM 不支援部分的方法，因此不會產生如其他語言。

## <a name="cross-xib-class-usage"></a>跨 XIB 類別的使用方式

有時候，使用者想要從多個參考相同的類別 **.xib**檔案，例如使用索引標籤上控制站。 這可以由另一個參考類別定義的明確 **.xib**檔案，或藉由定義相同的類別名稱，一次在第二個 **.xib**。

後者的情況下可能會因為 Visual Studio for Mac 處理造成問題 **.xib**個別檔案。 它無法自動偵測，並合併重複的定義，因此您可能會得到相同的部分類別定義多個設計工具檔案中的使用者註冊屬性套用多次的衝突。 最新版本的 Visual Studio for Mac 會嘗試解析，但它可能不一定如預期般運作。 這是可能會變成不受支援，在未來，並改為 Visual Studio for Mac 會在所有已定義的所有型別 **.xib**檔案和所有直接顯示專案中的 managed 程式碼 **.xib**檔案。

## <a name="type-resolution"></a>型別解析

IB 中使用的類型都是 OBJECTIVE-C 型別名稱。 這些會對應至透過暫存器屬性使用的 CLR 型別。 當產生輸出和動作的程式碼，Visual Studio for Mac 會解析所有 Xamarin.iOS 核心所包裝的 Objective C 類型對應的 CLR 型別，並完整限定其型別名稱。

不過，程式碼產生器目前無法解決從 OBJECTIVE-C 型別名稱，在使用者程式碼或程式庫中的 CLR 型別，因此在此情況下它會輸出逐字的型別名稱。 這表示對應的 CLR 型別必須有 OBJECTIVE-C 型別相同的名稱，而且是正在使用它的程式碼相同的命名空間。 這是藉由考慮專案中所有的 Objective C 類型程式碼產生期間在未來一段時間修正計劃。
