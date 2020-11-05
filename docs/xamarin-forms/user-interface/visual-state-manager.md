---
title: Xamarin.Forms 視覺狀態管理員
description: 您可以使用 [視覺狀態管理員]，根據從程式碼設定的視覺狀態來變更 XAML 元素。
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f1ceadb6d7985f5d448fe729a42e0cf0a3d5d6f6
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374468"
---
# <a name="no-locxamarinforms-visual-state-manager"></a>Xamarin.Forms 視覺狀態管理員

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_您可以使用 [視覺狀態管理員]，根據從程式碼設定的視覺狀態來變更 XAML 元素。_

Visual State Manager (VSM) 提供結構化的方式，從程式碼對使用者介面進行視覺變更。 在大部分的情況下，應用程式的使用者介面是以 XAML 定義的，而此 XAML 包含標記，描述視覺狀態管理員如何影響使用者介面的視覺效果。

VSM 引進了 _視覺狀態_ 的概念。 Xamarin.Forms視其基礎狀態而定，如已 `Button` &mdash; 停用、已按下或有輸入焦點，這類視圖可能會有數個不同的視覺外觀。 這些是按鈕的狀態。

視覺狀態會收集在 _視覺狀態群組_ 中。 視覺狀態群組內的所有視覺狀態都是互斥的。 視覺狀態和視覺狀態群組都是由簡單的文字字串所識別。

Xamarin.Forms視覺狀態管理員會定義一個名為 "CommonStates" 的視覺狀態群組，且具有下列視覺狀態：

- "Normal"
- 已
- 作用
- 目的

所有衍生自的類別都支援此視覺狀態群組 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，這是和的基類 [`View`](xref:Xamarin.Forms.View) [`Page`](xref:Xamarin.Forms.Page) 。

您也可以定義自己的視覺狀態群組和視覺狀態，如本文所示。

> [!NOTE]
> Xamarin.Forms 熟悉 [觸發](~/xamarin-forms/app-fundamentals/triggers.md) 程式的開發人員知道，觸發程式也可以根據視圖屬性中的變更或事件引發來變更使用者介面中的視覺效果。 不過，使用觸發程式來處理這些變更的各種組合可能會變得相當令人困惑。 在過去，視覺狀態管理員是在 Windows XAML 架構環境中引進的，可減輕因視覺狀態組合而產生的混淆。 透過 VSM，視覺狀態群組內的視覺狀態一律會互斥。 在任何時候，每個群組中只會有一個狀態是目前的狀態。

## <a name="common-states"></a>常見狀態

視覺狀態管理員可讓您在 XAML 檔案中包含標記，如果視圖為正常、已停用或具有輸入焦點，則可以變更視圖的視覺外觀。 這些都稱為 _一般狀態_ 。

例如，假設您的 `Entry` 頁面上有一個視圖，而且您想要 `Entry` 以下列方式變更的視覺外觀：

- `Entry`停用時，應該會有粉紅色背景 `Entry` 。
- `Entry`通常應該會有淺的背景。
- `Entry`當其具有輸入焦點時，應擴充為其一般高度的兩倍。

您可以將 VSM 標記附加至個別的視圖，也可以在套用至多個視圖的樣式中定義。 接下來的兩節將說明這些方法。

### <a name="vsm-markup-on-a-view"></a>查看視圖上的 VSM 標記

若要將 VSM 標記附加至 `Entry` 視圖，請先將匯 `Entry` 入至開始和結束標記：

```xaml
<Entry FontSize="18">

</Entry>
```

因為其中一個狀態會使用 `FontSize` 屬性來將中的文字大小加倍，所以會提供明確的字型大小 `Entry` 。

接下來， `VisualStateManager.VisualStateGroups` 在這些標記之間插入標記：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) 是類別所定義的附加可系結屬性 [`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager) 。  (如需附加可系結屬性的詳細資訊，請參閱文章 [附加屬性](~/xamarin-forms/xaml/attached-properties.md)。 ) 這是 `VisualStateGroups` 屬性附加至 `Entry` 物件的方式。

`VisualStateGroups`屬性是類型，也 [`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList) 就是物件的集合 [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) 。 在 `VisualStateManager.VisualStateGroups` 標記內， `VisualStateGroup` 針對您想要包含的每個視覺狀態群組，插入一組標記：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

