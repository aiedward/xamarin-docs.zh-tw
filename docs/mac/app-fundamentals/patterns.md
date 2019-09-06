---
title: Xamarin 中的常見模式和慣用語
description: 本檔說明建立 Xamarin. Mac 應用程式時所使用的常見設計模式。 它會討論模型視圖控制器模式、資料來源和委派模式，以及通訊協定。
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 06/17/2016
ms.openlocfilehash: 188bc8a04b62c97e9d6f80669fe50da1bf4d9340
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289542"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>Xamarin 中的常見模式和慣用語

在透過公開的所有 Apple C#api 中，特定的慣用語和模式會重複出現。 如果您有使用 Xamarin 進行程式設計的經驗，這些功能看起來可能很熟悉。 檔通常會參考這些模式並重複慣用語，因此充分瞭解它們將有助於您瞭解所找到的檔。

## <a name="mvc---model-view-controller"></a>MVC 模型視圖控制器

Model View Controller （簡稱 MVC）是在整個 Cocoa 中發現的一種非常常見的模式。 詳細的討論已超出本檔的範圍，但簡言之，這是將應用程式結構化至元件的方法：

- **模型**物件代表所要查看和操作的基礎資料（例如通訊錄中的位址）
- **View**物件會處理螢幕上指定物件的繪圖，並處理使用者互動（顯示幕幕上位址的文字欄位）
- **控制器**物件會處理模型和視圖之間的互動。 當使用者在 UI 中進行變更時，它們會推送模型變更「啟動」以更新視圖，並從視圖中推送「向下」變更。

如果您熟悉來自其他程式庫（例如 WPF）的 MVVM （模型視圖 ViewModel），控制器的作用類似于 ViewModel，但通常會更緊密地系結至特定的 UI 元素。

您可以在這裡找到更多詳細資料：

- [瞭解 Apple 網站上的 MVC](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [目標中的模型視圖控制器-C](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [使用 Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>資料來源/委派/子類別化

Cocoa 中另一個非常常見的模式，就是處理如何將資料提供給 UI 元素，以及對使用者互動做出反應。 使用`NSTableView`作為範例時，您必須以某種方式提供每個資料列的資料、一些代表該資料列的 UI 元素集合、一組用來回應使用者互動的行為，以及可能的自訂量。 資料來源和委派模式可讓您處理大部分的情況，而不需要`NSTableView`自行進行子類別化。

- 屬性會被指派自訂子`NSTableViewDataSource`類別的實例，其會呼叫以在`GetRowCount`資料表中填入資料（透過和`GetObjectValue`）。 `DataSource`

- `GetViewForItem` `NSTableViewDelegate` `MouseDownInHeaderOfTableColumn` `DidClickTableColumn`屬性會被指派自訂子類別的實例，其可提供給定模型物件（透過）的視圖，並處理 UI 互動（透過、等）。 `Delegate`

在某些情況下，您會想要從委派或資料來源中所指定的勾點以外的方式自訂控制項，而且您可以直接將此視圖子類別化。 不過，在許多情況下，覆寫預設行為會要求您自行處理所有的行為（自訂選取行為可能會要求您自行執行所有選取行為）。

在 Xamarin 中，某些 api （例如`UITableView` ）已使用同時執行委派和資料來源（`UITableViewSource`）的屬性擴充。 為了解決常見的限制，單一C#類別只能有一個基類，而我們的通訊協定呈現是透過基類來完成。

如需在 Xamarin. Mac 應用程式中使用資料表流覽的詳細資訊，請參閱我們的[資料表視圖](~/mac/user-interface/table-view.md)檔。

## <a name="protocols"></a>通訊協定

目標-C 中的通訊協定可以與中C#的介面比較，而且在許多情況下，也會在類似的情況下使用。 例如上述範例，委派和資料來源實際上都是通訊協定。 `NSTableView` Xamarin 會以您可以覆寫的虛擬方法，將這些類別公開為基類。 C#介面與目標-C 通訊協定的主要差異在於，通訊協定中的某些方法可能是選擇性的。 您必須查看 API 的檔和/或定義，以判斷什麼是選擇性的。

如需詳細資訊，請參閱我們的[委派、通訊協定和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)檔。



## <a name="related-links"></a>相關連結

- [資料表視圖](~/mac/user-interface/table-view.md)
- [使用 windows](~/mac/user-interface/window.md)
- [委派、通訊協定和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
