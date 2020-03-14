---
title: 企業應用程式中的驗證
description: 本章說明 eShopOnContainers 行動應用程式如何執行使用者輸入的驗證。 這包括指定驗證規則、觸發驗證，以及顯示驗證錯誤。
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: de5728710a408b8e0c7c68dc89c7e6484cbcc3ce
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305280"
---
# <a name="validation-in-enterprise-apps"></a>企業應用程式中的驗證

任何接受使用者輸入的應用程式都應確保輸入有效。 例如，應用程式可以檢查僅包含特定範圍中的字元、是否為特定長度，或符合特定格式的輸入。 如果沒有驗證，使用者可以提供會導致應用程式失敗的資料。 驗證會強制執行商務規則，並防止攻擊者插入惡意資料。

在模型 ViewModel （MVVM）模式的內容中，通常需要視圖模型或模型來執行資料驗證，並向視圖發出任何驗證錯誤，讓使用者可以加以更正。 EShopOnContainers 行動應用程式會執行 view model 屬性的同步用戶端驗證，並藉由反白顯示包含無效資料的控制項來通知使用者任何驗證錯誤，以及顯示通知使用者的錯誤訊息資料為何不正確。 圖6-1 顯示在 eShopOnContainers 行動裝置應用程式中執行驗證時所牽涉到的類別。

