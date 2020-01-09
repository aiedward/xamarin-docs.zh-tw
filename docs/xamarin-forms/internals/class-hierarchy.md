---
title: Xamarin Forms 控制項類別階層架構
description: 開發人員應該熟悉用來建立 Xamarin 應用程式使用者介面的類型階層架構。
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2020
ms.openlocfilehash: 7c55ed3082a031352a8eefb8ef579060a9dd6404
ms.sourcegitcommit: 4899cf4aaa73aa56c48f7ee2339c0af8112e1feb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2020
ms.locfileid: "75717288"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Xamarin Forms 控制項類別階層架構

Xamarin 會由多個命名空間的數百種類型所組成。 開發人員應該最熟悉用來建立 Xamarin 應用程式使用者介面（位於 `Xamarin.Forms` 命名空間）的類型階層架構。

這些類型可以分割成頁面、版面配置、視圖和資料格。 [Xamarin] 頁面通常會佔用整個畫面，而所有頁面類型則衍生自[`Page`](xref:Xamarin.Forms.Page)類別。 頁面通常包含配置，而所有版面配置類型都衍生自[`Layout`](xref:Xamarin.Forms.Layout)類別。 版面配置通常包含視圖和可能的其他配置，而所有檢視類型最終都是衍生自[`View`](xref:Xamarin.Forms.View)類別。 最後，儲存格是用來在[`TableView`](xref:Xamarin.Forms.TableView)和[`ListView`](xref:Xamarin.Forms.ListView)控制項中顯示資料的特製化控制項。 頁面、版面配置、視圖和資料格最後都是從[`Element`](xref:Xamarin.Forms.Element)類別衍生而來。

下列類別圖顯示通常用來在 Xamarin 中建立使用者介面的型別階層。表單：

[![Xamarin 控制項類別圖表](class-hierarchy-images/class-diagram.png "Xamarin 控制項類別圖表")](class-hierarchy-images/class-diagram-large.png#lightbox "Xamarin 控制項類別圖表")

> [!NOTE]
> 您可以從[這裡](class-hierarchy-images/class-diagram-high-resolution.png)下載類別圖表的高解析度版本。 不過，請注意，圖表只會顯示單一 Shell 類型。

## <a name="related-links"></a>相關連結

- [Xamarin. Forms 控制項參考](~/xamarin-forms/user-interface/controls/index.md)
