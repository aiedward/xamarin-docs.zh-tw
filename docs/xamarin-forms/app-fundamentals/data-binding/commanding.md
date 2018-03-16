---
title: "命令介面"
description: "實作`Command`屬性與資料繫結"
ms.topic: article
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 0456742ca30257670375e3ae781dcdc49707e2ce
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
# <a name="the-command-interface"></a>命令介面

ViewModel，這通常是衍生自類別的屬性之間定義模型-檢視-ViewModel (MVVM) 架構中，資料繫結`INotifyPropertyChanged`，以及在檢視中，這通常是 XAML 檔案的屬性。 有時候應用程式的需求超越這些屬性繫結，透過要求使用者若要啟動命令會影響 ViewModel 中的項目。 這些命令通常會按一下按鈕所收到信號或手指點選，並在程式碼後置檔案中的處理常式的處理傳統上`Clicked`事件`Button`或`Tapped`事件`TapGestureRecognizer`。 

命令的介面會提供實作命令比較適合 MVVM 架構的替代方法。 ViewModel 本身可以包含方法，例如執行在檢視中的特定活動的命令`Button`按一下。 這些命令之間已定義的資料繫結和`Button`。

若要允許之間的資料繫結`Button`和 ViewModel，`Button`定義兩個屬性：

- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Command/) 型別 <xref:System.Windows.Input.ICommand>
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.CommandParameter/) 型別 `Object`

若要使用命令介面，您可以定義為目標的資料繫結`Command`屬性`Button`來源所在的型別 ViewModel 屬性`ICommand`。 ViewModel 包含關聯的程式碼`ICommand`按鈕時所執行的屬性。 您可以設定`CommandParameter`來區別多個按鈕，如果全部的任意資料繫結至相同`ICommand`ViewModel 中的屬性。

`Command`和`CommandParameter`屬性也會定義下列類別：

- [`MenuItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) 因此， [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/)，其衍生自 `MenuItem`
- [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) 因此， [ `ImageCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/)，其衍生自 `TextCell`
- [`TapGestureRecognizer`](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)

[`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) 定義[ `SearchCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommand/)型別的屬性`ICommand`和[ `SearchCommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommandParameter/)屬性。 [ `RefreshCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RefreshCommand/)屬性[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)也屬於型別`ICommand`。 

所有這些命令可以處理內 ViewModel 不相依於特定的使用者介面中的物件檢視的方式。

## <a name="the-icommand-interface"></a>ICommand 介面

<xref:System.Windows.Input.ICommand>介面不是 Xamarin.Forms 的一部分。 而是定義在[System.Windows.Input](xref:System.Windows.Input)命名空間，而且包含兩個方法和一個事件：

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

