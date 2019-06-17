---
title: 在 Xamarin.Forms 中的動畫
description: Xamarin.Forms 包含自己的動畫基礎結構直接建立簡單的動畫，同時也很多方面足敷建立複雜的動畫。
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: bebb3e32f298a2079069787dba3453e1817cf64f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61158589"
---
# <a name="animation-in-xamarinforms"></a>在 Xamarin.Forms 中的動畫

_Xamarin.Forms 包含自己的動畫基礎結構直接建立簡單的動畫，同時也很多方面足敷建立複雜的動畫。_

Xamarin.Forms 動畫類別以一般動畫，以漸進方式從某個值經過一段時間，將屬性變更為另一個設為目標的 visual 元素，不同的屬性。 請注意，Xamarin.Forms 動畫類別沒有 XAML 介面。 不過，動畫可以封裝在[行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)，然後從 XAML 參照。

## <a name="simple-animationssimplemd"></a>[簡單動畫](simple.md)

[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)類別提供擴充方法，可用來建構簡單的動畫的旋轉、 縮放、 轉譯，和淡出[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)執行個體。 這篇文章示範如何建立和取消動畫使用`ViewExtensions`類別。

## <a name="easing-functionseasingmd"></a>[Easing 函式](easing.md)

包含 Xamarin.Forms [ `Easing` ](xref:Xamarin.Forms.Easing)可讓您指定傳輸函式可控制如何動畫加速或減慢它們執行的類別。 這篇文章會示範如何使用預先定義的 easing 函式，以及如何建立自訂的 easing 函式。

## <a name="custom-animationscustommd"></a>[自訂動畫](custom.md)

[ `Animation` ](xref:Xamarin.Forms.Animation)類別是所有的 Xamarin.Forms 動畫，使用中的擴充方法的建置組塊[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions)建立一或多個類別`Animation`物件。 這篇文章示範如何使用`Animation`類別來建立和取消動畫、 同步處理多個動畫，並建立自訂動畫顯示屬性不是由現有的動畫方法建立動畫的動畫。
