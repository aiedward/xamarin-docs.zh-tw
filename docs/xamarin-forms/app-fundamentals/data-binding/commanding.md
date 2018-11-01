---
title: Xamarin.Forms 命令介面
description: 這篇文章說明如何實作使用 Xamarin.Forms 資料繫結的 Command 屬性。 命令介面會提供實作命令的替代方法是比較適合 MVVM 架構。
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 68c7869254ae861cef8307431d925368082be921
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675242"
---
# <a name="the-xamarinforms-command-interface"></a>Xamarin.Forms 命令介面

在 Model View ViewModel (MVVM) 架構中，資料繫結所定義的 ViewModel，這通常是衍生自的類別中的屬性之間`INotifyPropertyChanged`，以及在檢視中，這通常是 XAML 檔案的屬性。 有時候應用程式有超越這些屬性繫結，透過要求若要啟動命令中的 ViewModel 的項目會影響使用者的需求。 這些命令通常會藉由按下按鈕即可收到訊號或手指點選，並在程式碼後置檔案中的處理常式的處理傳統上`Clicked`事件的`Button`或`Tapped`事件的`TapGestureRecognizer`。

命令介面會提供實作命令的替代方法是比較適合 MVVM 架構。 ViewModel 本身可以包含方法，例如執行的特定活動檢視中的命令`Button`按一下。 資料繫結所定義的這些命令之間， `Button`。

若要允許之間的資料繫結`Button`以及 ViewModel，`Button`定義兩個屬性：

- [`Command`](xref:Xamarin.Forms.Button.Command) 型別 [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) 型別 `Object`

若要使用命令介面，您會定義為目標的資料繫結`Command`的屬性`Button`來源所在的屬性類型的 ViewModel `ICommand`。 ViewModel 含有與它相關的程式碼`ICommand`按一下按鈕時執行的屬性。 您可以設定`CommandParameter`來區別多個按鈕，如果所有的任意資料繫結至相同`ICommand`在 ViewModel 的屬性。

`Command`和`CommandParameter`屬性也會定義下列類別：

- [`MenuItem`](xref:Xamarin.Forms.MenuItem) 因此， [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem)，衍生自 `MenuItem`
- [`TextCell`](xref:Xamarin.Forms.TextCell) 因此， [ `ImageCell` ](xref:Xamarin.Forms.ImageCell)，衍生自 `TextCell`
- [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)

[`SearchBar`](xref:Xamarin.Forms.SearchBar) 定義[ `SearchCommand` ](xref:Xamarin.Forms.SearchBar.SearchCommand)屬性的型別`ICommand`並[ `SearchCommandParameter` ](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)屬性。 [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand)屬性[ `ListView` ](xref:Xamarin.Forms.ListView)類型也是`ICommand`。

所有這些命令可以處理中的 ViewModel，不會相依於檢視中的特定的使用者介面物件的方式。

## <a name="the-icommand-interface"></a>ICommand 介面

[ `System.Windows.Input.ICommand` ](xref:System.Windows.Input.ICommand)介面不是 Xamarin.Forms 的一部分。 它改為定義於[System.Windows.Input](xref:System.Windows.Input)命名空間，而且包含兩個方法和一個事件：

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

