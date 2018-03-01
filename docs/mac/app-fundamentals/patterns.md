---
title: "常見的模式和慣用語"
description: "本文件描述模型-檢視-控制器模式、 資料來源和委派模式及通訊協定。"
ms.topic: article
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/17/2016
ms.openlocfilehash: 4926670a0cd0960c9a390bd388d3530929634153
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="common-patterns-and-idioms"></a>常見的模式和慣用語

Apple 公開的 Api 透過 C#，整個特定語言和模式會一再重複。 如果您有使用 Xamarin.iOS 程式設計經驗，這些可能看起來很熟悉。 文件將通常參考這些模式和慣用語重複，因此需要充分了解它們可協助您充分您找到的文件的意義。

## <a name="mvc---model-view-controller"></a>MVC 的模型檢視控制器

模型檢視控制器或簡稱，MVC 是普遍 Cocoa 很常見的模式。 詳細的討論已超出本文件的範圍，但是它是一種來建構您的應用程式元件的簡單地說：

- **模型**物件代表基礎資料的檢視及操作 （例如通訊錄中的位址）
- **檢視**物件處理給定的物件在螢幕上繪圖和處理使用者互動 （文字欄位顯示在畫面上的位址）
- **控制器**物件處理模型和檢視之間的互動。 它們推送模型變更 「 向上 」 更新檢視並將推送 「 向下 」 的變更從檢視當使用者在 UI 中進行變更時。

如果您很熟悉搭配 MVVM (模型檢視 ViewModel) 從 WPF 之類的其他程式庫，控制器做 ViewModel 類似，但通常更緊密繫結至特定的 UI 項目。

可以在這裡找到更多詳細資料：

- [學習 MVC Apple 網站上](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [Objective C 中的模型檢視控制器](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [使用視窗](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>資料來源 / 委派 / 子類別化

另一個 Cocoa 中很常見的模式也會處理提供資料的 UI 項目，並對回應使用者互動。 使用`NSTableView`為例，您要以某種方式提供資料給每個資料列中，部分設定的 UI 項目表示資料列，部分組來回應使用者互動，並可能一定程度的自訂行為。 資料來源和委派模式可讓您處理大部分的情況下，而不必訴諸於子類別化`NSTableView`自己。

- `DataSource`屬性會被指派的自訂子類別的執行個體`NSTableViewDataSource`為呼叫的目標資料表的資料填入 (透過`GetRowCount`和`GetObjectValue`)。

- `Delegate`屬性會被指派的自訂子類別的執行個體`NSTableViewDelegate`可提供給定的模型物件的檢視 (透過`GetViewForItem`)，以及處理使用者互動 (透過`DidClickTableColumn`，`MouseDownInHeaderOfTableColumn`等等)。

在某些情況下，您會想要自訂控制項的委派或資料來源所提供的攔截程序之外的方式，您可以直接檢視子類別。 不過要小心，在許多情況下，覆寫預設行為則會要求您自行處理該行為的所有問題 （自訂選取行為可能需要您實作所有的選取項目行為）。

在 Xamarin.iOS，某些應用程式開發介面，例如`UITableView`已擴充與實作委派和資料來源的屬性 (`UITableViewSource`)。 若要暫時解決單一的 C# 類別只能有一個基底類別，且我們面對的通訊協定的一般限制它是透過基底類別。

如需使用 Xamarin.Mac 應用程式中的資料表檢視的詳細資訊，請參閱我們[資料表檢視](~/mac/user-interface/table-view.md)文件。

## <a name="protocols"></a>通訊協定

Objective C 中的通訊協定可以與 C# 中的介面，並在許多情況下會以類似的情況。 例如`NSTableView`上述範例，委派和資料來源是實際的通訊協定。 Xamarin.Mac 以公開這些做為基底類別虛擬方法，您可以覆寫。 C# 介面和 OBJECTIVE-C 通訊協定的主要差異是一種通訊協定中的某些方法可能是選擇性的以實作。 您必須查看文件及/或 api 來判斷什麼是選擇性的定義。

詳細資訊，請參閱我們[委派、 通訊協定和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)文件。



## <a name="related-links"></a>相關連結

- [資料表檢視](~/mac/user-interface/table-view.md)
- [使用視窗](~/mac/user-interface/window.md)
- [委派、 通訊協定和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
