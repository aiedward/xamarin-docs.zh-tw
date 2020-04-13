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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "67650446"
---
# <a name="xamarinforms-dependencyservice"></a>Xamarin.Forms DependencyService

## <a name="introduction"></a>[簡介](introduction.md)

該[`DependencyService`](xref:Xamarin.Forms.DependencyService)類是一個服務定位器,使 Xamarin.Forms 應用程式能夠從共享代碼調用本機平臺功能。

## <a name="registration-and-resolution"></a>[註冊和解析](registration-and-resolution.md)

平台實現必須註冊到[`DependencyService`](xref:Xamarin.Forms.DependencyService), 然後從共享程式碼解析以呼叫它們。

## <a name="picking-a-photo-from-the-library"></a>[從圖片庫中挑選相片](photo-picker.md)

本文介紹如何使用 Xamarin.Forms[`DependencyService`](xref:Xamarin.Forms.DependencyService)類從手機的圖片庫中選取照片。
