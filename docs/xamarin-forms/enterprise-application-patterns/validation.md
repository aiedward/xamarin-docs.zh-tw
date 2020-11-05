---
title: 企業應用程式中的驗證
description: 本章說明 eShopOnContainers 行動應用程式如何執行使用者輸入的驗證。 這包括指定驗證規則、觸發驗證，以及顯示驗證錯誤。
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8a5b7ecb0272543785c98bfff12bec7fcccf7fc8
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373935"
---
# <a name="validation-in-enterprise-apps"></a>企業應用程式中的驗證

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

接受使用者輸入的任何應用程式都應該確保輸入有效。 例如，應用程式可以檢查輸入是否只包含特定範圍中的字元、是否為特定長度，或符合特定的格式。 如果沒有驗證，使用者可以提供導致應用程式失敗的資料。 驗證會強制執行商務規則，並防止攻擊者插入惡意資料。

在模型視圖 ViewModel (MVVM) 模式的情況下，通常需要有一個視圖模型或模型來執行資料驗證，並將任何驗證錯誤告知視圖，讓使用者可以更正這些錯誤。 EShopOnContainers 行動應用程式會執行 view model 屬性的同步用戶端驗證，並藉由反白顯示包含無效資料的控制項來通知使用者任何驗證錯誤，以及顯示錯誤訊息，告知使用者資料為何無效。 圖6-1 顯示在 eShopOnContainers 行動應用程式中執行驗證時所牽涉到的類別。

[![EShopOnContainers 行動應用程式中的驗證類別](validation-images/validation.png)](validation-images/validation-large.png#lightbox "EShopOnContainers 行動應用程式中的驗證類別")

**圖 6-1** ： eShopOnContainers 行動裝置應用程式中的驗證類別

視圖模型屬性需要驗證的型別為 `ValidatableObject<T>` ，而且每個實例都會在 `ValidatableObject<T>` 其屬性中加入驗證規則 `Validations` 。 藉由呼叫實例的方法，從 view 模型叫用驗證 `Validate` `ValidatableObject<T>` ，以抓取驗證規則，並針對 `ValidatableObject<T>` `Value` 屬性執行。 任何驗證錯誤都會放入 `Errors` 實例的屬性 `ValidatableObject<T>` 中，而實例的 `IsValid` 屬性 `ValidatableObject<T>` 會更新，以指出驗證成功或失敗。

屬性變更通知是由類別提供 `ExtendedBindableObject` ，因此 [`Entry`](xref:Xamarin.Forms.Entry) 控制項可以系結至 `IsValid` `ValidatableObject<T>` 視圖模型類別中實例的屬性，以通知輸入的資料是否有效。

## <a name="specifying-validation-rules"></a>指定驗證規則

驗證規則的指定方式是建立衍生自介面的類別 `IValidationRule<T>` ，如下列程式碼範例所示：

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

這個介面指定驗證規則類別必須提供 `boolean` `Check` 用來執行必要驗證的方法，以及 `ValidationMessage` 值為驗證錯誤訊息的屬性，如果驗證失敗，就會顯示此錯誤訊息。

下列程式碼範例示範在 `IsNotNullOrEmptyRule<T>` eShopOnContainers 行動裝置 `LoginView` 應用程式中使用模擬服務時，用來執行使用者輸入使用者名稱和密碼驗證的驗證規則：

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

`Check`方法 `boolean` 會傳回，指出值引數是 `null` 、空白或只包含空白字元。

雖然 eShopOnContainers 的行動應用程式未使用，但下列程式碼範例會顯示驗證電子郵件地址的驗證規則：

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

`Check`方法 `boolean` 會傳回，指出值引數是否為有效的電子郵件地址。 這是藉由在函式中指定的正則運算式模式的第一次出現時搜尋值引數來達成 `Regex` 。 在輸入字串中找到正則運算式模式，可以藉由檢查物件屬性的值來判斷 `Match` `Success` 。

> [!NOTE]
> 屬性驗證有時可能會涉及相依屬性。 相依屬性的範例是當屬性 A 的有效值集合取決於已在屬性 B 中設定的特定值時。若要檢查屬性 A 的值是否為其中一個允許的值，則會包含抓取屬性 B 的值。此外，當屬性 B 的值變更時，屬性 A 必須重新驗證。

## <a name="adding-validation-rules-to-a-property"></a>將驗證規則加入至屬性

在 eShopOnContainers 行動裝置應用程式中，需要驗證的視圖模型屬性會宣告為類型 `ValidatableObject<T>` ，其中 `T` 是要驗證之資料的型別。 下列程式碼範例顯示兩種這類屬性的範例：

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

若要進行驗證，必須將驗證規則加入至 `Validations` 每個實例的 `ValidatableObject<T>` 集合，如下列程式碼範例所示：

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

這個方法會將 `IsNotNullOrEmptyRule<T>` 驗證規則加入至 `Validations` 每個實例的集合 `ValidatableObject<T>` ，並指定驗證規則的屬性值，以指定驗證失敗時所 `ValidationMessage` 要顯示的驗證錯誤訊息。

## <a name="triggering-validation"></a>觸發驗證

EShopOnContainers 行動應用程式中使用的驗證方法可以手動觸發屬性的驗證，並在屬性變更時自動觸發驗證。

### <a name="triggering-validation-manually"></a>手動觸發驗證

您可以針對視圖模型屬性手動觸發驗證。 例如，當使用者在使用模擬服務時，在 eShopOnContainers 行動裝置應用程式中按下的 **登** 入按鈕時，就會發生這種情況 `LoginView` 。 命令委派 `MockSignInAsync` 會在中呼叫方法，此方法 `LoginViewModel` 會藉由執行方法來叫用驗證 `Validate` ，如下列程式碼範例所示：

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

方法會在 `Validate` `LoginView` 每個實例上叫用 Validate 方法，以在上執行使用者輸入的使用者名稱和密碼驗證 `ValidatableObject<T>` 。 下列程式碼範例顯示來自類別的 Validate 方法 `ValidatableObject<T>` ：

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

這個方法會清除 `Errors` 集合，然後抓取已加入至物件集合的任何驗證規則 `Validations` 。 `Check`會執行每個已抓取驗證規則的方法，並將 `ValidationMessage` 無法驗證資料之任何驗證規則的屬性值新增至 `Errors` 實例的集合 `ValidatableObject<T>` 。 最後， `IsValid` 會設定屬性，並將其值傳回給呼叫的方法，以指出驗證成功或失敗。

### <a name="triggering-validation-when-properties-change"></a>屬性變更時觸發驗證

只要系結的屬性變更，也可以觸發驗證。 例如，當中的雙向系結 `LoginView` 設定 `UserName` 或 `Password` 屬性時，就會觸發驗證。 下列程式碼範例將示範如何進行這項操作：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

控制項系結 [`Entry`](xref:Xamarin.Forms.Entry) 至 `UserName.Value` 實例的屬性 `ValidatableObject<T>` ，而且控制項的 `Behaviors` 集合已 `EventToCommandBehavior` 加入實例。 此行為會執行 `ValidateUserNameCommand` ，以回應中的 [ `TextChanged` ] 事件引發 `Entry` ，這會在變更中的文字時引發 `Entry` 。 然後，委派會 `ValidateUserNameCommand` 執行 `ValidateUserName` 方法，該方法會 `Validate` 在實例上執行方法 `ValidatableObject<T>` 。 因此，每次使用者在控制項中輸入使用者名稱的字元 `Entry` 時，就會執行輸入資料的驗證。

如需行為的詳細資訊，請參閱 [執行行為](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing-behaviors)。

## <a name="displaying-validation-errors"></a>顯示驗證錯誤

EShopOnContainers 行動裝置應用程式會藉由反白顯示包含無效資料的控制項和紅線來通知使用者任何驗證錯誤，並顯示錯誤訊息，通知使用者資料在包含無效資料的控制項底下無效。 當更正不正確資料時，線條會變更為黑色，而錯誤訊息則會被移除。 圖6-2 顯示驗證錯誤時，eShopOnContainers 行動裝置應用程式中的 LoginView。

![在登入期間顯示驗證錯誤](validation-images/validation-login.png)

**圖6-2：** 在登入期間顯示驗證錯誤

### <a name="highlighting-a-control-that-contains-invalid-data"></a>反白顯示包含無效資料的控制項

`LineColorBehavior`附加的行為是用來反白顯示 [`Entry`](xref:Xamarin.Forms.Entry) 發生驗證錯誤的控制項。 下列程式碼範例顯示附加的 `LineColorBehavior` 行為如何附加至 `Entry` 控制項：

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

此 [`Entry`](xref:Xamarin.Forms.Entry) 控制項會使用明確的樣式，如下列程式碼範例所示：

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

此樣式會設定 `ApplyLineColor` `LineColor` `LineColorBehavior` 控制項上附加行為的和附加屬性 [`Entry`](xref:Xamarin.Forms.Entry) 。 如需樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

當附加屬性的值 `ApplyLineColor` 設定或變更時，附加的行為會 `LineColorBehavior` 執行 `OnApplyLineColorChanged` 方法，如下列程式碼範例所示：

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

這個方法的參數會提供附加行為之控制項的實例，以及附加屬性的舊值和新值 `ApplyLineColor` 。 `EntryLineColorEffect`如果附加的屬性為，則會將類別加入至控制項的 [`Effects`](xref:Xamarin.Forms.Element.Effects) 集合 `ApplyLineColor` `true` ，否則會從控制項的集合中移除 `Effects` 。 如需行為的詳細資訊，請參閱 [執行行為](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing-behaviors)。

`EntryLineColorEffect`類別的子類別 [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) ，如下列程式碼範例所示：

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

[`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)類別代表平臺獨立效果，可包裝平臺特定的內部效果。 這可簡化效果移除程序，因為對於平台特定效果，並不存在對類型資訊的編譯時間資訊存取。 會 `EntryLineColorEffect` 呼叫基類的函式，並傳入包含解析組名串連的參數，以及每個平臺特定效果類別所指定的唯一識別碼。

下列程式碼範例顯示 `eShopOnContainers.EntryLineColorEffect` iOS 的執行：

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

方法會抓取 `OnAttached` 控制項的原生控制項 Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) ，並藉由呼叫方法來更新線條色彩 `UpdateLineColor` 。 覆 `OnElementPropertyChanged` 寫會在 `Entry` 附加屬性變更時更新線條色彩 `LineColor` ，或是變更的屬性，藉以回應控制項上的可系結屬性變更 [`Height`](xref:Xamarin.Forms.VisualElement.Height) `Entry` 。 如需效果的詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

在控制項中輸入有效的資料時 [`Entry`](xref:Xamarin.Forms.Entry) ，它會在控制項底部套用黑色線條，以指出沒有任何驗證錯誤。 圖6-3 顯示此範例。

![表示沒有驗證錯誤的黑色線條](validation-images/validation-blackline.png)

**圖 6-3** ：指出沒有驗證錯誤的黑色線條

[`Entry`](xref:Xamarin.Forms.Entry)控制項也會在 [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) 其 [`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) 集合中加入。 下列程式碼範例顯示 `DataTrigger` ：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

這會 [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) 監視 `UserName.IsValid` 屬性，如果它的值 `false` 為，則會執行 [`Setter`](xref:Xamarin.Forms.Setter) ，將 `LineColor` 附加行為的附加屬性變更 `LineColorBehavior` 為紅色。 圖6-4 顯示此範例。

![指出驗證錯誤的紅線](validation-images/validation-redline.png)

**圖 6-4** ：指出驗證錯誤的紅線

[`Entry`](xref:Xamarin.Forms.Entry)當輸入的資料無效時，控制項中的線條會維持紅色，否則會變更為黑色，表示輸入的資料是有效的。

如需觸發程式的詳細資訊，請參閱 [觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

### <a name="displaying-error-messages"></a>顯示錯誤訊息

UI 會在其資料驗證失敗的每個控制項下方，于標籤控制項中顯示驗證錯誤訊息。 下列程式碼範例示範 [`Label`](xref:Xamarin.Forms.Label) 如果使用者未輸入有效的使用者名稱，則會顯示驗證錯誤訊息：

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

每個都會系結 [`Label`](xref:Xamarin.Forms.Label) 至要 `Errors` 驗證之視圖模型物件的屬性。 `Errors`屬性是由 `ValidatableObject<T>` 類別提供，且的型別為 `List<string>` 。 因為 `Errors` 屬性可能包含多個驗證錯誤，所以 `FirstValidationErrorConverter` 會使用實例來從集合中取出第一個錯誤以供顯示。

## <a name="summary"></a>總結

EShopOnContainers 行動應用程式會執行 view model 屬性的同步用戶端驗證，並藉由反白顯示包含無效資料的控制項來通知使用者任何驗證錯誤，以及顯示錯誤訊息，告知使用者資料為何無效。

視圖模型屬性需要驗證的型別為 `ValidatableObject<T>` ，而且每個實例都會在 `ValidatableObject<T>` 其屬性中加入驗證規則 `Validations` 。 藉由呼叫實例的方法，從 view 模型叫用驗證 `Validate` `ValidatableObject<T>` ，以抓取驗證規則，並針對 `ValidatableObject<T>` `Value` 屬性執行。 任何驗證錯誤都會放入 `Errors` 實例的屬性 `ValidatableObject<T>` 中，而實例的 `IsValid` 屬性 `ValidatableObject<T>` 會更新，以指出驗證成功或失敗。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)
