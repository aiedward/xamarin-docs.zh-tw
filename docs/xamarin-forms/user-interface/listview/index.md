---
title: Xamarin.Forms ListView
description: 本指南介紹 Xamarin.Forms ListView，可用來呈現精美的互動式的清單中的資料。
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2015
---

# <a name="xamarinforms-listview"></a>Xamarin.Forms ListView

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/WorkingWithListview)

[`ListView`](xref:Xamarin.Forms.ListView) 是呈現資料，需要捲動，特別是較長的清單的清單檢視。

> [!IMPORTANT]
> `CollectionView` 呈現資料的清單檢視使用不同的版面配置規格。 它旨在提供更多的彈性，以及高效能替代方式，來[ `ListView` ](xref:Xamarin.Forms.ListView)。 如需詳細資訊，請參閱 < [Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)。

## <a name="use-cases"></a>使用案例

請確定 ListView 右邊的控制項，為您的需求。 ListView 可用在任何情況下，您要在其中顯示資料的可捲動清單。 Listview 支援內容動作和資料繫結。

ListView 不應該與混淆[TableView](~/xamarin-forms/user-interface/tableview.md)。 TableView 控制項是更好的選項，只要有一份選項或資料之間的繫結。 例如，iOS 設定應用程式，有一組主要的預先定義的選項，比較適合使用 TableView 比 ListView。

請注意，ListView 是最佳也適合同質資料&ndash;也就是所有的資料應該是相同類型。 這是因為只有一個資料格類型可用來在清單中的每個資料列。 TableViews 可以支援多個資料格類型，因此當您需要混用檢視時，它們會是較好的選擇。

## <a name="components"></a>元件
ListView 的可執行每個平台的原生功能的元件數目。 每個元件如下所述。

- **[頁首和頁尾](customizing-list-appearance.md#Headers_and_Footers)** &ndash;文字或檢視表顯示的開頭和結尾的清單中，分隔從清單中的資料。 頁首和頁尾可以繫結至資料來源分開 ListView 的資料來源。
- **[群組](customizing-list-appearance.md#Grouping)** &ndash;可以群組在 ListView 中的資料，以方便瀏覽。 群組通常是資料繫結：

![](images/grouping-depth.png "與群組資料的 ListView")

- **[資料格](customizing-cell-appearance.md)** &ndash; ListView 中的資料會顯示在資料格中。 每個資料格會對應至一個資料列。 有內建的儲存格可供選擇，或您可以定義您自己自訂的儲存格。 內建和自訂的資料格可以是在 XAML 或程式碼中使用/定義。
  - **[內建](customizing-cell-appearance.md#Built_in_Cells)** &ndash;內建的儲存格，特別是 TextCell 和 ImageCell，可以是絕佳的效能，因為它們對應至每個平台上的原生控制項。
       - **[TextCell](customizing-cell-appearance.md#TextCell)**  &ndash;顯示的文字，並選擇性地使用詳細資料的文字字串。 詳細資料的文字會轉譯為較小的字型，並且強調色彩中的第二個資料行。
       - **[ImageCell](customizing-cell-appearance.md#ImageCell)**  &ndash;顯示文字的映像。 會顯示為 TextCell 左側的映像。
  - **[自訂資料格](customizing-cell-appearance.md#customcells)** &ndash;自訂儲存格很棒，當您要呈現複雜的資料。 例如，自訂檢視可用來呈現一份歌曲、 專輯和藝術工作者包括：

![](images/image-cell-default.png "使用 ImageCells ListView")

若要深入了解自訂的 ListView 中的資料格，請參閱[自訂 ListView 儲存格的外觀](customizing-cell-appearance.md)。

## <a name="functionality"></a>功能
ListView 可支援多種互動樣式，包括：

- **[若要重新整理提取](interactivity.md#Pull_to_Refresh)** &ndash; ListView 在每個平台上支援提取以重新整理。
- **[內容動作](interactivity.md#Context_Actions)** &ndash; ListView 在清單中的個別項目上支援採取行動。 例如，您可以在 iOS 上，實作撥動-動作或按住在 Android 上的動作。
- **[選取項目](interactivity.md#selectiontaps)** &ndash;選取項目和 deselections 採取動作，當點選的資料列時，您可以接聽。

![](images/context-default.png "ListView 內容動作")

若要深入了解 ListView 的互動性功能，請參閱[動作 & ListView 互動](interactivity.md)。

## <a name="related-links"></a>相關連結

- [使用與 ListView （範例）](https://developer.xamarin.com/samples/WorkingWithListview)
- [雙向繫結 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [建置在儲存格 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [自訂資料格 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [群組 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [自訂轉譯器檢視 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/WorkingWithListviewNative)
- [ListView 互動性 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
