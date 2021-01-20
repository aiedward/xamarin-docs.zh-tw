---
title: 在 iOS 延伸模組中重複使用 Xamarin. Forms 頁面
description: 本檔將說明 iOS 延伸模組，以及如何使用中的 Xamarin. Forms 頁面。 其中包含有關如何建立擴充功能、整合 Xamarin，以及在 iOS 延伸模組專案中以原生方式轉譯簡單 ContentPage 的逐步解說。
ms.prod: xamarin
ms.assetid: 50076FFD-3EF6-41CC-BC7E-210DE3958F5B
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 05/13/2020
ms.openlocfilehash: ed7b7bae452db0067b330126315d5b029a08ccab
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98608932"
---
# <a name="reuse-xamarinforms-pages-in-an-ios-extension"></a>在 iOS 延伸模組中重複使用 Xamarin. Forms 頁面

iOS 延伸模組可讓您自訂現有的系統行為，方法是將額外的功能新增至 [iOS 和 MacOS 擴充點預先定義](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW2)，例如自訂內容動作、密碼自動填入、撥入電話篩選、通知內容修飾詞等等。 Xamarin 支援延伸模組， [本指南](./extensions.md) 將逐步引導您使用 Xamarin 工具建立 iOS 延伸模組。

延伸模組會作為容器應用程式的一部分散發，並從主應用程式中的特定擴充點啟用。 容器應用程式通常是簡單的 iOS 應用程式，可為使用者提供延伸模組的相關資訊、如何啟用，以及如何加以使用。 有三種主要的方法可在擴充功能與容器應用程式之間共用程式碼：

1. 常見的 iOS 專案。

    您可以將容器和延伸模組之間的所有共用程式碼放入共用的 iOS 程式庫，並從這兩個專案參考程式庫。 共用程式庫通常包含原生 UIViewControllers，而且必須是 Xamarin. iOS 程式庫。

1. 檔案連結。

    在某些情況下，容器應用程式會提供大部分的功能，但擴充功能需要呈現單一 `UIViewController` 。 有幾個檔案需要共用，通常會從位於容器應用程式中的檔案，將檔案連結新增至擴充功能應用程式。

1. 一般 Xamarin. Forms 專案。

    如果應用程式頁面已經與其他平臺（例如 Android）共用（使用 Xamarin 架構），常見的方法是在擴充功能專案中，以原生方式重新實施必要的頁面，因為 iOS 延伸模組適用于原生 UIViewControllers，而不是 Xamarin. Forms 頁面。 您必須執行其他步驟，才能在 iOS 延伸模組中使用 Xamarin，如下所述。

## <a name="xamarinforms-in-an-ios-extension-project"></a>IOS 擴充功能專案中的 Xamarin

在原生專案中使用 Xamarin 的能力是透過 [原生形式](~/xamarin-forms/platform/native-forms.md)提供。 它可讓 `ContentPage` 衍生的頁面直接新增至原生的 Xamarin iOS 專案。 `CreateViewController`擴充方法會將 Xamarin 頁面的實例轉換成原生，而這種方法可以當做 `UIViewController` 一般控制器使用或修改。 因為 iOS 延伸模組是一種特殊類型的原生 iOS 專案，所以您也可以在這裡使用 **原生表單** 。

