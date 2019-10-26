---
title: 使用 Visual Basic.NET 的 Xamarin
description: 您可以修改 Xamarin 專案範本，將 Visual Basic 用於主要元件，讓您可以使用 VB.NET 來建立跨平臺行動應用程式。
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: conceptdev
ms.author: crdun
ms.date: 04/24/2019
ms.openlocfilehash: ed7e1d65ed361a94ce72a724d797309b40ef8b6c
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959151"
---
# <a name="xamarinforms-using-visual-basicnet"></a>使用 Visual Basic.NET 的 Xamarin

Xamarin 不支援直接 Visual Basic-遵循此頁面上的指示來建立C# Xamarin 表單方案，然後以 Visual Basic 取代 .NET Standard C#專案。

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)

[![建立 Xamarin 表單方案，然後將 .NET Standard 專案取代為 Visual Basic](xamarin-forms-images/hero-sml.png)](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> 您必須使用 Windows 上的 Visual Studio，透過 Visual Basic 進行程式設計。

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>包含 Visual Basic 逐步解說的 Xamarin

請遵循下列步驟來建立使用 Visual Basic 的簡單 Xamarin. Forms 專案：

1. 從 Visual Studio 2019 選擇 [**建立新專案**]。

2. 在 [**建立新專案**] 視窗中，輸入 [ **Xamarin** ] 以篩選清單並選取 [行動**應用程式（Xamarin）** ]，然後按 **[下一步]** 。

    [Xamarin. Forms 應用程式的![篩選](xamarin-forms-images/02-sml.png)](xamarin-forms-images/02.png#lightbox)

3. 在下一個畫面上，輸入專案的名稱，然後按 [**建立**]。

4. 選擇 [**空白**] 範本，然後按 **[確定]** ：

    [![空白的 Xamarin Forms 範本](xamarin-forms-images/04-sml.png)](xamarin-forms-images/04.png#lightbox)

    這會使用C#在 Visual Studio 中建立 Xamarin 表單方案。 接下來的步驟會修改解決方案，以使用 Visual Basic。

5. 以滑鼠右鍵按一下方案，然後選擇 [**加入] > [新增專案**]。

6. 輸入**Visual Basic 程式庫** 來篩選項目選項，然後選擇 **類別庫 （.NET Standard）** 選項與 Visual Basic 圖示：

    [Visual Basic 程式庫的![篩選](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

7. 在下一個畫面上，輸入專案的名稱，然後按 [**建立**]。

8. 以滑鼠右鍵按一下 [Visual Basic] 專案，然後選擇 [**屬性**]，再變更**預設命名空間**以符合現有C#的專案：

    [![確保 Visual Basic 根命名空間符合 Xamarin. Forms 應用程式](xamarin-forms-images/07a-sml.png)](xamarin-forms-images/07a.png#lightbox)

9. 以滑鼠右鍵按一下新的 Visual Basic 專案，然後依序選擇 [**管理 NuGet 封裝**]、[安裝**Xamarin** ] 及 [套件管理員] 視窗。

    [![表單，然後關閉 [套件管理員] 視窗](xamarin-forms-images/07b-sml.png)](xamarin-forms-images/07b.png#lightbox)

10. 將預設的**Class1 .vb**檔案重新命名為 **.vb**：

    [![將預設的 Class1 檔案和類別重新命名為應用程式](xamarin-forms-images/08.png)](xamarin-forms-images/08.png#lightbox)

11. 將下列程式碼貼到**app.config**檔案中，這將成為您的 Xamarin. Forms 應用程式的起點：

    ```vb
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Welcome to Xamarin.Forms with Visual Basic.NET"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Dim page = New ContentPage
            page.Content = stack
            MainPage = page

        End Sub

    End Class
    ```

12. 更新 Android 和 iOS 專案，使其參考新的 Visual Basic 專案（而不是C#範本所建立的專案）。
以滑鼠右鍵按一下 Android 和 iOS 專案中的 [**參考**] 節點，以開啟 [**參考管理員**]。 取消勾選C#媒體櫃，並將 Visual Basic 程式庫刻度（別忘了，針對 Android 和 iOS 專案執行此動作）。

    [![移除舊的專案參考，請加入 Visual Basic 參考](xamarin-forms-images/10-sml.png)](xamarin-forms-images/10.png#lightbox)

13. 刪除C#專案。 加入新**的 .vb**檔案，以建立您的 Xamarin. Forms 應用程式。 Visual Basic 中新 `ContentPage`的範本如下所示：

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HorizontalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Visual Basic ContentPage"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Content = stack
        End Sub
    End Class
    ```

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Xamarin 中的 Visual Basic 限制

如[便攜視覺 Basic.NET 頁面](~/cross-platform/platform/visual-basic/index.md)上所述，Xamarin 不支援 Visual Basic 語言。 這表示您可以使用 Visual Basic 的位置有一些限制：

- XAML 頁面不能包含在 Visual Basic 專案中-程式碼後置產生器只能建立C#。 您可以將 XAML 包含在另一個參考的C#可移植類別庫中，並使用資料系結透過 Visual Basic 模型來填入 xaml 檔案（範例包含在[範例](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages)中）。

- 自訂轉譯器無法以 Visual Basic 撰寫，必須在原生C#平臺專案中撰寫。

- 相依性服務的執行無法以 Visual Basic 撰寫，必須在原生C#平臺專案中撰寫。

## <a name="related-links"></a>相關連結

- [XamarinFormsVB （範例）](https://docs.microsoft.com/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [使用 .NET Framework 進行跨平臺開發](https://docs.microsoft.com/dotnet/standard/cross-platform/)
