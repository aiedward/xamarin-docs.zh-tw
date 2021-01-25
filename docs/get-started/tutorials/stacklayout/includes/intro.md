---
ms.openlocfilehash: d5acf27dfbda0dcd31b00600cee2f16b98aaa70a
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634748"
---
在嘗試此教學課程之前，您應該已成功完成：

- [建置您的第一個 Xamarin.Forms 應用程式](~/get-started/first-app/index.md)快速入門。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
>
> - 在 XAML 中建立 Xamarin.Forms [`StackLayout`](xref:Xamarin.Forms.StackLayout)。
> - 指定 `StackLayout` 的方向。
> - 控制 `StackLayout` 內的子檢視對齊和延展。

您將會使用 Visual Studio 2019 或 Visual Studio for Mac 來建立能示範如何在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 內對齊控制項的簡單應用程式。 下列螢幕擷取畫面顯示的是最終的應用程式：

[![螢幕擷取畫面：iOS 和 Android 上已設定對齊和延展選項的 StackLayout 子檢視](../images/alignment-expansion-reduced.png "包含 Label 執行個體並已設定對齊和延展的 StackLayout")](../images/alignment-expansion-large.png#lightbox "包含 Label 執行個體並已設定對齊和延展的 StackLayout")

您也會使用[適用於 Xamarin.Forms 的 XAML 熱重新載入](~/xamarin-forms/xaml/hot-reload.md)查看 UI 變更，而不需要重建您的應用程式。