若要使用命令介面，您 ViewModel 包含型別的屬性`ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

ViewModel 也必須參考實作的類別`ICommand`介面。 這個類別會在稍後說明。 在檢視中，`Command`屬性`Button`繫結至該屬性： 

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

當使用者按`Button`、`Button`呼叫`Execute`方法中的`ICommand`物件繫結至其`Command`屬性。 屬於最簡單的命令介面。

`CanExecute`方法是更複雜。 當第一次定義的繫結上`Command`屬性`Button`，當資料繫結變更某種方式時`Button`呼叫`CanExecute`方法中的`ICommand`物件。 如果`CanExecute`傳回`false`，然後在`Button`停用。 這表示，特定命令目前已無法使用或無效。

`Button`也將處理常式附加上`CanExecuteChanged`事件`ICommand`。 ViewModel 內從引發事件。 當引發該事件時，`Button`呼叫`CanExecute`一次。 `Button`如果讓本身`CanExecute`傳回`true`和停用`CanExecute`傳回`false`。

> [!IMPORTANT]
> 請勿使用`IsEnabled`屬性`Button`如果您使用命令介面。  

## <a name="the-command-class"></a>命令類別

當您 ViewModel 定義屬性的型別`ICommand`，ViewModel 必須也包含或參照類別可實作`ICommand`介面。 這個類別必須包含或參考`Execute`和`CanExecute`方法及引發`CanExecuteChanged`事件每當`CanExecute`方法可能會傳回不同的值。

您可以自行撰寫這種類別，或者您可以使用其他人已撰寫的類別。 因為`ICommand`是一部分的 Microsoft Windows，已在使用多年來搭配 MVVM Windows 應用程式。 使用 Windows 類別可實作`ICommand`可讓您共用您的 Windows 應用程式和 Xamarin.Forms 應用程式之間 ViewModels。

如果共用 ViewModels Windows 和 Xamarin.Forms 間不是問題，則您可以使用[ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/)或[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/)類別包含在實作Xamarin.Forms`ICommand`介面。 這些類別可讓您指定的本文`Execute`和`CanExecute`類別建構函式中的方法。 使用`Command<T>`當您使用`CommandParameter`屬性來區別多個檢視之間的繫結至相同`ICommand`屬性，而較簡單的`Command`類別時，不需要。

## <a name="basic-commanding"></a>基本命令

**人員項目**頁面[**資料繫結示範**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)程式會示範如何在 ViewModel 中實作一些簡單的命令。

`PersonViewModel`定義三個屬性，名為`Name`， `Age`，和`Skills`所定義的人員。 這個類別會針對執行*不*包含任何`ICommand`屬性：

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    string name;
    double age;
    string skills;

    public event PropertyChangedEventHandler PropertyChanged;

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public double Age
    {
        set { SetProperty(ref age, value); }
        get { return age; }
    }

    public string Skills
    {
        set { SetProperty(ref skills, value); }
        get { return skills; }
    }

    public override string ToString()
    {
        return Name + ", age " + Age;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

`PersonCollectionViewModel`顯示底下建立新的物件型別的`PersonViewModel`並允許使用者填入的資料。 基於這個目的，類別會定義屬性`IsEditing`型別的`bool`和`PersonEdit`型別的`PersonViewModel`。 此外，類別會定義三個屬性的型別`ICommand`和屬性，名為`Persons`型別的`IList<PersonViewModel>`: 

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    PersonViewModel personEdit;
    bool isEditing;

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public bool IsEditing
    {
        private set { SetProperty(ref isEditing, value); }
        get { return isEditing; }
    }

    public PersonViewModel PersonEdit
    {
        set { SetProperty(ref personEdit, value); }
        get { return personEdit; }
    }

    public ICommand NewCommand { private set; get; }

    public ICommand SubmitCommand { private set; get; }

    public ICommand CancelCommand { private set; get; }

    public IList<PersonViewModel> Persons { get; } = new ObservableCollection<PersonViewModel>();

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

此縮寫的清單不包含該類別的建構函式，是要在哪裡的三個屬性的型別`ICommand`所定義，這將會很快顯示。 請注意，變更為三個屬性的型別`ICommand`和`Persons`屬性不會導致`PropertyChanged`所引發的事件。 這些屬性全部設定第一次建立類別時，請勿變更之後。

之前檢查的建構函式`PersonCollectionViewModel`類別，讓我們看看的 XAML 檔案**人員項目**程式。 這包含`Grid`具有其`BindingContext`屬性設定為`PersonCollectionViewModel`。 `Grid`包含`Button`以文字**新增**具有其`Command`屬性繫結至`NewCommand`ViewModel 屬性，屬性的項目表單繫結至`IsEditing`屬性，做為內容以及`PersonViewModel`，而且兩個按鈕的繫結至`SubmitCommand`和`CancelCommand`ViewModel 屬性。 最終`ListView`顯示人員已輸入的集合：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.PersonEntryPage"
             Title="Person Entry">
    <Grid Margin="10">
        <Grid.BindingContext>
            <local:PersonCollectionViewModel />
        </Grid.BindingContext>
        
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <!-- New Button -->
        <Button Text="New"
                Grid.Row="0"
                Command="{Binding NewCommand}"
                HorizontalOptions="Start" />

        <!-- Entry Form -->
        <Grid Grid.Row="1"
              IsEnabled="{Binding IsEditing}">
            
            <Grid BindingContext="{Binding PersonEdit}">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Label Text="Name: " Grid.Row="0" Grid.Column="0" />
                <Entry Text="{Binding Name}" 
                       Grid.Row="0" Grid.Column="1" />

                <Label Text="Age: " Grid.Row="1" Grid.Column="0" />
                <StackLayout Orientation="Horizontal" 
                             Grid.Row="1" Grid.Column="1">
                    <Stepper Value="{Binding Age}"
                             Maximum="100" />
                    <Label Text="{Binding Age, StringFormat='{0} years old'}"
                           VerticalOptions="Center" />
                </StackLayout>

                <Label Text="Skills: " Grid.Row="2" Grid.Column="0" />
                <Entry Text="{Binding Skills}"
                       Grid.Row="2" Grid.Column="1" />

            </Grid>
        </Grid>

        <!-- Submit and Cancel Buttons -->
        <Grid Grid.Row="2">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <Button Text="Submit"
                    Grid.Column="0"
                    Command="{Binding SubmitCommand}"
                    VerticalOptions="CenterAndExpand" />

            <Button Text="Cancel"
                    Grid.Column="1"
                    Command="{Binding CancelCommand}"
                    VerticalOptions="CenterAndExpand" />
        </Grid>

        <!-- List of Persons -->
        <ListView Grid.Row="3"
                  ItemsSource="{Binding Persons}" />
    </Grid>
</ContentPage>
```

