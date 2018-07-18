---
title: 在 企業應用程式的驗證
description: 本章說明在 eShopOnContainers 的行動應用程式執行驗證的使用者輸入的方式。 這包括指定驗證規則、 觸發驗證，以及顯示驗證錯誤。
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 2b4be17e3c96ee223433b435a7b1011eafa8e9db
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995823"
---
# <a name="validation-in-enterprise-apps"></a>在 企業應用程式的驗證

接受使用者輸入的任何應用程式應該確定輸入有效。 比方說，應用程式可以檢查包含只有在特定範圍內的字元、 具有特定長度，或符合特定格式的輸入。 不需要驗證，使用者可以提供會導致應用程式失敗的資料。 驗證會強制執行商務規則，並防止攻擊者將惡意的資料。

在內容的模型-ViewModel (MVVM) 模式，檢視模型或模型通常必須執行資料驗證，並發出信號至檢視的任何驗證錯誤，以便讓使用者更正。 EShopOnContainers 的行動裝置應用程式執行的檢視模型屬性的同步用戶端驗證，並通知使用者任何驗證錯誤，反白顯示控制項，其中包含無效的資料，並顯示錯誤訊息，通知使用者資料不正確的原因。 圖 6-1 顯示的類別參與在 eShopOnContainers 的行動裝置應用程式中執行驗證。

