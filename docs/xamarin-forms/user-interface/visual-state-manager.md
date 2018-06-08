---
title: Xamarin.Forms 視覺狀態管理員
description: 使用 Visual State Manager，來進行 XAML 項目，根據從程式碼設定的視覺狀態變更。
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: 3c0330d8d6d07112350db007d0500d57c236dc24
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848020"
---
# <a name="the-xamarinforms-visual-state-manager"></a>Xamarin.Forms 視覺狀態管理員

_使用 Visual State Manager，來進行 XAML 項目，根據從程式碼設定的視覺狀態變更。_

視覺狀態管理員 (VSM) 是在 Xamarin.Forms 3.0 新功能。 VSM 提供結構化的方式來進行使用者介面中的視覺變更，從程式碼。 在大部分情況下，應用程式的使用者介面定義在 XAML 中，而且此 XAML 包含描述 Visual State Manager 如何影響使用者介面的視覺效果的標記。

VSM 介紹的概念_視覺狀態_。 Xamarin.Forms 檢視這類`Button`可以根據其基礎的狀態有數個不同的視覺外觀&mdash;它已停用，或按下，還是具有輸入焦點。 這些是按鈕的狀態。

視覺狀態的收集_視覺狀態群組_。 視覺狀態群組中的所有視覺狀態會互斥。 視覺狀態和視覺狀態群組會識別簡單的文字字串。

Xamarin.Forms Visual State Manager 定義一個名為"CommonStates"三個視覺狀態的視覺狀態群組：

- "Normal"
- 「 停用 」
- 「 已取得焦點 」

此視覺狀態群組支援所有的類別衍生自[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)，這是基底類別[ `View` ](xref:Xamarin.Forms.View)和[ `Page` ](xref:Xamarin.Forms.Page)。 

您也可以定義自己的視覺狀態群組及視覺狀態為這篇文章將示範。

> [!NOTE]
> Xamarin.Forms 熟悉的開發人員[觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)了解，觸發程序也可以變更檢視的屬性或事件的引發中變更的使用者介面中的視覺效果。 不過，使用觸發程序來處理這些變更的各種組合可能會變得很容易混淆。 在過去，Visual State Manager 中引進了 Windows XAML 為基礎的環境，以減少混淆所產生的視覺狀態的組合。 使用 VSM 視覺狀態群組內的視覺狀態一律是互斥的。 在任何時候，每個群組中的只能有一個狀態會是目前的狀態。

## <a name="the-common-states"></a>常見的狀態

Visual State Manager 可讓您在您可以變更檢視的視覺外觀，如果檢視已正常的或停用，或是具有輸入的焦點的 XAML 檔案中包含區段。 這些值稱為_常見狀態_。

例如，假設您有`Entry`在頁面上，檢視和您想要的視覺外觀`Entry`變更如下：

- `Entry`應有粉紅色背景時`Entry`已停用。
- `Entry`通常應該有淺綠色背景。
- `Entry`應該設正常高度的兩次來展開它具有輸入焦點時。

您可以將 VSM 標記為個別的檢視，或者您可以將它定義於樣式中適用於多個檢視。 接下來兩節則描述這些方法。

### <a name="vsm-markup-on-a-view"></a>在檢視上 VSM 標記

要附加至 VSM 標記`Entry`檢視，請先`Entry`成開始和結束標記：

```xaml
<Entry FontSize="18">

</Entry>
```

它已指定明確的字型大小，因為其中一個狀態將會使用`FontSize`屬性中的文字大小加倍`Entry`。

接下來，插入`VisualStateManager.VisualStateGroups`之間這些標記的標記：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) 是附加的繫結屬性所定義[ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager)類別。 (如需有關附加的繫結屬性的詳細資訊，請參閱文章[附加屬性](~/xamarin-forms/xaml/attached-properties.md)。)這是如何`VisualStateGroups`屬性附加至`Entry`物件。

`VisualStateGroups`屬性屬於型別[ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList)，這是集合的[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)物件。 內`VisualStateManager.VisualStateGroups`標記，插入對`VisualStateGroup`標記每一組您想要包含的視覺狀態：

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