請注意， `VisualStateGroup` 標記具有 `x:Name` 指出群組名稱的屬性。 `VisualStateGroup`類別 `Name` 會定義您可以改用的屬性：

```xaml
<VisualStateGroup Name="CommonStates">
```

您可以 `x:Name` `Name` 在相同元素中使用或，但不能同時使用兩者。

`VisualStateGroup`類別會定義名為的屬性 [`States`](xref:Xamarin.Forms.VisualStateGroup.States) ，它是物件的集合 [`VisualState`](xref:Xamarin.Forms.VisualState) 。 `States` 是的 _content 屬性_ ， `VisualStateGroups` 因此您可以直接在 `VisualState` 標記之間包含標記 `VisualStateGroup` 。  (內容屬性將在本文的 [基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)中討論。 ) 

下一步是針對該群組中的每個視覺狀態包含一組標記。 您也可以使用或來識別這些 `x:Name` `Name` 內容：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState` 定義名為的屬性 [`Setters`](xref:Xamarin.Forms.VisualState.Setters) ，它是物件的集合 [`Setter`](xref:Xamarin.Forms.Setter) 。 這些是 `Setter` 您在物件中使用的相同物件 [`Style`](xref:Xamarin.Forms.Style) 。

`Setters` 不 _是的_ content 屬性 `VisualState` ，因此必須包含屬性的屬性元素標記 `Setters` ：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

您現在可以在每組標記之間插入一或多個 `Setter` 物件 `Setters` 。 這些 `Setter` 物件定義了稍早所述的視覺狀態：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

每個 `Setter` 標記都會指出當狀態為最新狀態時，特定屬性的值。 物件所參考的任何屬性都必須受到可系結 `Setter` 屬性的支援。

與此類似的標記是 **[VsmDemos](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 範例程式中 **VSM** 頁面的基礎。 此頁面包含三個 `Entry` 視圖，但只有第二個會附加 VSM 標記：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />
        <Entry />
        <Label Text="Entry with VSM: " />
        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>
        <Label Text="Entry to enable 2nd Entry:" />
        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

請注意，第二個也會在 `Entry` `DataTrigger` 其集合中包含 `Trigger` 。 這會導致 `Entry` 停用，直到將某個內容輸入至第三個為止 `Entry` 。 以下是在 iOS、Android 和通用 Windows 平臺 (UWP) 上執行的啟動頁面：

[![VSM on View： Disabled](vsm-images/VsmOnViewDisabled.png "已停用 VSM-已停用")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

目前的視覺狀態為「已停用」，因此 `Entry` iOS 和 Android 畫面上的第二個背景為粉紅色。 的 UWP 執行 `Entry` 不允許在停用時設定背景色彩 `Entry` 。

當您在第三個文字中輸入一些文字時 `Entry` ，第二個 `Entry` 參數會切換為「正常」狀態，而背景現在會有酸橙色：

[![VSM on View： Normal](vsm-images/VsmOnViewNormal.png "VSM 的查看-正常")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

當您觸及第二個時 `Entry` ，它會取得輸入焦點。 它會切換至「焦點」狀態，並將其高度擴展為兩倍：

[![查看上的 VSM：焦點](vsm-images/VsmOnViewFocused.png "以視圖為焦點的 VSM")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

請注意， `Entry` 當它取得輸入焦點時，不會保留橙色背景。 當視覺狀態管理員在視覺狀態之間切換時，會取消設定先前狀態設定的屬性。 請記住，視覺狀態是互斥的。 「正常」狀態並不表示 `Entry` 已啟用。 這表示 `Entry` 已啟用，且沒有輸入焦點。

如果您想要 `Entry` 讓背景處於「焦點」狀態，請新增另一個 `Setter` 至該視覺狀態：

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

為了讓這些 `Setter` 物件正常運作， `VisualStateGroup` 必須包含 `VisualState` 該群組中所有狀態的物件。 如果視覺狀態沒有任何 `Setter` 物件，請將其包含為空白標記：

```xaml
<VisualState x:Name="Normal" />
```

### <a name="visual-state-manager-markup-in-a-style"></a>樣式中的視覺狀態管理員標記

通常必須在兩個或多個視圖之間共用相同的視覺狀態管理員標記。 在此情況下，您會想要將標記放在 `Style` 定義中。

以下是 `Style` `Entry` [ **VSM On View** ] 頁面中專案的現有隱含專案：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style>
```