若要使用命令介面，您的 ViewModel 包含型別的屬性`ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

ViewModel 還必須參考實作的類別`ICommand`介面。 這個類別會短時間內所述。 在檢視中，`Command`屬性`Button`繫結至該屬性：

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

當使用者按下`Button`，則`Button`呼叫`Execute`中的方法`ICommand`物件繫結至其`Command`屬性。 屬於最簡單的命令介面。

`CanExecute`方法是更複雜。 當第一次定義的繫結上`Command`屬性`Button`，並以某種方式變更的資料繫結時`Button`呼叫`CanExecute`中的方法`ICommand`物件。 如果`CanExecute`會傳回`false`，然後在`Button`停用。 這表示，特定命令目前為無法使用或無效。

`Button`也將處理常式附加上`CanExecuteChanged`事件的`ICommand`。 從 ViewModel 內引發事件。 當引發該事件時，`Button`呼叫`CanExecute`一次。 `Button`時，請啟用本身`CanExecute`會傳回`true`並停用`CanExecute`傳回`false`。

> [!IMPORTANT]
> 請勿使用`IsEnabled`屬性`Button`如果您使用命令介面。  

## <a name="the-command-class"></a>命令類別

當您的 ViewModel 定義類型的屬性`ICommand`、 ViewModel 必須也包含或參考類別可實作`ICommand`介面。 這個類別必須包含或參考`Execute`並`CanExecute`方法，並引發`CanExecuteChanged`事件每當`CanExecute`方法可能會傳回不同的值。

您可以自行撰寫這種類別，或者您可以使用其他人所撰寫的類別。 因為`ICommand`屬於 Microsoft Windows 的已使用多年來 Windows MVVM 應用程式。 使用 Windows 類別可實作`ICommand`可讓您共用您 Windows 應用程式和 Xamarin.Forms 應用程式之間的 Viewmodel。

如果共用之間 Windows 和 Xamarin.Forms 的 Viewmodel 無關，則您可以使用[ `Command` ](xref:Xamarin.Forms.Command)或是[ `Command<T>` ](xref:Xamarin.Forms.Command`1)類別中實作Xamarin.Forms包含`ICommand`介面。 這些類別可讓您指定的主體`Execute`和`CanExecute`類別建構函式中的方法。 使用 `Command<T>`當您使用`CommandParameter`屬性來區別多個檢視之間的繫結至相同`ICommand`屬性，且更簡單`Command`類別時，不一定。

## <a name="basic-commanding"></a>基本命令

**人員項目**頁面[**資料繫結示範**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)程式會示範如何實作 ViewModel 一些簡單的命令。

`PersonViewModel`會定義三個屬性，名為`Name`， `Age`，和`Skills`所定義的人員。 這個類別會執行*未*包含任何`ICommand`屬性：

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

`PersonCollectionViewModel`顯示以下會建立新的物件型別的`PersonViewModel`並允許使用者填入資料。 基於這個目的，此類別會定義屬性`IsEditing`型別的`bool`並`PersonEdit`型別的`PersonViewModel`。 此外，類別會定義三個屬性的型別`ICommand`和名為的屬性`Persons`型別的`IList<PersonViewModel>`:

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

這個縮寫的清單不包含類別的建構函式，是要在哪裡的三個屬性的型別`ICommand`定義，這將會很快顯示。 請注意，變更為三種類型的屬性`ICommand`而`Persons`屬性不會導致`PropertyChanged`所引發的事件。 這些屬性都會先建立類別時的設定，且不會被變更之後。

之前檢查的建構函式`PersonCollectionViewModel`類別中，讓我們看看的 XAML 檔案**人員項目**程式。 這包含`Grid`使用其`BindingContext`屬性設定為`PersonCollectionViewModel`。 `Grid`包含`Button`包含文字**新增**具有其`Command`屬性繫結至`NewCommand`ViewModel 屬性，屬性的項目表單繫結至`IsEditing`屬性，做為屬性以及`PersonViewModel`，且兩個按鈕的繫結至`SubmitCommand`和`CancelCommand`ViewModel 的屬性。 最終`ListView`顯示人員已輸入的集合：

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

以下是它的運作方式： 使用者第一次按下**新增** 按鈕。 這可讓項目表單，但是會停用**新增** 按鈕。 使用者再輸入一個名稱、 年齡和技能。 在編輯期間任何時間，使用者可以按下**取消** 按鈕，從頭開始。 已輸入的名稱和有效的存留期時只有**送出**已啟用 按鈕。 按下這**提交** 按鈕所顯示的集合來傳輸人員`ListView`。 之後**取消**或是**提交**按下按鈕時，會清除項目表單和**新增**按鈕再次啟用。

在左側的 [iOS] 畫面會顯示配置，先輸入有效的存留期。 Android 和 UWP 螢幕顯示**送出**啟用 byla nastavena 的 age 的按鈕：

[![人員的項目](commanding-images/personentry-small.png "人員項目")](commanding-images/personentry-large.png#lightbox "人員項目")

程式沒有任何設備編輯現有的項目，並不會儲存項目，當您離開頁面時。

所有的邏輯**新增**，**提交**，並**取消**按鈕會在處理`PersonCollectionViewModel`透過定義`NewCommand`， `SubmitCommand`，和`CancelCommand`屬性。 建構函式`PersonCollectionViewModel`類型的物件來設定這三個屬性`Command`。  

A[建構函式](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean}))的`Command`類別可讓您傳遞引數的型別`Action`並`Func<bool>`對應`Execute`和`CanExecute`方法。 它是最簡單的方式定義這些動作和函式做為 lambda 函式直接在`Command`建構函式。 以下是定義`Command`物件`NewCommand`屬性：

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

