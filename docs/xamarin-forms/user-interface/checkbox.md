---
標題： " Xamarin.Forms checkbox" 描述： Xamarin.Forms 核取方塊是可核取或空白的按鈕類型。 核取核取方塊時，會將其視為開啟。 如果核取方塊是空的，則會被視為 off。
ms. 生產： xamarin assetid： B8B9268B-BCB8-42B9-B08C-C0F22C137238 ms. 技術： xamarin-forms author： davidbritch ms. author： dabritch ms. date： 06/11/2019 no loc：
- "Xamarin.Forms"
- "Xamarin.Essentials"

---

# <a name="no-locxamarinforms-checkbox"></a>Xamarin.Forms 相應

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)

Xamarin.Forms `CheckBox` 是可以核取或空白的按鈕類型。 核取核取方塊時，會將其視為開啟。 如果核取方塊是空的，則會被視為 off。

`CheckBox` 定義 `bool` 名為的屬性 `IsChecked` ，指出是否已 `CheckBox` 核取。 這個屬性也可以由物件支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它可以設定樣式，也可以是資料系結的目標。

> [!NOTE]
> 可系結屬性的預設系結 `IsChecked` 模式為 [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) 。

`CheckBox` 定義 `CheckedChanged` 屬性變更時所引發的事件 `IsChecked` ，不論是透過使用者操作或應用程式設定 `IsChecked` 屬性。 `CheckedChangedEventArgs`事件隨附的物件 `CheckedChanged` 具有名為的單一屬性 `Value` ，類型為 `bool` 。 引發事件時，屬性的值 `Value` 會設定為屬性的新值 `IsChecked` 。

## <a name="create-a-checkbox"></a>建立核取方塊

下列範例顯示如何 `CheckBox` 在 XAML 中具現化：

```xaml
<CheckBox />
```

此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![IOS 和 Android 上空白核取方塊的螢幕擷取畫面](checkbox-images/checkbox-empty.png "空白核取方塊")

依預設， `CheckBox` 是空的。 您 `CheckBox` 可以藉由使用者操作來檢查，或藉由將 `IsChecked` 屬性設定為 `true` ：

```xaml
<CheckBox IsChecked="true" />
```

此 XAML 會產生下列螢幕擷取畫面中顯示的外觀：

![IOS 和 Android 上核取核取方塊的螢幕擷取畫面](checkbox-images/checkbox-checked.png "核取核取方塊")

或者，您 `CheckBox` 可以在程式碼中建立：

```csharp
CheckBox checkBox = new CheckBox { IsChecked = true };
```

## <a name="respond-to-a-checkbox-changing-state"></a>回應核取方塊變更狀態

當 `IsChecked` 屬性變更時，無論是透過使用者操作，或是當應用程式設定 `IsChecked` 屬性時，都會 `CheckedChanged` 引發事件。 您可以註冊這個事件的事件處理常式，以回應變更：

```xaml
<CheckBox CheckedChanged="OnCheckBoxCheckedChanged" />
```

程式碼後端檔案包含事件的處理常式 `CheckedChanged` ：

```csharp
void OnCheckBoxCheckedChanged(object sender, CheckedChangedEventArgs e)
{
    // Perform required operation after examining e.Value
}
```

`sender`引數是 `CheckBox` 此事件的負責任。 您可以使用這個來存取 `CheckBox` 物件，或區別多個 `CheckBox` 共用相同 `CheckedChanged` 事件處理常式的物件。

或者，事件的事件處理常式 `CheckedChanged` 可以在程式碼中註冊：

```csharp
CheckBox checkBox = new CheckBox { ... };
checkBox.CheckedChanged += (sender, e) =>
{
    // Perform required operation after examining e.Value
};
```

## <a name="data-bind-a-checkbox"></a>資料系結核取方塊

您 `CheckedChanged` 可以使用資料系結和觸發程式來回應所檢查的或空白，以消除事件處理常式 `CheckBox` ：

