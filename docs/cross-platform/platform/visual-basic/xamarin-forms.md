---
title: Xamarin. 使用 Visual Basic.NET 的表單
description: 您可以修改 Xamarin. Forms 專案範本，以針對主要元件使用 Visual Basic，有效率地讓您使用 VB.NET 建立跨平臺行動應用程式。
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: davidortinau
ms.author: daortin
ms.date: 04/24/2019
ms.openlocfilehash: 8cb26c3d8cec03cfaa12f7acb974c9944119bdfc
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374104"
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin. 使用 Visual Basic.NET 的表單

Xamarin 不支援直接 Visual Basic-請遵循此頁面上的指示來建立 c # Xamarin Forms 方案，然後以 Visual Basic 取代 .NET Standard c # 專案。

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)

[![建立 Xamarin 方案，然後將 .NET Standard 專案取代為 Visual Basic](xamarin-forms-images/hero-sml.png)](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> 您必須使用 Windows 上的 Visual Studio，以使用 Visual Basic 進行程式設計。

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>使用 Visual Basic 逐步解說的 Xamarin

遵循下列步驟來建立使用 Visual Basic 的簡易 Xamarin. Forms 專案：

1. 從 Visual Studio 2019 選擇 [ **建立新專案** ]。

2. 在 [ **建立新專案** ] 視窗中，輸入 [ **xamarin** ] 以篩選清單，然後選取 [行動 **應用程式] ([xamarin])** ，然後按 **[下一步** ]。

    [![Xamarin. Forms 應用程式的篩選](xamarin-forms-images/02-sml.png)](xamarin-forms-images/02.png#lightbox)

3. 在下一個畫面中，輸入專案的名稱，然後按 [ **建立** ]。

4. 選擇 **空白** 範本，然後按 **[確定]** ：

    [![空白的 Xamarin. Forms 範本](xamarin-forms-images/04-sml.png)](xamarin-forms-images/04.png#lightbox)

    這會使用 c #，在 Visual Studio 中建立 Xamarin 方案。 接下來的步驟會修改要使用 Visual Basic 的解決方案。

5. 以滑鼠右鍵按一下方案，然後選擇 [ **加入 > 新專案 ...** ]

6. 輸入 **Visual Basic 程式庫** 以篩選項目選項，然後選擇 [ **類別庫] ( .NET Standard)** 選項與 Visual Basic 圖示：

    [![Visual Basic 程式庫篩選](xamarin-forms-images/06-sml.png)](xamarin-forms-images/06.png#lightbox)

7. 在下一個畫面中，輸入專案的名稱，然後按 [ **建立** ]。

8. 以滑鼠右鍵按一下 Visual Basic 專案並選擇 [ **屬性** ]，然後變更 **預設命名空間** 以符合現有的 c # 專案：

    [![確定 Visual Basic 根命名空間符合 Xamarin. Forms 應用程式](xamarin-forms-images/07a-sml.png)](xamarin-forms-images/07a.png#lightbox)

9. 以滑鼠右鍵按一下新的 Visual Basic 專案，然後選擇 [ **管理 NuGet 套件** ]，再安裝 **Xamarin** ，然後關閉 [套件管理員] 視窗。

    [![表單並關閉套件管理員視窗](xamarin-forms-images/07b-sml.png)](xamarin-forms-images/07b.png#lightbox)

10. 將預設的 **Class1** 檔案重新命名為 **app.config** ：

    [![將預設的 Class1 檔案和類別重新命名為應用程式](xamarin-forms-images/08.png)](xamarin-forms-images/08.png#lightbox)

11. 將下列程式碼貼入 **app.config** 檔案中，這將成為您的 Xamarin 應用程式的起始點：

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

12. 更新 Android 和 iOS 專案，使其參考新的 Visual Basic 專案 (而不是範本) 所建立的 c # 專案。
以滑鼠右鍵按一下 Android 和 iOS 專案中的 [ **參考** ] 節點，以開啟 [ **參考管理員** ]。 取消勾選 c # 程式庫，並 Visual Basic 程式庫 (別忘了，請) 的 Android 和 iOS 專案進行這項作業。

    [![移除舊的專案參考，加入 Visual Basic 參考](xamarin-forms-images/10-sml.png)](xamarin-forms-images/10.png#lightbox)

13. 刪除 c # 專案。 新增 **.vb** 檔案，以建立您的 Xamarin. Forms 應用程式。 Visual Basic 中新的範本如下 `ContentPage` 所示：

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

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Xamarin 中 Visual Basic 的限制

如同 [可攜性視覺效果 Basic.NET 頁面](~/cross-platform/platform/visual-basic/index.md)中所述，Xamarin 不支援 Visual Basic 的語言。 這表示您可以使用 Visual Basic 的一些限制：

- XAML 頁面無法包含在 Visual Basic 專案中，程式碼後端產生器只能建立 c #。 您可以在個別、參考的 c # 可移植類別庫中包含 XAML，並使用資料系結透過 Visual Basic 模型來填入 XAML 檔案 ([範例) 包含此範例。](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)

- 無法在 Visual Basic 中撰寫自訂轉譯器，它們必須以 c # 撰寫于原生平臺專案中。

- 相依性服務的執行無法在 Visual Basic 中寫入，它們必須以 c # 撰寫于原生平臺專案中。

## <a name="related-links"></a>相關連結

- [XamarinFormsVB (範例) ](/samples/xamarin/mobile-samples/visualbasic-xamarinformsvb/)
- [使用 .NET Framework 進行跨平台開發](/dotnet/standard/cross-platform/)