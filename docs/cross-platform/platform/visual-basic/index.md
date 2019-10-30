---
title: Visual Basic 和 .NET Standard
description: 本指南將說明如何使用 Visual Basic 來撰寫可用於以 Xamarin. iOS 和 Xamarin 為目標之方案中的 .NET Standard 專案。
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 594f7584e914b7bd8f4d7b72b3c82c42bb2fb73e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014578"
---
# <a name="visual-basic-and-net-standard"></a>Visual Basic 和 .NET Standard

Xamarin Android 和 iOS 專案原本就不支援 Visual Basic;不過，開發人員可以使用[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)程式庫，將現有的 Visual Basic 程式碼遷移至 Android 和 iOS，或在 Visual Basic 中寫入其應用程式邏輯的重要部分。 Xamarin. Forms 應用程式可以完全在 Visual Basic 中建立（不包括自訂轉譯器、相依性服務和 XAML 程式碼後置）。

## <a name="requirements"></a>需求

若要建立和編譯 Visual Basic .NET Standard 程式庫，您必須在 Windows 上使用 Visual Studio （Visual Studio 2017 或更新版本）。

> [!NOTE]
> Visual Basic 程式庫只能使用 Visual Studio 建立和編譯。 [Xamarin] 和 [Xamarin] 不支援 Visual Basic 語言。
>
> 如果您僅在 Visual Studio 中工作，您可以從 [Xamarin] 和 [Xamarin] 專案參考 Visual Basic 專案。
>
> 如果您的 Android 和 iOS 專案也必須載入 Visual Studio for Mac 您應該從 Visual Basic 元件參考輸出元件。

## <a name="creating-a-visual-basicnet-net-standard-library"></a>建立 Visual Basic.NET .NET Standard 程式庫

本節逐步解說如何使用 Visual Studio 2019 來建立 Visual Basic .NET Standard 程式庫。
然後，程式庫可以在其他專案中參考，包括 Xamarin、Xamarin 和 Xamarin。 Forms 應用程式。

在 Visual Studio 中加入 Visual Basic .NET Standard 程式庫時，您必須小心選擇正確的專案類型：

1. 從 Visual Studio 2019 選擇 [**建立新專案**]。

2. 輸入**Visual Basic 程式庫** 來篩選項目選項，然後選擇 **類別庫 （.NET Standard）** 選項與 Visual Basic 圖示：

    [Visual Basic 程式庫的![篩選](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

3. 在下一個畫面上，輸入專案的名稱，然後按 [**建立**]。

4. 如下所示，Visual Basic 專案將會顯示**方案總管**：

    [![空的 Visual Basic 專案](images/new-library-sml.png)](images/new-library.png#lightbox)

專案現在已準備好要加入 Visual Basic 程式碼。 其他專案可以參考 .NET Standard 專案（應用程式專案或程式庫專案）。

## <a name="summary"></a>總結

本文已示範如何使用 Visual Studio 在 Xamarin 應用程式中使用 Visual Basic 程式碼。 雖然 Xamarin 不支援直接 Visual Basic，但是將 Visual Basic 編譯成 .NET Standard 程式庫，可以讓以 Visual Basic 撰寫的程式碼包含在 Android 和 iOS 應用程式中。

下列頁面說明如何在原生或 Xamarin 中使用 Visual Basic.NET .NET Standard 程式庫。 Forms 應用程式：

- [建立原生的 Xamarin. iOS 和 Xamarin Android 應用程式（使用 VB）](native-apps.md)
- [使用 VB 建立 Xamarin. Forms 應用程式](xamarin-forms.md)

## <a name="related-links"></a>相關連結

- [TaskyVB （範例）](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-taskyvb/)
- [XamarinFormsVB （範例）](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [.NET Standard 和 Xamarin](~/cross-platform/app-fundamentals/net-standard.md)
- [.NET Standard](/dotnet/standard/net-standard/)