它的運作方式如下： 使用者第一次按**新增** 按鈕。 這可讓項目表單，但是會停用**新增** 按鈕。 使用者輸入的名稱、 年齡和技術。 在編輯期間的任何時間，使用者可以按**取消**按鈕以重新開始。 只有已輸入的名稱和有效的存在時間時為**送出**按鈕啟用。 按下此**送出**按鈕所顯示的集合來傳輸個人`ListView`。 之後**取消**或**送出**按下按鈕、 清除項目表單和**新增**按鈕重新啟用。

之前輸入有效的存留期，左邊的 [iOS] 畫面會顯示配置。 Android 和 UWP 螢幕顯示**送出**啟用設定 [age] 按鈕：

[![人員的項目](commanding-images/personentry-small.png "人員項目")](commanding-images/personentry-large.png#lightbox "人員項目")

程式不會儲存項目，當您離開頁面時，就不需要任何設備編輯現有的項目。

所有的邏輯**新增**，**送出**，和**取消**按鈕中，會處理`PersonCollectionViewModel`透過定義的`NewCommand`， `SubmitCommand`，和`CancelCommand`屬性。 建構函式`PersonCollectionViewModel`設定這三個屬性的型別物件`Command`。  

A[建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/System.Func%7BSystem.Boolean%7D/)的`Command`類別可讓您將型別引數傳遞`Action`和`Func<bool>`對應至`Execute`和`CanExecute`方法。 它是最簡單的方式定義這些動作和函數中的 lambda 函式右方`Command`建構函式。 以下是定義`Command`物件`NewCommand`屬性：

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {
        NewCommand = new Command(
            execute: () =>
            {
                PersonEdit = new PersonViewModel();
                PersonEdit.PropertyChanged += OnPersonEditPropertyChanged;
                IsEditing = true;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return !IsEditing;
            });

        ···

    }

    void OnPersonEditPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        (SubmitCommand as Command).ChangeCanExecute();
    }
    
    void RefreshCanExecutes()
    {
        (NewCommand as Command).ChangeCanExecute();
        (SubmitCommand as Command).ChangeCanExecute();
        (CancelCommand as Command).ChangeCanExecute();
    }

    ···

}
```

當使用者按一下**新增** 按鈕，`execute`函式傳遞至`Command`建構函式執行。 這會建立新`PersonViewModel`物件，該物件上設定的處理常式`PropertyChanged`事件時，設定`IsEditing`至`true`，並呼叫`RefreshCanExecutes`方法定義建構函式之後。

除了實作`ICommand`介面，`Command`類別也會定義方法，名為`ChangeCanExecute`。 您 ViewModel 應該呼叫`ChangeCanExecute`如`ICommand`屬性，每當任何項目發生時，可能會變更的傳回值`CanExecute`方法。 呼叫`ChangeCanExecute`導致`Command`類別引發`CanExecuteChanged`方法。 `Button`已附加該事件的處理常式，並藉由呼叫回應`CanExecute`一次，然後再啟用本身根據該方法的傳回值。

當`execute`方法`NewCommand`呼叫`RefreshCanExecutes`、`NewCommand`屬性會取得呼叫`ChangeCanExecute`，而`Button`呼叫`canExecute`方法，現在會傳回`false`因為`IsEditing`內容現在是`true`。

`PropertyChanged`針對新的處理常式`PersonViewModel`物件會呼叫`ChangeCanExecute`方法`SubmitCommand`。 該命令屬性的實作方式如下：


```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        SubmitCommand = new Command(
            execute: () =>
            {
                Persons.Add(PersonEdit);
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return PersonEdit != null && 
                       PersonEdit.Name != null && 
                       PersonEdit.Name.Length > 1 && 
                       PersonEdit.Age > 0;
            });

        ···
    }

    ···

}
```

`canExecute`函式`SubmitCommand`每次變更中屬性呼叫`PersonViewModel`正在編輯的物件。 它會傳回`true`時，才`Name`屬性是在至少一個字元和`Age`大於 0。 在這段時間，**送出**按鈕會變成啟用。 

`execute`函式**送出**移除屬性變更處理常式從`PersonViewModel`，將物件加入`Persons`集合，並傳回初始條件的所有項目。

`execute`函式**取消**按鈕的所有作業也**送出**按鈕沒有 execept 加入至集合的物件：

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        CancelCommand = new Command(
            execute: () =>
            {
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return IsEditing;
            }); 
    }

    ···

}
```