[![](validation-images/validation.png "在 eShopOnContainers 的行動裝置應用程式中的驗證類別")](validation-images/validation-large.png#lightbox "eShopOnContainers 的行動裝置應用程式的驗證類別")

**圖 6-1**: eShopOnContainers 的行動裝置應用程式的驗證類別

需要驗證的檢視模型屬性都屬於型別`ValidatableObject<T>`，而且每個`ValidatableObject<T>`執行個體已驗證規則新增至其`Validations`屬性。 叫用驗證從檢視模型藉由呼叫`Validate`方法`ValidatableObject<T>`執行個體，它會擷取驗證規則，並執行防範`ValidatableObject<T>``Value`屬性。 任何驗證錯誤會放入`Errors`的屬性`ValidatableObject<T>`執行個體，而`IsValid`屬性`ValidatableObject<T>`執行個體也會更新，表示驗證是否成功或失敗。

提供屬性變更通知`ExtendedBindableObject`類別，因此[ `Entry` ](xref:Xamarin.Forms.Entry)控制項可以繫結至`IsValid`屬性`ValidatableObject<T>`中是否要通知檢視模型類別的執行個體輸入的資料是有效的。

## <a name="specifying-validation-rules"></a>指定驗證規則

驗證規則所建立的類別，衍生自指定`IValidationRule<T>`介面，下列程式碼範例所示：

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

這個介面會指定驗證規則類別必須提供`boolean``Check`用來執行必要的驗證方法和`ValidationMessage`屬性且值為要顯示的驗證錯誤訊息驗證失敗。

下列程式碼範例所示`IsNotNullOrEmptyRule<T>`用來執行驗證的使用者名稱和密碼使用者輸入的驗證規則`LoginView`時在 eShopOnContainers 的行動裝置應用程式中使用模擬 （mock） 的服務：

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

`Check`方法會傳回`boolean`指出數值引數是否為`null`、 空白，或只包含空白字元。

雖然不使用 eShopOnContainers 的行動裝置應用程式，下列程式碼範例會顯示驗證規則來驗證電子郵件地址：

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

`Check`方法會傳回`boolean`指出數值引數是否為有效的電子郵件地址。 這藉由搜尋值引數中指定之規則運算式模式的第一個相符項目來達成`Regex`建構函式。 規則運算式模式是否找到輸入字串中您可以檢查的值來判斷`Match`物件的`Success`屬性。

> [!NOTE]
> 屬性驗證有時會涉及相依屬性。 屬性的有效值集取決於已設定屬性 b 中的特定值時，相依屬性的範例若要檢查屬性的值是其中一個允許的值會包含擷取屬性 b 的值此外，當屬性 B 的值變更時，屬性的需要重新驗證。

## <a name="adding-validation-rules-to-a-property"></a>將驗證規則加入至屬性

在 eShopOnContainers 行動裝置應用程式，需要驗證的檢視模型屬性宣告為類型`ValidatableObject<T>`，其中`T`來驗證資料的類型。 下列程式碼範例顯示兩個這類屬性的範例：

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

發生的驗證，驗證規則必須加入至`Validations`的每個集合`ValidatableObject<T>`執行個體，如下列程式碼範例所示：

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

這個方法會加入`IsNotNullOrEmptyRule<T>`的驗證規則`Validations`每個集合`ValidatableObject<T>`執行個體，指定驗證規則的值`ValidationMessage`屬性，指定要顯示的驗證錯誤訊息驗證失敗。

## <a name="triggering-validation"></a>觸發驗證

在 eShopOnContainers 的行動裝置應用程式中使用的驗證方法可以手動觸發驗證的屬性，及自動觸發程序的驗證，當屬性變更時。

### <a name="triggering-validation-manually"></a>手動觸發驗證

檢視模型屬性可以手動觸發驗證。 比方說，這個問題發生在 eShopOnContainers 的行動裝置應用程式在使用者點選時**登入**按鈕`LoginView`，當使用模擬 （mock） 的服務。 此命令會委派呼叫`MockSignInAsync`方法中的`LoginViewModel`，其執行叫用驗證`Validate`方法，以下列程式碼範例所示：

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

`Validate`方法會執行驗證的使用者名稱和使用者輸入的密碼`LoginView`，藉由叫用的驗證方法，在每個`ValidatableObject<T>`執行個體。 下列程式碼範例顯示的驗證方法，從`ValidatableObject<T>`類別：

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

這個方法會清除`Errors`集合，然後再擷取任何驗證規則，已新增至物件的`Validations`集合。 `Check`執行每個擷取的驗證規則的方法，而`ValidationMessage`屬性值，任何失敗的資料進行驗證的驗證規則加入至`Errors`的集合`ValidatableObject<T>`執行個體。 最後，`IsValid`設定屬性，且其值會傳回至呼叫的方法，指出驗證成功或失敗。

### <a name="triggering-validation-when-properties-change"></a>當屬性變更時觸發驗證

每當繫結的屬性變更時，也會觸發驗證。 例如，當中的雙向繫結`LoginView`設定`UserName`或`Password`屬性，驗證就會觸發。 下列程式碼範例示範如何發生這種情況：

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

[ `Entry` ](xref:Xamarin.Forms.Entry)控制項繫結至`UserName.Value`屬性`ValidatableObject<T>`執行個體，以及控制`Behaviors`集合中有`EventToCommandBehavior`加入它的執行個體。 執行此行為`ValidateUserNameCommand`以回應 [`TextChanged`] 上引發的事件`Entry`，它引發時間中的文字`Entry`變更。 依次`ValidateUserNameCommand`委派會執行`ValidateUserName`方法，它會執行`Validate`方法`ValidatableObject<T>`執行個體。 因此，每次使用者在輸入中的字元`Entry`執行使用者名稱、 輸入資料的驗證控制項。

如需行為的詳細資訊，請參閱[實作行為](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>顯示驗證錯誤

EShopOnContainers 的行動裝置應用程式會通知任何驗證錯誤的使用者透過反白顯示控制項，其中包含無效的資料，紅色底線，且顯示錯誤訊息，告知使用者為什麼資料下方包含的控制項無效無效的資料。 如果已更正無效的資料，行變更為黑色，並移除錯誤訊息。 圖 6-2 會顯示 LoginView eShopOnContainers 的行動裝置應用程式有驗證錯誤時。

![](validation-images/validation-login.png "登入期間顯示驗證錯誤")

**圖 6-2:** 登入期間顯示驗證錯誤

### <a name="highlighting-a-control-that-contains-invalid-data"></a>反白顯示包含無效的資料控制項

`LineColorBehavior`附加的行為用來反白顯示[ `Entry` ](xref:Xamarin.Forms.Entry)控制項發生驗證錯誤。 下列程式碼範例示範如何`LineColorBehavior`附加的行為附加至`Entry`控制項：

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

[ `Entry` ](xref:Xamarin.Forms.Entry)控制項使用明確樣式，下列程式碼範例所示：

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

此樣式會設定`ApplyLineColor`並`LineColor`附加的屬性`LineColorBehavior`上附加行為[ `Entry` ](xref:Xamarin.Forms.Entry)控制項。 如需有關樣式的詳細資訊，請參閱 <<c0> [ 樣式](~/xamarin-forms/user-interface/styles/index.md)。

時的值`ApplyLineColor`附加的屬性是集合或變更`LineColorBehavior`附加的行為執行`OnApplyLineColorChanged`方法，以下列程式碼範例所示：

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

此方法的參數提供行為附加至，控制項的執行個體的舊和新值`ApplyLineColor`附加屬性。 `EntryLineColorEffect`類別加入至控制項的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合如果`ApplyLineColor`附加的屬性是`true`，否則它會從控制項移除`Effects`集合。 如需行為的詳細資訊，請參閱[實作行為](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

`EntryLineColorEffect`子類別[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)類別，並在下列程式碼範例所示：

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)類別代表包裝的內部的效果，是特定平台的平台獨立效果。 這可簡化效果移除程序，因為沒有任何編譯時間資訊的存取權類型平台專屬的效果。 `EntryLineColorEffect`呼叫基底類別建構函式，傳入參數的串連，解析群組名稱，以及每個平台特定效果類別指定的唯一識別碼所組成。

下列程式碼範例示範`eShopOnContainers.EntryLineColorEffect`適用於 iOS 的實作：

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

`OnAttached`方法會擷取原生控制項適用於 Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry)控制項，並藉由呼叫更新的線條色彩`UpdateLineColor`方法。 `OnElementPropertyChanged`上可繫結的屬性變更回應覆寫`Entry`藉由更新的線條色彩，如果控制項附加`LineColor`屬性變更，或[ `Height` ](xref:Xamarin.Forms.VisualElement.Height)屬性`Entry`變更。 如需效果的詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

有效的資料中的輸入時[ `Entry` ](xref:Xamarin.Forms.Entry)控制項，它會套用黑色資料行底部的控制項，以指出沒有任何驗證錯誤。 圖 6-3 顯示這個範例。

![](validation-images/validation-blackline.png "不指出任何驗證錯誤的黑色列")

**圖 6-3**： 黑色線條不表示任何驗證錯誤

[ `Entry` ](xref:Xamarin.Forms.Entry)控制項也有[ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger)新增至其[ `Triggers` ](xref:Xamarin.Forms.VisualElement.Triggers)集合。 下列程式碼範例示範`DataTrigger`:

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

這[ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger)監視器`UserName.IsValid`屬性，而值後，才會變成`false`，它會執行[ `Setter` ](xref:Xamarin.Forms.Setter)，哪些變更`LineColor`附加屬性`LineColorBehavior`附加為紅色的行為。 圖 6-4 顯示這個範例。

![](validation-images/validation-redline.png "表示驗證錯誤的紅線")

**圖 6-4**： 紅線表示驗證錯誤

中的一行[ `Entry` ](xref:Xamarin.Forms.Entry)無效輸入的資料時，控制會保持紅色，否則將會變更成黑色，表示輸入的資料有效。

如需有關觸發程序的詳細資訊，請參閱[觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)。

### <a name="displaying-error-messages"></a>顯示錯誤訊息

UI 會在其資料驗證失敗。 每個控制項下方的標籤控制項中顯示驗證錯誤訊息。 下列程式碼範例所示[ `Label` ](xref:Xamarin.Forms.Label) ，會顯示驗證錯誤訊息，如果使用者未輸入有效的使用者名稱：

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

每個[ `Label` ](xref:Xamarin.Forms.Label)繫結至`Errors`正在驗證的檢視模型物件的屬性。 `Errors`所提供屬性`ValidatableObject<T>`類別，而且屬於型`List<string>`。 因為`Errors`屬性可包含多個驗證錯誤，`FirstValidationErrorConverter`執行個體用來擷取顯示集合中的第一個錯誤。

## <a name="summary"></a>總結

EShopOnContainers 的行動裝置應用程式執行的檢視模型屬性的同步用戶端驗證，並通知使用者任何驗證錯誤，反白顯示控制項，其中包含無效的資料，並顯示錯誤訊息，通知使用者資料不正確的原因。

需要驗證的檢視模型屬性都屬於型別`ValidatableObject<T>`，而且每個`ValidatableObject<T>`執行個體已驗證規則新增至其`Validations`屬性。 叫用驗證從檢視模型藉由呼叫`Validate`方法`ValidatableObject<T>`執行個體，它會擷取驗證規則，並執行防範`ValidatableObject<T>``Value`屬性。 任何驗證錯誤會放入`Errors`的屬性`ValidatableObject<T>`執行個體，而`IsValid`屬性`ValidatableObject<T>`執行個體也會更新，表示驗證是否成功或失敗。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
