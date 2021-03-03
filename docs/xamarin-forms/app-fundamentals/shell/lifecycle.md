---
title: Xamarin.Forms Shell 生命週期
description: Shell 應用程式會遵循 Xamarin.Forms 生命週期，而當頁面即將出現在畫面上時，就會引發顯示的事件，而且當頁面即將從畫面上消失時，就會引發消失事件。
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 830b86c0e8eeeef528c3c1f55a565ef08dad6896
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373272"
---
# <a name="xamarinforms-shell-lifecycle"></a>Xamarin.Forms Shell 生命週期

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Shell 應用程式 Xamarin.Forms 會遵循生命週期，而 `Appearing` 當頁面即將出現在畫面上時，就會引發事件，而且 `Disappearing` 當頁面即將從畫面上消失時，就會引發事件。 這些事件會傳播到頁面，而且可以藉由覆寫 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 頁面上的或方法來處理 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 。

> [!NOTE]
> 在 Shell 應用程式中，在平台程式碼顯示頁面或從螢幕中移除頁面之前，即會從跨平台程式碼引發 `Appearing` 和 `Disappearing` 事件。

如需 Xamarin.Forms 應用程式生命週期的詳細資訊，請參閱[ Xamarin.Forms 應用程式生命週期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)。

## <a name="hierarchical-navigation"></a>階層式瀏覽

在 Shell 應用程式中，將頁面推送到瀏覽堆疊上會產生目前可見的 `ShellContent` 物件，而其頁面內容則會引發 `Disappearing` 事件。 同樣地，快顯瀏覽堆疊的最後一頁會產生最新可見的 `ShellContent` 物件，而其頁面內容則會引發 `Appearing` 事件。

如需階層式流覽的詳細資訊，請參閱[ Xamarin.Forms 階層式流覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="modal-navigation"></a>強制回應瀏覽

在 Shell 應用程式中，將強制回應頁面推送到強制回應瀏覽堆疊上會導致所有可見的 Shell 物件引發 `Disappearing` 事件。 同樣地，從強制回應瀏覽堆疊快顯最後強制回應頁面會導致所有可見的 Shell 物件引發 `Appearing` 事件。

如需強制回應導覽的詳細資訊，請參閱強制回應[ Xamarin.Forms 頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms 應用程式生命週期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Xamarin.Forms 強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)