`canExecute`方法會傳回`true`隨時`PersonViewModel`正在進行編輯。

這些技巧可能是適用於更複雜的案例： 中的屬性`PersonCollectionViewModel`無法繫結至`SelectedItem`屬性`ListView`編輯現有的項目和**刪除**可以將按鈕加入到刪除這些項目。

不需要定義`execute`和`canExecute`lambda 函式的方法。 您可以在 ViewModel 中撰寫為一般的私用方法，並參考它們在`Command`建構函式。 不過，這種方法沒有通常會導致大量 ViewModel 中一次所參考的方法。

## <a name="using-command-parameters"></a>使用命令參數

可能會很方便的一個或多個按鈕 （或其他使用者介面物件） 會共用相同`ICommand`ViewModel 中的屬性。 在此情況下，您使用`CommandParameter`屬性來區別按鈕之間。 

您可以繼續使用`Command`共用這些類別`ICommand`屬性。 類別會定義[替代建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action%7BSystem.Object%7D/System.Func%7BSystem.Object,System.Boolean%7D/)接受`execute`和`canExecute`具有參數的型別方法`Object`。 這是如何`CommandParameter`傳遞給這些方法。

不過，當使用`CommandParameter`，是最容易使用泛型[ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/)類別，以指定的物件設定為型別`CommandParameter`。 `execute`和`canExecute`您指定的方法有該類型的參數。

**十進位鍵盤**頁如何實作用於輸入的十進位數字鍵台，以說明這項技術。 `BindingContext`如`Grid`是`DecimalKeypadViewModel`。 `Entry`此 ViewModel 屬性繫結至`Text`屬性`Label`。 所有`Button`ViewModel 中的各種命令的物件會繫結： `ClearCommand`， `BackspaceCommand`，和`DigitCommand`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.DecimalKeypadPage"
             Title="Decimal Keyboard">

    <Grid WidthRequest="240"
          HeightRequest="480"
          ColumnSpacing="2"
          RowSpacing="2"
          HorizontalOptions="Center"
          VerticalOptions="Center">

        <Grid.BindingContext>
            <local:DecimalKeypadViewModel />
        </Grid.BindingContext>
        
        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Button">
                    <Setter Property="FontSize" Value="32" />
                    <Setter Property="BorderWidth" Value="1" />
                    <Setter Property="BorderColor" Value="Black" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Label Text="{Binding Entry}"
               Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3"
               FontSize="32"
               LineBreakMode="HeadTruncation"
               VerticalTextAlignment="Center"
               HorizontalTextAlignment="End" />

        <Button Text="CLEAR"
                Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding ClearCommand}" />

        <Button Text="&#x21E6;"
                Grid.Row="1" Grid.Column="2" 
                Command="{Binding BackspaceCommand}" />

        <Button Text="7"
                Grid.Row="2" Grid.Column="0" 
                Command="{Binding DigitCommand}"
                CommandParameter="7" />

        <Button Text="8"
                Grid.Row="2" Grid.Column="1" 
                Command="{Binding DigitCommand}"
                CommandParameter="8" />
        
        <Button Text="9"
                Grid.Row="2" Grid.Column="2" 
                Command="{Binding DigitCommand}"
                CommandParameter="9" />

        <Button Text="4"
                Grid.Row="3" Grid.Column="0" 
                Command="{Binding DigitCommand}"
                CommandParameter="4" />

        <Button Text="5"
                Grid.Row="3" Grid.Column="1" 
                Command="{Binding DigitCommand}"
                CommandParameter="5" />

        <Button Text="6"
                Grid.Row="3" Grid.Column="2" 
                Command="{Binding DigitCommand}"
                CommandParameter="6" />

        <Button Text="1"
                Grid.Row="4" Grid.Column="0" 
                Command="{Binding DigitCommand}"
                CommandParameter="1" />

        <Button Text="2"
                Grid.Row="4" Grid.Column="1" 
                Command="{Binding DigitCommand}"
                CommandParameter="2" />

        <Button Text="3"
                Grid.Row="4" Grid.Column="2" 
                Command="{Binding DigitCommand}"
                CommandParameter="3" />

        <Button Text="0"
                Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding DigitCommand}"
                CommandParameter="0" />

        <Button Text="&#x00B7;"
                Grid.Row="5" Grid.Column="2" 
                Command="{Binding DigitCommand}"
                CommandParameter="." />
    </Grid>
