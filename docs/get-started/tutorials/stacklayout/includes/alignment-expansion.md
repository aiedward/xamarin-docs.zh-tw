---
ms.openlocfilehash: d6dbc82e56959399c2befb6a12f0a2cf3793ee5b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "67559985"
---
子檢視在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的大小和位置取決於子檢視的 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性值，以及 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性的值。

您可以從 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 結構中，將 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 和 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 屬性設定為欄位，該結構涵蓋兩個版面配置喜好設定：

- **對齊** – 子檢視慣用的對齊方式，這會決定子檢視在其父系版面配置中的位置和大小。
- **延展** – 指出如果可行，子檢視是否應該使用額外的空間 (僅供 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 使用)。

# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

1. 在 **MainPage.xaml** 中修改 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 宣告，以針對每個 [`Label`](xref:Xamarin.Forms.Label) 設定對齊和延展選項：

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    此程式碼會在前四個 [`Label`](xref:Xamarin.Forms.Label) 執行個體上設定對齊的喜好設定，並在最後四個 `Label` 執行個體上設定延展的喜好設定。 [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)、[`Center`](xref:Xamarin.Forms.LayoutOptions.Center)、[`End`](xref:Xamarin.Forms.LayoutOptions.End) 和 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 欄位會用來在 父系 [`Label`](xref:Xamarin.Forms.Label) 中定義 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 執行個體的對齊方式。 [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)、[`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)、[`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) 和 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 欄位會用來定義對齊的喜好設定，以及在父系 `StackLayout` 中，該檢視是否需要占用更多空間 (如果可行)。

    > [!NOTE]
    > 檢視的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性預設值為 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)。

1. 在 Visual Studio 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選遠端 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：iOS 和 Android 上已設定對齊和延展選項的 StackLayout 子檢視](../images/alignment-expansion.png "包含 Label 執行個體並已設定對齊和延展的 StackLayout")](../images/alignment-expansion-large.png#lightbox "包含 Label 執行個體並已設定對齊和延展的 StackLayout")

    [`StackLayout`](xref:Xamarin.Forms.StackLayout) 只會遵循子檢視上的對齊喜好設定，而這與 `StackLayout` 方向相反。 因此，在垂直方向的 [ 中，`Label`](xref:Xamarin.Forms.Label)`StackLayout` 子檢視會將其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性設定為其中一個對齊欄位：

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)，這會將 [`Label`](xref:Xamarin.Forms.Label) 放在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的左側。
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)，這會將 [`Label`](xref:Xamarin.Forms.Label) 放在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的中心位置。
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End)，這會將 [`Label`](xref:Xamarin.Forms.Label) 放在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的右側。
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)，這會確保 [`Label`](xref:Xamarin.Forms.Label) 填入 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的寬度。

    [`StackLayout`](xref:Xamarin.Forms.StackLayout) 只會讓子檢視往其方向延展。 因此，垂直方向的 `StackLayout` 若要延展 [`Label`](xref:Xamarin.Forms.Label) 子檢視，其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性須設定為其中一個延展欄位。 這表示，在垂直對齊的情況下，每個 `Label` 會佔用 `StackLayout` 內相同數量的空間。 不過，最後的 `Label` 會將其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設為 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)，因此只有其大小不同。

    > [!IMPORTANT]
    > 如果 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的所有空間都已使用，則延展喜好設定不會有作用。

    如需對齊和延展的詳細資訊，請參閱 [Xamarin.Forms 中的版面配置選項](~/xamarin-forms/user-interface/layouts/layout-options.md)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 **MainPage.xaml** 中修改 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 宣告，以針對每個 [`Label`](xref:Xamarin.Forms.Label) 設定對齊和延展選項：

    ```xaml
    <StackLayout Margin="20,35,20,25">
        <Label Text="Start"
               HorizontalOptions="Start"
               BackgroundColor="Gray" />
        <Label Text="Center"
               HorizontalOptions="Center"
               BackgroundColor="Gray" />
        <Label Text="End"
               HorizontalOptions="End"
               BackgroundColor="Gray" />
        <Label Text="Fill"
               HorizontalOptions="Fill"
               BackgroundColor="Gray" />
        <Label Text="StartAndExpand"
               VerticalOptions="StartAndExpand"
               BackgroundColor="Gray" />
        <Label Text="CenterAndExpand"
               VerticalOptions="CenterAndExpand"
               BackgroundColor="Gray" />
        <Label Text="EndAndExpand"
               VerticalOptions="EndAndExpand"
               BackgroundColor="Gray" />
        <Label Text="FillAndExpand"
               VerticalOptions="FillAndExpand"
               BackgroundColor="Gray" />
    </StackLayout>
    ```

    此程式碼會在前四個 [`Label`](xref:Xamarin.Forms.Label) 執行個體上設定對齊的喜好設定，並在最後四個 `Label` 執行個體上設定延展的喜好設定。 [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)、[`Center`](xref:Xamarin.Forms.LayoutOptions.Center)、[`End`](xref:Xamarin.Forms.LayoutOptions.End) 和 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 欄位會用來在 父系 [`Label`](xref:Xamarin.Forms.Label) 中定義 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 執行個體的對齊方式。 [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)、[`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)、[`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) 和 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 欄位會用來定義對齊的喜好設定，以及在父系 `StackLayout` 中，該檢視是否需要占用更多空間 (如果可行)。

    > [!NOTE]
    > 檢視的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性預設值為 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)。

1. 在 Visual Studio for Mac 工具列中，按下 [啟動]  按鈕 (類似於 [播放] 按鈕的三角形按鈕)，以啟動所選 iOS 模擬器或 Android 模擬器內的應用程式：

    [![螢幕擷取畫面：iOS 和 Android 上已設定對齊和延展選項的 StackLayout 子檢視](../images/alignment-expansion.png "包含 Label 執行個體並已設定對齊和延展的 StackLayout")](../images/alignment-expansion-large.png#lightbox "包含 Label 執行個體並已設定對齊和延展的 StackLayout")

    [`StackLayout`](xref:Xamarin.Forms.StackLayout) 只會遵循子檢視上的對齊喜好設定，而這與 `StackLayout` 方向相反。 因此，在垂直方向的 [ 中，`Label`](xref:Xamarin.Forms.Label)`StackLayout` 子檢視會將其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性設定為其中一個對齊欄位：

    - [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)，這會將 [`Label`](xref:Xamarin.Forms.Label) 放在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的左側。
    - [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)，這會將 [`Label`](xref:Xamarin.Forms.Label) 放在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的中心位置。
    - [`End`](xref:Xamarin.Forms.LayoutOptions.End)，這會將 [`Label`](xref:Xamarin.Forms.Label) 放在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的右側。
    - [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)，這會確保 [`Label`](xref:Xamarin.Forms.Label) 填入 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的寬度。

    [`StackLayout`](xref:Xamarin.Forms.StackLayout) 只會讓子檢視往其方向延展。 因此，垂直方向的 `StackLayout` 若要延展 [`Label`](xref:Xamarin.Forms.Label) 子檢視，其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性須設定為其中一個延展欄位。 這表示，在垂直對齊的情況下，每個 `Label` 會佔用 `StackLayout` 內相同數量的空間。 不過，最後的 `Label` 會將其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設為 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)，因此只有其大小不同。

    > [!IMPORTANT]
    > 如果 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的所有空間都已使用，則延展喜好設定不會有作用。

    如需對齊和延展的詳細資訊，請參閱 [Xamarin.Forms 中的版面配置選項](~/xamarin-forms/user-interface/layouts/layout-options.md)。
