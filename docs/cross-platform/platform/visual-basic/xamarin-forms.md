---
title: 使用 Visual Basic.NET 的 Xamarin.Forms
description: Xamarin.Forms PCL 專案範本可以修改為使用主要組件，有效地讓您建置跨平台行動應用程式使用 VB.NET 的 Visual Basic 中。
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f397cf595a9ae151c5f105341733b2c57023fe99
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109714"
---
# <a name="xamarinforms-using-visual-basicnet"></a>使用 Visual Basic.NET 的 Xamarin.Forms

Xamarin 不直接支援 Visual Basic-依此頁面，即可建立 C# Xamarin.Forms PCL 的解決方案，並取代一般的程式碼 PCL 專案使用 Visual Basic 中的指示。

[![](xamarin-forms-images/hero-sml.png "建立 Xamarin.Forms PCL 的解決方案，並取代一般的程式碼 PCL 專案使用 Visual Basic")](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> 使用 Visual Basic 的程式，您必須在 Windows 上使用 Visual Studio。

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>使用 Visual Basic 逐步解說的 Xamarin.Forms

請遵循下列步驟來建立簡單的 Xamarin.Forms 專案會使用 Visual Basic:

1. 建立新*Xamarin.Forms C#* 使用可攜式類別庫 (PCL) 解決方案。
移至**檔案 > 新增專案**然後在**新增專案**視窗瀏覽至**已安裝 > 範本 > Visual C# > 跨平台**然後選擇  **跨平台應用程式 （Xamarin.Forms 或原生） > Xamarin.Forms**。

2. 以滑鼠右鍵按一下方案並**新增 > 新增專案**。

3. 選擇**Visual Basic > 類別庫 （可攜式）** 專案類型：

   [![](xamarin-forms-images/add-vb-2-sml.png "加入新的可攜式類別庫專案")](xamarin-forms-images/add-vb-2.png#lightbox)

4. 選取的平台，若要設定正確的 PCL 設定檔所示 （請務必包含 Xamarin.iOS 和 Xamarin.Android）：

   ![](xamarin-forms-images/add-vb-3-sml.png "選擇支援的平台")

5. Visual Basic 專案上按一下滑鼠右鍵，然後選擇 **屬性**，然後變更**預設命名空間**以符合現有的 C# 專案：

   ![](xamarin-forms-images/add-vb-4s-sml.png "請確定 Visual Basic 的根命名空間符合 Xamarin.Forms 應用程式")

6. 以滑鼠右鍵按一下新的 Visual Basic 專案，然後選擇 **管理 Nuget 套件**，然後安裝**Xamarin.Forms**並關閉 套件管理員 視窗。

   [![](xamarin-forms-images/add-vb-4-sml.png "表單並關閉 [套件管理員] 視窗")](xamarin-forms-images/add-vb-4.png#lightbox)

7. 重新命名預設**Class1**檔案*並*類別`App`:

   [![](xamarin-forms-images/add-vb-5-sml.png "重新命名應用程式的預設 Class1 檔案與類別")](xamarin-forms-images/add-vb-5.png#lightbox)

8. 貼上下列程式碼插入**App.vb**檔案，這會成為您的 Xamarin.Forms 應用程式的起點。 請記得納入`Imports Xamarin.Forms`並新增`Inherits Application`至類別：

    ```vb 
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
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

9. 現在我們需要使其指向新的 Visual Basic 專案中的 iOS 和 Android 專案。
以滑鼠右鍵按一下**參考**節點中的 iOS 和 Android 專案，以開啟**參考管理員**。 取消勾號C#可攜式程式庫和刻度 VB 可攜式程式庫 （不要忘了，對 iOS 和 Android 專案執行此動作）。

   [![](xamarin-forms-images/add-vb-8-sml.png "移除舊的專案參考、 新增 Visual Basic 參考")](xamarin-forms-images/add-vb-8.png#lightbox)

10. 刪除 C# 可攜式專案。 加入新 **.vb**檔案來建置出您的 Xamarin.Forms 應用程式。 對新範本`ContentPage`Visual Basic 中的 s 如下所示：

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
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

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>在 Xamarin.Forms 中的 Visual Basic 的限制

上所述[可攜式 Visual Basic.NET 頁面](~/cross-platform/platform/visual-basic/index.md)，Xamarin 不支援 Visual Basic 語言。 這表示有一些限制，您可以使用 Visual Basic:

 - 無法以 Visual Basic 中撰寫自訂轉譯器，必須將它們寫入在 C# 中的原生平台專案中。

 - 無法以 Visual Basic 中撰寫相依性服務實作，必須將它們寫入在 C# 中的原生平台專案中。

 - XAML 頁面不能包含在 Visual Basic 專案-程式碼後置產生器只能建立 C#。 可納入個別的參考，C# 可攜式類別庫中的 XAML，並填入的 XAML 檔案，透過 Visual Basic 模型使用資料繫結 (此範例包含在[範例](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages))。

 - Xamarin 不支援的 Visual Basic.NET 的語言。

## <a name="related-links"></a>相關連結

- [XamarinFormsVB （範例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [使用.NET Framework 的跨平台開發](https://docs.microsoft.com/dotnet/standard/cross-platform/)