加入附加可系結 `Setter` 屬性的標記 `VisualStateManager.VisualStateGroups` ：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style>
```

的 content 屬性為 `Setter` `Value` ，因此 `Value` 可以直接在這些標記內指定屬性的值。 該屬性的類型為 `VisualStateGroupList` ：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style>
```

在這些標記內，您可以包含一個以上的 `VisualStateGroup` 物件：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style>
```

VSM 標記的其餘部分與之前相同。

以下是 [ **在樣式中的 vsm** ] 頁面，其中顯示完整的 VSM 標記：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>
                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />
        <Entry />
        <Label Text="Entry with VSM: " />
        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>
        <Label Text="Entry to enable 2nd Entry:" />
        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

`Entry`此頁面上的所有觀點現在都會以相同的方式回應其視覺狀態。 另外也請注意，「焦點」狀態現在還包含第二個，它會在 `Setter` `Entry` 有輸入焦點時，讓每一個都有淺的背景：

[![VSM 的樣式](vsm-images/VsmInStyle.png "VSM 的樣式")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="visual-states-in-no-locxamarinforms"></a>中的視覺狀態 Xamarin.Forms

下表列出中定義的視覺狀態 Xamarin.Forms ：

| 執行個體 | 狀態 | 相關資訊 |
| ----- | ------ | ---------------- |
| `Button` | `Pressed` | [按鈕視覺狀態](~/xamarin-forms/user-interface/button.md#button-visual-states) |
| `CheckBox` | `IsChecked` | [核取方塊視覺狀態](~/xamarin-forms/user-interface/checkbox.md#checkbox-visual-states) |
| `CarouselView` | `DefaultItem`, `CurrentItem`, `PreviousItem`, `NextItem` | [CarouselView 視覺狀態](~/xamarin-forms/user-interface/carouselview/interaction.md#define-visual-states) |
| `ImageButton` | `Pressed` | [ImageButton 視覺狀態](~/xamarin-forms/user-interface/imagebutton.md#imagebutton-visual-states) |
| `RadioButton` | `IsChecked` | [選項按鈕視覺狀態](~/xamarin-forms/user-interface/radiobutton.md#radiobutton-visual-states) |
| `Switch` | `On`, `Off` | [切換視覺狀態](~/xamarin-forms/user-interface/switch.md#switch-visual-states) |
| `VisualElement` | `Normal`, `Disabled`, `Focused`, `Selected` | [常見狀態](#common-states) |

您可以透過名為的視覺狀態群組來存取每個狀態 `CommonStates` 。

此外，也會實 `CollectionView` 作為 `Selected` 狀態。 如需詳細資訊，請參閱 [變更選取的專案色彩](~/xamarin-forms/user-interface/collectionview/selection.md#change-selected-item-color)。

## <a name="set-state-on-multiple-elements"></a>設定多個元素的狀態

在先前的範例中，視覺狀態已附加至單一元素並操作。 不過，您也可以建立附加至單一專案的視覺狀態，但在相同範圍內的其他元素上設定屬性。 這可避免在狀態操作的每個專案上重複顯示視覺狀態。

[`Setter`](xref:Xamarin.Forms.Setter)類型具有 `TargetName` 類型的屬性， `string` 代表 `Setter` 視覺狀態將操控的目標元素。 `TargetName`定義屬性時，會將 `Setter` `Property` 中定義之元素的設定 `TargetName` 為 `Value` ：

```xaml
<Setter TargetName="label"
        Property="Label.TextColor"
        Value="Red" />
