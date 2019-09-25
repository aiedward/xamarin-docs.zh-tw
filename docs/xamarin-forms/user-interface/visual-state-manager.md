---
title: Xamarin.Forms 視覺狀態管理員
description: 您可以使用 Visual State Manager 來變更視覺狀態由程式碼設定為基礎的 XAML 元素。
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 228501172ede71204c64e1efe1673ce92be424ea
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "68656061"
---
# <a name="the-xamarinforms-visual-state-manager"></a>Xamarin.Forms 視覺狀態管理員

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)

_您可以使用 Visual State Manager 來變更視覺狀態由程式碼設定為基礎的 XAML 元素。_

Visual State Manager (VSM) 是新 Xamarin.Forms 3.0。 VSM 提供結構化的方式來進行使用者介面中的視覺變更，從程式碼。 在大部分情況下，應用程式的使用者介面會定義於 XAML，而且這個 XAML 包含描述 Visual State Manager 會如何影響的使用者介面之視覺效果的標記。

VSM 介紹的概念_視覺狀態_。 這類的 Xamarin.Forms 檢視`Button`可以根據其基礎的狀態有數個不同的視覺外觀&mdash;是否已停用，或按下，或具有輸入焦點。 這些是按鈕的狀態。

視覺狀態會收集在_視覺狀態群組_。 視覺狀態群組內的所有視覺狀態互斥。 簡單的文字字串會識別視覺狀態及視覺狀態群組。

Xamarin.Forms Visual State Manager 定義一個名為"CommonStates"，具有三種視覺狀態的視覺狀態群組：

- "Normal"
- [停用]
- 「 已取得焦點 」

此視覺狀態群組支援所有的類別衍生自[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)，這是基底類別[ `View` ](xref:Xamarin.Forms.View)並[ `Page` ](xref:Xamarin.Forms.Page)。 

您也可以定義自己的視覺狀態群組並視覺狀態，為這篇文章會示範。

> [!NOTE]
> Xamarin.Forms 開發人員熟悉[觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)都知道，觸發程序也可以變更檢視的屬性或事件的引發中的變更，使用者介面中的視覺效果。 不過，使用觸發程序來處理各種組合這些變更可能會相當困擾。 在過去，Visual State Manager 中引進 Windows XAML 為基礎的環境，以減少從視覺狀態的組合產生混淆。 透過 VSM，視覺狀態群組內的視覺狀態一律是互斥的。 在任何時間，每個群組中的只能有一個狀態會是目前的狀態。

## <a name="the-common-states"></a>常見的狀態

Visual State Manager 可讓您包含在您可以變更檢視的視覺外觀，如果檢視是正常現象，或已停用，或是具有輸入的焦點的 XAML 檔案中的區段。 這些值稱為_常見狀態_。

例如，假設您有`Entry`檢視在頁面上，和您想要的視覺外觀`Entry`變更如下：

- `Entry`應有粉紅色背景時`Entry`已停用。
- `Entry`通常應該有淡黃綠色背景。
- `Entry`它具有輸入焦點時，應該擴充來正常高度的兩倍。

您可以附加 VSM 標記至個別的檢視，或您可以將它定義於樣式中套用至多個檢視。 下面兩節會說明這些方法。

### <a name="vsm-markup-on-a-view"></a>在檢視上的 VSM 標記

要附加至 VSM 標記`Entry`檢視中，第一次分隔`Entry`成開始和結束標記：

```xaml
<Entry FontSize="18">

</Entry>
```

這已提供明確的字型大小，因為其中一個狀態將會使用`FontSize`屬性中的文字大小的兩倍`Entry`。