</ContentPage>
```

10 的數字和小數點的 11 按鈕共用繫結至`DigitCommand`。 `CommandParameter`區分這些按鈕。 若要設定的值`CommandParameter`通常都是做為小數點，為了加以釐清中間點字元就會顯示除了按鈕所顯示的文字一樣。

以下是程式中的動作：

[![十進位鍵盤](commanding-images/decimalkeyboard-small.png "十進位鍵盤")](commanding-images/decimalkeyboard-large.png#lightbox "十進位鍵盤")

請注意，所有三個螢幕擷取畫面中小數點的按鈕已停用，因為輸入的數字已經包含小數點。 

`DecimalKeypadViewModel`定義`Entry`型別的屬性`string`(這是觸發程序的唯一屬性`PropertyChanged`事件) 和三個屬性的型別`ICommand`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{
    string entry = "0";

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public string Entry
    {
        private set
        {
            if (entry != value)
            {
                entry = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Entry"));
            }
        }
        get
        {
            return entry;
        }
    }

    public ICommand ClearCommand { private set; get; }

    public ICommand BackspaceCommand { private set; get; }

    public ICommand DigitCommand { private set; get; }
}
```

按鈕對應至`ClearCommand`永遠啟用，而且只是設定為"0"的項目：

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {
        ClearCommand = new Command(
            execute: () =>
            {
                Entry = "0";
                RefreshCanExecutes();
            });

        ···
    
    }

    void RefreshCanExecutes()
    {
        ((Command)BackspaceCommand).ChangeCanExecute();
        ((Command)DigitCommand).ChangeCanExecute();
    }

    ···
    
}
```

因為永遠啟用按鈕，所以不需要指定`canExecute`引數中的`Command`建構函式。

輸入數字及退格邏輯是有點困難，因為如果沒有數字已輸入，然後在`Entry`屬性是字串"0"。 如果使用者輸入多個零，則`Entry`仍然包含只有一個零。 如果使用者輸入任何數字，該數字會取代為零。 但是，如果使用者輸入之前的任何其他數字、 小數點然後`Entry`為字串"0"。

**退格鍵**按鈕已啟用，只有當項目的長度大於 1，或是如果`Entry`不等於字串"0":

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        BackspaceCommand = new Command(
            execute: () =>
            {
                Entry = Entry.Substring(0, Entry.Length - 1);
                if (Entry == "")
                {
                    Entry = "0";
                }
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return Entry.Length > 1 || Entry != "0";
            });

        ···

    }

    ···
    
}
```

邏輯`execute`函式**退格鍵**按鈕時，可確保`Entry`這至少是"0"的字串。

