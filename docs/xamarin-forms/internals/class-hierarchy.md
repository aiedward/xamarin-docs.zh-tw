---
title： " Xamarin.Forms 控制項類別階層架構" 描述：「開發人員應該熟悉用來建立應用程式使用者介面的類型階層」 Xamarin.Forms 。
assetid： C89E6B98-464D-4BBE-BF11-13A5FCBBF420 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：01/07/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-controls-class-hierarchy"></a>Xamarin.Forms控制項類別階層架構

Xamarin.Forms是由多個命名空間的數百個類型所組成。 開發人員應該最熟悉用來建立 Xamarin.Forms 應用程式使用者介面（位於命名空間中）的類型階層 `Xamarin.Forms` 。

這些類型可以分割成頁面、版面配置、視圖和資料格。 Xamarin.Forms頁面通常會佔用整個畫面，而所有頁面類型則衍生自 [`Page`](xref:Xamarin.Forms.Page) 類別。 頁面通常包含版面配置，而且所有版面配置類型都衍生自 [`Layout`](xref:Xamarin.Forms.Layout) 類別。 版面配置通常包含視圖和可能的其他配置，而所有檢視類型最終都是衍生自 [`View`](xref:Xamarin.Forms.View) 類別。 最後，儲存格是用來在和控制項中顯示資料的特製化控制項 [`TableView`](xref:Xamarin.Forms.TableView) [`ListView`](xref:Xamarin.Forms.ListView) 。 頁面、版面配置、視圖和儲存格最後都是衍生自 [`Element`](xref:Xamarin.Forms.Element) 類別。

下列類別圖顯示通常用來在中建立使用者介面的類型階層 Xamarin.Forms ：

[![Xamarin.Forms控制項類別圖表](class-hierarchy-images/class-diagram.png "[!OP.無 LOC （Xamarin）控制項類別圖表")](class-hierarchy-images/class-diagram-large.png#lightbox "[!OP.無 LOC （Xamarin）控制項類別圖表")

> [!NOTE]
> 您可以從[這裡](class-hierarchy-images/class-diagram-high-resolution.png)下載類別圖表的高解析度版本。 不過，請注意，圖表只會顯示單一 Shell 類型。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms控制項參考](~/xamarin-forms/user-interface/controls/index.md)
