---
title: 在 iOS 延伸模組中重複使用 Xamarin. Forms 頁面
description: 本檔說明 iOS 延伸模組，以及如何在其中使用 Xamarin. 表單頁面。 其中包含有關如何建立擴充功能、整合 Xamarin，以及在 iOS 擴充功能專案中以原生方式呈現簡單 ContentPage 的逐步解說。
ms.prod: xamarin
ms.assetid: 50076FFD-3EF6-41CC-BC7E-210DE3958F5B
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 05/13/2020
ms.openlocfilehash: 4006bb3ef82264b5a7a6d764719bee81a8ce78a1
ms.sourcegitcommit: 5bc37b384706bfbf5bf8e5b1288a34ddd0f3303b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/15/2020
ms.locfileid: "83418011"
---
# <a name="reuse-xamarinforms-pages-in-an-ios-extension"></a>在 iOS 延伸模組中重複使用 Xamarin. Forms 頁面

iOS 延伸模組可讓您自訂現有的系統行為，方法是將額外的功能新增至[iOS 和 MacOS 擴充點](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW2)，例如自訂內容動作、密碼自動填入、撥入電話篩選器、通知內容修飾詞等。 Xamarin 支援延伸模組，[本指南](https://docs.microsoft.com/xamarin/ios/platform/extensions)將逐步引導您使用 Xamarin 工具建立 iOS 延伸模組。

延伸模組會當做容器應用程式的一部分散發，並從主機應用程式中的特定擴充點啟動。 Container app 通常是簡單的 iOS 應用程式，可為使用者提供延伸模組的相關資訊、如何啟用及使用。 在擴充功能和容器應用程式之間共用程式碼的主要方法有三種：

1. 一般 iOS 專案。

    您可以將容器和擴充功能之間的所有共用程式碼放入共用的 iOS 程式庫，並從這兩個專案參考程式庫。 共用程式庫通常會包含原生 UIViewControllers，而且它必須是 Xamarin. iOS 程式庫。

1. 檔案連結。

    在某些情況下，容器應用程式會提供大部分的功能，而延伸模組則需要呈現單一 `UIViewController` 。 只要共用少量檔案，就可以從容器應用程式中的檔案新增檔案連結至延伸模組應用程式。

1. 通用的 Xamarin. 表單專案。

    如果應用程式頁面已與另一個平臺共用，例如 Android，使用 Xamarin. form 架構，則常見的方法是在擴充功能專案中，以原生方式重新使用必要的頁面，因為 iOS 延伸模組可搭配原生 UIViewControllers 而不是 Xamarin. Forms 頁面。 您必須執行其他步驟，才能在 iOS 延伸模組中使用 Xamarin，如下所述。

## <a name="xamarinforms-in-an-ios-extension-project"></a>IOS 擴充功能專案中的 Xamarin

在原生專案中使用 Xamarin 的能力是透過[原生格式](~/xamarin-forms/platform/native-forms.md)提供。 它允許 `ContentPage` 將衍生的頁面直接新增至原生的 Xamarin iOS 專案。 `CreateViewController`擴充方法會將 Xamarin 表單頁面的實例轉換成原生 `UIViewController` ，這可以做為一般控制器使用或修改。 由於 iOS 延伸模組是一種特殊類型的原生 iOS 專案，因此您也可以在這裡使用**原生表單**。

> [!IMPORTANT]
> IOS 擴充功能有許多[已知的限制](https://docs.microsoft.com/xamarin/ios/platform/extensions#limitations)。 雖然您可以在 iOS 延伸模組中使用 Xamarin，但您應該非常小心地監視記憶體使用量和啟動時間。 否則，iOS 可能會終止延伸模組，而不會有任何方式可正常處理。

## <a name="walkthrough"></a>逐步介紹

在本逐步解說中，您將建立一個 xamarin 的 node.js 應用程式，並在擴充功能專案中重複使用共用程式碼：

1. 開啟 Visual Studio for Mac，使用**空白的 Forms 應用程式**範本建立新的 Xamarin 專案，並將其命名為**FormsShareExtension**：

    ![建立專案](extensions-xf-images/1.walkthrough-createproject.png)

1. 在**FormsShareExtension/MainPage**中，將內容取代為下列版面配置：

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

1. 將名為**MainPageViewMode**的新類別新增至**FormsShareExtension**專案，並將類別的內容取代為下列程式碼：

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

    程式碼會在所有平臺間共用，而且也會由 iOS 延伸模組使用。

1. 在 [solution pad] 中，以滑鼠右鍵按一下方案，選取 [**新增] > 新專案 > iOS > 擴充 > 動作延伸**模組，並將其命名為**MyAction** ，然後按 [**建立**]：

    ![建立擴充功能](extensions-xf-images/2.walkthrough-createextension.png)

1. 若要在 iOS 擴充功能和共用程式碼中使用 Xamarin，您需要新增必要的參考：

    - 以滑鼠右鍵按一下 iOS 延伸模組，選取 [**參考] > 新增參考 > 專案] > FormsShareExtension** ，然後按 **[確定]**。

    - 以滑鼠右鍵按一下 iOS 延伸模組，選取 [**套件] > [管理 NuGet 套件 ...] > [Xamarin** ]，然後按 [**新增套件**]。

1. 展開延伸模組專案，並修改進入點以初始化 Xamarin，並建立頁面。 根據 iOS 需求，延伸模組必須將**Info. plist**中的進入點定義 `NSExtensionMainStoryboard` 為 `NSExtensionPrincipalClass` 或。 啟動進入點之後，在此案例中是 `ActionViewController.ViewDidLoad` 方法，您可以建立 [Xamarin] 頁面的實例，並向使用者顯示。 因此，請開啟進入點，並 `ViewDidLoad` 將方法取代為下列執行：

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

    是使用標準的函式來具 `MainPage` 現化，而且在擴充功能中使用它之前，請 `UIViewController` 使用擴充方法將它轉換成原生 `CreateViewController` 。 
    
    建立並執行應用程式：

    ![建立擴充功能](extensions-xf-images/3.walkthrough-runapp.png)

    若要啟用擴充功能，請流覽至 Safari 瀏覽器，輸入任何網址（例如[microsoft.com](https://microsoft.com)），按下 [流覽]，然後按頁面底部的 [**共用**] 圖示，以查看可用的動作延伸模組。 從可用延伸模組的清單中，依下列方式選取**MyAction**擴充功能：

    ![建立擴充功能](extensions-xf-images/4.walkthrough-run1.png) ![建立擴充功能](extensions-xf-images/5.walkthrough-run2.png) ![建立擴充功能](extensions-xf-images/6.walkthrough-run3.png)

    延伸模組隨即啟動，並向使用者顯示 [Xamarin] 頁面。 所有的系結和命令都可在容器應用程式中正常執行。

1. 原始的進入點視圖控制器為可見狀態，因為它是由 iOS 所建立和啟動。 若要修正此問題，請在呼叫之前新增下列程式碼，將強制回應呈現樣式變更為 `UIModalPresentationStyle.FullScreen` 新的控制器 `PresentModalViewController` ：

    ```csharp
    newController.ModalPresentationStyle = UIModalPresentationStyle.FullScreen;
    ```

    在 iOS 模擬器或裝置中建立並執行：

    ![IOS 中的 Xamarin 擴充功能](extensions-xf-images/7.walkthrough-result.png)

    > [!IMPORTANT]
    > 針對裝置組建，請務必使用正確的組建設定和**發行**設定，如[這裡所述](~/iOS/platform/extensions.md#debug-and-release-versions-of-extensions)。

## <a name="related-links"></a>相關連結

- [在 Xamarin 中的 iOS 擴充功能](~/iOS/platform/extensions.md)
- [Xamarin 原生專案中的 xamarin](~/xamarin-forms/platform/native-forms.md)
- [將 iOS 應用程式延伸模組的效率和效能優化](https://developer.apple.com/library/archive/documentation/General/Conceptual/ExtensibilityPG/ExtensionCreation.html#//apple_ref/doc/uid/TP40014214-CH5-SW7)
- [範例原始程式碼](https://github.com/xamcat/xamarin-forms-ios-extension)