`DigitCommand`屬性繫結至 11 按鈕，其中每個識別本身會向`CommandParameter`屬性。 `DigitCommand`無法設為一般的執行個體是`Command`類別，但它的容易使用`Command<T>`泛型類別。 使用 XAML 時，使用命令介面時`CommandParameter`屬性通常是字串，而這類型的泛型引數。 `execute`和`canExecute`函式則會有類型引數`string`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        DigitCommand = new Command<string>(
            execute: (string arg) =>
            {
                Entry += arg;
                if (Entry.StartsWith("0") && !Entry.StartsWith("0."))
                {
                    Entry = Entry.Substring(1);
                }
                RefreshCanExecutes();
            },
            canExecute: (string arg) =>
            {
                return !(arg == "." && Entry.Contains("."));
            });
    }

    ···
    
}
```

`execute`方法會將附加的字串引數`Entry`屬性。 不過，如果結果為開頭有零 （但不是零和小數點） 然後該初始零必須移除使用`Substring`函式。

`canExecute`方法會傳回`false`只有當引數是小數點 （表示小數點已被按下） 和`Entry`已經包含小數點。 

所有`execute`方法呼叫`RefreshCanExecutes`，然後呼叫`ChangeCanExecute`兩者`DigitCommand`和`ClearCommand`。 這可確保小數點及退格鍵按鈕都已啟用或停用根據目前的輸入的數字序列。

## <a name="adding-commands-to-existing-views"></a>將命令加入至現有的檢視

如果您想要使用命令介面與不支援的檢視，便可使用 Xamarin.Forms 行為，將事件轉換成命令。 這發行項中所述[**可重複使用 EventToCommandBehavior**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md)。

## <a name="asynchronous-commanding-for-navigation-menus"></a>非同步對於瀏覽功能表命令

指揮很方便，實作瀏覽功能表中，例如[**資料繫結示範**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)程式本身。 以下是部份**MainPage.xaml**:


```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MainPage"
             Title="Data Binding Demos"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection Title="Basic Bindings">

                <TextCell Text="Basic Code Binding"
                          Detail="Define a data-binding in code"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicCodeBindingPage}" />

                <TextCell Text="Basic XAML Binding"
                          Detail="Define a data-binding in XAML"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicXamlBindingPage}" />

                <TextCell Text="Alternative Code Binding"
                          Detail="Define a data-binding in code without a BindingContext"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:AlternativeCodeBindingPage}" />

                ···

            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

當使用以 XAML，指揮`CommandParameter`屬性通常會設為字串。 在此情況下，不過，XAML 標記延伸會使用以便`CommandParameter`的型別`System.Type`。

每個`Command`屬性繫結至具名屬性`NavigateCommand`。 屬性定義在程式碼後置檔案中， **MainPage.xaml.cs**:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(
            async (Type pageType) =>
            {
                Page page = (Page)Activator.CreateInstance(pageType);
                await Navigation.PushAsync(page);
            });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

建構函式設定`NavigateCommand`屬性`execute`具現化的方法`System.Type`參數，並接著移動它。 因為`PushAsync`呼叫需要`await`運算子，`execute`必須標示為非同步方法。 這是與`async`關鍵字前面的參數清單。 

建構函式也會設定`BindingContext`本身的頁面，讓繫結參考`NavigateCommand`此類別中。

這個建構函式中的程式碼的順序會形成差異：`InitializeComponent`呼叫會造成無法剖析 XAML，但在該時間屬性繫結命名`NavigateCommand`無法解析，因為`BindingContext`設`null`。 如果`BindingContext`設定於建構函式*之前*`NavigateCommand`設定，然後繫結可以解決當`BindingContext`設定，但在這段時間，`NavigateCommand`仍然是`null`。 設定`NavigateCommand`之後`BindingContext`會有繫結上的沒有作用，因為變更`NavigateCommand`不引發`PropertyChanged`事件及繫結並不知道，`NavigateCommand`現在是有效。

設定同時`NavigateCommand`和`BindingContext`（順序不拘） 之前呼叫`InitializeComponent`會運作，因為 XAML 剖析器遇到繫結定義時，會設定繫結的兩個元件。 

資料繫結有時可能很困難，但如您所見在這一系列的文章中，也是功能強大且靈活，幫助您，以區隔從使用者介面的基礎邏輯，以組織您的程式碼。



## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