[![](validation-images/validation.png "Validation classes in the eShopOnContainers mobile app")](validation-images/validation-large.png#lightbox "Validation classes in the eShopOnContainers mobile app")

**圖 6-1**： eShopOnContainers 行動應用程式中的驗證類別

需要驗證的視圖模型屬性是 `ValidatableObject<T>`類型，而且每個 `ValidatableObject<T>` 實例都已將驗證規則新增至其 `Validations` 屬性。 藉由呼叫 `ValidatableObject<T>` 實例的 `Validate` 方法，從視圖模型叫用驗證，它會抓取驗證規則，並針對 `ValidatableObject<T>` `Value` 屬性加以執行。 任何驗證錯誤都會放入 `ValidatableObject<T>` 實例的 `Errors` 屬性中，並更新 `ValidatableObject<T>` 實例的 `IsValid` 屬性，以指出驗證成功或失敗。

屬性變更通知是由 `ExtendedBindableObject` 類別所提供，因此[`Entry`](xref:Xamarin.Forms.Entry)控制項可以系結至 view 模型類別中 `ValidatableObject<T>` 實例的 `IsValid` 屬性，以收到輸入的資料是否有效的通知。

## <a name="specifying-validation-rules"></a>指定驗證規則

驗證規則的指定方式是建立衍生自 `IValidationRule<T>` 介面的類別，如下列程式碼範例所示：

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

這個介面指定驗證規則類別必須提供用來執行必要驗證的 `boolean` `Check` 方法，以及一個 `ValidationMessage` 屬性，其值為驗證錯誤訊息，會在驗證失敗時顯示。

下列程式碼範例示範在 eShopOnContainers 行動應用程式中使用 mock 服務時，用來執行使用者在 `LoginView` 上所輸入之使用者名稱和密碼的驗證規則 `IsNotNullOrEmptyRule<T>`：

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

`Check` 方法會傳回 `boolean`，指出值引數是 `null`、空白，或只包含空白字元。

雖然 eShopOnContainers 行動應用程式未使用，但下列程式碼範例會顯示驗證電子郵件地址的驗證規則：

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

`Check` 方法會傳回 `boolean`，指出值引數是否為有效的電子郵件地址。 藉由在 `Regex` 的函式中指定的正則運算式模式第一次出現時，搜尋 value 引數來達成此目的。 是否在輸入字串中找到正則運算式模式，可以藉由檢查 `Match` 物件的 `Success` 屬性的值來判斷。

> [!NOTE]
> 屬性驗證有時會牽涉到相依屬性。 相依屬性的一個範例是，當屬性 A 的有效值集合取決於在屬性 B 中設定的特定值時。若要檢查屬性 A 的值是否為其中一個允許的值，會牽涉到抓取屬性 B 的值。此外，當屬性 B 的值變更時，屬性 A 就必須重新驗證。

## <a name="adding-validation-rules-to-a-property"></a>將驗證規則加入至屬性

在 eShopOnContainers 行動裝置應用程式中，需要驗證的視圖模型屬性會宣告為 `ValidatableObject<T>`類型，其中 `T` 是要驗證的資料類型。 下列程式碼範例顯示兩個這類屬性的範例：

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

若要進行驗證，必須將驗證規則新增至每個 `ValidatableObject<T>` 實例的 `Validations` 集合，如下列程式碼範例所示：

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

這個方法會將 `IsNotNullOrEmptyRule<T>` 驗證規則加入至每個 `ValidatableObject<T>` 實例的 `Validations` 集合，指定驗證規則 `ValidationMessage` 屬性的值，以指定驗證失敗時將顯示的驗證錯誤訊息。

## <a name="triggering-validation"></a>觸發驗證

EShopOnContainers mobile 應用程式中使用的驗證方法可以手動觸發屬性的驗證，並在屬性變更時自動觸發驗證。

### <a name="triggering-validation-manually"></a>手動觸發驗證

View model 屬性可以手動觸發驗證。 例如，當使用者在使用模擬服務時，于 eShopOnContainers 行動應用程式中按下 [**登**入] `LoginView`按鈕時，就會發生這種情況。 命令委派會呼叫 `LoginViewModel`中的 `MockSignInAsync` 方法，藉由執行 `Validate` 方法來叫用驗證，如下列程式碼範例所示：

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

`Validate` 方法藉由在每個 `ValidatableObject<T>` 實例上叫用 Validate 方法，來執行使用者在 `LoginView`上輸入的使用者名稱和密碼驗證。 下列程式碼範例顯示來自 `ValidatableObject<T>` 類別的 Validate 方法：

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

這個方法會清除 `Errors` 的集合，然後抓取已加入物件之 `Validations` 集合中的任何驗證規則。 會針對每個抓取的驗證規則執行 `Check` 方法，而且無法驗證資料之任何驗證規則的 `ValidationMessage` 屬性值都會加入至 `ValidatableObject<T>` 實例的 `Errors` 集合。 最後，會設定 `IsValid` 屬性，並將其值傳回給呼叫的方法，指出驗證成功或失敗。

### <a name="triggering-validation-when-properties-change"></a>屬性變更時觸發驗證

每當系結屬性變更時，也會觸發驗證。 例如，當 `LoginView` 中的雙向系結設定 `UserName` 或 `Password` 屬性時，就會觸發驗證。 下列程式碼範例將示範如何進行這項操作：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

[`Entry`](xref:Xamarin.Forms.Entry)控制項系結至 `ValidatableObject<T>` 實例的 `UserName.Value` 屬性，而控制項的 `Behaviors` 集合已加入 `EventToCommandBehavior` 實例。 這個行為會執行 `ValidateUserNameCommand`，以回應 `Entry`上的 [`TextChanged`] 事件引發，而 `Entry` 中的文字變更時，就會引發此問題。 接著，`ValidateUserNameCommand` 委派會執行 `ValidateUserName` 方法，這會在 `ValidatableObject<T>` 實例上執行 `Validate` 方法。 因此，每次使用者在使用者名稱的 `Entry` 控制項中輸入字元時，就會執行輸入資料的驗證。

如需行為的詳細資訊，請參閱[執行行為](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>顯示驗證錯誤

EShopOnContainers 行動應用程式會藉由反白顯示包含無效資料的控制項來通知使用者任何驗證錯誤，並顯示一則錯誤訊息，通知使用者在包含下列內容的控制項底下，資料不正確原因不正確資料。 更正不正確資料時，線條會變更為黑色，並移除錯誤訊息。 圖6-2 顯示驗證錯誤時，eShopOnContainers 行動應用程式中的 LoginView。

![](validation-images/validation-login.png "Displaying validation errors during login")

**圖6-2：** 在登入期間顯示驗證錯誤

### <a name="highlighting-a-control-that-contains-invalid-data"></a>反白顯示包含無效資料的控制項

`LineColorBehavior` 附加的行為可用來反白顯示驗證錯誤發生所在的[`Entry`](xref:Xamarin.Forms.Entry)控制項。 下列程式碼範例示範如何將 `LineColorBehavior` 附加行為附加至 `Entry` 控制項：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

[`Entry`](xref:Xamarin.Forms.Entry)控制項會使用明確的樣式，如下列程式碼範例所示：

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

此樣式會設定[`Entry`](xref:Xamarin.Forms.Entry)控制項上 `LineColorBehavior` 附加行為的 `ApplyLineColor` 和 `LineColor` 附加屬性。 如需樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

當 `ApplyLineColor` 附加屬性的值設定為或變更時，`LineColorBehavior` 附加的行為會執行 `OnApplyLineColorChanged` 方法，如下列程式碼範例所示：

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

這個方法的參數會提供行為所附加的控制項實例，以及 `ApplyLineColor` 附加屬性的舊值和新值。 如果 `true``ApplyLineColor` 附加屬性，則會將 `EntryLineColorEffect` 類別加入控制項的[`Effects`](xref:Xamarin.Forms.Element.Effects)集合中，否則會從控制項的 `Effects` 集合中移除。 如需行為的詳細資訊，請參閱[執行行為](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

`EntryLineColorEffect` 會將[`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)類別子類別化，如下列程式碼範例所示：

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

[`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)類別代表以平臺為獨立的效果，它會包裝平臺特定的內部效果。 這可簡化效果移除程序，因為對於平台特定效果，並不存在對類型資訊的編譯時間資訊存取。 `EntryLineColorEffect` 會呼叫基類處理常式，傳入包含解析組名串連的參數，以及在每個平臺特定效果類別上指定的唯一識別碼。

下列程式碼範例顯示適用于 iOS 的 `eShopOnContainers.EntryLineColorEffect` 執行：

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

`OnAttached` 方法會抓取 Xamarin [`Entry`](xref:Xamarin.Forms.Entry)控制項的原生控制項，並藉由呼叫 `UpdateLineColor` 方法來更新線條色彩。 `OnElementPropertyChanged` 覆寫會藉由更新附加的 `LineColor` 屬性變更時的線條色彩，或 `Entry` 變更的[`Height`](xref:Xamarin.Forms.VisualElement.Height)屬性，來回應 `Entry` 控制項上可系結的屬性變更。 如需效果的詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

在[`Entry`](xref:Xamarin.Forms.Entry)控制項中輸入有效的資料時，它會將黑色行套用到控制項的底部，以指出沒有任何驗證錯誤。 圖6-3 顯示這種情況的範例。

![](validation-images/validation-blackline.png "Black line indicating no validation error")

**圖 6-3**：指出沒有驗證錯誤的黑色線條

[`Entry`](xref:Xamarin.Forms.Entry)控制項也已將[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)新增至其[`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)集合。 下列程式碼範例顯示 `DataTrigger`：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

這個[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)會監視 `UserName.IsValid` 屬性，如果它的值變成 `false`，它就會執行[`Setter`](xref:Xamarin.Forms.Setter)，這會將 `LineColor` 附加行為的 `LineColorBehavior` 附加屬性變更為紅色。 圖6-4 顯示這種情況的範例。

![](validation-images/validation-redline.png "Red line indicating validation error")

**圖 6-4**：指出驗證錯誤的紅線

當輸入的資料無效時， [`Entry`](xref:Xamarin.Forms.Entry)控制項中的那一行會保持紅色，否則會變更為黑色，表示輸入的資料是有效的。

如需觸發程式的詳細資訊，請參閱[觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

### <a name="displaying-error-messages"></a>顯示錯誤訊息

UI 會在其資料驗證失敗的每個控制項底下的標籤控制項中顯示驗證錯誤訊息。 下列程式碼範例顯示當使用者未輸入有效的使用者名稱時，會顯示驗證錯誤訊息的[`Label`](xref:Xamarin.Forms.Label) ：

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

每個[`Label`](xref:Xamarin.Forms.Label)都會系結至要驗證之視圖模型物件的 `Errors` 屬性。 `Errors` 屬性是由 `ValidatableObject<T>` 類別所提供，且屬於類型 `List<string>`。 因為 `Errors` 屬性可以包含多個驗證錯誤，所以 `FirstValidationErrorConverter` 實例會用來從集合中取出第一個錯誤，以供顯示。

## <a name="summary"></a>摘要

EShopOnContainers 行動應用程式會執行 view model 屬性的同步用戶端驗證，並藉由反白顯示包含無效資料的控制項來通知使用者任何驗證錯誤，以及顯示通知使用者的錯誤訊息資料不正確原因。

需要驗證的視圖模型屬性是 `ValidatableObject<T>`類型，而且每個 `ValidatableObject<T>` 實例都已將驗證規則新增至其 `Validations` 屬性。 藉由呼叫 `ValidatableObject<T>` 實例的 `Validate` 方法，從視圖模型叫用驗證，它會抓取驗證規則，並針對 `ValidatableObject<T>` `Value` 屬性加以執行。 任何驗證錯誤都會放入 `ValidatableObject<T>`實例的 `Errors` 屬性中，並更新 `ValidatableObject<T>` 實例的 `IsValid` 屬性，以指出驗證成功或失敗。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
