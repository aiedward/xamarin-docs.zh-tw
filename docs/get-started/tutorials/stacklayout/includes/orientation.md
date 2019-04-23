---
ms.openlocfilehash: a7c2aa15521b89e4930746dc5421ce67fa26b2b9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382529"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 宣告，讓其與其子系水平對齊，而不是垂直對齊：

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    此程式碼會將 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 屬性設定為 [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)。

1. 在 Visual Studio 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![子檢視在 iOS 和 Android 的 StackLayout 中呈水平方向的螢幕擷取畫面](../images/orientation.png "包含水平方向標籤執行個體的 StackLayout")](../images/orientation-large.png#lightbox "包含水平方向標籤執行個體的 StackLayout")

    請注意，[`StackLayout`](xref:Xamarin.Forms.StackLayout) 內的 [`Label`](xref:Xamarin.Forms.Label) 執行個體現在會以水平方向對齊，不是以垂直方向。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 宣告，讓其與其子系水平對齊，而不是垂直對齊：

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    此程式碼會將 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 屬性設定為 [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動] 按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![子檢視在 iOS 和 Android 的 StackLayout 中呈水平方向的螢幕擷取畫面](../images/orientation.png "包含水平方向標籤執行個體的 StackLayout")](../images/orientation-large.png#lightbox "包含水平方向標籤執行個體的 StackLayout")

    請注意，[`StackLayout`](xref:Xamarin.Forms.StackLayout) 內的 [`Label`](xref:Xamarin.Forms.Label) 執行個體現在會以水平方向對齊，不是以垂直方向。
