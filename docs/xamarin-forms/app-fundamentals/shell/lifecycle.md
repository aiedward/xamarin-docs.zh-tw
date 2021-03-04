---
title: Xamarin.Forms Shell 生命週期
description: Shell 應用程式會遵循 Xamarin.Forms 生命週期，並在頁面即將出現在畫面上時，另外引發出現的事件，以及當頁面即將從畫面上消失時的消失事件。
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/15/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1897f04c4dc1c148fa1963fb2620aad33b38e524
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757528"
---
# <a name="xamarinforms-shell-lifecycle"></a>Xamarin.Forms Shell 生命週期

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Shell 應用程式 Xamarin.Forms 會遵循生命週期，並 [`Appearing`](xref:Xamarin.Forms.BaseShellItem.Appearing) 在頁面即將出現在畫面上時另外引發事件，以及在 [`Disappearing`](xref:Xamarin.Forms.BaseShellItem.Disappearing) 頁面即將從畫面上消失時引發事件。 這些事件會傳播到頁面，而且可以藉由覆寫 [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) 頁面上的或方法來處理 [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) 。

> [!NOTE]
> 在 Shell 應用程式中，在平台程式碼顯示頁面或從螢幕中移除頁面之前，即會從跨平台程式碼引發 `Appearing` 和 `Disappearing` 事件。

如需 Xamarin.Forms 應用程式生命週期的詳細資訊，請參閱[ Xamarin.Forms 應用程式生命週期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)。

## <a name="hierarchical-navigation"></a>階層式瀏覽

在 Shell 應用程式中，將頁面推送到導覽堆疊上將會產生目前可見的 [`ShellContent`](xref:Xamarin.Forms.ShellContent) 物件及其頁面內容，並引發 [`Disappearing`](xref:Xamarin.Forms.BaseShellItem.Disappearing) 事件。 同樣地，從導覽堆疊中取出最後一個頁面，將會產生新的可見 `ShellContent` 物件及其頁面內容，並引發  [`Appearing`](xref:Xamarin.Forms.BaseShellItem.Appearing) 事件。

如需階層式流覽的詳細資訊，請參閱[ Xamarin.Forms 階層式流覽](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="modal-navigation"></a>強制回應瀏覽

在 Shell 應用程式中，將強制回應頁面推送至強制回應流覽堆疊，會導致所有可見的 Shell 物件引發 [`Disappearing`](xref:Xamarin.Forms.BaseShellItem.Disappearing) 事件。 同樣地，從強制回應流覽堆疊中取出最後一個強制回應頁面，將會導致引發事件的所有可見 Shell 物件 [`Appearing`](xref:Xamarin.Forms.BaseShellItem.Appearing) 。

如需強制回應導覽的詳細資訊，請參閱強制回應[ Xamarin.Forms 頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

## <a name="related-links"></a>相關連結

- [Xaminals (範例)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms 應用程式生命週期](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Xamarin.Forms 強制回應頁面](~/xamarin-forms/app-fundamentals/navigation/modal.md)