請注意，`VisualStateGroup`標記有`x:Name`屬性，指出群組的名稱。 `VisualStateGroup`類別會定義`Name`屬性，您可以改為使用：

```xaml
<VisualStateGroup Name="CommonStates">
```

您可以使用`x:Name`或`Name`但非兩者相同的項目中。

`VisualStateGroup`類別會定義名為的屬性[ `States` ](xref:Xamarin.Forms.VisualStateGroup.States)，這是集合的[ `VisualState` ](xref:Xamarin.Forms.VisualState)物件。 `States` 是_內容屬性_的`VisualStateGroups`因此您可以將包含`VisualState`標記之間直接`VisualStateGroup`標記。 (內容屬性會包含本文中討論[基本 XAML 語法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties)。)

下一個步驟是要包含該群組中成對的每個視覺狀態的標記。 這些也可以使用來識別`x:Name`或`Name`:

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

`VisualState` 定義屬性，名為[ `Setters` ](xref:Xamarin.Forms.VisualState.Setters)，這是集合的[ `Setter` ](xref:Xamarin.Forms.Setter)物件。 這些都是相同`Setter`使用中的物件[ `Style` ](xref:Xamarin.Forms.Style)物件。

`Setters` 是_不_的內容屬性`VisualState`，因此必須包含的屬性項目標記`Setters`屬性：

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

您現在可以插入一或多個`Setter`每對之間的物件`Setters`標記。 這些是`Setter`定義稍早所述的視覺狀態的物件：

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

每個`Setter`目前處於該狀態時，標記表示特定屬性的值。 所參考的任何屬性`Setter`物件必須在備份可繫結的屬性。

