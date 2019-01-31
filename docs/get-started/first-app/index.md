---
title: 建置您的第一個 Xamarin.Forms 應用程式
description: 示範如何在 Visual Studio 中建置第一個 Xamarin.Forms 應用程式的影片指南。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 72B6AF82-4D98-47E5-AB54-0A35B3253468
ms.technology: xamarin-forms
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 09/24/2018
ms.openlocfilehash: d89488e3f6e42f84fc9519eedaa38c99b90ae068
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292815"
---
# <a name="build-your-first-xamarinforms-app"></a>建置您的第一個 Xamarin.Forms 應用程式

觀看這段影片，並遵循指示建立第一個使用 Xamarin.Forms 的行動應用程式。

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>適用於 Windows 的逐步指示

請遵循下列步驟及上面的影片進行：

1. 選擇 [檔案] > [新增] > [專案...] 或按下 [建立新專案...] 按鈕，然後選取 [Visual C#] > [跨平台] > [行動應用程式 (Xamarin.Forms)]：

    [![行動應用程式 (Xamarin.Forms)](images/win/01-sml.png)](images/win/01.png#lightbox)

2. 確定已選取 [Android] 和 [iOS]，並搭配 [.NET Standard] 程式碼共用：

    [![[Android] 和 [iOS] 搭配 [.NET Standard]](images/win/02-sml.png)](images/win/02.png#lightbox)

3. 等候 NuGet 套件還原完成 (狀態列中將會顯示 [還原完成] 訊息)。

4. 按下偵錯按鈕 (或 [偵錯] > [開始偵錯] 功能表項目) 來啟動 Android Emulator。

5. 編輯 **MainPage.xaml**，並在 `</StackPanel>` 結尾前面新增此 XAML：

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

6. 編輯 **MainPage.xaml.cs**，並將這段程式碼新增至類別結尾：

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. 偵錯 Android 上的應用程式：

    ![Android 應用程式](images/win/07-sml.png)

    > [!TIP]
    > 您可以使用網路相連的 Mac 電腦，從 Visual Studio 建置及偵錯 iOS 應用程式。 如需詳細資訊，請參閱[安裝指示](~/ios/get-started/installation/windows/index.md)。

::: zone-end
::: zone pivot="macos"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-iOS--Android-App-in-Visual-Studio-for-Mac/player]

## <a name="step-by-step-instructions-for-mac"></a>適用於 Mac 的逐步指示

請遵循下列步驟及上面的影片進行：

1. 選擇 [檔案] > [新增方案...] 或按下 [新增專案...] 按鈕，然後選取 [多平台] > [應用程式] > [空白的 Forms App]：

    [![空白的 Forms App](images/01-sml.png)](images/01.png#lightbox)

2. 確定已選取 [Android] 和 [iOS]，並搭配 [.NET Standard] 程式碼共用：

    [![[Android] 和 [iOS] 搭配 [.NET Standard]](images/02-sml.png)](images/02.png#lightbox)

3. 以滑鼠右鍵按一下方案來還原 NuGet 套件：

    ![Android 應用程式](images/03-sml.png)

4. 按下偵錯按鈕 (或 [執行] > [開始偵錯]) 來啟動 Android Emulator。

5. 編輯 **MainPage.xaml**，並在 `</StackPanel>` 結尾前面新增此 XAML：

    ```xaml
    <Button Text="Click Me" Clicked="Handle_Clicked" />
    ```

6. 編輯 **MainPage.xaml.cs**，並將這段程式碼新增至類別結尾：

    ```csharp
    int count = 0;
    void Handle_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. 偵錯 Android 上的應用程式：

    ![Android 應用程式](images/07-sml.png)

8. 按一下滑鼠右鍵將 iOS 設定為 [啟始專案]：

    [![將啟始專案設定為 iOS](images/08-sml.png)](images/08.png#lightbox)

9. 偵錯 iOS 上的應用程式：

    ![iOS 應用程式](images/09-sml.png)

::: zone-end

您可以從[範例庫](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)下載完整的程式碼，或在 [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp) 上進行檢視。

## <a name="next-steps"></a>後續步驟

- [單一頁面的快速入門](~/get-started/quickstarts/single-page.md)&ndash;建置功能較多的應用程式。
- [Xamarin.Forms 範例](~/xamarin-forms/samples/index.yml) &ndash; 下載並執行程式碼範例和範例應用程式。
- [建立行動應用程式電子書](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; 教導 Xamarin.Forms 開發的深入章節，提供 PDF 並包含數以百計的其他範例。
