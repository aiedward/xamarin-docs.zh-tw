---
title: 自訂 ViewCell
description: Xamarin.Forms ViewCell 就是資料格可以加入至 ListView 或 TableView，其中包含開發人員定義的檢視。 本文示範如何建立裝載於 Xamarin.Forms ListView 控制項 ViewCell 自訂轉譯器。
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 2011049180aa47b7be68486d4f30bd356e2ba813
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241799"
---
# <a name="customizing-a-viewcell"></a>自訂 ViewCell

_Xamarin.Forms ViewCell 就是資料格可以加入至 ListView 或 TableView，其中包含開發人員定義的檢視。本文示範如何建立裝載於 Xamarin.Forms ListView 控制項 ViewCell 自訂轉譯器。這會停止從 Xamarin.Forms 配置計算期間 ListView 捲動重複呼叫。_

每個 Xamarin.Forms 儲存格有隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) Xamarin.Forms 應用程式，在 iOS 中呈現`ViewCellRenderer`類別具現化，這又會具現化原生`UITableViewCell`控制項。 Android 平台上，`ViewCellRenderer`類別具現化的原生`View`控制項。 在通用 Windows 平台 (UWP)，`ViewCellRenderer`類別具現化的原生`DataTemplate`。 如需有關轉譯器，而且 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱[轉譯器的基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)和對應的原生控制項實作它：

![](viewcell-images/viewcell-classes.png "ViewCell 控制項實作的原生控制項之間的關聯性")

轉譯程序可以採取利用平台專屬的自訂實作所建立的自訂轉譯器[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)每個平台上。 執行此程序如下所示：