當使用者按一下**的新** 按鈕，`execute`函式傳遞至`Command`建構函式執行。 這會建立新`PersonViewModel`物件，該物件上設定的處理常式`PropertyChanged`事件，設定`IsEditing`要`true`，並呼叫`RefreshCanExecutes`定義建構函式之後的方法。

除了實作`ICommand`介面，`Command`類別也會定義一個名為方法`ChangeCanExecute`。 應該呼叫您的 ViewModel `ChangeCanExecute` for`ICommand`屬性時，發生任何事情可能會變更的傳回值`CanExecute`方法。 呼叫`ChangeCanExecute`會導致`Command`類別來引發`CanExecuteChanged`方法。 `Button`已附加該事件的處理常式，並藉由呼叫回應`CanExecute`同樣地，然後再啟用本身根據該方法的傳回值。

當`execute`方法`NewCommand`呼叫`RefreshCanExecutes`，則`NewCommand`屬性會取得呼叫`ChangeCanExecute`，而`Button`呼叫`canExecute`方法，它現在會傳回`false`因為`IsEditing`內容現在是`true`。

`PropertyChanged`新的處理常式`PersonViewModel`物件會呼叫`ChangeCanExecute`方法`SubmitCommand`。 以下是如何實作該命令的屬性：


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

`canExecute`函式`SubmitCommand`每次變更中屬性，稱為`PersonViewModel`正在編輯的物件。 它會傳回`true`時，才`Name`屬性是至少 1 個字元，並`Age`大於 0。 屆時**送出**按鈕即變成啟用。

`execute`函式**提交**移除屬性變更處理常式，從`PersonViewModel`，將物件加入`Persons`集合，並傳回所有項目到初始的情況。

`execute`函式**取消**按鈕執行作業的所有項目，**提交**除外 按鈕加入至集合的物件：

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

`canExecute`方法會傳回`true`隨時`PersonViewModel`正在編輯。

這些技術可能是適用於更複雜的案例： 中的屬性`PersonCollectionViewModel`無法繫結至`SelectedItem`屬性`ListView`來編輯現有的項目，以及**刪除**按鈕可能會新增到刪除這些項目。

不需要定義`execute`和`canExecute`做為 lambda 函式的方法。 您可以在 ViewModel 中撰寫為一般的私用方法，並參考在`Command`建構函式。 不過，這種方法沒有通常會導致大量的 ViewModel 中只有一次所參考的方法。

## <a name="using-command-parameters"></a>使用命令參數

可能會很方便的一個或多個按鈕 （或其他使用者介面物件） 會共用相同`ICommand`在 ViewModel 的屬性。 在此案例中，您使用`CommandParameter`屬性來區別的按鈕。

您可以繼續使用`Command`類別，這些共用`ICommand`屬性。 類別會定義[替代的建構函式](xref:Xamarin.Forms.Command.%23ctor(System.Action{System.Object},System.Func{System.Object,System.Boolean}))它會接受`execute`並`canExecute`具有參數的型別方法`Object`。 這是如何`CommandParameter`傳遞給這些方法。

