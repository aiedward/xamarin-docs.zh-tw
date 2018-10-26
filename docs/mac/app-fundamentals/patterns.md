---
title: 常見的模式和慣用語 xamarin.mac
description: 本文件說明建立 Xamarin.Mac 應用程式時使用的常見設計模式。 它討論 「 模型-檢視-控制器模式、 資料來源和委派模式及通訊協定。
ms.prod: xamarin
ms.assetid: BF0A3517-17D8-453D-87F7-C8A34BEA8FF5
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 06/17/2016
ms.openlocfilehash: b4266582ce0cec522e207cd06987f2d0eeff8b2d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104332"
---
# <a name="common-patterns-and-idioms-in-xamarinmac"></a>常見的模式和慣用語 xamarin.mac

透過 C# 公開的 Apple Api，在某些慣用語和模式出現一再重複。 如果您已利用 Xamarin.iOS 的程式設計體驗，這些可能看起來很熟悉。 文件會經常參考這些模式和慣用語重複，因此需要充分了解它們，將可協助您了解您所尋找的文件。

## <a name="mvc---model-view-controller"></a>MVC-模型檢視控制器

模型檢視控制器或簡稱 MVC 是普遍 Cocoa 很常見的模式。 詳細的討論已超出本文的範圍，但它是一種建構您的應用程式元件的簡單地說：

- **模型**物件代表基礎的資料在檢視和操作 （例如通訊錄中的位址）
- **檢視**物件處理，在指定的物件，在螢幕上繪圖及處理使用者互動 （文字欄位顯示在畫面上的位址）
- **控制器**物件處理的模型和檢視之間的互動。 它們將模型變更時，推送 「 啟動 」 來更新檢視，並當使用者在 UI 中進行變更時，「 關閉 」 的變更推送從檢視。

如果您是熟悉的其他程式庫，例如 WPF MVVM (Model View ViewModel)，控制器會類似於 ViewModel，但通常更緊密繫結至特定的 UI 項目。

可以在這裡找到更多詳細資料：

- [學習 Apple 網站上的 MVC](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)

- [在 OBJECTIVE-C 中的模型檢視控制器](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
- [使用 Windows](~/mac/user-interface/window.md)

## <a name="data-source--delegate--subclassing"></a>資料來源 / 委派] / [子類別化

另一個非常常見的模式，在 Cocoa 專門提供資料給 UI 項目和回應使用者互動。 使用`NSTableView`做為範例，您需要以某種方式提供資料給每個資料列中，部分設定的 UI 項目，以表示該資料列，部分組來回應使用者互動，並可能一定程度的自訂行為。 資料來源和委派模式可讓您處理大部分的情況下，而不必訴諸於子類別化`NSTableView`自己。

- `DataSource`屬性會被指派的自訂子類別的執行個體`NSTableViewDataSource`稱為來填入資料的資料表 (透過`GetRowCount`和`GetObjectValue`)。

- `Delegate`屬性會被指派的自訂子類別的執行個體`NSTableViewDelegate`可指定的模型物件的檢視 (透過`GetViewForItem`)，以及處理 UI 互動 (透過`DidClickTableColumn`，`MouseDownInHeaderOfTableColumn`等等)。

在某些情況下，您會想要自訂控制項的委派或資料來源所提供的勾點以外的方式，以及您可以直接進行子分類的檢視。 不過要小心，在許多情況下，覆寫預設行為則會要求您自行處理該行為的所有問題 （自訂選取行為可能會需要您自行實作所有的選取項目行為）。

在 Xamarin.iOS，某些 Api，例如`UITableView`已擴充實作委派和資料來源的內容 (`UITableViewSource`)。 若要解決常見的限制此 it 的單一C#類別只能有一個基底類別，和我們呈現的通訊協定會透過基底類別。

如需有關使用 「 在 Xamarin.Mac 應用程式中的表格檢視的詳細資訊，請參閱我們[資料表檢視](~/mac/user-interface/table-view.md)文件。

## <a name="protocols"></a>通訊協定

中的介面，就可以比較在 OBJECTIVE-C 中的通訊協定C#，並在許多情況下會在類似情況下使用。 例如`NSTableView`上述範例中，委派和資料來源是實際的通訊協定。 Xamarin.Mac 公開 （expose) 這些做為基底類別虛擬方法，您可以覆寫。 主要差異C#介面與 OBJECTIVE-C 通訊協定是一種通訊協定中的某些方法可能是選擇性的以實作。 您必須查看的文件及/或 API，以判斷項目是選擇性的定義。

詳細資訊，請參閱我們[委派、 通訊協定和事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)文件。



## <a name="related-links"></a>相關連結

- [資料表檢視](~/mac/user-interface/table-view.md)
- [使用 windows](~/mac/user-interface/window.md)
- [委派、 通訊協定，以及事件](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html)
