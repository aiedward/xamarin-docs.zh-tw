---
title: 驗證
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 58254fd3c7a3949b0ed6bb448223e34cf76f7103
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="validation"></a>驗證

可接受使用者輸入的任何應用程式應該確定輸入有效。 包含在特定範圍內的唯一字元、 為特定長度，或符合特定格式的輸入，例如可以檢查應用程式。 不需要驗證，使用者可以提供會導致失敗的應用程式的資料。 驗證會強制執行商務規則，並防止攻擊者插入惡意的資料。

在內容的模型 ViewModel 模型 (MVVM) 模式，檢視模型或模型通常需要執行資料驗證，並發出訊號檢視任何驗證錯誤，以便讓使用者更正。 EShopOnContainers 行動裝置應用程式執行的檢視模型內容的同步用戶端驗證，並反白顯示的控制項，包含無效的資料，並顯示錯誤訊息，通知使用者，通知使用者的任何驗證錯誤資料不正確的原因。 圖 6-1 會顯示 eShopOnContainers 行動應用程式中執行驗證所需的類別。

[![](validation-images/validation.png "EShopOnContainers 行動應用程式中的驗證類別")](validation-images/validation-large.png#lightbox "eShopOnContainers 行動應用程式中的驗證類別")

**圖 6-1**: eShopOnContainers 行動應用程式中的驗證類別

需要驗證的檢視模型內容都屬於型別`ValidatableObject<T>`，而且每個`ValidatableObject<T>`執行個體具有的驗證規則加入至其`Validations`屬性。 叫用驗證時從檢視模型藉由呼叫`Validate`方法`ValidatableObject<T>`執行個體，擷取驗證規則，並針對執行`ValidatableObject<T>``Value`屬性。 任何驗證錯誤會放入`Errors`屬性`ValidatableObject<T>`執行個體，而`IsValid`屬性`ValidatableObject<T>`執行個體也會更新，表示驗證成功或失敗。

屬性變更通知係由`ExtendedBindableObject`類別，因此[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項可以繫結至`IsValid`屬性`ValidatableObject<T>`中檢視模型類別，以得知是否執行個體輸入的資料無效。

## <a name="specifying-validation-rules"></a>指定驗證規則

驗證規則所建立的類別，衍生自指定`IValidationRule<T>`介面，下列程式碼範例所示：

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

這個介面會指定驗證規則類別必須提供`boolean``Check`用來執行必要的驗證方法和`ValidationMessage`屬性的值是要顯示的驗證錯誤訊息驗證失敗。

下列程式碼範例示範`IsNotNullOrEmptyRule<T>`驗證規則，用來執行驗證的使用者名稱和密碼的使用者所輸入`LoginView`eShopOnContainers 行動應用程式中使用模擬的服務時：

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

`Check`方法會傳回`boolean`表示數值引數是否為`null`、 空白，或只包含空白字元。

不使用 eShopOnContainers 行動裝置應用程式，但是下列的程式碼範例會顯示驗證電子郵件地址的驗證規則：

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

`Check`方法會傳回`boolean`表示數值引數是否為有效的電子郵件地址。 這藉由搜尋的數值引數中指定之規則運算式模式的第一個相符項目`Regex`建構函式。 規則運算式模式是否找到輸入字串中您可以檢查的值來判斷`Match`物件的`Success`屬性。

> [!NOTE]
> 屬性驗證有時可能涉及到相依的屬性。 將屬性的有效值取決於已設定屬性 b 中的特定值時的相依屬性範例若要檢查的屬性的值是其中一個允許的值會包含擷取屬性 b 的值此外，當屬性 B 的值變更時，屬性的需要重新驗證。

## <a name="adding-validation-rules-to-a-property"></a>將驗證規則加入至屬性

EShopOnContainers 行動裝置應用程式，在需要驗證的檢視模型屬性宣告型別`ValidatableObject<T>`，其中`T`是要驗證的資料類型。 下列程式碼範例顯示兩個這類屬性的範例：

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

為了執行驗證，必須驗證規則加入至`Validations`的每個集合`ValidatableObject<T>`執行個體，如下列程式碼範例所示：

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

這個方法會加入`IsNotNullOrEmptyRule<T>`用來驗證規則`Validations`的每個集合`ValidatableObject<T>`執行個體，並指定驗證規則的值`ValidationMessage`屬性，指定要顯示的驗證錯誤訊息驗證失敗。

## <a name="triggering-validation"></a>觸發驗證

EShopOnContainers 行動裝置應用程式中使用的驗證方法可以手動觸發驗證的屬性，及自動觸發程序的驗證，當屬性變更時。

### <a name="triggering-validation-manually"></a>手動觸發驗證

檢視模型屬性可以手動觸發驗證。 比方說，這 eShopOnContainers 行動裝置應用程式時，發生在使用者點選**登入**按鈕`LoginView`，當使用模擬的服務。 命令的委派呼叫`MockSignInAsync`方法中的`LoginViewModel`，它會執行叫用驗證`Validate`方法，下列程式碼範例所示：

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

`Validate`方法會執行驗證的使用者名稱和密碼的使用者所輸入`LoginView`，藉由叫用每個上的 Validate 方法`ValidatableObject<T>`執行個體。 下列程式碼範例示範 Validate 方法，從`ValidatableObject<T>`類別：

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

這個方法會清除`Errors`集合，並擷取任何驗證規則加入至物件的`Validations`集合。 `Check`執行每個擷取的驗證規則的方法，而`ValidationMessage`屬性值，任何失敗的資料進行驗證的驗證規則加入至`Errors`集合`ValidatableObject<T>`執行個體。 最後，`IsValid`屬性已設定，而且其值會傳回到呼叫的方法，指出驗證成功或失敗。

### <a name="triggering-validation-when-properties-change"></a>當屬性變更時觸發驗證

每當繫結的屬性變更時，也會自動觸發驗證。 例如，當雙向繫結中`LoginView`設定`UserName`或`Password`屬性，驗證就會觸發。 下列程式碼範例將示範如何發生這種情況：

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

[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項繫結至`UserName.Value`屬性`ValidatableObject<T>`執行個體，以及控制項的`Behaviors`集合具有`EventToCommandBehavior`加入至該執行個體。 執行這項行為`ValidateUserNameCommand`以回應 [`TextChanged`] 引發事件`Entry`、 哪些時引發中的文字`Entry`變更。 接著，`ValidateUserNameCommand`委派執行`ValidateUserName`方法，它會執行`Validate`方法`ValidatableObject<T>`執行個體。 因此，每次使用者在輸入中的字元`Entry`執行控制項的使用者名稱，而輸入資料的驗證。

如需行為的詳細資訊，請參閱[實作行為](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>顯示驗證錯誤

EShopOnContainers 行動裝置應用程式通知的任何驗證錯誤的使用者透過反白顯示的控制項，包含無效的資料，紅色底線，而且藉由顯示錯誤訊息，告知使用者為什麼的資料無效下方包含的控制項無效的資料。 如果已更正無效的資料，該行變更為黑色，並移除錯誤訊息。 圖 6-2 會顯示 LoginView eShopOnContainers 行動應用程式時有驗證錯誤。

![](validation-images/validation-login.png "在登入期間顯示驗證錯誤")

**圖 6-2:**登入期間顯示驗證錯誤

### <a name="highlighting-a-control-that-contains-invalid-data"></a>反白顯示包含無效的資料的控制項

`LineColorBehavior`附加的行為可用來反白顯示[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項發生驗證錯誤。 下列程式碼範例示範如何`LineColorBehavior`附加的行為已附加至`Entry`控制項：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP, WinRT, WinPhone" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項使用明確樣式，下列程式碼範例所示：

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

這個樣式設定`ApplyLineColor`和`LineColor`附加屬性的`LineColorBehavior`上附加行為[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項。 如需有關樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

當值`ApplyLineColor`附加的屬性是集合或變更，`LineColorBehavior`附加的行為執行`OnApplyLineColorChanged`方法，下列程式碼範例所示：

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

這個方法的參數提供之控制項的行為已附加至，執行個體和舊的和新值`ApplyLineColor`附加屬性。 `EntryLineColorEffect`類別隨即加入至控制項的[ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/)集合如果`ApplyLineColor`附加屬性`true`，否則會從控制項移除`Effects`集合。 如需行為的詳細資訊，請參閱[實作行為](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

`EntryLineColorEffect`子類別[ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)類別，以及下列程式碼範例所示：

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

[ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)類別代表包裝的內部的效果，適用於平台的平台無關效果。 這樣可簡化效果移除程序，因為沒有任何編譯時間資訊的存取權類型平台專屬效果。 `EntryLineColorEffect`呼叫基底類別建構函式，傳遞參數，其中 解析群組名稱，與每個平台專屬效果類別指定的唯一識別碼的串連。

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

`OnAttached`方法擷取原生控制項 Xamarin.Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項，然後呼叫以更新的線條色彩`UpdateLineColor`方法。 `OnElementPropertyChanged`可繫結屬性的變更覆寫回應`Entry`更新線條色彩，如果控制項附加`LineColor`屬性變更，或[ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/)屬性`Entry`變更。 如需效果的詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

在輸入有效的資料時[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項，則會套用一條黑色線條控制項，以表示沒有任何驗證錯誤的底部。 圖 6-3 顯示此動作的範例。

![](validation-images/validation-blackline.png "不指出任何驗證錯誤的黑色線條")

**圖 6-3**： 黑色任何驗證錯誤，指出資料行

[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)控制項也有[ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/)加入至其[ `Triggers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Triggers/)集合。 下列程式碼範例示範`DataTrigger`:

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

這[ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/)監視器`UserName.IsValid`屬性，而如果它是值會變成`false`，它會執行[ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)，哪些變更`LineColor`附加屬性`LineColorBehavior`附加為紅色的行為。 圖 6-4 顯示此動作的範例。

![](validation-images/validation-redline.png "表示驗證錯誤的紅線")

**圖 6-4**： 紅線，表示驗證錯誤

中的一行[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)輸入的資料無效時，控制項將保留紅色，否則將會變更成黑色，表示輸入的資料無效。

如需觸發程序的詳細資訊，請參閱[觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)。

### <a name="displaying-error-messages"></a>顯示錯誤訊息

UI 會在其資料驗證失敗的每個控制項下方的標籤控制項中顯示驗證錯誤訊息。 下列程式碼範例示範[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) ，會顯示驗證錯誤訊息，如果使用者未輸入有效的使用者名稱：

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

每個[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)繫結至`Errors`正在驗證的檢視模型物件的屬性。 `Errors`屬性由`ValidatableObject<T>`類別，並屬於型別`List<string>`。 因為`Errors`屬性可以包含多個驗證錯誤，`FirstValidationErrorConverter`執行個體用來擷取顯示集合中的第一個錯誤。

## <a name="summary"></a>總結

EShopOnContainers 行動裝置應用程式執行的檢視模型內容的同步用戶端驗證，並反白顯示的控制項，包含無效的資料，並顯示錯誤訊息，通知使用者，通知使用者的任何驗證錯誤資料不正確的原因。

需要驗證的檢視模型內容都屬於型別`ValidatableObject<T>`，而且每個`ValidatableObject<T>`執行個體具有的驗證規則加入至其`Validations`屬性。 叫用驗證時從檢視模型藉由呼叫`Validate`方法`ValidatableObject<T>`執行個體，擷取驗證規則，並針對執行`ValidatableObject<T>``Value`屬性。 任何驗證錯誤會放入`Errors`屬性`ValidatableObject<T>`執行個體，而`IsValid`屬性`ValidatableObject<T>`執行個體也會更新，表示驗證成功或失敗。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
