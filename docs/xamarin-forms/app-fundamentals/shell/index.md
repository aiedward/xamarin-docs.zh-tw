---
title: Xamarin.Forms 殼
description: 本指南說明如何使用 Xamarin.Forms Shell，藉 Xamarin.Forms 由提供大部分應用程式所需的基本功能，降低應用程式的複雜度。
ms.prod: xamarin
ms.assetid: 85B322AA-808F-41B6-953A-5877264AE643
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/22/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 76598f50199eb88b809b1d63e49885b750cf3e07
ms.sourcegitcommit: 1b542afc0f6f2f6adbced527ae47b9ac90eaa1de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101757571"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms 殼

## <a name="introduction"></a>[簡介](introduction.md)

Xamarin.Forms Shell 會提供大部分行動應用程式所需的基本功能，藉此降低行動應用程式開發的複雜度。 這包括一般導覽使用者體驗、URI 型導覽配置，以及整合式搜尋處理常式。

## <a name="create-a-xamarinforms-shell-application"></a>[建立 Xamarin.Forms Shell 應用程式](create.md)

建立 Xamarin.Forms Shell 應用程式的程式是建立將類別分類的 XAML 檔案 [`Shell`](xref:Xamarin.Forms.Shell) 、將應用程式類別的屬性設定為子類別化的 `MainPage` `App` `Shell` 物件，然後在子類別化類別中描述應用程式的視覺化階層 `Shell` 。

## <a name="flyout"></a>[飛出視窗](flyout.md)

飛出視窗是 Shell 應用程式的選擇性根功能表，可透過圖示或從螢幕側邊刷來存取。 飛出視窗包含選擇性的標頭、飛出視窗專案、選擇性功能表項目和選擇性頁尾。

## <a name="tabs"></a>[索引標籤](tabs.md)

飛出視窗之後，Shell 應用程式中的下一個導覽層級是底部的索引標籤列。 或者，應用程式的導覽模式可以從底部索引標籤開始，而不使用飛出視窗。 在這兩種情況下，當底部索引標籤包含多個頁面時，頁面將可透過頂端索引標籤來導覽。

## <a name="pages"></a>[頁面](pages.md)

[`ShellContent`](xref:Xamarin.Forms.ShellContent)物件代表 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 每個或的物件 [`FlyoutItem`](xref:Xamarin.Forms.FlyoutItem) [`Tab`](xref:Xamarin.Forms.Tab) 。

## <a name="navigation"></a>[導覽](navigation.md)

Shell 應用程式可以利用 URI 型導覽配置，使用路由導覽至應用程式中的任何頁面，而不需要遵循設定的導覽階層。

## <a name="search"></a>[搜尋](search.md)

Shell 應用程式可以使用能夠新增至每個頁面頂端的搜尋方塊所提供的整合式搜尋功能。

## <a name="lifecycle"></a>[生命週期](lifecycle.md)

Shell 應用程式 Xamarin.Forms 會遵循生命週期，並 `Appearing` 在頁面即將出現在畫面上時另外引發事件，以及在 `Disappearing` 頁面即將從畫面上消失時引發事件。

## <a name="custom-renderers"></a>[自訂轉譯器](customrenderers.md)

您可以透過各種 Shell 類別所公開的屬性和方法，自訂 shell 應用程式。 不過，當需要更複雜的平臺特定自訂時，也可以建立 Shell 自訂轉譯器。