```xaml
<CheckBox x:Name="checkBox" />
<Label Text="Lorem ipsum dolor sit amet, elit rutrum, enim hendrerit augue vitae praesent sed non, lorem aenean quis praesent pede.">
    <Label.Triggers>
        <DataTrigger TargetType="Label"
                     Binding="{Binding Source={x:Reference checkBox}, Path=IsChecked}"
                     Value="true">
            <Setter Property="FontAttributes"
                    Value="Italic, Bold" />
            <Setter Property="FontSize"
                    Value="Large" />
        </DataTrigger>
    </Label.Triggers>
</Label>
```

在此範例中，會在 [`Label`](xref:Xamarin.Forms.Label) 資料觸發程式中使用系結運算式來監視的 `IsChecked` 屬性 `CheckBox` 。 當這個屬性為時，就是 `true` `FontAttributes` 變更的和 `FontSize` 屬性 `Label` 。 當 `IsChecked` 屬性回到時 `false` ，的 `FontAttributes` 和 `FontSize` 屬性 `Label` 會重設為其初始狀態。

在以下螢幕擷取畫面中，iOS 螢幕擷取畫面 [`Label`](xref:Xamarin.Forms.Label) 會顯示當為空白時的格式 `CheckBox` ，而 Android 螢幕擷取畫面則顯示 `Label` 核取時的格式 `CheckBox` ：

[![IOS 和 Android 上的資料系結核取方塊的螢幕擷取畫面](checkbox-images/checkbox-databinding.png "資料系結核取方塊")](checkbox-images/checkbox-databinding-large.png#lightbox "資料系結核取方塊")

如需觸發程式的詳細資訊，請參閱[ Xamarin.Forms 觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

## <a name="disable-a-checkbox"></a>停用核取方塊

有時候，應用程式會進入檢查不是有效作業的狀態 `CheckBox` 。 在這種情況下，您 `CheckBox` 可以藉由將其 `IsEnabled` 屬性設定為來停用 `false` 。

## <a name="checkbox-appearance"></a>CheckBox 外觀

除了 `CheckBox` 繼承自類別的屬性之外 [`View`](xref:Xamarin.Forms.View) ， `CheckBox` 也會定義將 `Color` 其色彩設定為的屬性 [`Color`](xref:Xamarin.Forms.Color) ：

```xaml
<CheckBox Color="Red" />
```

下列螢幕擷取畫面顯示一系列已核取的 `CheckBox` 物件，其中每個物件的 `Color` 屬性設定為不同的 [`Color`](xref:Xamarin.Forms.Color) ：

![IOS 和 Android 上彩色核取方塊的螢幕擷取畫面](checkbox-images/checkbox-colors.png "彩色核取方塊")

## <a name="checkbox-visual-states"></a>核取方塊視覺狀態

`CheckBox` 具有 `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可用來在檢查時起始對的視覺效果變更 `CheckBox` 。

下列 XAML 範例顯示如何定義狀態的視覺狀態 `IsChecked` ：

```xaml
<CheckBox ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Red" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="IsChecked">
                <VisualState.Setters>
                    <Setter Property="Color"
                            Value="Green" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</CheckBox>
```

在此範例中， `IsChecked` [`VisualState`](xref:Xamarin.Forms.VisualState) 會指定當核取時 `CheckBox` ，其 `Color` 屬性會設定為綠色。 `Normal` `VisualState` 指定當 `CheckBox` 處於正常狀態時，其 `Color` 屬性會設定為紅色。 因此，整體效果是在 `CheckBox` 空白時為紅色，而在檢查時為綠色。

如需視覺狀態的詳細資訊，請參閱[ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [ (範例) 的核取方塊示範 ](/samples/xamarin/xamarin-forms-samples/userinterface-checkboxdemos/)
- [Xamarin.Forms 觸發器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)