接下來，插入`VisualStateManager.VisualStateGroups`之間這些標記的標記：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) 是附加可繫結屬性所定義[ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager)類別。 (如需有關可繫結的附加屬性的詳細資訊，請參閱文章[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。)這是如何`VisualStateGroups`屬性會附加至`Entry`物件。

`VisualStateGroups`屬性的類型是[ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList)，這是一堆[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)物件。 內`VisualStateManager.VisualStateGroups`標記，插入一組`VisualStateGroup`視覺狀態，您要包含的每個群組的標記：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

請注意，`VisualStateGroup`標記具有`x:Name`屬性，指出群組的名稱。 `VisualStateGroup`類別會定義`Name`屬性，您可以改為使用：

```xaml
<VisualStateGroup Name="CommonStates">
```

您可以使用`x:Name`或`Name`但不是能同時在相同的項目。

`VisualStateGroup`類別會定義名為的屬性[ `States` ](xref:Xamarin.Forms.VisualStateGroup.States)，這是一堆[ `VisualState` ](xref:Xamarin.Forms.VisualState)物件。 `States` 已_內容屬性_的`VisualStateGroups`因此您可以將包含`VisualState`標記之間直接`VisualStateGroup`標記。 (內容屬性會包含本文中討論[基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)。)

下一個步驟是該群組中包含一對每個視覺狀態的標記。 這些也可以使用來識別`x:Name`或`Name`:

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

`VisualState` 定義屬性，名為[ `Setters` ](xref:Xamarin.Forms.VisualState.Setters)，這是集合的[ `Setter` ](xref:Xamarin.Forms.Setter)物件。 這些都是相同`Setter`您在中使用的物件[ `Style` ](xref:Xamarin.Forms.Style)物件。

`Setters` 已_未_之內容屬性的`VisualState`，因此必須包含的屬性項目標記`Setters`屬性：

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

您現在可以插入一或多個`Setter`之間的每一對物件`Setters`標記。 這些是`Setter`定義稍早所述的視覺狀態的物件：

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

每個`Setter`目前該狀態時，標籤會指示特定屬性的值。 所參考的任何屬性`Setter`物件必須由可繫結的屬性。

如下所示的標記是的基礎 **檢視上的 VSM** 頁面 **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 範例程式。 此頁面包含三個`Entry`檢視中，但第二個已連結的 VSM 標記：

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

請注意，第二個`Entry`還有`DataTrigger`一部分其`Trigger`集合。 這會導致`Entry`停用，直到第三個輸入項目`Entry`。 以下是在啟動 iOS、 Android 和通用 Windows 平台 (UWP) 上執行的頁面：

[![VSM on View：已]停用(vsm-images/VsmOnViewDisabled.png "視圖上的 VSM-已停用")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

目前的視覺狀態的動作"Disabled"因此第二個背景`Entry`是在 iOS 和 Android 的畫面上粉紅色。 UWP 實作`Entry`不允許設定背景色彩的時機`Entry`已停用。 

當您輸入一些文字到第三個`Entry`，第二個`Entry`現已切換成 「 正常 」 狀態，以及背景淡黃綠色：

[![VSM on View：一般](vsm-images/VsmOnViewNormal.png "VSM 的查看-正常")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

當您在同一個第二個`Entry`，它會取得輸入的焦點。 它會切換成 「 Focused 」 狀態，並展開以高度的兩倍：

[![VSM on View：以](vsm-images/VsmOnViewFocused.png "觀點為焦點的焦點 VSM")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

請注意，`Entry`收到輸入的焦點時，不會保留淡黃綠色背景。 視覺狀態間切換時 Visual State Manager，先前的狀態所設定的屬性是未設定。 請注意，視覺狀態互斥。 「 正常 」 狀態並不表示僅`Entry`已啟用。 這表示`Entry`已啟用，而且沒有輸入的焦點。 

如果您想`Entry`若要讓淡黃綠色背景狀態為 「 Focused"，新增另一個`Setter`對該視覺狀態：

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

為了讓這些`Setter`才能正常運作，物件`VisualStateGroup`必須包含`VisualState`針對該群組中的所有狀態的物件。 如果沒有任何視覺狀態`Setter`物件的空標記要包含它：

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>在樣式中的視覺狀態管理員標記

它通常是必須共用兩個或多個檢視之間的相同 Visual State Manager 標記。 在此情況下，您會想要將標記放在`Style`定義。

以下是現有隱含`Style`for`Entry`中的項目**VSM 上檢視**頁面：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

新增`Setter`標籤`VisualStateManager.VisualStateGroups`附加可繫結的屬性：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

內容屬性`Setter`已`Value`，因此值`Value`可以直接在這些標記內指定屬性。 屬性的類型是`VisualStateGroupList`:

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

這些標記內可以包含一或多個`VisualStateGroup`物件：

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

VSM 標記的其餘部分會如往常般。

以下是**VSM 樣式** 畫面，顯示完整的 VSM 標記：

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

現在所有`Entry`此頁面上的檢視回應其視覺狀態的相同方式。 也請注意，狀態為 「 Focused"現在會包含第二個`Setter`提供每個`Entry`淡黃綠色也背景時，它具有輸入焦點：

[![在樣式中的 VSM](vsm-images/VsmInStyle.png "VSM 樣式")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>定義您自己的視覺狀態

每個類別衍生自`VisualElement`支援三種常見狀態 「 正常 」、 「 已取得焦點 」，和 「 停用 」。 就內部而言， [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs)現為啟用或停用，或具有焦點或未取得焦點，而且會呼叫靜態時，會偵測到類別[ `VisualStateManager.GoToState` ](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String))方法：

```csharp
VisualStateManager.GoToState(this, "Focused");
```

這是唯一的 Visual State Manager 程式碼，您會發現在`VisualElement`類別。 因為`GoToState`會針對每一個衍生自的類別為基礎的每個物件呼叫`VisualElement`，您可以使用 Visual State Manager 與任何`VisualElement`來回應這些變更的物件。

有趣的是，視覺狀態群組"CommonStates 」 的名稱中未明確參考`VisualElement`。 群組名稱不屬於 Visual State Manager 的 API。 其中一個內兩個目前顯示的範例程式，您可以變更任何其他動作，以從 「 CommonStates 」 群組的名稱，以及程式仍然可以運作。 群組名稱是只是該群組中的狀態的一般描述。 它會隱含地瞭解任何群組中的視覺狀態是互斥的：一種狀態，而且每次只有一種狀態。

如果您想要實作您自己的視覺狀態，您必須呼叫`VisualStateManager.GoToState`從程式碼。 通常您要進行此呼叫，從您的網頁類別的程式碼後置檔案。

**VSM 驗證** 頁面 **[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)** 範例示範如何使用 Visual State Manager 與輸入驗證。 XAML 檔案包含兩個`Label`項目， `Entry`，和`Button`:

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

VSM 標記會附加至第二個`Label`(名為`helpLabel`) 和`Button`(名為`submitButton`)。 有兩個互斥狀態，名為 「 有效 」 和 「 無效 」。 請注意，兩個 「 ValidationState 」 群組的每個包含`VisualState`標記為 「 有效 」 和 「 無效 」，雖然其中沒有在每個案例。 

如果`Entry`不包含有效的電話號碼，則目前的狀態為 「 無效 」，因此第二個`Label`會顯示與`Button`已停用：

[![VSM 驗證：不正確]狀態(vsm-images/VsmValidationInvalid.png "VSM 驗證-無效")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

當您輸入有效的電話號碼時，就目前的狀態會變成 「 有效 」。 第二個`Entry`消失，`Button`現在已啟用：

[![VSM 驗證：有效的]狀態(vsm-images/VsmValidationValid.png "VSM 驗證-有效")](vsm-images/VsmValidationValid-Large.png#lightbox)

程式碼後置檔案會負責處理`TextChanged`從事件`Entry`。 處理常式會使用規則運算式，以判斷輸入的字串是否為有效。 名為程式碼後置檔案中的方法`GoToState`呼叫靜態`VisualStateManager.GoToState`方法，同時`helpLabel`和`submitButton`:

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

也請注意`GoToState`方法從初始化狀態的建構函式呼叫。 應該一律有最新的狀態。 但是，共用程式碼中有任何參考的視覺狀態群組的名稱雖然它在 XAML 中參照為"ValidationStates"為了清楚起見。 

請注意程式碼後置檔案必須採取之每個物件的帳戶會受到影響的頁面上，這些視覺狀態，以及如何呼叫`VisualStateManager.GoToState`的每個物件。 在此範例中，就只有兩個物件 (`Label`而`Button`)，但它可以是數個更多。

您可能會想：如果程式碼後置檔案必須參考頁面上受這些視覺狀態影響的每個物件，為什麼程式碼後置檔案無法直接存取物件？ 當然可以。 不過，使用 VSM 的優點是，您可以控制如何視覺項目回應不同的狀態，完全以 XAML，會將所有 UI 設計放在一個位置。 這會藉由直接從程式碼後置存取視覺項目避免設定視覺外觀。

它可能會想要衍生的類別，請考慮`Entry`和或許定義此屬性，您可以將它設定為外部驗證函式。 衍生自類別`Entry`就可以呼叫`VisualStateManager.GoToState`方法。 此配置會正常運作，但只有當`Entry`是不同的視覺狀態所影響的唯一物件。 在此範例中，`Label`和`Button`也會受到影響。 沒有任何方法的 VSM 標記附加到`Entry`來控制頁面上，而且沒有任何方法上的其他物件附加至這些其他物件，若要從另一個物件參考視覺狀態變更的 VSM 標記。

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>使用 Visual State Manager 調適性的版面配置

Xamarin.Forms，通常可以在直向或橫向的外觀比例，並在桌面上執行的 Xamarin.Forms 程式檢視手機上執行的應用程式可以作為許多不同的大小和外觀比例調整大小。 設計良好的應用程式可能會顯示其內容，以不同方式的這些不同的頁面或視窗板型規格。 

這項技術有時也稱為_調適性的版面配置_。 調適性的版面配置只牽涉到該應用程式的視覺效果，因為它會是理想的應用程式的 Visual State Manager。

顯示會影響應用程式的內容的按鈕的小型集合的應用程式是一個簡單的例子。 在直向模式中，這些按鈕可能會顯示在頁面頂端的水平資料列：

[![VSM 調適型版面配置：縱向]VSM 自動調整(vsm-images/VsmAdaptiveLayoutPortrait.png "版面配置-縱向")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

在橫向模式中，可能會移至一端，並顯示在資料行的按鈕陣列：

[![VSM 調適型版面配置：橫向]VSM 自動調整(vsm-images/VsmAdaptiveLayoutLandscape.png "版面配置-橫向")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

從頂端到底部，在通用 Windows 平台、 Android 和 iOS 上執行程式。

**VSM 調適性的版面配置**頁面[VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)範例會定義名為 「 縱向 」 和 「 景觀 」 的兩種視覺狀態為 「 OrientationStates"的群組。 （較複雜的方法可能會根據數個不同的頁面或視窗寬度。） 

VSM 標記就會發生在 XAML 檔案中的四個位置中。 `StackLayout`名為`mainStack`包含功能表和內容，也就是`Image`項目。 這`StackLayout`應該具有垂直方向，以直向模式和水平方向中以橫向模式：

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

內部`ScrollView`名為`menuScroll`並`StackLayout`名為`menuStack`實作按鈕的功能表。 這些配置的方向會相反的`mainStack`。 功能表應該在直向模式中的水平和垂直以橫向模式。

VSM 標記的第四個區段是隱含樣式，按鈕本身。 此標記會將`VerticalOptions`， `HorizontalOptions`，和`Margin`portait 和橫向方向的特定屬性。

程式碼後置檔案組`BindingContext`的屬性`menuStack`實作`Button`命令，並也將附加的處理常式`SizeChanged`頁面的事件：

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

`SizeChanged`處理常式會呼叫`VisualStateManager.GoToState`為兩個`StackLayout`並`ScrollView`項目，以及再執行迴圈的子系`menuStack`呼叫`VisualStateManager.GoToState`如`Button`項目。

似乎如同程式碼後置檔案可以處理方向變更更直接在 XAML 檔案中，設定項目的屬性，但 Visual State Manager 絕對是更結構化的方法。 所有視覺效果會保留在 XAML 檔案中，其中它們變得更輕鬆地檢查，維護和修改。

## <a name="visual-state-manager-with-xamarinuniversity"></a>Visual State Manager 與 Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin 3.0 視覺狀態管理員影片**

## <a name="related-links"></a>相關連結

- [VsmDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-vsmdemos)
