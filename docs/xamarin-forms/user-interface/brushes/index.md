---
title: Xamarin.Forms畫
description: Xamarin.Forms筆刷類別是一種抽象類別，可繪製區域及其輸出。
ms.prod: xamarin
ms.assetid: 44420FC2-304C-4175-8654-76769F79A813
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f1f56103b20eac84ce6106c0955acebf974cfe3
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918917"
---
# <a name="no-locxamarinforms-brushes"></a>Xamarin.Forms畫

![預覽 API](~/media/shared/preview.png "此 API 目前是發行前版本")

筆刷可讓您使用不同的方法繪製區域，例如控制項的背景。 中的筆刷支援 Xamarin.Forms 適用于 `Xamarin.Forms` IOS、Android、macOS 上的命名空間、通用 WINDOWS 平臺 (UWP) ，以及 (WPF) 的 Windows Presentation Foundation。

> [!IMPORTANT]
> 中的筆刷支援 Xamarin.Forms 目前為實驗性，只能透過設定旗標來使用 `Brush_Experimental` 。 如需詳細資訊，請參閱[實驗性旗標](~/xamarin-forms/internals/experimental-flags.md)。

`Brush`類別是一種抽象類別，會使用其輸出繪製區域。 衍生自的類別 `Brush` 描述繪製區域的不同方式。 下列清單描述中可用的不同筆刷類型 Xamarin.Forms ：

- `SolidColorBrush`，其以純色繪製區域。 如需詳細資訊，請參閱[ Xamarin.Forms 筆刷：純色](solidcolor.md)。
- `LinearGradientBrush`，使用線性漸層繪製區域。 如需詳細資訊，請參閱[ Xamarin.Forms 筆刷：線性](lineargradient.md)漸層。
- `RadialGradientBrush`，其繪製具有放射狀漸層的區域。 如需詳細資訊，請參閱[ Xamarin.Forms 筆刷：](radialgradient.md)星形漸層。

這些筆刷類型的實例可以指派給的 `Stroke` 和 `Fill` 屬性 `Shape` ，以及的 `Background` 屬性 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。

> [!NOTE]
> `VisualElement.Background`屬性可讓筆刷當做任何控制項的背景使用。

`Brush`類別也有傳回的 `IsNullOrEmpty` 方法， `bool` 表示是否已定義筆刷。
