---
title: Xamarin.Forms 資料範本
description: Datatemplate 的範圍用來針對支援的控制項，指定資料的外觀，通常要顯示的繫結至資料。
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 771ae22c3e28a4fce758bbfd6a3bd63bafb75e53
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994967"
---
# <a name="xamarinforms-data-templates"></a>Xamarin.Forms 資料範本

_Datatemplate 的範圍用來針對支援的控制項，指定資料的外觀，通常要顯示的繫結至資料。_

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

Xamarin.Forms 資料範本提供支援的控制項上定義資料的呈現方式的能力。 這篇文章介紹資料範本，檢查為什麼需要這些項目。

## <a name="creating-a-datatemplatecreatingmd"></a>[建立 DataTemplate](creating.md)

資料範本可以在建立內嵌[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，或從自訂型別或適當的 Xamarin.Forms 資料格類型。 如果不需要重複使用資料範本其他位置，則應該使用內嵌範本。 或者，可以藉由定義它，做為自訂類型，或做為控制層級、 頁面層級或應用程式層級資源重複資料範本。

## <a name="creating-a-datatemplateselectorselectormd"></a>[建立 DataTemplateSelector](selector.md)

A [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)可用來選擇[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)在執行階段根據資料繫結屬性的值。 這可讓多個`DataTemplate`来套用至的物件，以自訂特定物件的外觀相同類型的執行個體。 這篇文章示範如何建立和使用`DataTemplateSelector`。


## <a name="related-links"></a>相關連結

- [資料範本 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
