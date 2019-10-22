---
title: '[Xamarin] 視覺狀態管理員'
description: 使用 [視覺狀態管理員]，根據從程式碼設定的視覺狀態來變更 XAML 元素。
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 228501172ede71204c64e1efe1673ce92be424ea
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68656061"
---
# <a name="the-xamarinforms-visual-state-manager"></a>[Xamarin] 視覺狀態管理員

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_使用 [視覺狀態管理員]，根據從程式碼設定的視覺狀態來變更 XAML 元素。_

Visual State Manager （VSM）是 Xamarin 中的新功能。表單3.0。 VSM 提供了一種結構化的方式，可讓您從程式碼對使用者介面進行視覺化變更。 在大部分情況下，應用程式的使用者介面會定義在 XAML 中，而此 XAML 會包含標記，描述視覺狀態管理員如何影響使用者介面的視覺效果。

VSM 引進了_視覺狀態_的概念。 如 `Button` 之類的 Xamarin 視圖，可以根據其基礎狀態 &mdash; 是否已停用、已按下或具有輸入焦點，而有數個不同的視覺外觀。 這些是按鈕的狀態。

視覺狀態會在_視覺狀態群組_中收集。 視覺狀態群組中的所有視覺狀態都是互斥的。 視覺狀態和視覺狀態群組都是由簡單的文字字串所識別。

[Xamarin] 視覺狀態管理員會定義一個名為 "CommonStates" 且具有三個視覺狀態的視覺狀態群組：

- "Normal"
- 禁止
- 作用

所有衍生自[`VisualElement`](xref:Xamarin.Forms.VisualElement)的類別都支援此視覺狀態群組，這是[`View`](xref:Xamarin.Forms.View)和[`Page`](xref:Xamarin.Forms.Page)的基類。 

您也可以定義自己的視覺狀態群組和視覺狀態，如本文所示。

> [!NOTE]
> 熟悉[觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式的 Xamarin 開發人員知道，觸發程式也可以根據視圖屬性的變更或引發事件，對使用者介面中的視覺效果進行變更。 不過，使用觸發程式來處理這些變更的各種組合，可能會變得相當令人困惑。 在過去，視覺狀態管理員是在以 Windows XAML 為基礎的環境中引進，以減輕視覺狀態組合所產生的混淆。 使用 VSM 時，視覺狀態群組內的視覺狀態一律是互斥的。 在任何時間，每個群組中只有一個狀態是目前狀態。

## <a name="the-common-states"></a>常見狀態

視覺狀態管理員可讓您在 XAML 檔案中包含區段，以便在視圖為正常、已停用或具有輸入焦點時，變更視圖的視覺外觀。 這些就是所謂的_常見狀態_。

例如，假設您在頁面上有一個 `Entry` 視圖，而您想要讓 `Entry` 的視覺外觀以下列方式變更：

- 停用 `Entry` 時，`Entry` 應該會有粉紅色的背景。
- @No__t_0 應正常具有酸淺的背景。
- 當 `Entry` 有輸入焦點時，應該會展開為其一般高度的兩倍。

您可以將 VSM 標記附加至個別的視圖，或者您也可以在套用到多個視圖的樣式中定義它。 接下來的兩節將說明這些方法。

### <a name="vsm-markup-on-a-view"></a>在視圖上進行 VSM 標記

若要將 VSM 標記附加至 `Entry` 視圖，請先將 `Entry` 分隔成開始和結束標記：

```xaml
<Entry FontSize="18">

</Entry>
```

因為其中一種狀態會使用 [`FontSize`] 屬性來將 `Entry` 中的文字大小加倍，所以會提供明確的字型大小。

接下來，在這些標記之間插入 `VisualStateManager.VisualStateGroups` 標記：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty)是由[`VisualStateManager`](xref:Xamarin.Forms.VisualStateManager)類別所定義的附加可系結屬性。 （如需附加可系結屬性的詳細資訊，請參閱[附加屬性](~/xamarin-forms/xaml/attached-properties.md)一文）。這是 `VisualStateGroups` 屬性附加至 `Entry` 物件的方式。

