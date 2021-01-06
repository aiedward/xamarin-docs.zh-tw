---
title: Xamarin.Forms 刷
description: Xamarin.Forms筆刷類別是一種抽象類別，它會使用其輸出繪製區域。
ms.prod: xamarin
ms.assetid: 44420FC2-304C-4175-8654-76769F79A813
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e0bb8b5e1668e683fa8b15f752b77d865cb317b5
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940391"
---
# <a name="no-locxamarinforms-brushes"></a>Xamarin.Forms 刷

筆刷可讓您使用不同的方法來繪製區域（例如控制項的背景）。 中的筆刷支援 Xamarin.Forms 可在 `Xamarin.Forms` IOS、Android、macOS、通用 WINDOWS 平臺 (UWP) 以及 WINDOWS PRESENTATION FOUNDATION (WPF) 上的命名空間中取得。

`Brush`類別是抽象類別，它會使用其輸出繪製區域。 衍生自的類別會 `Brush` 描述繪製區域的不同方式。 下列清單說明中可用的不同筆刷類型 Xamarin.Forms ：

- `SolidColorBrush`，以純色繪製區域。 如需詳細資訊，請參閱[ Xamarin.Forms 筆刷：單色](solidcolor.md)。
- `LinearGradientBrush`，用線性漸層繪製區域。 如需詳細資訊，請參閱[ Xamarin.Forms 筆刷：線性](lineargradient.md)漸層。
- `RadialGradientBrush`，用放射狀漸層繪製區域。 如需詳細資訊，請參閱[ Xamarin.Forms 筆刷：放射狀](radialgradient.md)漸層。

這些筆刷類型的實例可以指派給的 `Stroke` 和 `Fill` 屬性 `Shape` ，以及的 `Background` 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。

> [!NOTE]
> `VisualElement.Background`屬性可讓筆刷當做任何控制項中的背景使用。

`Brush`類別也有一個 `IsNullOrEmpty` 方法，它會傳回 `bool` 代表是否已定義筆刷的。
