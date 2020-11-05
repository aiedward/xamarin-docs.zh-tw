---
title: Xamarin.Forms 資料範本
description: DataTemplate 用於在支援的控制項中指定資料外觀，通常會繫結至要顯示的資料。
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 682ed3b0d0f54617a0238dcc1bbf413e1ee0156a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373103"
---
# <a name="no-locxamarinforms-data-templates"></a>Xamarin.Forms 資料範本

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_DataTemplate 用於在支援的控制項中指定資料外觀，通常會繫結至要顯示的資料。_

## <a name="introduction"></a>[簡介](introduction.md)

Xamarin.Forms 資料範本提供在支援的控制項上定義資料呈現方式的能力。 本文提供資料範本的簡介，探討為何資料範本是必要的。

## <a name="creating-a-datatemplate"></a>[建立 DataTemplate](creating.md)

資料範本可以內嵌方式建立， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 或是從自訂類型或適當的資料 Xamarin.Forms 格類型建立。 如果不需要在他處重複使用資料範本，則應該使用內嵌範本。 或者，您可以將資料範本定義為自訂類型，或是定義為控制項層級、頁面層級或應用程式層級資源，來重複使用它。

## <a name="creating-a-datatemplateselector"></a>[建立 DataTemplateSelector](selector.md)

[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)可以用來根據資料系結 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 屬性的值，在執行時間選擇。 這可將多個 `DataTemplate` 執行個體套用至相同類型的物件，以自訂特定物件的外觀。 本文示範如何建立和使用 `DataTemplateSelector`。

## <a name="related-links"></a>相關連結

- [Data Templates (Samples)](/samples/xamarin/xamarin-forms-samples/templates-datatemplates) (資料範本 (範例))