@No__t_0 屬性是[`VisualStateGroupList`](xref:Xamarin.Forms.VisualStateGroupList)類型，這是[`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup)物件的集合。 在 `VisualStateManager.VisualStateGroups` 標籤中，為您想要包含的每個視覺狀態群組插入一對 `VisualStateGroup` 標記：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

請注意，`VisualStateGroup` 標記具有 `x:Name` 屬性，指出群組的名稱。 @No__t_0 類別會定義您可以改用的 `Name` 屬性：

```xaml
<VisualStateGroup Name="CommonStates">
```

您可以使用 `x:Name` 或 `Name`，但不能同時在相同的專案中。

@No__t_0 類別會定義名為[`States`](xref:Xamarin.Forms.VisualStateGroup.States)的屬性，也就是[`VisualState`](xref:Xamarin.Forms.VisualState)物件的集合。 `States` 是 `VisualStateGroups` 的_content 屬性_，因此您可以直接在 `VisualStateGroup` 標記之間包含 `VisualState` 標記。 （內容屬性會在「[基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)」一文中討論）。

下一個步驟是針對該群組中的每個視覺狀態包含一對標記。 這些也可以使用 `x:Name` 或 `Name` 來識別：

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

`VisualState` 會定義名為[`Setters`](xref:Xamarin.Forms.VisualState.Setters)的屬性，這是[`Setter`](xref:Xamarin.Forms.Setter)物件的集合。 這些是您在[`Style`](xref:Xamarin.Forms.Style)物件中使用的 `Setter` 物件。

`Setters`_不_是 `VisualState` 的 content 屬性，因此必須包含 `Setters` 屬性的屬性元素標記：

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

您現在可以在每一對 `Setters` 標記之間插入一或多個 `Setter` 物件。 這些是定義先前所述之視覺狀態的 `Setter` 物件：

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

每個 `Setter` 標籤都會指出當狀態為「目前」時，特定屬性的值。 @No__t_0 物件所參考的任何屬性，都必須由可系結的屬性支援。

這種標記類似于 **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 範例程式中的**VSM on View**頁面的基礎。 此頁面包含三個 `Entry` 視圖，但只有第二個是附加的 VSM 標記：

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

請注意，第二個 `Entry` 也有 `DataTrigger` 作為其 `Trigger` 集合的一部分。 這會導致 `Entry` 停用，直到 `Entry` 輸入到第三個為止。 以下是啟動時，在 iOS、Android 和通用 Windows 平臺（UWP）上執行的頁面：

[![在 View 上的 VSM：已停用](vsm-images/VsmOnViewDisabled.png "已停用 VSM-已停用")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

目前的視覺狀態為「已停用」，因此第二個 `Entry` 的背景在 iOS 和 Android 畫面上為粉紅色。 @No__t_0 的 UWP 執行不允許在停用 `Entry` 時設定背景色彩。 

當您在第三個 `Entry` 中輸入一些文字時，第二個 `Entry` 會切換為「正常」狀態，而背景現在則為「酸淺」：

[![查看 VSM：正常](vsm-images/VsmOnViewNormal.png "VSM on view-normal")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

當您接觸第二個 `Entry` 時，它會取得輸入焦點。 它會切換為「焦點」狀態，並將其高度擴展為兩倍：

[![VSM on View：已聚焦](vsm-images/VsmOnViewFocused.png "以視圖為焦點的 VSM")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

請注意，當 `Entry` 取得輸入焦點時，不會保留淺的背景。 當視覺狀態管理員在視覺狀態之間切換時，先前狀態所設定的屬性將會取消設定。 請記住，視覺狀態是互斥的。 「正常」狀態不表示已啟用 `Entry`。 這表示 `Entry` 已啟用，而且沒有輸入焦點。 

如果您想要 `Entry` 在「焦點」狀態下具有淺的背景，請將另一個 `Setter` 新增至該視覺狀態：

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

為了讓這些 `Setter` 物件正常運作，`VisualStateGroup` 必須包含該群組中所有狀態的 `VisualState` 物件。 如果視覺狀態沒有任何 `Setter` 物件，請將它包含為空的標記：

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>樣式中的視覺狀態管理員標記

通常必須在兩個或多個視圖之間共用相同的視覺狀態管理員標記。 在此情況下，您會想要將標記放在 `Style` 定義中。

以下是**VSM On View**頁面中 `Entry` 元素的現有隱含 `Style`：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

為 `VisualStateManager.VisualStateGroups` 附加的可系結屬性新增 `Setter` 標記：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

@No__t_0 的 content 屬性 `Value`，因此可以直接在這些標記內指定 `Value` 屬性的值。 該屬性的類型為 `VisualStateGroupList`：

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

以下是在顯示完整 VSM 標記的**樣式頁面中的 VSM** ：

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

現在，此頁面上的所有 `Entry` 視圖都會以相同的方式回應其視覺狀態。 同時也請注意，「焦點」狀態現在包含第二個 `Setter`，讓每個 `Entry` 在有輸入焦點時也有一個酸淺的背景：

[![以樣式的 VSM](vsm-images/VsmInStyle.png "以樣式的 VSM")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>定義您自己的視覺狀態

衍生自 `VisualElement` 的每個類別都支援三種常見狀態「正常」、「焦點」和「已停用」。 就內部而言， [`VisualElement`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs)類別會偵測到它已啟用或停用，或為焦點或未取得焦點，並呼叫靜態[`VisualStateManager.GoToState`](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String))方法：

```csharp
VisualStateManager.GoToState(this, "Focused");
```

這是您可以在 `VisualElement` 類別中找到的唯一視覺狀態管理員程式碼。 因為會根據衍生自 `VisualElement` 的每個類別，針對每個物件呼叫 `GoToState`，所以您可以使用視覺狀態管理員搭配任何 `VisualElement` 物件來回應這些變更。

有趣的是，`VisualElement` 中未明確參考視覺狀態群組 "CommonStates" 的名稱。 組名不是 Visual State Manager API 的一部分。 在目前為止所顯示的兩個範例程式中，您可以將群組的名稱從 "CommonStates" 變更為其他任何專案，程式仍然可正常執行。 組名只是該群組中狀態的一般描述。 它會隱含地瞭解任何群組中的視覺狀態都是互斥的：一種狀態，而且每次只有一個狀態是最新的。

如果您想要執行自己的視覺狀態，就必須從程式碼呼叫 `VisualStateManager.GoToState`。 通常您會從頁面類別的程式碼後置檔案進行此呼叫。

**[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 範例中的 [ **VSM 驗證**] 頁面會顯示如何使用與輸入驗證連接的視覺狀態管理員。 XAML 檔案包含兩個 `Label` 元素、一個 `Entry` 和一個 `Button`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout Padding="10, 10">
        
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />

        <Entry Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />

        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter Property="TextColor" Value="Transparent" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Invalid" />
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Label>

        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid" />

                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter Property="IsEnabled" Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

VSM 標記會附加至第二個 `Label` （名為 `helpLabel`）和 `Button` （名為 `submitButton`）。 有兩個互斥的狀態，名為「有效」和「無效」。 請注意，兩個 "ValidationState" 群組中的每一個都包含「有效」和「無效」的 `VisualState` 標記，不過其中一個在每個案例中都是空的。 

如果 `Entry` 不包含有效的電話號碼，則目前的狀態會是「無效」，因此會顯示第二個 `Label` 並停用 `Button`：

[![VSM 驗證：不正確狀態](vsm-images/VsmValidationInvalid.png "VSM 驗證-無效")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

輸入有效的電話號碼時，目前的狀態會變成「有效」。 第二個 `Entry` 會消失，且現在已啟用 `Button`：

[![VSM 驗證：有效的狀態](vsm-images/VsmValidationValid.png "VSM 驗證-有效")](vsm-images/VsmValidationValid-Large.png#lightbox)

程式碼後置檔案會負責，以處理來自 `Entry` 的 `TextChanged` 事件。 處理常式會使用正則運算式來判斷輸入字串是否有效。 程式碼後置檔案中名為 `GoToState` 的方法會針對 `helpLabel` 和 `submitButton` 呼叫靜態 `VisualStateManager.GoToState` 方法：

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage ()
    {
        InitializeComponent ();

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
        VisualStateManager.GoToState(helpLabel, visualState);
        VisualStateManager.GoToState(submitButton, visualState);
    }
}
```

同時也請注意，會從函式呼叫 `GoToState` 方法，以初始化狀態。 應該一律是目前的狀態。 但在程式碼中沒有任何名稱參考視覺狀態群組的名稱，但在 XAML 中是以「ValidationStates」的形式來表示，以利明確起見。 

請注意，程式碼後置檔案必須考慮受這些視覺狀態影響之頁面上的每個物件，並針對每個物件呼叫 `VisualStateManager.GoToState`。 在此範例中，只有兩個物件（`Label` 和 `Button`），但它可能會更多。

您可能會想：如果程式碼後置檔案必須參考受這些視覺狀態影響之頁面上的每個物件，為什麼程式碼後置檔案無法直接存取物件？ 當然可以。 不過，使用 VSM 的優點是，您可以控制視覺專案完全以 XAML 回應不同狀態的方式，這會將所有 UI 設計保持在同一個位置。 這可避免直接從程式碼後置存取視覺效果元素來設定視覺外觀。

您可能會想要考慮從 `Entry` 衍生類別，而且可能會定義可設定為外部驗證函式的屬性。 衍生自 `Entry` 的類別可以接著呼叫 `VisualStateManager.GoToState` 方法。 此配置可正常執行，但只有在 `Entry` 是受不同視覺狀態影響的唯一物件時才會正常執行。 在此範例中，`Label` 和 `Button` 也會受到影響。 沒有任何方法可將附加至 `Entry` 的 VSM 標記控制頁面上的其他物件，也無法使用任何附加至其他物件的 VSM 標記來參考另一個物件的視覺狀態變更。

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>使用視覺狀態管理員進行自我調整版面配置

在手機上執行的 Xamarin. Forms 應用程式通常會以直向或橫向外觀比例來查看，而且可以調整在桌面上執行的 Xamarin 程式，以採用許多不同大小和外觀比例。 設計良好的應用程式可能會以不同的方式來顯示其內容，以因應各種頁面或視窗的外型規格。 

這項技術有時也稱為「調適型_版面_配置」。 因為自動調整版面配置只包含程式的視覺效果，所以它是視覺狀態管理員的理想應用程式。

一個簡單的範例是一個應用程式，它會顯示影響應用程式內容的小型按鈕集合。 在直向模式中，這些按鈕可能會顯示在頁面頂端的水準列中：

[![VSM 調適型版面配置：縱向](vsm-images/VsmAdaptiveLayoutPortrait.png "VSM 調適型版面配置-縱向")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

在橫向模式中，按鈕的陣列可能會移到一邊，並顯示在資料行中：

[![VSM 調適型版面配置：橫向](vsm-images/VsmAdaptiveLayoutLandscape.png "VSM 調適型版面配置-橫向")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

從上到下，程式正在通用 Windows 平臺、Android 和 iOS 上執行。

[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)範例中的**VSM**調適型配置頁面會定義名為 "OrientationStates" 的群組，其中具有兩個名為「直向」和「橫向」的視覺狀態。 （較複雜的方法可能是以數種不同的頁面或視窗寬度為基礎）。 

VSM 標記會出現在 XAML 檔案的四個位置中。 名為 `mainStack` 的 `StackLayout` 同時包含功能表和內容，也就是 `Image` 元素。 此 `StackLayout` 在直向模式中應該有垂直方向，而在橫向模式中應該是水準方向：

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

名為 `menuScroll` 的內部 `ScrollView` 和名為 `menuStack` 的 `StackLayout` 會執行按鈕的功能表。 這些版面配置的方向與 `mainStack` 相反。 功能表在直向模式中應該是水準的，而在橫向模式中則是垂直。

VSM 標記的第四個區段是按鈕本身的隱含樣式。 此標記會設定 portait 和橫向方向的特定 `VerticalOptions`、`HorizontalOptions` 和 `Margin` 屬性。

程式碼後置檔案會設定 `menuStack` 的 `BindingContext` 屬性，以執行 `Button` 命令，同時也會將處理常式附加至頁面的 `SizeChanged` 事件：

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

@No__t_0 處理常式會針對兩個 `StackLayout` 和 `ScrollView` 專案呼叫 `VisualStateManager.GoToState`，然後在 `menuStack` 的子系上執行迴圈，以呼叫 `VisualStateManager.GoToState` 元素的 `Button`。

看起來像程式碼後置檔案可以藉由設定 XAML 檔案中專案的屬性，更直接處理方向變更，但是視覺狀態管理員絕對是更具結構化的方法。 所有視覺效果都會保留在 XAML 檔案中，讓它們變得更容易檢查、維護和修改。

## <a name="visual-state-manager-with-xamarinuniversity"></a>具有 Xamarin 的視覺狀態管理員

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin 3.0 視覺狀態管理員影片**

## <a name="related-links"></a>相關連結

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
