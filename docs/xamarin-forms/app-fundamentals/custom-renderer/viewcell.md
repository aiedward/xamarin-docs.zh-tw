---
title: 自訂 ViewCell
description: Xamarin.Forms ViewCell 是可以新增至 ListView 或 TableView 的資料格，其包含開發人員定義檢視。 本文示範如何建立裝載在 Xamarin.Forms ListView 控制項內的自訂 ViewCell 轉譯器。
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 6fd952fc929fc11d19d8ceb0153aa8d7702a21a7
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889232"
---
# <a name="customizing-a-viewcell"></a>自訂 ViewCell

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)

_Xamarin.Forms ViewCell 是可以新增至 ListView 或 TableView 的資料格，其包含開發人員定義檢視。本文示範如何建立裝載在 Xamarin.Forms ListView 控制項內的自訂 ViewCell 轉譯器。這會停止在 ListView 捲動期間重複呼叫 Xamarin.Forms 配置計算。_

每個 Xamarin.Forms 資料格都隨附每個平台的轉譯器，這些平台可建立原生控制項的執行個體。 當 Xamarin.Forms 應用程式轉譯 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 時，在 iOS 中，`ViewCellRenderer` 類別會具現化，並依序具現化原生的 `UITableViewCell` 控制項。 在 Android 平台上，`ViewCellRenderer` 類別會具現化原生的 `View` 控制項。 在通用 Windows 平台 (UWP) 上，`ViewCellRenderer` 類別會具現化原生的 `DataTemplate`。 如需 Xamarin.Forms 控制項對應的轉譯器和原生控制項類別詳細資訊，請參閱[轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 和實作它之對應原生控制項間的關聯性：

![](viewcell-images/viewcell-classes.png "ViewCell 控制項與實作原生控制項之間的關聯性")

您可在每個平台上建立 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 的自訂轉譯器，利用轉譯程序實作平台特定的自訂。 執行這項作業的程序如下：

1. [建立](#Creating_the_Custom_Cell) Xamarin.Forms 自訂資料格。
1. [使用](#Consuming_the_Custom_Cell) Xamarin.Forms 的自訂資料格。
1. 在每個平台上[建立](#Creating_the_Custom_Renderer_on_each_Platform)資料格的自訂轉譯器。

現在可以依序討論每個項目，實作利用平台特定配置的 `NativeCell` 轉譯器，處理 Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 控制項內裝載的每個資料格。 這會停止在 `ListView` 捲動期間重複呼叫 Xamarin.Forms 配置計算。

<a name="Creating_the_Custom_Cell" />

## <a name="creating-the-custom-cell"></a>建立自訂資料格

您可以子類別化 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 類別，建立自訂的資料格控制項，如下列程式碼範例所示：

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

在 .NET Standard 程式庫專案中建立的 `NativeCell` 類別，會為自訂資料格定義 API。 自訂資料格會公開可透過資料繫結顯示的 `Name`、`Category` 和 `ImageFilename` 屬性。 如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Consuming_the_Custom_Cell" />

## <a name="consuming-the-custom-cell"></a>使用自訂的資料格

您可以宣告自訂資料格的位置命名空間並使用自訂資料格項目上的命名空間前置詞，在 .NET Standard 程式庫專案的 XAML 中參考 `NativeCell` 自訂資料格。 下列程式碼範例示範 XAML 頁面可如何使用 `NativeCell` 自訂資料格：

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

`local` 命名空間前置詞沒有命名限制。 不過，`clr-namespace` 和 `assembly` 值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，即會使用前置詞來參考自訂資料格。

下列程式碼範例示範 C# 頁面如何使用 `NativeCell` 自訂資料格：

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

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 控制項用以顯示透過 [`ItemSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) 屬性填入的資料清單。 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 快取策略嘗試透過回收清單資料格，將 `ListView` 磁碟使用量和執行速度降至最低。 如需詳細資訊，請參閱[快取資料](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)。

清單中的每個資料列都包含三個資料項目 – 名稱、類別和影像檔案名稱。 清單中每個資料列的配置都是由 `DataTemplate` 所定義，它是透過 [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) 可繫結屬性所參考。 `DataTemplate` 將清單中的每個資料列定義為 `NativeCell`，透過資料繫結顯示其 `Name`、`Category` 和 `ImageFilename` 屬性。 如需 `ListView` 控制項的詳細資訊，請參閱 [ListView](~/xamarin-forms/user-interface/listview/index.md)。

自訂轉譯器現在可以新增至每個應用程式專案，為每個資料格自訂平台特定的配置。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每個平台上建立自訂轉譯器

建立自訂轉譯器類別的程序如下：

1. 建立轉譯自訂資料格之 `ViewCellRenderer` 類別的子類別。
1. 覆寫轉譯自訂資料格的平台特定方法，並撰寫自訂方法的邏輯。
1. 將 `ExportRenderer` 屬性新增至自訂轉譯器類別，指定使用它來轉譯 Xamarin.Forms 自訂資料格。 這個屬性用來向 Xamarin.Forms 註冊自訂轉譯器。

> [!NOTE]
> 對大部分的 Xamarin.Forms 項目而言，可以選擇是否在每個平台專案中提供自訂轉譯器。 如果自訂轉譯器未註冊，則會使用控制項基底類別的預設轉譯器。 不過，轉譯 [ViewCell](xref:Xamarin.Forms.ViewCell) 項目時，每個平台專案都必須要有自訂轉譯器。

下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![](viewcell-images/solution-structure.png "NativeCell 自訂轉譯器專案責任")

`NativeCell` 自訂資料格是由平台特定轉譯器類別轉譯，其全部衍生自各平台的 `ViewCellRenderer` 類別。 這會導致每個 `NativeCell` 自訂資料格都使用平台特定配置轉譯，如下列螢幕擷取畫面所示：

![](viewcell-images/screenshots.png "每個平台上的 NativeCell")

`ViewCellRenderer` 類別會公開平台轉譯自訂資料格的特定方法。 這在 iOS 平台為 `GetCell` 方法、在 Android 平台為 `GetCellCore` 方法，在 UWP 為 `GetTemplate` 方法。

每個自訂轉譯器類別都裝飾了向 Xamarin.Forms 註冊轉譯器的 `ExportRenderer` 屬性。 此屬性採用兩個參數 – 正在轉譯的 Xamarin.Forms 資料格類型名稱，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

下列各節會討論每個平台特定自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例示範適用於 iOS 平台的自訂轉譯器：

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

呼叫 `GetCell` 方法來建置要顯示的每個資料格。 每個資料格都是 `NativeiOSCell` 執行個體，其定義資料格的配置及其資料。 `GetCell` 方法的操作取決於 [`ListView`](xref:Xamarin.Forms.ListView) 快取策略：

- 當 [`ListView`](xref:Xamarin.Forms.ListView) 快取策略為 [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) 時，每個資料格都需要叫用 `GetCell` 方法。 畫面最初顯示的每個 `NativeCell` 執行個體都需要建立 `NativeiOSCell` 執行個體。 當使用者捲動 `ListView` 時，會重複使用 `NativeiOSCell` 執行個體。 如需 iOS 資料格重複使用的詳細資訊，請參閱[資料格重複使用](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。

  > [!NOTE]
  > 即使當 [`ListView`](xref:Xamarin.Forms.ListView) 設為保留資料格時，此自訂轉譯器程式碼仍會執行一些資料格重複使用。

  `UpdateCell` 方法會使用每個 `NativeCell` 執行個體的資料來更新按每個 `NativeiOSCell` 執行個體顯示的資料，無論其為新建或為重複使用。

  > [!NOTE]
  > 當 [`ListView`](xref:Xamarin.Forms.ListView) 快取策略設為保留資料格時，絕不叫用 `OnNativeCellPropertyChanged` 方法。

- 當 [`ListView`](xref:Xamarin.Forms.ListView) 快取策略為 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 時，畫面最初顯示的每個資料格都需要叫用 `GetCell` 方法。 畫面最初顯示的每個 `NativeCell` 執行個體都需要建立 `NativeiOSCell` 執行個體。 `UpdateCell` 方法會使用 `NativeCell` 執行個體的資料來更新按每個 `NativeiOSCell` 執行個體顯示的資料。 不過，當使用者捲動 `ListView` 時，不會叫用 `GetCell` 方法。 而是重複使用 `NativeiOSCell` 執行個體。 當資料變更時，`NativeCell` 執行個體上會引發 `PropertyChanged` 事件，而 `OnNativeCellPropertyChanged` 事件處理常式會更新每個重複使用的 `NativeiOSCell` 執行個體資料。

下列程式碼範例示範 `PropertyChanged` 事件引發時叫用的 `OnNativeCellPropertyChanged` 方法：

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

這個方法會更新按重複使用之 `NativeiOSCell` 執行個體顯示的資料。 因為此方法會呼叫多次，所以會檢查變更的屬性。

`NativeiOSCell` 類別定義每個資料格的配置，如下列程式碼範例所示：

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

這個類別會定義用來轉譯資料格內容的控制項及其配置。 此類別會實作 [`INativeElementView`](xref:Xamarin.Forms.INativeElementView) 介面，當 [`ListView`](xref:Xamarin.Forms.ListView) 使用 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 快取策略時為必要。 此介面會指定類別必須實作 [`Element`](xref:Xamarin.Forms.INativeElementView.Element) 屬性，這應會傳回回收資料格的自訂資料格資料。

`NativeiOSCell` 建構函式會初始化 `HeadingLabel`、`SubheadingLabel` 和 `CellImageView` 屬性的外觀。 這些屬性用以顯示儲存在 `NativeCell` 執行個體的資料，使用正在呼叫的 `UpdateCell` 方法來設定每個屬性的值。 此外，當 [`ListView`](xref:Xamarin.Forms.ListView) 使用 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 快取策略時，您可在自訂轉譯器中使用 `OnNativeCellPropertyChanged` 方法來更新按 `HeadingLabel`、`SubheadingLabel` 和 `CellImageView` 屬性顯示的資料。

資料格配置是由 `LayoutSubviews` 覆寫所執行，這會在資料格中設定 `HeadingLabel`、`SubheadingLabel` 和 `CellImageView` 的座標。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

下列程式碼範例示範適用於 Android 平台的自訂轉譯器：

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

呼叫 `GetCellCore` 方法來建置要顯示的每個資料格。 每個資料格都是 `NativeAndroidCell` 執行個體，其定義資料格的配置及其資料。 `GetCellCore` 方法的操作取決於 [`ListView`](xref:Xamarin.Forms.ListView) 快取策略：

- 當 [`ListView`](xref:Xamarin.Forms.ListView) 快取策略為 [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) 時，每個資料格都需要叫用 `GetCellCore` 方法。 畫面最初顯示的每個 `NativeCell` 執行個體都需要建立 `NativeAndroidCell`。 當使用者捲動 `ListView` 時，會重複使用 `NativeAndroidCell` 執行個體。 如需 Android 資料格重複使用的詳細資訊，請參閱[資料列檢視重複使用](~/android/user-interface/layouts/list-view/populating.md)。

  > [!NOTE]
  > 請注意，當 [`ListView`](xref:Xamarin.Forms.ListView) 設為保留資料格時，此自訂轉譯器程式碼會執行一些資料格重複使用。

  `UpdateCell` 方法會使用每個 `NativeCell` 執行個體的資料來更新按每個 `NativeAndroidCell` 執行個體顯示的資料，無論其為新或為重複使用。

  > [!NOTE]
  > 請注意，雖然 [`ListView`](xref:Xamarin.Forms.ListView) 設為保留資料格時會叫用 `OnNativeCellPropertyChanged` 方法，但它不會更新 `NativeAndroidCell` 屬性值。

- 當 [`ListView`](xref:Xamarin.Forms.ListView) 快取策略為 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 時，畫面最初顯示的每個資料格都需要叫用 `GetCellCore` 方法。 畫面最初顯示的每個 `NativeCell` 執行個體都需要建立 `NativeAndroidCell` 執行個體。 `UpdateCell` 方法會使用 `NativeCell` 執行個體的資料來更新按每個 `NativeAndroidCell` 執行個體顯示的資料。 不過，當使用者捲動 `ListView` 時，不會叫用 `GetCellCore` 方法。 而是重複使用 `NativeAndroidCell` 執行個體。  當資料變更時，`NativeCell` 執行個體上會引發 `PropertyChanged` 事件，而 `OnNativeCellPropertyChanged` 事件處理常式會更新每個重複使用的 `NativeAndroidCell` 執行個體資料。

下列程式碼範例示範 `PropertyChanged` 事件引發時叫用的 `OnNativeCellPropertyChanged` 方法：

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

這個方法會更新按重複使用之 `NativeAndroidCell` 執行個體顯示的資料。 因為此方法會呼叫多次，所以會檢查變更的屬性。

`NativeAndroidCell` 類別定義每個資料格的配置，如下列程式碼範例所示：

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

這個類別會定義用來轉譯資料格內容的控制項及其配置。 此類別會實作 [`INativeElementView`](xref:Xamarin.Forms.INativeElementView) 介面，當 [`ListView`](xref:Xamarin.Forms.ListView) 使用 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 快取策略時為必要。 此介面會指定類別必須實作 [`Element`](xref:Xamarin.Forms.INativeElementView.Element) 屬性，這應會傳回回收資料格的自訂資料格資料。

`NativeAndroidCell` 建構函式會擴大 `NativeAndroidCell` 配置，並初始化擴大配置中的控制項 `HeadingTextView`、`SubheadingTextView` 和 `ImageView` 屬性。 這些屬性用以顯示儲存在 `NativeCell` 執行個體的資料，使用正在呼叫的 `UpdateCell` 方法來設定每個屬性的值。 此外，當 [`ListView`](xref:Xamarin.Forms.ListView) 使用 [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) 快取策略時，您可在自訂轉譯器中使用 `OnNativeCellPropertyChanged` 方法來更新按 `HeadingTextView`、`SubheadingTextView` 和 `ImageView` 屬性顯示的資料。

下列程式碼範例示範 `NativeAndroidCell.axml` 配置檔案的配置定義：

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

此配置指定顯示資料格內容所用的兩個 `TextView` 控制項和一個 `ImageView` 控制項。 兩個 `TextView` 控制項在 `LinearLayout` 控制項內是垂直方向，而且所有控制項都包含在 `RelativeLayout` 內。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上建立自訂轉譯器

下列程式碼範例示範適用於 UWP 的自訂轉譯器：

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

呼叫 `GetTemplate` 方法以傳回清單中每個資料列要轉譯的資料格。 它會為將顯示在畫面中的每個 `NativeCell` 執行個體建立 `DataTemplate`，以 `DataTemplate` 定義資料格的外觀和內容。

`DataTemplate` 儲存在應用程式層級的資源字典中，如下列程式碼範例所示：

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

`DataTemplate` 指定顯示資料格內容及其配置和外觀所用的控制項。 透過資料繫結使用兩個 `TextBlock` 控制項和一個 `Image` 控制項來顯示資料格的內容。 此外，使用 `ConcatImageExtensionConverter` 的執行個體將 `.jpg` 副檔名串連到每個影像檔案名稱。 這可確保 `Image` 控制項在設定了 `Source` 屬性後，可以載入及轉譯影像。

## <a name="summary"></a>總結

本文已示範如何建立裝載在 Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 控制項內的自訂 [`ViewCell`](xref:Xamarin.Forms.ViewCell) 轉譯器。 這會停止在 `ListView` 捲動期間重複呼叫 Xamarin.Forms 配置計算。


## <a name="related-links"></a>相關連結

- [ListView 效能](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
