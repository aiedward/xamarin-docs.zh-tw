---
title: Xamarin.Forms Shell 生命週期
description: Shell 應用程式採用 Xamarin.Forms 生命週期，在頁面即將出現在螢幕上時引發 Appearing 事件，在頁面即將從螢幕中消失時引發 Disappearing 事件。
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2019
ms.openlocfilehash: c008cc29d2ceae073459766597040af653329d71
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69893954"
---
# <a name="xamarinforms-shell-lifecycle"></a>Xamarin.Forms Shell 生命週期

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

Shell 應用程式採用 Xamarin.Forms 生命週期，在頁面即將出現在螢幕上時引發 `Appearing` 事件，在頁面即將從螢幕中消失時引發 `Disappearing` 事件。 這些事件會傳播至頁面，並可藉由覆寫頁面的 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 或 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 方法來處理。

> [!NOTE]
> 在 Shell 應用程式中，在平台程式碼顯示頁面或從螢幕中移除頁面之前，即會從跨平台程式碼引發 `Appearing` 和 `Disappearing` 事件。

如需有關 Xamarin.Forms 應用程式生命週期的詳細資訊，請參閱 [Xamarin.Forms 應用程式生命週期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)。

## <a name="hierarchical-navigation"></a>階層式瀏覽

在 Shell 應用程式中，將頁面推送到瀏覽堆疊上會產生目前可見的 `ShellContent` 物件，而其頁面內容則會引發 `Disappearing` 事件。 同樣地，快顯瀏覽堆疊的最後一頁會產生最新可見的 `ShellContent` 物件，而其頁面內容則會引發 `Appearing` 事件。

如需階層式瀏覽的詳細資訊，請參閱 [Xamarin.Forms 階層式瀏覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="modal-navigation"></a>強制回應瀏覽

在 Shell 應用程式中，將強制回應頁面推送到強制回應瀏覽堆疊上會導致所有可見的 Shell 物件引發 `Disappearing` 事件。 同樣地，從強制回應瀏覽堆疊快顯最後強制回應頁面會導致所有可見的 Shell 物件引發 `Appearing` 事件。

如需強制回應瀏覽的詳細資訊，請參閱 [Xamarin.Forms 強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [Xamarin.Forms 應用程式生命週期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Xamarin.Forms 強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)
