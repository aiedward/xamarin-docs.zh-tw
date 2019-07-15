---
title: Xamarin.Forms DependencyService
description: Xamarin.Forms DependencyService 類別是服務定位器，讓 Xamarin.Forms 應用程式能夠從共用程式碼叫用原生平台功能。
ms.prod: xamarin
ms.assetid: 403479F2-6751-41F2-ADCE-3AF595062FE4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: ea259d1ee9dc4a94322c38b3e96bee654197bb87
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650446"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.Forms DependencyService

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

[`DependencyService`](xref:Xamarin.Forms.DependencyService) 類別是服務定位器，讓 Xamarin.Forms 應用程式能夠從共用程式碼叫用原生平台功能。

## <a name="registration-and-resolutionregistration-and-resolutionmd"></a>[註冊和解析](registration-and-resolution.md)

平台實作必須向 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 登錄，然後從共用程式碼解析以叫用它們。

## <a name="picking-a-photo-from-the-libraryphoto-pickermd"></a>[從圖片庫中挑選相片](photo-picker.md)

此文章說明如何使用 Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) 類別從手機的圖片庫挑選相片。