如下所示的標記是的基礎**檢視上的 VSM**頁面**[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** 範例程式。 此頁面包含三個`Entry`檢視中，但只有第二個已連接到它的 VSM 標記：

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

請注意，第二個`Entry`還有`DataTrigger`一部分其`Trigger`集合。 這會導致`Entry`停用，直到項目中輸入的第三個`Entry`。 以下是在啟動 iOS、 Android 和通用 Windows 平台 (UWP) 上執行的頁面：

[![在檢視上 VSM： 停用](vsm-images/VsmOnViewDisabled.png "VSM-已停用檢視上")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

目前的視覺狀態的動作為"Disabled"所以第二個背景`Entry`為 iOS 和 Android 的螢幕上粉紅色。 UWP 實作`Entry`不允許設定背景色彩的時機`Entry`已停用。 

當您輸入一些文字到第三個`Entry`，第二個`Entry`切換至 「 標準 」 狀態，以及背景現在是 lime:

[![在檢視上的 VSM: Normal](vsm-images/VsmOnViewNormal.png "VSM 上檢視-一般")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

當您觸控第二個`Entry`，它會取得輸入的焦點。 它會切換成 「 集中 」 狀態，並展開成高度的兩次：

[![在檢視上的 VSM： 已取得焦點](vsm-images/VsmOnViewFocused.png "VSM 上檢視-已取得焦點")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

請注意，`Entry`不會保留淺綠色背景，取得輸入的焦點時發生。 視覺狀態間切換的 Visual State Manager，先前的狀態所設定的屬性是未設定。 請注意視覺狀態會互斥。 "Normal"狀態並不表示僅`Entry`已啟用。 這表示`Entry`已啟用，而且沒有輸入的焦點。 

如果您想`Entry`有淺綠色背景 「 集中 」 狀態中，加入另一個`Setter`對該視覺狀態：

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

為了讓這些`Setter`物件正常運作，`VisualStateGroup`必須包含`VisualState`該群組中的所有狀態的物件。 如果沒有任何視覺狀態`Setter`物件，包括它仍為空白的標記：

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>視覺狀態管理員標記樣式

通常是為了共用兩個或多個檢視之間的相同 Visual State Manager 標記。 在此情況下，您會想要將標記放在`Style`定義。

以下是現有隱含`Style`如`Entry`中的項目**VSM 上檢視**頁面：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

新增`Setter`標籤`VisualStateManager.VisualStateGroups`附加可繫結屬性：

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

內容屬性`Setter`是`Value`，因此值`Value`可以直接在這些標記中指定屬性。 屬性為類型`VisualStateGroupList`:

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

VSM 標記的其餘部分是與之前相同。

以下是**樣式 VSM**顯示完整的 VSM 標記頁面：

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

現在所有`Entry`此頁面上的檢視回應其視覺狀態以相同的方式。 另請注意 「 集中 」 狀態，現在包含第二個`Setter`提供每個`Entry`lime 也背景時，它具有輸入焦點：

[![樣式 VSM](vsm-images/VsmInStyle.png "VSM 樣式")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>定義您自己的視覺狀態

每個類別衍生自`VisualElement`支援三個一般狀態 「 正常 」、 「 已取得焦點 」，和 「 停用 」。 就內部而言， [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs)類別會偵測它會成為啟用或停用，或已取得焦點或未取得焦點，並呼叫靜態[ `VisualStateManager.GoToState` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualStateManager.GoToState/p/Xamarin.Forms.VisualElement/System.String/)方法：

```csharp
VisualStateManager.GoToState(this, "Focused");
```

這是只 Visual State Manager 的程式碼中，您會發現`VisualElement`類別。 因為`GoToState`呼叫每個物件根據每個類別衍生自`VisualElement`，您可以使用 Visual State Manager，搭配任何`VisualElement`來回應這些變更的物件。

有趣的是，視覺狀態群組 」 CommonStates 」 的名稱中未明確參考`VisualElement`。 群組名稱不是 Visual State Manager 的 API 的一部分。 其中一個內兩個目前為止所顯示的範例程式，您可以變更為任何其他帳戶，從 「 CommonStates 」 群組的名稱和程式仍可運作。 群組名稱是只是該群組中的狀態的一般描述。 隱含地了解任何群組中的視覺狀態是互斥： 一種狀態，只能有一個狀態是目前在任何時間。

如果您想要實作您自己的視覺狀態，您必須呼叫`VisualStateManager.GoToState`從程式碼。 通常，您要進行此呼叫，從您的網頁類別的程式碼後置檔案。

**VSM 驗證**頁面**[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** 範例示範如何使用 Visual State Manager 與輸入驗證。 XAML 檔案包含兩個`Label`項目， `Entry`，和`Button`:

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

VSM 標記會附加至第二個`Label`(名為`helpLabel`) 和`Button`(名為`submitButton`)。 有兩個互斥狀態，名為 「 有效 」 和 「 無效 」。 請注意，每兩個 「 ValidationState"群組包含`VisualState`雖然是空的每個案例中的其中一個標記為 「 有效 」 和 「 無效 」。 

如果`Entry`不包含有效的電話號碼，則目前的狀態是 「 無效 」，因此第二個`Label`是可見和`Button`已停用：

[![VSM 驗證： 無效的狀態](vsm-images/VsmValidationInvalid.png "VSM 驗證為無效")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

當您輸入有效的電話號碼時，然後，目前狀態會變為 「 有效 」。 第二個`Entry`消失而`Button`現在已啟用：

[![VSM 驗證： 無效的狀態](vsm-images/VsmValidationValid.png "VSM 驗證-有效")](vsm-images/VsmValidationValid-Large.png#lightbox)

程式碼後置檔案是處理 reponsible`TextChanged`事件從`Entry`。 處理常式會使用規則運算式來判斷輸入的字串是否為有效。 名為程式碼後置檔案中的方法`GoToState`呼叫靜態`VisualStateManager.GoToState`兩個方法`helpLabel`和`submitButton`:

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

也請注意`GoToState`方法從初始化狀態的建構函式呼叫。 有應該一律會是目前的狀態。 但不是在程式碼中有任何參考的視覺狀態群組名稱，雖然它參考在 XAML 中為"ValidationStates"為了釐清。 

請注意這些視覺狀態，以及呼叫程式碼後置檔案必須採取的每個物件在受影響的頁面上的帳戶`VisualStateManager.GoToState`的每個物件。 在此範例中，就只有兩個物件 (`Label`和`Button`)，但是也可能是數個多個。

您可能會懷疑： 如果程式碼後置檔案都必須參考會受到這些視覺狀態的頁面上的每個物件，為何無法程式碼後置檔案只需存取的物件直接？ 一定可以。 不過，使用 VSM 的優點是您可以控制如何視覺項目可在 XAML，會將所有 UI 設計在單一位置中的不同狀態的回應。 這可以避免設定視覺外觀的直接從程式碼後置存取視覺項目。

它可能會想要考慮衍生自`Entry`以及可能定義您可以設定外部驗證函式的屬性。 類別衍生自`Entry`接著便可以呼叫`VisualStateManager.GoToState`方法。 此配置會正常運作，但僅限於`Entry`了不同的視覺狀態所影響的唯一物件。 在此範例中，`Label`和`Button`也會受到影響。 沒有任何方法的 VSM 標記附加至`Entry`至 VSM 標記從另一個物件參考的視覺狀態變更的其他物件附加至這些控制項在頁面上，且無法在其他物件。

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>使用 Visual State Manager，自動調整的版面配置

在電話上執行的應用程式通常可以在直向或橫向長寬比及在桌面上執行的 Xamarin.Forms 程式檢視 Xamarin.Forms 可以採用許多不同的大小和外觀比例調整大小。 設計良好的應用程式可能會顯示其內容以不同的方式為這些不同的頁面或視窗表單係數。 

這項技術稱為_自動調整的版面配置_。 自動調整的版面配置只牽涉到程式的視覺效果，因為它是理想的應用程式的 Visual State Manager。

簡單的範例是顯示會影響應用程式的內容的按鈕的小型集合的應用程式。 在直向模式中，這些按鈕可能會顯示在頁面頂端的水平列中：

[![VSM 調整版面配置： 直向](vsm-images/VsmAdaptiveLayoutPortrait.png "調整版面配置 VSM-直向")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

以橫向模式的按鈕陣列可能會移到某一端，並顯示在資料行：

[![VSM 調整版面配置： 橫向](vsm-images/VsmAdaptiveLayoutLandscape.png "VSM 調整版面配置-橫向")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

從頂端到底部，在通用 Windows 平台、 Android 和 iOS 上執行程式。

**VSM 調整版面配置**頁面[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)範例會定義名為"OrientationStates"，有兩個名為 「 縱向 」 和 「 橫印 」 的視覺狀態的群組。 （更複雜的方式可能會根據數個不同的頁面或視窗寬度。） 

VSM 標記就會發生在 XAML 檔案中的四個位置。 `StackLayout`名為`mainStack`包含功能表和內容，也就是`Image`項目。 這`StackLayout`應有直向模式中的垂直方向，以及以橫向模式的水平方向：

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

內部`ScrollView`名為`menuScroll`和`StackLayout`名為`menuStack`實作按鈕的功能表。 這些配置的方向會相反的`mainStack`。 功能表應該在直向模式中水平及垂直以橫向模式。

VSM 標記的第四個區段處於隱含本身，按鈕的樣式。 這個標記設定`VerticalOptions`， `HorizontalOptions`，和`Margin`portait 和橫印方向的特定屬性。

程式碼後置檔案集`BindingContext`屬性`menuStack`實作`Button`命令，以及也會將附加至處理常式`SizeChanged`頁面的事件：

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

`SizeChanged`處理常式呼叫`VisualStateManager.GoToState`兩`StackLayout`和`ScrollView`項目，然後迴圈的子系`menuStack`呼叫`VisualStateManager.GoToState`如`Button`項目。

看起來像是程式碼後置檔案可以處理方向的變更，更直接的設定項目的屬性，在 XAML 檔案中，但 Visual State Manager 明確更結構化的方法。 所有視覺效果會保留在 XAML 檔案，其中它們變得更輕鬆地檢查，維護及修改。

## <a name="visual-state-manager-with-xamarinuniversity"></a>與 Xamarin.University visual State Manager

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Xamarin.Forms 3.0 Visual State Manager，依[Xamarin 大學](https://university.xamarin.com/)**

## <a name="related-links"></a>相關連結

- [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)