```

在此範例中， `Label` 名為的 `label` 會將其 `TextColor` 屬性設定為 `Red` 。 設定屬性時 `TargetName` ，您必須在中指定屬性的完整路徑 `Property` 。 因此，若要 `TextColor` 在上設定屬性 `Label` ， `Property` 則會將指定為 `Label.TextColor` 。

> [!NOTE]
> 物件所參考的任何屬性都必須受到可系結 `Setter` 屬性的支援。

**[VsmDemos](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 範例中的 **VSM 與 Setter TargetName** 頁面會示範如何從單一視覺狀態群組，設定多個元素的狀態。 XAML 檔案包含 `StackLayout` 包含 `Label` 元素、 `Entry` 和的 `Button` 。

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmSetterTargetNamePage"
             Title="VSM with Setter TargetName">
    <StackLayout Margin="10">
        <Label Text="What is the capital of France?" />
        <Entry x:Name="entry"
               Placeholder="Enter answer" />
        <Button Text="Reveal answer">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Pressed">
                        <VisualState.Setters>
                            <Setter Property="Scale"
                                    Value="0.8" />
                            <Setter TargetName="entry"
                                    Property="Entry.Text"
                                    Value="Paris" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

VSM 標記會附加至 `StackLayout` 。 有兩個互斥狀態，名為「一般」和「已按下」，每個狀態都包含 `VisualState` 標記。

當未按下時，「正常」狀態為 [作用 `Button` 中]，而且可以輸入問題的回應：

[![VSM Setter TargetName：正常狀態](vsm-images/VsmSetterTargetNameNormal.png "VSM setter targetname-一般")](vsm-images/VsmSetterTargetNameNormal-Large.png#lightbox)

當按下時，「已按下」狀態會變成作用中 `Button` ：

[![VSM Setter TargetName：按下的狀態](vsm-images/VsmSetterTargetNamePressed.png "VSM setter targetname-已按下")](vsm-images/VsmSetterTargetNamePressed-Large.png#lightbox)

「已按下」 `VisualState` 指定當 `Button` 按下時，其 `Scale` 屬性將會從預設值1變更為0.8。 此外，指定的 `Entry` `entry` 會將其 `Text` 屬性設定為巴黎。 因此，結果是當 `Button` 按下時，它的重新調整會稍微減少一點，而且會 `Entry` 顯示巴黎。 然後，當發行時， `Button` 它會重新調整為其預設值1，並 `Entry` 顯示任何先前輸入的文字。

> [!IMPORTANT]
> 在指定屬性的元素中，目前不支援屬性路徑 `Setter` `TargetName` 。

## <a name="define-your-own-visual-states"></a>定義您自己的視覺狀態

每個衍生自的類別都 `VisualElement` 支援常見的狀態「標準」、「焦點」和「已停用」。 此外，此類別也支援「已 `CollectionView` 選取」狀態。 就內部而言， [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) 類別會在它變成啟用或停用、或焦點或未取得焦點時偵測，並呼叫靜態 [ `VisualStateManager.GoToState` ] (x： Xamarin.Forms 。VisualStateManager. GoToState (Xamarin.Forms 。VisualElement，System.string) # A3 方法：

```csharp
VisualStateManager.GoToState(this, "Focused");
```

這是您將在類別中找到的唯一視覺狀態管理員程式碼 `VisualElement` 。 因為 `GoToState` 會針對每個衍生自的類別呼叫每個物件 `VisualElement` ，所以您可以使用視覺狀態管理員與任何 `VisualElement` 物件來回應這些變更。

有趣的是，不會在中明確參考視覺狀態群組 "CommonStates" 的名稱 `VisualElement` 。 組名不是視覺狀態管理員 API 的一部分。 在目前為止所顯示的兩個範例程式中的其中一個，您可以將群組的名稱從 "CommonStates" 變更為其他任何專案，而且程式仍可運作。 組名只是該群組中狀態的一般描述。 它隱含地瞭解任何群組中的視覺狀態都是互斥的：一種狀態，而且隨時都只會有一個狀態為最新狀態。

如果您想要執行自己的視覺狀態，則必須 `VisualStateManager.GoToState` 從程式碼呼叫。 您通常會從頁面類別的程式碼後端檔案進行此呼叫。

**[VsmDemos](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 範例中的 **VSM 驗證** 頁面會示範如何使用視覺狀態管理員連接到輸入驗證。 XAML 檔案包含 `StackLayout` 包含兩個 `Label` 元素、 `Entry` 和的 `Button` 。

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout x:Name="stackLayout"
                 Padding="10, 10">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter TargetName="helpLabel"
                                    Property="Label.TextColor"
                                    Value="Transparent" />
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter TargetName="entry"
                                    Property="Entry.BackgroundColor"
                                    Value="Pink" />
                            <Setter TargetName="submitButton"
                                    Property="Button.IsEnabled"
                                    Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />
        <Entry x:Name="entry"
               Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />
        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1" />
        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center" />
    </StackLayout>
</ContentPage>
```

