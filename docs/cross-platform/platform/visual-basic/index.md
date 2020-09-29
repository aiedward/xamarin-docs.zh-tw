---
title: Visual Basic 和 .NET Standard
description: 本指南將說明如何使用 Visual Basic 來撰寫可用於以 Xamarin 和 Xamarin 為目標之解決方案中的 .NET Standard 專案。
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 8ccf96e266f9d5eae69a178cfcad3d1e48fb6962
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457522"
---
# <a name="visual-basic-and-net-standard"></a>Visual Basic 和 .NET Standard

Xamarin Android 和 iOS 專案原本不支援 Visual Basic;不過，開發人員可以使用 [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md) 程式庫，將現有的 Visual Basic 程式碼遷移至 Android 和 iOS，或在 Visual Basic 中撰寫其應用程式邏輯的重要部分。 Xamarin. Forms 應用程式可以完全在 Visual Basic 中建立， (不包括自訂轉譯器、相依性服務和 XAML 程式碼後端) 。

## <a name="requirements"></a>需求

若要建立和編譯 Visual Basic .NET Standard 程式庫，您必須在 Windows (Visual Studio 2017 或更新版本的) 上使用 Visual Studio。

> [!NOTE]
> Visual Basic 程式庫只能使用 Visual Studio 來建立和編譯。 Xamarin. Android 和 Xamarin 不支援 Visual Basic 語言。
>
> 如果您只在 Visual Studio，您可以參考來自 Xamarin. Android 和 Xamarin. iOS 專案的 Visual Basic 專案。
>
> 如果您的 Android 和 iOS 專案也必須載入 Visual Studio for Mac 您應該從 Visual Basic 元件參考輸出元件。

## <a name="creating-a-visual-basicnet-net-standard-library"></a>建立 Visual Basic.NET .NET Standard 程式庫

本節將逐步解說如何使用 Visual Studio 2019 來建立 Visual Basic .NET Standard 程式庫。
然後，您可以在其他專案中參考該程式庫，包括 Xamarin、xamarin 和 Xamarin 應用程式。

在 Visual Studio 中新增 Visual Basic .NET Standard 程式庫時，您必須小心選擇正確的專案類型：

1. 從 Visual Studio 2019 選擇 [ **建立新專案**]。

2. 輸入 **Visual Basic 程式庫** 以篩選項目選項，然後選擇 [ **類別庫] ( .NET Standard) ** 選項與 Visual Basic 圖示：

    [![Visual Basic 程式庫篩選](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

3. 在下一個畫面中，輸入專案的名稱，然後按 [ **建立**]。

4. Visual Basic 專案將會顯示如下所示  **方案總管** 如下所示：

    [![空白 Visual Basic 專案](images/new-library-sml.png)](images/new-library.png#lightbox)

專案現在已準備好要加入 Visual Basic 程式碼。 .NET Standard 專案可由其他專案參考) 的 (應用程式專案或程式庫專案。

## <a name="summary"></a>摘要

本文示範如何使用 Visual Studio 在 Xamarin 應用程式中使用 Visual Basic 程式碼。 雖然 Xamarin 並不支援直接 Visual Basic，但是將 Visual Basic 編譯成 .NET Standard 程式庫，可讓以 Visual Basic 撰寫的程式碼包含在 Android 和 iOS 應用程式中。

下列頁面說明如何在原生或 Xamarin 中使用 Visual Basic.NET .NET Standard 程式庫。 Forms 應用程式：

- [建立使用 VB 的原生 Xamarin iOS 和 Xamarin. Android 應用程式](native-apps.md)
- [使用 VB 建立 Xamarin. Forms 應用程式](xamarin-forms.md)

## <a name="related-links"></a>相關連結

- [TaskyVB (範例) ](/samples/xamarin/mobile-samples/visualbasic-taskyvb/)
- [XamarinFormsVB (範例) ](/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [.NET Standard 和 Xamarin](~/cross-platform/app-fundamentals/net-standard.md)
- [.NET Standard](/dotnet/standard/net-standard/)