1. [建立](#Creating_the_Custom_Cell)Xamarin.Forms 自訂儲存格。
1. [取用](#Consuming_the_Custom_Cell)Xamarin.Forms 的自訂儲存格。
1. [建立](#Creating_the_Custom_Renderer_on_each_Platform)每個平台上的資料格的自訂轉譯器。

每個項目將現在依次討論實作`NativeCell`利用裝載在 Xamarin.Forms 內，每個資料格的版面配置是平台特定的轉譯器[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)控制項。 這會停止從重複呼叫期間 Xamarin.Forms 配置計算`ListView`捲動。

<a name="Creating_the_Custom_Cell" />

## <a name="creating-the-custom-cell"></a>建立自訂儲存格

您可以建立自訂儲存格控制項子類別化[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)類別，如下列程式碼範例所示：

```csharp
public class NativeCell : ViewCell
{
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(NativeCell), "");

  public string Name {
    get { return (string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }

  public static readonly BindableProperty CategoryProperty =
    BindableProperty.Create ("Category", typeof(string), typeof(NativeCell), "");

  public string Category {
    get { return (string)GetValue (CategoryProperty); }
    set { SetValue (CategoryProperty, value); }
  }

  public static readonly BindableProperty ImageFilenameProperty =
    BindableProperty.Create ("ImageFilename", typeof(string), typeof(NativeCell), "");

  public string ImageFilename {
    get { return (string)GetValue (ImageFilenameProperty); }
    set { SetValue (ImageFilenameProperty, value); }
  }
}
```
`NativeCell`類別的.NET 標準程式庫專案中建立，並定義自訂的儲存格的 API。 自訂儲存格公開`Name`， `Category`，和`ImageFilename`資料繫結所能顯示的屬性。 如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Consuming_the_Custom_Cell" />

## <a name="consuming-the-custom-cell"></a>使用自訂的儲存格

`NativeCell`自訂儲存格可以在 Xaml 中參考.NET 標準程式庫專案中，藉由宣告它的位置的命名空間和自訂資料格元素上使用的命名空間前置詞。 下列程式碼範例示範如何`NativeCell`自訂儲存格可供 XAML 頁面：

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <StackLayout>
              <Label Text="Xamarin.Forms native cell" HorizontalTextAlignment="Center" />
              <ListView x:Name="listView" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                  <ListView.ItemTemplate>
                      <DataTemplate>
                          <local:NativeCell Name="{Binding Name}" Category="{Binding Category}" ImageFilename="{Binding ImageFilename}" />
                      </DataTemplate>
                  </ListView.ItemTemplate>
              </ListView>
          </StackLayout>
      </ContentPage.Content>
</ContentPage>
```

`local`命名空間前置詞可以命名為任何項目。 不過，`clr-namespace`和`assembly`值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，前置詞用來參考自訂儲存格。

下列程式碼範例示範如何`NativeCell`自訂儲存格可供 C# 頁面：

```csharp
public class NativeCellPageCS : ContentPage
{
    ListView listView;

    public NativeCellPageCS()
    {
        listView = new ListView(ListViewCachingStrategy.RecycleElement)
        {
            ItemsSource = DataSource.GetList(),
            ItemTemplate = new DataTemplate(() =>
            {
                var nativeCell = new NativeCell();
                nativeCell.SetBinding(NativeCell.NameProperty, "Name");
                nativeCell.SetBinding(NativeCell.CategoryProperty, "Category");
                nativeCell.SetBinding(NativeCell.ImageFilenameProperty, "ImageFilename");

                return nativeCell;
            })
        };

        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
                Padding = new Thickness(0, 20, 0, 0);
                break;
            case Device.Android:
            case Device.UWP:
                Padding = new Thickness(0);
                break;
        }

        Content = new StackLayout
        {
            Children = {
                new Label { Text = "Xamarin.Forms native cell", HorizontalTextAlignment = TextAlignment.Center },
                listView
            }
        };
        listView.ItemSelected += OnItemSelected;
    }
    ...
}
```

Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)控制項用來顯示一份資料，填入透過[ `ItemSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%601.ItemsSource/)屬性。 [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)快取策略會嘗試最小化`ListView`記憶體耗用量和執行速度所回收清單資料格。 如需詳細資訊，請參閱[快取策略](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)。

在清單中的每個資料列包含三個項目資料 – 名稱、 類別和影像檔案名稱。 在清單中的每個資料列的配置由定義`DataTemplate`透過參考[ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%601.ItemTemplate/)可繫結屬性。 `DataTemplate`會定義每個清單中的資料列將會`NativeCell`會顯示其`Name`， `Category`，和`ImageFilename`透過資料繫結的屬性。 如需有關`ListView`控制，請參閱[ListView](~/xamarin-forms/user-interface/listview/index.md)。

自訂轉譯器現在可以新增至自訂每個資料格的平台專屬配置每個應用程式專案。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>每個平台上建立的自訂轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`ViewCellRenderer`呈現自訂儲存格的類別。
1. 覆寫特定平台方法呈現的自訂儲存格，撰寫邏輯來加以自訂。
1. 新增`ExportRenderer`屬性來指定，它會用來呈現 Xamarin.Forms 自訂儲存格的自訂轉譯器類別。 此屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 大部分的 Xamarin.Forms 項目，則是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，將使用預設的產生器控制項的基底類別。 不過，自訂轉譯器所需每個平台專案中時呈現[ViewCell](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/)項目。

下圖說明範例應用程式，以及它們之間的關聯性中的每一個專案的責任：

![](viewcell-images/solution-structure.png "NativeCell 自訂轉譯器專案責任")

`NativeCell`自訂資料格會轉譯由平台特定轉譯器類別，這些全都衍生自`ViewCellRenderer`每個平台的類別。 這會導致每個`NativeCell`自訂儲存格呈現具有特定平台的配置，如下列螢幕擷取畫面所示：

![](viewcell-images/screenshots.png "每個平台上 NativeCell")

`ViewCellRenderer`類別會公開特定平台的方法，以呈現自訂儲存格。 這是`GetCell`iOS 平台上的方法`GetCellCore`Android 平台上的方法和`GetTemplate`UWP 上的方法。

每個自訂轉譯器類別以裝飾`ExportRenderer`xamarin.forms 註冊轉譯器的屬性。 屬性會採用兩個參數 – Xamarin.Forms 儲存格所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列章節會討論每個平台專屬的自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例會顯示 iOS 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeiOSCellRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        NativeiOSCell cell;

        public override UITableViewCell GetCell(Cell item, UITableViewCell reusableCell, UITableView tv)
        {
            var nativeCell = (NativeCell)item;

            cell = reusableCell as NativeiOSCell;
            if (cell == null)
                cell = new NativeiOSCell(item.GetType().FullName, nativeCell);
            else
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;
            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

`GetCell`建置會顯示每個資料格所呼叫的方法。 每個資料格是`NativeiOSCell`執行個體，定義的資料格，而其資料的版面配置。 作業`GetCell`方法會取決於[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)快取策略：

- 當[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)快取策略是[ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/)、`GetCell`方法會叫用每個資料格。 A`NativeiOSCell`將建立執行個體，每個`NativeCell`最初顯示在螢幕的執行個體。 當使用者捲動`ListView`，`NativeiOSCell`執行個體將會重新使用。 如需 iOS 重複使用資料格的詳細資訊，請參閱[重複使用的儲存格](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。

  > [!NOTE]
  > 此自訂轉譯器程式碼會執行一些儲存格重複使用，即使[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)設定為保留的資料格。

  每個所顯示的資料`NativeiOSCell`執行個體，將來自每個資料更新是否新建立或重複使用，`NativeCell`執行個體之`UpdateCell`方法。

  > [!NOTE]
  > `OnNativeCellPropertyChanged`方法絕對不會叫用時[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)快取策略設定為保留的資料格。

- 當[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)快取策略是[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)、`GetCell`方法會叫用每個資料格最初顯示在螢幕上。 A`NativeiOSCell`將建立執行個體，每個`NativeCell`最初顯示在螢幕的執行個體。 每個所顯示的資料`NativeiOSCell`執行個體將更新的資料`NativeCell`執行個體之`UpdateCell`方法。 不過，`GetCell`將不會叫用方法，以在使用者捲動`ListView`。 相反地，`NativeiOSCell`執行個體將會重新使用。 `PropertyChanged` 在上，將會引發事件`NativeCell`當其資料變更時，執行個體和`OnNativeCellPropertyChanged`事件處理常式將會更新每個重複使用資料`NativeiOSCell`執行個體。

下列程式碼範例示範`OnNativeCellPropertyChanged`時已叫用的方法`PropertyChanged`引發：

```csharp
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingLabel.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingLabel.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.CellImageView.Image = cell.GetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

這個方法會更新所顯示的資料重複使用`NativeiOSCell`執行個體。 進行變更之屬性的核取，因為此方法可以呼叫多次。

`NativeiOSCell`類別定義，每個儲存格的版面配置和下列程式碼範例所示：

```csharp
internal class NativeiOSCell : UITableViewCell, INativeElementView
{
  public UILabel HeadingLabel { get; set; }
  public UILabel SubheadingLabel { get; set; }
  public UIImageView CellImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeiOSCell(string cellId, NativeCell cell) : base(UITableViewCellStyle.Default, cellId)
  {
    NativeCell = cell;

    SelectionStyle = UITableViewCellSelectionStyle.Gray;
    ContentView.BackgroundColor = UIColor.FromRGB(255, 255, 224);
    CellImageView = new UIImageView();

    HeadingLabel = new UILabel()
    {
      Font = UIFont.FromName("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB(127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    SubheadingLabel = new UILabel()
    {
      Font = UIFont.FromName("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB(38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add(HeadingLabel);
    ContentView.Add(SubheadingLabel);
    ContentView.Add(CellImageView);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingLabel.Text = cell.Name;
    SubheadingLabel.Text = cell.Category;
    CellImageView.Image = GetImage(cell.ImageFilename);
  }

  public UIImage GetImage(string filename)
  {
    return (!string.IsNullOrWhiteSpace(filename)) ? UIImage.FromFile("Images/" + filename + ".jpg") : null;
  }

  public override void LayoutSubviews()
  {
    base.LayoutSubviews();

    HeadingLabel.Frame = new CGRect(5, 4, ContentView.Bounds.Width - 63, 25);
    SubheadingLabel.Frame = new CGRect(100, 18, 100, 20);
    CellImageView.Frame = new CGRect(ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

這個類別會定義用來呈現資料格的內容和其配置的控制項。 此類別會實作[ `INativeElementView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INativeElementView/)介面時，需要[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)使用[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)快取策略。 這個介面可讓您指定的類別必須實作[ `Element` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INativeElementView.Element/)屬性，這應傳回回收儲存格的自訂儲存格資料。

`NativeiOSCell`建構函式初始化的外觀`HeadingLabel`， `SubheadingLabel`，和`CellImageView`屬性。 這些屬性用來顯示資料儲存在`NativeCell`執行個體，與`UpdateCell`方法呼叫來設定每個屬性的值。 此外，當[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)使用[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)快取策略，所顯示的資料`HeadingLabel`， `SubheadingLabel`，和`CellImageView`屬性可以是更新`OnNativeCellPropertyChanged`中自訂轉譯器的方法。

資料格的版面配置由執行`LayoutSubviews`覆寫，可設定的座標`HeadingLabel`， `SubheadingLabel`，和`CellImageView`與資料格內。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立的自訂轉譯器

下列程式碼範例會顯示 Android 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeAndroidCellRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        NativeAndroidCell cell;

        protected override Android.Views.View GetCellCore(Cell item, Android.Views.View convertView, ViewGroup parent, Context context)
        {
            var nativeCell = (NativeCell)item;
            Console.WriteLine("\t\t" + nativeCell.Name);

            cell = convertView as NativeAndroidCell;
            if (cell == null)
            {
                cell = new NativeAndroidCell(context, nativeCell);
            }
            else
            {
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;
            }

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;

            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

`GetCellCore`建置會顯示每個資料格所呼叫的方法。 每個資料格是`NativeAndroidCell`執行個體，定義的資料格，而其資料的版面配置。 作業`GetCellCore`方法會取決於[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)快取策略：

- 當[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)快取策略是[ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/)、`GetCellCore`方法會叫用每個資料格。 A`NativeAndroidCell`將會建立每個`NativeCell`最初顯示在螢幕的執行個體。 當使用者捲動`ListView`，`NativeAndroidCell`執行個體將會重新使用。 如需重複使用 Android 的儲存格的詳細資訊，請參閱[列檢視重複使用](~/android/user-interface/layouts/list-view/populating.md)。

  > [!NOTE]
  > 請注意，此自訂轉譯器程式碼會執行某些資料格重複使用，即使[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)設定為保留的資料格。

  每個所顯示的資料`NativeAndroidCell`執行個體，將來自每個資料更新是否新建立或重複使用，`NativeCell`執行個體之`UpdateCell`方法。

  > [!NOTE]
  > 請注意，當`OnNativeCellPropertyChanged`方法會叫用時[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)是設定為保留的資料格時，不會更新`NativeAndroidCell`屬性值。

- 當[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)快取策略是[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)、`GetCellCore`方法會叫用每個資料格最初顯示在螢幕上。 A`NativeAndroidCell`將建立執行個體，每個`NativeCell`最初顯示在螢幕的執行個體。 每個所顯示的資料`NativeAndroidCell`執行個體將更新的資料`NativeCell`執行個體之`UpdateCell`方法。 不過，`GetCellCore`將不會叫用方法，以在使用者捲動`ListView`。 相反地，`NativeAndroidCell`執行個體將會重新使用。  `PropertyChanged` 在上，將會引發事件`NativeCell`當其資料變更時，執行個體和`OnNativeCellPropertyChanged`事件處理常式將會更新每個重複使用資料`NativeAndroidCell`執行個體。

下列程式碼範例示範`OnNativeCellPropertyChanged`時已叫用的方法`PropertyChanged`引發：

```csharp
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingTextView.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingTextView.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.SetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

這個方法會更新所顯示的資料重複使用`NativeAndroidCell`執行個體。 進行變更之屬性的核取，因為此方法可以呼叫多次。

`NativeAndroidCell`類別定義，每個儲存格的版面配置和下列程式碼範例所示：

```csharp
internal class NativeAndroidCell : LinearLayout, INativeElementView
{
  public TextView HeadingTextView { get; set; }
  public TextView SubheadingTextView { get; set; }
  public ImageView ImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeAndroidCell(Context context, NativeCell cell) : base(context)
  {
    NativeCell = cell;

    var view = (context as Activity).LayoutInflater.Inflate(Resource.Layout.NativeAndroidCell, null);
    HeadingTextView = view.FindViewById<TextView>(Resource.Id.HeadingText);
    SubheadingTextView = view.FindViewById<TextView>(Resource.Id.SubheadingText);
    ImageView = view.FindViewById<ImageView>(Resource.Id.Image);

    AddView(view);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingTextView.Text = cell.Name;
    SubheadingTextView.Text = cell.Category;

    // Dispose of the old image
    if (ImageView.Drawable != null)
    {
      using (var image = ImageView.Drawable as BitmapDrawable)
      {
        if (image != null)
        {
          if (image.Bitmap != null)
          {
            image.Bitmap.Dispose();
          }
        }
      }
    }

    SetImage(cell.ImageFilename);
  }

  public void SetImage(string filename)
  {
    if (!string.IsNullOrWhiteSpace(filename))
    {
      // Display new image
      Context.Resources.GetBitmapAsync(filename).ContinueWith((t) =>
      {
        var bitmap = t.Result;
        if (bitmap != null)
        {
          ImageView.SetImageBitmap(bitmap);
          bitmap.Dispose();
        }
      }, TaskScheduler.FromCurrentSynchronizationContext());
    }
    else
    {
      // Clear the image
      ImageView.SetImageBitmap(null);
    }
  }
}
```

這個類別會定義用來呈現資料格的內容和其配置的控制項。 此類別會實作[ `INativeElementView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INativeElementView/)介面時，需要[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)使用[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)快取策略。 這個介面可讓您指定的類別必須實作[ `Element` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INativeElementView.Element/)屬性，這應傳回回收儲存格的自訂儲存格資料。

`NativeAndroidCell`建構函式會擴大`NativeAndroidCell`版面配置，並初始化`HeadingTextView`， `SubheadingTextView`，和`ImageView`擴大配置中控制項的屬性。 這些屬性用來顯示資料儲存在`NativeCell`執行個體，與`UpdateCell`方法呼叫來設定每個屬性的值。 此外，當[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)使用[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/)快取策略，所顯示的資料`HeadingTextView`， `SubheadingTextView`，和`ImageView`屬性可以是更新`OnNativeCellPropertyChanged`中自訂轉譯器的方法。

下列程式碼範例示範的版面配置定義`NativeAndroidCell.axml`配置檔案：

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="8dp"
    android:background="@drawable/CustomSelector">
    <LinearLayout
        android:id="@+id/Text"
        android:orientation="vertical"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="10dip">
        <TextView
            android:id="@+id/HeadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/SubheadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="14dip"
            android:textColor="#FF267F00"
            android:paddingLeft="100dip" />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout>
```

此配置命名為指定的兩個`TextView`控制項和`ImageView`控制項用來顯示儲存格的內容。 這兩個`TextView`控制項的垂直方向內`LinearLayout`控制項所內含的所有控制項`RelativeLayout`。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上建立的自訂轉譯器

下列程式碼範例示範 UWP 的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeUWPCellRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPCellRenderer : ViewCellRenderer
    {
        public override Windows.UI.Xaml.DataTemplate GetTemplate(Cell cell)
        {
            return App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
        }
    }
}
```

`GetTemplate`方法呼叫以傳回要轉譯的每個清單中的資料列的資料格。 它會建立`DataTemplate`每個`NativeCell`執行個體，其中會顯示在畫面上，`DataTemplate`定義外觀和儲存格的內容。

`DataTemplate`會儲存在應用程式層級的資源字典，並在下列程式碼範例所示：

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="LightYellow">
        <Grid.Resources>
            <local:ConcatImageExtensionConverter x:Name="ConcatImageExtensionConverter" />
        </Grid.Resources>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.40*" />
            <ColumnDefinition Width="0.40*"/>
            <ColumnDefinition Width="0.20*" />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.ColumnSpan="2" Foreground="#7F3300" FontStyle="Italic" FontSize="22" VerticalAlignment="Top" Text="{Binding Name}" />
        <TextBlock Grid.RowSpan="2" Grid.Column="1" Foreground="#267F00" FontWeight="Bold" FontSize="12" VerticalAlignment="Bottom" Text="{Binding Category}" />
        <Image Grid.RowSpan="2" Grid.Column="2" HorizontalAlignment="Left" VerticalAlignment="Center" Source="{Binding ImageFilename, Converter={StaticResource ConcatImageExtensionConverter}}" Width="50" Height="50" />
        <Line Grid.Row="1" Grid.ColumnSpan="3" X1="0" X2="1" Margin="30,20,0,0" StrokeThickness="1" Stroke="LightGray" Stretch="Fill" VerticalAlignment="Bottom" />
    </Grid>
</DataTemplate>
```

`DataTemplate`指定用來顯示資料格，其版面配置和外觀的內容的控制項。 兩個`TextBlock`控制項和`Image`控制項用來顯示透過資料繫結的儲存格的內容。 此外，執行個體`ConcatImageExtensionConverter`用來串連`.jpg`檔案副檔名為每個映像檔案名稱。 如此可確保`Image`控制項可以載入並呈現影像時，它`Source`屬性設定。

## <a name="summary"></a>總結

本文示範如何建立自訂轉譯器[ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) Xamarin.Forms 內裝載[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)控制項。 這會停止從重複呼叫期間 Xamarin.Forms 配置計算`ListView`捲動。


## <a name="related-links"></a>相關連結

- [ListView 效能](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
