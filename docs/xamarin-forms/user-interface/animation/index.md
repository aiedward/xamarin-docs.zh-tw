---
title: "動畫"
description: "Xamarin.Forms 包含本身可以直接建立簡單的動畫，同時也會多到足以的用途，來建立複雜的動畫的動畫基礎結構。"
ms.topic: article
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: e50419eaa6466e94fc5192a77ffd7cb89ca9d965
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="animation"></a>動畫

_Xamarin.Forms 包含本身可以直接建立簡單的動畫，同時也會多到足以的用途，來建立複雜的動畫的動畫基礎結構。_

Xamarin.Forms 動畫類別以一般的動畫逐漸從一個值經過一段時間，將屬性變更為另一個為目標的視覺項目，不同的屬性。 請注意，Xamarin.Forms 動畫類別沒有 XAML 介面。 不過，封裝動畫，在[行為](~/xamarin-forms/app-fundamentals/behaviors/index.md)，然後從 XAML 參照。

## <a name="simple-animationssimplemd"></a>[簡單的動畫](simple.md)

[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別提供擴充方法，可以用來建構簡單旋轉、 縮放、 轉譯，和淡出動畫[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)執行個體。 本文示範如何建立，並取消使用動畫`ViewExtensions`類別。

## <a name="easing-functionseasingmd"></a>[Easing 函式](easing.md)

Xamarin.Forms 包含[ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)可讓您指定傳輸函式可控制如何動畫加速或慢它們正在執行的類別。 本文示範如何使用預先定義的 easing 函式，以及如何建立自訂 easing 函式。

## <a name="custom-animationscustommd"></a>[自訂動畫](custom.md)

[ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)類別是所有 Xamarin.Forms 動畫中的擴充方法使用的建置組塊[ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)類別建立一個或多個`Animation`物件。 本文示範如何使用`Animation`類別來建立和取消動畫、 同步處理多個動畫，並建立自訂的動畫，而不現有動畫方法所繪製的屬性以動畫顯示。

