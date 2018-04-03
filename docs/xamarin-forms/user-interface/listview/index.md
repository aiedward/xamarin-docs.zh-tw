---
title: ListView
description: 美觀、 更互動的清單中呈現您的資料。
ms.topic: article
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2015
ms.openlocfilehash: 494c69700ed0b12b4c9151b9a1b04ea091ebfa57
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="listview"></a>ListView

清單檢視是以呈現資料，需要捲動，特別是較長的清單的清單檢視。 本指南將說明如何使用清單檢視：

1. **[資料來源](data-and-databinding.md)** &ndash;填入資料，不論資料繫結的 ListView。
2. **[儲存格外觀](customizing-cell-appearance.md)** &ndash;自訂內建的儲存格的外觀，或建立您自己自訂的儲存格。
3. **[列出外觀](customizing-list-appearance.md)** &ndash;自訂 ListView 的外觀。 設定頁首和頁尾、 啟用群組及變更資料列的高度。
4. **[互動功能](interactivity.md)** &ndash;處理點選，以及選取項目，並執行更新提取，以及新增內容的動作。
5. **[效能](performance.md)** &ndash;避免發生效能問題。

## <a name="use-cases"></a>使用案例
請確定 ListView 適合的控制項，您的需求。 ListView 可用在任何情況下，您要在其中顯示可捲動資料的清單。 Listview 支援的動作內容和資料繫結。

不應與混淆 ListView [TableView](~/xamarin-forms/user-interface/tableview.md)。 TableView 控制項是更好的選項，只要有一份選項或資料之間的繫結。 例如，iOS 設定應用程式，有一組大部分預先定義的選項，會更好適合使用 TableView 比 ListView。

也請注意，是最佳的 ListView 適合同質資料&ndash;也就是所有資料必須都是相同的型別。 這是因為資料格的一種可用於在清單中的每個資料列。 TableViews 可以支援多個儲存格類型，因此您需要混合檢視時有更好的選項。


## <a name="components"></a>元件
ListView 有多個元件可用來執行每個平台的原生功能。 下面會描述每個元件：

- **[頁首和頁尾](customizing-list-appearance.md#Headers_and_Footers)** &ndash;文字或檢視表顯示在開頭和結尾的清單，以便與清單的資料分開。 頁首和頁尾可以繫結至資料來源分開 ListView 的資料來源。
- **[群組](customizing-list-appearance.md#Grouping)** &ndash;可以群組在 ListView 中的資料，以方便瀏覽。 群組通常是資料繫結：

![](images/grouping-depth.png "ListView 與群組資料")

- **[資料格](customizing-cell-appearance.md)** &ndash; ListView 中的資料會以資料格。 每個資料格會對應至一個資料列。 內建資料格，以供選擇，或您可以定義您自己自訂的儲存格。 內建和自訂的資料格可以是在 XAML 或程式碼中使用/定義。
  - **[內建](customizing-cell-appearance.md#Built_in_Cells)** &ndash;內建的儲存格，特別是 TextCell 和 ImageCell，可能會很好的效能，因為它們對應於每個平台上的原生控制項。
    - **[TextCell](customizing-cell-appearance.md#TextCell)**  &ndash;顯示的文字，並選擇性地使用詳細文字的字串。 詳細資料的文字會轉譯成較小的字型與輔色中的第二個資料行。
    - **[ImageCell](customizing-cell-appearance.md#ImageCell)**  &ndash;顯示文字的影像。 會顯示為 TextCell 使用左側的映像。
  - **[自訂儲存格](customizing-cell-appearance.md#customcells)** &ndash;自訂儲存格相當，當您需要提供複雜的資料。 例如，自訂檢視可用於呈現一份歌曲、 專輯和演出者包括：

![](images/image-cell-default.png "ListView ImageCells")

若要深入了解自訂 ListView 中的儲存格，請參閱[自訂 ListView 儲存格外觀](customizing-cell-appearance.md)。

## <a name="functionality"></a>功能
ListView 支援多個互動的樣式，包括：

- **[若要重新整理提取](interactivity.md#Pull_to_Refresh)** &ndash; ListView 上每個平台支援提取-重新整理。
- **[內容動作](interactivity.md#Context_Actions)** &ndash; ListView 支援清單中的個別項目上採取行動。 比方說，您可以在 iOS 上，實作撥動至動作或長時間點選在 Android 和 Windows Phone 上的動作。
- **[選取項目](interactivity.md#selectiontaps)** &ndash;您可以接聽項目，並 deselections 時要採取的動作所點選的資料列。

![](images/context-default.png "動作內容的 ListView")

若要深入了解 ListView 的互動功能，請參閱[動作 （& s) 互動 ListView](interactivity.md)。


## <a name="related-links"></a>相關連結

- [使用與 ListView （範例）](https://developer.xamarin.com/samples/WorkingWithListview)
- [兩個方式繫結 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [建置在儲存格 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [自訂儲存格 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [群組 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [自訂轉譯器檢視 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/WorkingWithListviewNative)
- [ListView 互動功能 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [iOS Workbook](https://developer.xamarin.com/workbooks/xamarin-forms/user-interface/listview/ListView1-ios.workbook)
- [Android Workbook](https://developer.xamarin.com/workbooks/xamarin-forms/user-interface/listview/ListView1-android.workbook)
