---
title: Xamarin.Forms 資料範本
description: DataTemplate 用來指定資料的外觀上支援的控制項，並且通常繫結的資料會顯示。
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: b461cb8be9dcf618b7a497b001186fde5f2b2aa1
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35239852"
---
# <a name="xamarinforms-data-templates"></a>Xamarin.Forms 資料範本

_DataTemplate 用來指定資料的外觀上支援的控制項，並且通常繫結的資料會顯示。_

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

Xamarin.Forms 資料範本會提供支援的控制項上定義資料的呈現方式的能力。 本文章會提供為什麼會需要這些檢查的資料範本的簡介。

## <a name="creating-a-datatemplatecreatingmd"></a>[建立 DataTemplate](creating.md)

資料範本中可以建立內嵌， [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，或從自訂類型或適當的 Xamarin.Forms 資料格類型。 如果不需要重複使用資料範本的其他位置，則應該使用內嵌範本。 或者，資料範本可以重複使用定義為自訂類型，或控制層級頁面層級或應用程式層級資源。

## <a name="creating-a-datatemplateselectorselectormd"></a>[建立 DataTemplateSelector](selector.md)

A [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/)可用來選擇[ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/)在執行階段根據資料繫結屬性的值。 這可讓多個`DataTemplate`来套用至的物件，以自訂特定物件的外觀相同類型的執行個體。 本文示範如何建立和使用`DataTemplateSelector`。


## <a name="related-links"></a>相關連結

- [資料範本 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
