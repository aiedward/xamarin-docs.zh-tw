---
title: Xamarin.Forms Controls 類別階層
description: 開發人員應該熟悉用來建立應用程式使用者介面的類型階層 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ed3fc6d7aab48886f0c6166390b0ff224f66da60
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115173"
---
# <a name="no-locxamarinforms-controls-class-hierarchy"></a>Xamarin.Forms Controls 類別階層

Xamarin.Forms 是由多個命名空間所組成的數百種類型。 開發人員應該最熟悉用來建立 Xamarin.Forms 應用程式使用者介面（位於命名空間中）的類型階層 `Xamarin.Forms` 。

這些類型可以分為多個頁面、版面配置、視圖和資料格。 Xamarin.Forms頁面通常會佔用整個畫面，而所有頁面類型都是衍生自 [`Page`](xref:Xamarin.Forms.Page) 類別。 頁面通常包含配置，而且所有版面配置類型都是衍生自 [`Layout`](xref:Xamarin.Forms.Layout) 類別。 版面配置通常包含視圖，可能還有其他版面配置，而且所有檢視類型最終都是衍生自 [`View`](xref:Xamarin.Forms.View) 類別。 最後，儲存格是用來在和控制項中顯示資料的特殊化控制項 [`TableView`](xref:Xamarin.Forms.TableView) [`ListView`](xref:Xamarin.Forms.ListView) 。 頁面、版面配置、視圖和儲存格最後都是衍生自 [`Element`](xref:Xamarin.Forms.Element) 類別。

下列類別圖顯示通常用來在中建立使用者介面之類型的階層 Xamarin.Forms ：

[![：：：非 loc (Xamarin. Forms) ：：： Controls 類別圖表](class-hierarchy-images/class-diagram.png "：：：非 loc (Xamarin. Forms) ：：： controls 類別圖表")](class-hierarchy-images/class-diagram-large.png#lightbox "：：：非 loc (Xamarin. Forms) ：：： controls 類別圖表")

不過，請注意，此圖表只會顯示單一 Shell 型別。

> [!NOTE]
> 您可以從 [這裡](class-hierarchy-images/class-diagram-high-resolution.png)下載類別圖表的高解析度版本。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms 控制項參考](~/xamarin-forms/user-interface/controls/index.md)