VSM 標記會附加至 `StackLayout` 名為) 的 (`stackLayout` 。 有兩個互斥狀態，名為「有效」和「無效」，每個狀態都包含 `VisualState` 標記。

如果不 `Entry` 包含有效的電話號碼，則目前的狀態為「無效」，所以會 `Entry` 有粉紅色的背景，第二個 `Label` 是可見的，且已 `Button` 停用：

[![VSM 驗證：不正確狀態](vsm-images/VsmValidationInvalid.png "VSM 驗證-無效")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

輸入有效的電話號碼時，目前的狀態會變成「有效」。 `Entry`會取得橙色背景，第二個 `Label` 會消失，而且 `Button` 現在已啟用：

[![VSM 驗證：有效的狀態](vsm-images/VsmValidationValid.png "VSM 驗證-有效")](vsm-images/VsmValidationValid-Large.png#lightbox)

程式碼後端檔案負責處理 `TextChanged` 來自的事件 `Entry` 。 處理常式會使用正則運算式來判斷輸入字串是否有效。 在程式碼後端檔案中，名為的方法會 `GoToState` 呼叫的靜態 `VisualStateManager.GoToState` 方法 `stackLayout` ：

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage()
    {
        InitializeComponent();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(stackLayout, visualState);
    }
}
```

另外也請注意， `GoToState` 方法是從函式呼叫，以初始化狀態。 應該一律會有目前的狀態。 但在程式碼中的任何地方都有視覺狀態群組的名稱參考，不過在 XAML 中參考為 "ValidationStates"，以利理解。

請注意，程式碼後端檔案只需要在定義視覺狀態的頁面上取得物件的帳戶，並為 `VisualStateManager.GoToState` 此物件呼叫。 這是因為這兩種視覺狀態都會以頁面上的多個物件為目標。

您可能會想：如果程式碼後端檔案必須參考頁面上定義視覺狀態的物件，為什麼程式碼後端檔案不能直接存取此物件和其他物件？ 當然可以。 不過，使用 VSM 的優點是您可以控制視覺元素如何在 XAML 中完全反應不同的狀態，這樣會將所有的 UI 設計都保持在一個位置。 這可避免直接從程式碼後端存取視覺效果專案來設定視覺外觀。

## <a name="visual-state-triggers"></a>視覺狀態觸發程式

視覺狀態支援狀態觸發程式，這是一組特殊的觸發程式，可定義應套用的條件 [`VisualState`](xref:Xamarin.Forms.VisualState) 。

狀態觸發程式會加入至的 [`StateTriggers`](xref:Xamarin.Forms.VisualState.StateTriggers) 集合 [`VisualState`](xref:Xamarin.Forms.VisualState) 。 此集合可以包含單一狀態觸發程式或多個狀態觸發程式。 [`VisualState`](xref:Xamarin.Forms.VisualState)當集合中的任何狀態觸發程式為作用中時，將會套用。

使用狀態觸發程式來控制視覺狀態時， Xamarin.Forms 會使用下列優先順序規則來判斷 (的觸發程式和對應的) 將會是作用中的 [`VisualState`](xref:Xamarin.Forms.VisualState) 活動：

1. 衍生自的任何觸發程式 [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) 。
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)因為 [`MinWindowWidth`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowWidth) 符合條件而啟用。
1. [`AdaptiveTrigger`](xref:Xamarin.Forms.AdaptiveTrigger)因為 [`MinWindowHeight`](xref:Xamarin.Forms.AdaptiveTrigger.MinWindowHeight) 符合條件而啟用。

如果多個觸發程式同時為作用中 (例如，有兩個自訂觸發程式) 則會優先使用在標記中宣告的第一個觸發程式。

如需狀態觸發程式的詳細資訊，請參閱 [狀態觸發](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)程式。

## <a name="use-the-visual-state-manager-for-adaptive-layout"></a>使用視覺狀態管理員進行自我調整版面配置

在 Xamarin.Forms 手機上執行的應用程式通常會以直向或橫向的外觀比例來查看，而 Xamarin.Forms 在桌面上執行的程式可以調整大小以採用許多不同的大小和外觀比例。 設計完善的應用程式可能會以不同的頁面或視窗外型規格來顯示其內容。

這項技術 _有時也稱為彈性配置。_ 由於適應性配置只牽涉到程式的視覺效果，因此是 Visual State Manager 的理想應用程式。

一個簡單的範例是應用程式，它會顯示影響應用程式內容的小型按鈕集合。 在直向模式中，這些按鈕可能會顯示在頁面頂端的水準列中：

[![VSM 自我調整配置：縱向](vsm-images/VsmAdaptiveLayoutPortrait.png "VSM 調適型版面配置-縱向")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

在橫向模式中，按鈕的陣列可能會移至一側，並顯示在資料行中：

[![VSM 自我調整配置：橫向](vsm-images/VsmAdaptiveLayoutLandscape.png "VSM 自我調整配置-橫向")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

從上到下，程式正在通用 Windows 平臺、Android 和 iOS 上執行。

[VsmDemos](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)範例中的 **VSM** 調適型配置頁面會定義名為 "OrientationStates" 的群組，其中包含兩個名為 "直向" 和 "橫向" 的視覺狀態。  (更複雜的方法可能會以數種不同的頁面或視窗寬度為基礎。 ) 

VSM 標記會在 XAML 檔案中的四個位置進行。 `StackLayout`指定的 `mainStack` 包含功能表和內容，也就是 `Image` 元素。 這 `StackLayout` 應該會在直向模式中具有垂直方向，並在橫向模式中具有水準方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">
                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>
                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>
                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">
                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">
                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>
                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

名為的內部 `ScrollView` `menuScroll` 和 `StackLayout` 名為的會 `menuStack` 執行按鈕的功能表。 這些版面配置的方向相反 `mainStack` 。 功能表在直向模式中應水準水準，並在橫向模式中垂直。

VSM 標記的第四個區段是按鈕本身的隱含樣式。 此標記會設定直 `VerticalOptions` `HorizontalOptions` `Margin` 向和橫向方向特有的、和屬性。

程式碼後端檔案會設定的 `BindingContext` 屬性 `menuStack` 來執行 `Button` 命令，並將處理常式附加至 `SizeChanged` 頁面的事件：

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

`SizeChanged`處理常式會呼叫 `VisualStateManager.GoToState` 這兩個 `StackLayout` 和專案 `ScrollView` ，然後迴圈執行的子系， `menuStack` 以呼叫 `VisualStateManager.GoToState` `Button` 元素。

這看起來可能像程式碼後端檔案可以透過設定 XAML 檔案中專案的屬性來更直接處理方向變更，但視覺狀態管理員絕對是結構化的方法。 所有視覺效果都會保留在 XAML 檔案中，使其變得更容易檢查、維護和修改。

## <a name="visual-state-manager-with-xamarinuniversity"></a>使用 Xamarin 的視覺狀態管理員

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin.Forms 3.0 視覺狀態管理員影片**

## <a name="related-links"></a>相關連結

- [VsmDemos](/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
- [狀態觸發程式](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers)