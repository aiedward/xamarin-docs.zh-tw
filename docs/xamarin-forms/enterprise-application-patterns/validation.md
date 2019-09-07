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
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760156"
---
# <a name="validation-in-enterprise-apps"></a>企業應用程式中的驗證

任何接受使用者輸入的應用程式都應確保輸入有效。 例如，應用程式可以檢查僅包含特定範圍中的字元、是否為特定長度，或符合特定格式的輸入。 如果沒有驗證，使用者可以提供會導致應用程式失敗的資料。 驗證會強制執行商務規則，並防止攻擊者插入惡意資料。

在模型 ViewModel （MVVM）模式的內容中，通常需要視圖模型或模型來執行資料驗證，並向視圖發出任何驗證錯誤，讓使用者可以加以更正。 EShopOnContainers 行動應用程式會執行 view model 屬性的同步用戶端驗證，並藉由反白顯示包含無效資料的控制項來通知使用者任何驗證錯誤，以及顯示通知使用者的錯誤訊息資料為何不正確。 圖6-1 顯示在 eShopOnContainers 行動裝置應用程式中執行驗證時所牽涉到的類別。

[EShopOnContainers 行動應用程式中的驗證類別![(validation-images/validation.png "")] ](validation-images/validation-large.png#lightbox "EShopOnContainers 行動應用程式中的驗證類別")

**圖 6-1**：EShopOnContainers 行動應用程式中的驗證類別

需要驗證的視圖模型屬性屬於類型`ValidatableObject<T>`，而且每個`ValidatableObject<T>`實例都已將驗證規則加入其`Validations`屬性中。 藉`Validate`由呼叫`ValidatableObject<T>`實例的方法，從視圖模型叫用驗證，它會抓取驗證規則`ValidatableObject<T>` `Value` ，並針對屬性執行。 任何驗證`Errors`錯誤都會放入`ValidatableObject<T>`實例的屬性中， `ValidatableObject<T>`而實例的`IsValid`屬性則會更新，以指出驗證成功或失敗。

屬性變更通知是`ExtendedBindableObject`由類別所提供， [`Entry`](xref:Xamarin.Forms.Entry)因此控制項可以系結至 view 模型`IsValid`類別中`ValidatableObject<T>`實例的屬性，以收到輸入的資料是否有效的通知。

## <a name="specifying-validation-rules"></a>指定驗證規則

藉由建立衍生自`IValidationRule<T>`介面的類別來指定驗證規則，如下列程式碼範例所示：

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

這個介面指定驗證規則類別必須提供`boolean` `Check`用來執行必要驗證的方法，以及一個`ValidationMessage`屬性，其值為將顯示的驗證錯誤訊息驗證失敗。

下列程式碼範例`IsNotNullOrEmptyRule<T>`示範在使用 eShopOnContainers 行動應用程式中的模擬服務`LoginView`時，用來執行使用者在上輸入之使用者名稱和密碼的驗證規則：

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

方法會傳回， `null`指出值引數為、空白，或只包含空白字元。 `boolean` `Check`

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

`Check`方法會傳回，指出值引數是否為有效`boolean`的電子郵件地址。 這是藉由在函式中`Regex`指定的正則運算式模式第一次出現時，搜尋 value 引數來達成。 是否在輸入字串中找到正則運算式模式，可以藉由檢查`Match` `Success`物件屬性的值來判斷。

> [!NOTE]
> 屬性驗證有時會牽涉到相依屬性。 相依屬性的一個範例是，當屬性 A 的有效值集合取決於在屬性 B 中設定的特定值時。若要檢查屬性 A 的值是否為其中一個允許的值，會牽涉到抓取屬性 B 的值。此外，當屬性 B 的值變更時，屬性 A 就必須重新驗證。

## <a name="adding-validation-rules-to-a-property"></a>將驗證規則加入至屬性

在 eShopOnContainers 行動應用程式中，需要驗證的 view model 屬性會宣告為型`ValidatableObject<T>`別，其中`T`是要驗證之資料的型別。 下列程式碼範例顯示兩個這類屬性的範例：

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

若要進行驗證，必須將驗證規則加入至每`Validations`個`ValidatableObject<T>`實例的集合，如下列程式碼範例所示：

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

這個方法會將`IsNotNullOrEmptyRule<T>`驗證規則新增至`Validations`每個`ValidatableObject<T>`實例的集合`ValidationMessage` ，並指定驗證規則之屬性的值，以指定將顯示的驗證錯誤訊息。驗證失敗。

## <a name="triggering-validation"></a>觸發驗證

EShopOnContainers mobile 應用程式中使用的驗證方法可以手動觸發屬性的驗證，並在屬性變更時自動觸發驗證。

### <a name="triggering-validation-manually"></a>手動觸發驗證

View model 屬性可以手動觸發驗證。 例如，當使用者在使用模擬服務時`LoginView`，在 eShopOnContainers 行動應用程式中按下 [**登**入] 按鈕時，就會發生這種情況。 命令委派會呼叫`MockSignInAsync` `LoginViewModel`中的方法，它會藉由執行`Validate`方法來叫用驗證，如下列程式碼範例所示：

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

方法會在每個`ValidatableObject<T>`實例上叫用 Validate 方法`LoginView`，以執行使用者在上輸入的使用者名稱和密碼驗證。 `Validate` 下列程式碼範例顯示`ValidatableObject<T>`類別中的 Validate 方法：

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

這個方法會清除`Errors`集合，然後抓取已加入至`Validations`物件集合的任何驗證規則。 會`Check`針對每個已抓取的驗證規則執行方法， `ValidationMessage`而且無法驗證資料之任何驗證規則的屬性值都會加入`ValidatableObject<T>`至實例的`Errors`集合。 最後， `IsValid`會設定屬性，並將其值傳回給呼叫方法，指出驗證成功或失敗。

### <a name="triggering-validation-when-properties-change"></a>屬性變更時觸發驗證

每當系結屬性變更時，也會觸發驗證。 例如，當中`LoginView`的雙向系結`UserName`設定或`Password`屬性時，就會觸發驗證。 下列程式碼範例將示範如何進行這項操作：

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

`UserName.Value` `ValidatableObject<T>` `Behaviors`控制項系結至實例的屬性`EventToCommandBehavior` ，而控制項的集合已加入實例。 [`Entry`](xref:Xamarin.Forms.Entry) 這個行為會執行`ValidateUserNameCommand` `Entry`，以回應中的`TextChanged`[] 事件引發， `Entry`這會在變更中的文字時引發。 接著， `ValidateUserNameCommand`委派會`ValidateUserName`執行`Validate`方法，這會在`ValidatableObject<T>`實例上執行方法。 因此，每次使用者在`Entry`控制項中輸入使用者名稱的字元時，都會執行輸入資料的驗證。

如需行為的詳細資訊，請參閱[執行行為](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>顯示驗證錯誤

EShopOnContainers 行動應用程式會藉由反白顯示包含無效資料的控制項來通知使用者任何驗證錯誤，並顯示一則錯誤訊息，通知使用者在包含下列內容的控制項底下，資料不正確原因不正確資料。 更正不正確資料時，線條會變更為黑色，並移除錯誤訊息。 圖6-2 顯示驗證錯誤時，eShopOnContainers 行動應用程式中的 LoginView。

![](validation-images/validation-login.png "在登入期間顯示驗證錯誤")

**圖6-2：** 在登入期間顯示驗證錯誤

### <a name="highlighting-a-control-that-contains-invalid-data"></a>反白顯示包含無效資料的控制項

附加`LineColorBehavior`的行為是用來反[`Entry`](xref:Xamarin.Forms.Entry)白顯示發生驗證錯誤的控制項。 下列程式碼範例顯示附加的`LineColorBehavior`行為如何附加`Entry`至控制項：

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

此樣式會在`ApplyLineColor` `LineColor` [控制項`Entry`](xref:Xamarin.Forms.Entry)上設定`LineColorBehavior`附加行為的和附加屬性。 如需樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

當`ApplyLineColor`附加屬性的值已設定或變更時`LineColorBehavior` ，附加的行為會執行`OnApplyLineColorChanged`方法，如下列程式碼範例所示：

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

這個方法的參數會提供行為所附加的控制項實例，以及`ApplyLineColor`附加屬性的舊值和新值。 [`Effects`](xref:Xamarin.Forms.Element.Effects) `Effects`如果`EntryLineColorEffect` 附加屬性`true`為，則類別會加入至控制項的集合，否則會從控制項的集合中移除。 `ApplyLineColor` 如需行為的詳細資訊，請參閱[執行行為](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

會將[`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)類別子類別化，如下列程式碼範例所示：`EntryLineColorEffect`

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

[`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)類別代表與平臺無關的效果，它會包裝平臺特定的內部效果。 這可簡化效果移除程序，因為對於平台特定效果，並不存在對類型資訊的編譯時間資訊存取。 會`EntryLineColorEffect`呼叫基類處理常式，傳入包含解析組名串連的參數，以及在每個平臺特定效果類別上指定的唯一識別碼。

下列程式碼範例顯示 iOS `eShopOnContainers.EntryLineColorEffect`的執行：

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

方法會抓取 Xamarin 表單[`Entry`](xref:Xamarin.Forms.Entry)控制項的原生控制項，並藉由呼叫`UpdateLineColor`方法來更新線條色彩。 `OnAttached` `Entry` `LineColor` [`Height`](xref:Xamarin.Forms.VisualElement.Height)覆寫會在附加`Entry`屬性變更時更新線條色彩，或變更的屬性，藉以回應控制項上可系結的屬性變更。 `OnElementPropertyChanged` 如需效果的詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

當[`Entry`](xref:Xamarin.Forms.Entry)控制項中輸入有效的資料時，它會將黑色行套用到控制項的底部，以指出沒有任何驗證錯誤。 圖6-3 顯示這種情況的範例。

![](validation-images/validation-blackline.png "表示沒有驗證錯誤的黑色線條")

**圖 6-3**：表示沒有驗證錯誤的黑色線條

控制項也已將[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)新增至其[`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)集合。 [`Entry`](xref:Xamarin.Forms.Entry) 下列程式碼範例顯示`DataTrigger`：

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

這[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)會[`Setter`](xref:Xamarin.Forms.Setter) `false` `LineColor` `LineColorBehavior`監視屬性，如果其值為，則會執行，這會將附加行為的附加屬性變更為紅色。 `UserName.IsValid` 圖6-4 顯示這種情況的範例。

![](validation-images/validation-redline.png "指出驗證錯誤的紅線")

**圖 6-4**：指出驗證錯誤的紅線

當輸入的資料[`Entry`](xref:Xamarin.Forms.Entry)無效時，控制項中的線條會保持紅色，否則會變更為黑色，表示輸入的資料是有效的。

如需觸發程式的詳細資訊，請參閱[觸發](~/xamarin-forms/app-fundamentals/triggers.md)程式。

### <a name="displaying-error-messages"></a>顯示錯誤訊息

UI 會在其資料驗證失敗的每個控制項底下的標籤控制項中顯示驗證錯誤訊息。 下列程式碼範例會顯示[`Label`](xref:Xamarin.Forms.Label) ，如果使用者未輸入有效的使用者名稱，則會顯示驗證錯誤訊息：

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

每[`Label`](xref:Xamarin.Forms.Label)個都會系`Errors`結至要驗證之視圖模型物件的屬性。 屬性是由類別提供，而且屬於型`List<string>`別。 `ValidatableObject<T>` `Errors` 因為屬性可以包含多個驗證錯誤`FirstValidationErrorConverter` ，所以會使用實例來抓取集合中的第一個錯誤，以供顯示。 `Errors`

## <a name="summary"></a>總結

EShopOnContainers 行動應用程式會執行 view model 屬性的同步用戶端驗證，並藉由反白顯示包含無效資料的控制項來通知使用者任何驗證錯誤，以及顯示通知使用者的錯誤訊息資料不正確原因。

需要驗證的視圖模型屬性屬於類型`ValidatableObject<T>`，而且每個`ValidatableObject<T>`實例都已將驗證規則加入其`Validations`屬性中。 藉`Validate`由呼叫`ValidatableObject<T>`實例的方法，從視圖模型叫用驗證，它會抓取驗證規則`ValidatableObject<T>` `Value` ，並針對屬性執行。 任何驗證`Errors`錯誤都會放入`ValidatableObject<T>`實例的屬性中， `ValidatableObject<T>`而實例的`IsValid`屬性則會更新，以指出驗證成功或失敗。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