> [!IMPORTANT]
> IOS 延伸模組有許多 [已知的限制](./extensions.md#limitations) 。 雖然您可以在 iOS 延伸模組中使用 Xamarin，但您應該非常小心地監視記憶體使用量和啟動時間。 否則，iOS 將無法正常處理此延伸模組。

## <a name="walkthrough"></a>逐步介紹

在本逐步解說中，您將建立 Xamarin. Forms 應用程式、一個 Xamarin 副檔名，並在擴充功能專案中重複使用共用程式碼：

1. 開啟 Visual Studio for Mac，使用 **空白的 Forms 應用程式** 範本建立新的 Xamarin 專案，並將其命名為 **FormsShareExtension**：

    ![建立專案](extensions-xf-images/1.walkthrough-createproject.png)

1. 在 **FormsShareExtension/MainPage** 中，將內容取代為下列配置：

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <ContentPage
        x:Class="FormsShareExtension.MainPage"
        xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:d="http://xamarin.com/schemas/2014/forms/design"
        xmlns:local="clr-namespace:FormsShareExtension"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        x:DataType="local:MainPageViewModel"
        BackgroundColor="Orange"
        mc:Ignorable="d">
        <ContentPage.BindingContext>
            <local:MainPageViewModel Message="Hello from Xamarin.Forms!" />
        </ContentPage.BindingContext>
        <StackLayout HorizontalOptions="Center" VerticalOptions="Center">
            <Label
                Margin="20"
                Text="{Binding Message}"
                VerticalOptions="CenterAndExpand" />
            <Button Command="{Binding DoCommand}" Text="Do the job!" />
        </StackLayout>
    </ContentPage>
    ```

1. 將名為 **MainPageViewMode** 的新類別加入 **FormsShareExtension** 專案中，並以下列程式碼取代類別的內容：

    ```csharp
    using System;
    using System.ComponentModel;
    using System.Windows.Input;
    using Xamarin.Forms;

    namespace FormsShareExtension
    {
        public class MainPageViewModel : INotifyPropertyChanged
        {
            public event PropertyChangedEventHandler PropertyChanged;

            private string _message;
            public string Message
            {
                get { return _message; }
                set
                {
                    if (_message != value)
                    {
                        _message = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Message)));
                    }
                }
            }

            private ICommand _doCommand;
            public ICommand DoCommand
            {
                get { return _doCommand; }
                set
                {
                    if(_doCommand != value)
                    {
                        _doCommand = value;
                        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(DoCommand)));
                    }
                }
            }

            public MainPageViewModel()
            {
                DoCommand = new Command(OnDoCommandExecuted);
            }

            private void OnDoCommandExecuted(object state)
            {
                Message = $"Job {Environment.TickCount} has been completed!";
            }
        }
    }
    ```

    程式碼會在所有平臺上共用，而且也會由 iOS 延伸模組使用。

1. 在 solution pad 中，以滑鼠右鍵按一下方案，選取 [ **新增 > 新專案] > iOS > 延伸模組 > 動作延伸** 模組，將它命名為 **MyAction** ，然後按 [ **建立**]：

    ![螢幕擷取畫面顯示 [選擇已選取動作延伸模組的範本]。](extensions-xf-images/2.walkthrough-createextension.png)

1. 若要在 iOS 擴充功能和共用程式碼中使用 Xamarin，您需要新增必要的參考：

    - 以滑鼠右鍵按一下 iOS 延伸模組，選取 [ **參考] > > FormsShareExtension 新增參考 > 專案** ，然後按 **[確定]**。

    - 以滑鼠右鍵按一下 iOS 延伸模組，選取 [ **套件] > 管理 NuGet 套件 ...] > Xamarin**  ，然後按 [ **新增套件**]。

1. 展開延伸模組專案，並修改進入點以初始化 Xamarin 和 create pages。 根據 iOS 需求，擴充功能必須將 **plist** 中的進入點定義為 `NSExtensionMainStoryboard` 或 `NSExtensionPrincipalClass` 。 一旦啟動進入點，在此案例中為 `ActionViewController.ViewDidLoad` 方法，您可以建立 Xamarin 頁面的實例，並向使用者顯示。 因此，請開啟進入點，並 `ViewDidLoad` 將方法取代為下列實作為：

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        // Initialize Xamarin.Forms framework
        global::Xamarin.Forms.Forms.Init();
        // Create an instance of XF page with associated View Model
        var xfPage = new MainPage();
        var viewModel = (MainPageViewModel)xfPage.BindingContext;
        viewModel.Message = "Welcome to XF Page created from an iOS Extension";
        // Override the behavior to complete the execution of the Extension when a user press the button
        viewModel.DoCommand = new Command(() => DoneClicked(this));
        // Convert XF page to a native UIViewController which can be consumed by the iOS Extension
        var newController = xfPage.CreateViewController();
        // Present new view controller as a regular view controller
        this.PresentModalViewController(newController, false);
    }
    ```

    `MainPage`會使用標準的函式來具現化，而且您必須先使用擴充方法將它轉換成原生，才能在擴充功能中使用它 `UIViewController` `CreateViewController` 。 
    
    建立並執行應用程式：

    ![螢幕擷取畫面顯示行動裝置上的 Xamarin dot 表單訊息的 Hello。](extensions-xf-images/3.walkthrough-runapp.png)

    若要啟用擴充功能，請流覽至 Safari 瀏覽器，輸入任何網址（例如 [microsoft.com](https://microsoft.com)），按下 [流覽]，然後按頁面底部的 [ **共用** ] 圖示，以查看可用的動作延伸模組。 從可用的擴充功能清單中，透過下列方式來選取 **MyAction** 延伸模組：

    ![螢幕擷取畫面顯示 Microsoft 小組 [深入瞭解] 頁面，其中顯示在行動裝置上醒目提示的共用圖示。](extensions-xf-images/4.walkthrough-run1.png) ![螢幕擷取畫面顯示已在行動裝置上反白顯示 MyAction 的官方首頁。](extensions-xf-images/5.walkthrough-run2.png) ![螢幕擷取畫面顯示從行動裝置上的 i O S 延伸模組訊息建立的歡迎使用 X F 頁面。](extensions-xf-images/6.walkthrough-run3.png)

    擴充功能隨即啟用，並向使用者顯示 [Xamarin] 頁面。 所有的系結和命令都如同在容器應用程式中運作。

1. 您可以看到原始的進入點查看控制器，因為它是由 iOS 建立和啟用。 若要修正此問題，請在呼叫之前新增下列程式碼，將強制回應呈現樣式變更為 `UIModalPresentationStyle.FullScreen` 新的控制器 `PresentModalViewController` ：

    ```csharp
    newController.ModalPresentationStyle = UIModalPresentationStyle.FullScreen;
    ```

    在 iOS 模擬器或裝置中建立並執行：

    ![IOS 延伸模組中的 Xamarin](extensions-xf-images/7.walkthrough-result.png)

    > [!IMPORTANT]
    > 針對裝置組建，請務必使用適當的組建設定和 **發行** 設定，如下 [所述](~/iOS/platform/extensions.md#debug-and-release-versions-of-extensions)。

## <a name="related-links"></a>相關連結

- [Xamarin.iOS 中的 iOS 延伸模組](~/iOS/platform/extensions.md)
- [Xamarin 原生專案中的 xamarin](~/xamarin-forms/platform/native-forms.md)
- [將 iOS 應用程式擴充功能的效率和效能優化](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionCreation.html#//apple_ref/doc/uid/TP40014214-CH5-SW7)
- [範例原始程式碼](https://github.com/xamcat/xamarin-forms-ios-extension)