不過，使用時`CommandParameter`，它是最簡單的方式使用泛型[ `Command<T>` ](xref:Xamarin.Forms.Command`1)類別，以指定的設定為物件類型`CommandParameter`。 `execute`和`canExecute`您指定的方法有該類型的參數。

**十進位鍵盤**頁面說明這項技巧，藉由示範如何實作輸入十進位數字鍵台。 `BindingContext` For`Grid`是`DecimalKeypadViewModel`。 `Entry`此 ViewModel 屬性繫結至`Text`屬性`Label`。 所有`Button`物件會繫結中的 ViewModel 的各種命令： `ClearCommand`， `BackspaceCommand`，和`DigitCommand`:

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

10 的數字和小數點的 11 按鈕共用繫結至`DigitCommand`。 `CommandParameter`區分這些按鈕。 將值設為`CommandParameter`通常是除了小數點，為了清楚起見中間點字元就會顯示按鈕所顯示的文字相同。

以下是作用中的程式：

[![十進位鍵盤](commanding-images/decimalkeyboard-small.png "十進位鍵盤")](commanding-images/decimalkeyboard-large.png#lightbox "十進位鍵盤")

請注意，所有三個螢幕擷取畫面中小數點的按鈕停用，因為輸入的數字已包含小數點。

`DecimalKeypadViewModel`定義`Entry`型別的屬性`string`(這是唯一的屬性，以觸發`PropertyChanged`事件) 和三個屬性的型別`ICommand`:

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

對應至的按鈕`ClearCommand`永遠啟用，而且只是設定為"0"的項目：

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

因為永遠啟用按鈕，所以不需要指定`canExecute`中的引數`Command`建構函式。

輸入數字和退格的邏輯很難，因為如果已不輸入任何數字，則`Entry`屬性是字串"0"。 如果使用者輸入多個零，則`Entry`仍會包含只有一個零。 如果使用者輸入任何其他數字，該數字會取代為零。 但是，如果使用者輸入之前任何其他數字、 小數點然後`Entry`是"0"。 此字串。

**退格鍵** 按鈕已啟用，只有項目的長度大於 1，或如果`Entry`不等於字串"0":

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

邏輯`execute`函式**退格鍵** 按鈕可確保`Entry`是至少一個"0"的字串。

`DigitCommand`屬性繫結至 11 的按鈕，其中每一個識別本身，而`CommandParameter`屬性。 `DigitCommand`無法設定的一般執行個體`Command`類別，但它的使用變得更容易`Command<T>`泛型類別。 使用 XAML，命令介面時`CommandParameter`屬性通常是字串，而且，泛型引數的類型。 `execute`並`canExecute`函式則會有類型引數`string`:

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

`execute`方法會將附加的字串引數`Entry`屬性。 不過，如果結果是以零 （但不是為零值和小數點） 然後該初始零必須移除使用`Substring`函式。

`canExecute`方法會傳回`false`只有當引數是小數點 （表示，按下小數點） 和`Entry`已經包含小數點。

所有`execute`方法會呼叫`RefreshCanExecutes`，然後呼叫`ChangeCanExecute`兩者`DigitCommand`和`ClearCommand`。 這可確保小數點和退格鍵按鈕都已啟用或停用根據目前的輸入的數字序列。

## <a name="adding-commands-to-existing-views"></a>將命令加入至現有的檢視

如果您想要使用不支援它的檢視中的命令介面，就可以使用 Xamarin.Forms 行為將事件轉換成命令。 這本文所述[**可重複使用 EventToCommandBehavior**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md)。

## <a name="asynchronous-commanding-for-navigation-menus"></a>非同步巡覽功能表命令

命令是實作導覽功能表，例如，在方便[**資料繫結示範**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)程式本身。 以下是部份**MainPage.xaml**:

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

使用的 XAML，命令執行時`CommandParameter`屬性通常會設為字串。 只有在使用 XAML 標記延伸不過，在此情況下，如此`CommandParameter`別的`System.Type`。

每個`Command`屬性繫結至屬性，名為`NavigateCommand`。 屬性定義在程式碼後置檔案中， **MainPage.xaml.cs**:

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

建構函式集合`NavigateCommand`屬性，以`execute`具現化方法`System.Type`參數，然後巡覽至它。 因為`PushAsync`呼叫都需要`await`運算子，`execute`必須標示為非同步方法。 這可完成`async`關鍵字前面的參數清單。

建構函式也會設定`BindingContext`本身的頁面以便繫結參考`NavigateCommand`這個類別中。

這個建構函式中的程式碼的順序會形成差異：`InitializeComponent`呼叫會導致要剖析的 XAML，但在該時間命名之屬性繫結`NavigateCommand`因為無法解析`BindingContext`設定為`null`。 如果`BindingContext`建構函式中設定*之前*`NavigateCommand`設定，則繫結是可解析時`BindingContext`設定，但屆時`NavigateCommand`仍`null`。 設定`NavigateCommand`之後`BindingContext`將不會影響繫結，因為變更`NavigateCommand`並不會引發`PropertyChanged`事件，並繫結不知道的`NavigateCommand`現在是有效。

設定兩者`NavigateCommand`並`BindingContext`（依任意順序） 之前呼叫`InitializeComponent`會運作，因為 XAML 剖析器遇到繫結定義時，會設定繫結的兩個元件。

資料繫結有時可能需要一些技巧，但是您在這一系列的文章中所見，這些功能強大而且靈活，而且來組織您的程式碼分隔從使用者介面的基礎邏輯的大有幫助。

## <a name="related-links"></a>相關連結

- [資料繫結示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [資料繫結 Xamarin.Forms 書籍章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
