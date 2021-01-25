---
ms.openlocfilehash: 1ff81c0399a0abb321831f8a72dfb2a8560816c4
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98689721"
---
在嘗試此教學課程之前，您應該已成功完成：

- [建置您的第一個 Xamarin.Forms 應用程式](~/get-started/first-app/index.md)快速入門。
- [StackLayout](~/get-started/tutorials/stacklayout/index.yml) 教學課程。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
>
> - 在 XAML 中建立 Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry)。
> - 回應 `Entry` 變更中的文字。
> - 自訂 `Entry` 的行為。

您將會使用 Visual Studio 2019 或 Visual Studio for Mac 來建立能示範如何自訂 [`Entry`](xref:Xamarin.Forms.Entry) 之行為的簡單應用程式。 下列螢幕擷取畫面顯示的是最終的應用程式：

[![螢幕擷取畫面：在 iOS 和 Android 上，文字由密碼字元遮罩的 Entry](../images/customize-behavior.png "使用密碼字元遮罩的 Entry")](../images/customize-behavior-large.png#lightbox "使用密碼字元遮罩的 Entry")

您也會使用[適用於 Xamarin.Forms 的 XAML 熱重新載入](~/xamarin-forms/xaml/hot-reload.md)查看 UI 變更，而不需要重建您的應用程式。
