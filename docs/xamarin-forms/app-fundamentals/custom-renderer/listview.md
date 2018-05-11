---
title: 自訂清單檢視
description: Xamarin.Forms ListView 是以垂直清單顯示的資料集合的檢視。 本文示範如何建立自訂轉譯器會封裝特定平台清單控制項和原生的儲存格的版面配置，允許更充分掌控原生清單控制效能。
ms.prod: xamarin
ms.assetid: 2FBCB8C8-4F32-45E7-954F-63AD29D5F1B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 0d1afc2c14b19bbd03244affed494405776a3c99
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="customizing-a-listview"></a>自訂清單檢視

_Xamarin.Forms ListView 是以垂直清單顯示的資料集合的檢視。本文示範如何建立自訂轉譯器會封裝特定平台清單控制項和原生的儲存格的版面配置，允許更充分掌控原生清單控制效能。_

Xamarin.Forms 中的每個檢視有隨附的轉譯器，每個平台建立原生控制項的執行個體。 當[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) Xamarin.Forms 應用程式，在 iOS 中呈現`ListViewRenderer`類別具現化，這又會具現化原生`UITableView`控制項。 Android 平台上，`ListViewRenderer`類別具現化的原生`ListView`控制項。 在通用 Windows 平台 (UWP)，`ListViewRenderer`類別具現化的原生`ListView`控制項。 如需有關轉譯器，而且 Xamarin.Forms 控制項對應至原生控制項類別的詳細資訊，請參閱[轉譯器的基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明之間的關聯性[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)控制項和對應的原生控制項實作它：

![](listview-images/listview-classes.png "ListView 控制項和實作的原生控制項之間的關聯性")

轉譯程序可以採取利用平台專屬的自訂實作所建立的自訂轉譯器[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)每個平台上。 執行此程序如下所示：

1. [建立](#Creating_the_Custom_ListView_Control)Xamarin.Forms 自訂控制項。
1. [取用](#Consuming_the_Custom_Control)Xamarin.Forms 中的，將自訂控制項。
1. [建立](#Creating_the_Custom_Renderer_on_each_Platform)自訂轉譯器，針對每個平台上的控制項。

每個項目將現在依次討論實作`NativeListView`轉譯器，利用特定平台清單控制項和原生的儲存格的版面配置。 移植現有的原生應用程式包含清單和可重複使用的儲存格程式碼時，這個狀況非常有用。 此外，它可讓詳細的自訂可能會影響效能，例如資料虛擬化的清單控制項功能。

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>建立自訂的 ListView 控制項

自訂[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)控制項可以由子類別化`ListView`類別，如下列程式碼範例所示：

```csharp
public class NativeListView : ListView
{
  public static readonly BindableProperty ItemsProperty =
    BindableProperty.Create ("Items", typeof(IEnumerable<DataSource>), typeof(NativeListView), new List<DataSource> ());

  public IEnumerable<DataSource> Items {
    get { return (IEnumerable<DataSource>)GetValue (ItemsProperty); }
    set { SetValue (ItemsProperty, value); }
  }

  public event EventHandler<SelectedItemChangedEventArgs> ItemSelected;

  public void NotifyItemSelected (object item)
  {
    if (ItemSelected != null) {
      ItemSelected (this, new SelectedItemChangedEventArgs (item));
    }
  }
}
```

`NativeListView` .NET 標準程式庫專案中建立並定義自訂控制項的 API。 此控制項會公開`Items`屬性用於填入`ListView`與資料，它可以是資料繫結到針對顯示用途。 它也會公開`ItemSelected`會在特定平台的原生清單控制項中選取項目時引發的事件。 如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>使用自訂控制項

`NativeListView`自訂控制項可以在 Xaml 中參考.NET 標準程式庫專案中，宣告的命名空間，做為其位置，並在控制項上使用命名空間前置詞。 下列程式碼範例示範如何`NativeListView`自訂控制項以供 XAML 頁面：

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
        <Grid>
          <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*" />
          </Grid.RowDefinitions>
          <Label Text="{x:Static local:App.Description}" HorizontalTextAlignment="Center" />
          <local:NativeListView Grid.Row="1" x:Name="nativeListView" ItemSelected="OnItemSelected" VerticalOptions="FillAndExpand" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

`local`命名空間前置詞可以命名為任何項目。 不過，`clr-namespace`和`assembly`值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，前置詞用來參考自訂控制項。

下列程式碼範例示範如何`NativeListView`自訂控制項可供 C# 頁面：

```csharp
public class MainPageCS : ContentPage
{
    NativeListView nativeListView;

    public MainPageCS()
    {
        nativeListView = new NativeListView
        {
            Items = DataSource.GetList(),
            VerticalOptions = LayoutOptions.FillAndExpand
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

        Content = new Grid
        {
            RowDefinitions = {
                new RowDefinition { Height = GridLength.Auto },
                new RowDefinition { Height = new GridLength (1, GridUnitType.Star) }
            },
            Children = {
                new Label { Text = App.Description, HorizontalTextAlignment = TextAlignment.Center },
                nativeListView
            }
        };
        nativeListView.ItemSelected += OnItemSelected;
    }
    ...
}
```

`NativeListView`自訂控制項使用平台專屬的自訂轉譯器顯示一份資料，填入透過`Items`屬性。 在清單中的每個資料列包含三個項目資料 – 名稱、 類別和影像檔案名稱。 平台專屬的自訂轉譯器所定義的清單中的每個資料列配置。

> [!NOTE]
> 因為`NativeListView`會使用特定平台清單控制項包含捲動功能來呈現自訂控制項、 自訂控制項應該不會裝載在可捲動的版面配置控制項例如[ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/)。

自訂轉譯器現在可以新增至每個應用程式專案，來建立特定平台清單控制項和原生的儲存格的版面配置。

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>每個平台上建立的自訂轉譯器

建立自訂轉譯器類別的程序如下所示：

1. 建立的子類別`ListViewRenderer`呈現自訂控制項的類別。
1. 覆寫`OnElementChanged`呈現自訂它的自訂控制項和寫入邏輯方法。 這個方法時，會呼叫對應的 Xamarin.Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)建立。
1. 新增`ExportRenderer`屬性來指定它將會用來呈現 Xamarin.Forms 自訂控制項的自訂轉譯器類別。 此屬性用來向 Xamarin.Forms 中的自訂轉譯器。

> [!NOTE]
> 它是選擇性的以提供每個平台專案中的自訂轉譯器。 如果未登錄的自訂轉譯器，則會使用儲存格的基底類別的預設轉譯器。

下圖說明範例應用程式，以及它們之間的關聯性中的每一個專案的責任：

![](listview-images/solution-structure.png "NativeListView 自訂轉譯器專案責任")

`NativeListView`自訂控制項的呈現由平台特定轉譯器類別，這些全都衍生自`ListViewRenderer`每個平台的類別。 這會導致每個`NativeListView`呈現具有特定平台清單控制項和原生的儲存格的版面配置，如下列螢幕擷取畫面所示的自訂控制項：

![](listview-images/screenshots.png "每個平台上 NativeListView")

`ListViewRenderer`類別會公開`OnElementChanged`Xamarin.Forms 自訂控制項是用來呈現對應的原生控制項時所呼叫的方法。 這個方法會採用`ElementChangedEventArgs`參數包含`OldElement`和`NewElement`屬性。 這些屬性代表 Xamarin.Forms 項目，轉譯器*已*附加和 Xamarin.Forms 的項目，轉譯器*是*附加至分別。 範例應用程式、`OldElement`屬性會是`null`和`NewElement`屬性會包含參考`NativeListView`執行個體。

覆寫的版本`OnElementChanged`方法，在每個平台特定轉譯器類別，是執行原生控制項自訂的位置。 平台上使用原生控制項的型別的參考可以透過存取`Control`屬性。 此外，要呈現 Xamarin.Forms 控制項的參考可以透過取得`Element`屬性。

必須小心訂閱中的事件處理常式時`OnElementChanged`方法，如下列程式碼範例所示：

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

應該只設定原生控制項，而且事件處理常式訂閱自訂轉譯器附加至新的 Xamarin.Forms 項目時。 同樣地，任何已訂閱的事件處理常式時，應該從取消訂閱只轉譯器的項目附加至變更。 採用這個方法將有助於您建立自訂轉譯器，不會發生記憶體流失。

覆寫的版本`OnElementPropertyChanged`方法，在每個平台特定轉譯器類別，是 Xamarin.Forms 自訂控制項繫結的屬性變更回應的位置。 變更的屬性應該一律會進行檢查，為此覆寫可呼叫多次。

每個自訂轉譯器類別以裝飾`ExportRenderer`xamarin.forms 註冊轉譯器的屬性。 屬性會採用兩個參數 – Xamarin.Forms 自訂控制項所呈現的型別名稱和自訂轉譯器的型別名稱。 `assembly`屬性的前置詞指定的屬性會套用至整個組件。

下列章節會討論每個平台專屬的自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例會顯示 iOS 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer (typeof(NativeListView), typeof(NativeiOSListViewRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSListViewRenderer : ListViewRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
            }

            if (e.NewElement != null) {
                Control.Source = new NativeiOSListViewSource (e.NewElement as NativeListView);
            }
        }
    }
}
```

`UITableView`控制項設定所建立的執行個體`NativeiOSListViewSource`類別，前提是自訂轉譯器附加至新的 Xamarin.Forms 項目。 這個類別提供資料給`UITableView`藉由覆寫控制項`RowsInSection`和`GetCell`方法`UITableViewSource`類別，並藉由公開`Items`屬性，其中包含要顯示資料的清單。 這個類別也會提供`RowSelected`叫用的方法覆寫`ItemSelected`所提供的事件`NativeListView`自訂控制項。 覆寫此方法的詳細資訊，請參閱[子類別化 UITableViewSource](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。 `GetCell`方法會傳回`UITableCellView`中填入每個資料列在清單中，而且下列程式碼範例所示：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
  // request a recycled cell to save memory
  NativeiOSListViewCell cell = tableView.DequeueReusableCell (cellIdentifier) as NativeiOSListViewCell;

  // if there are no cells to reuse, create a new one
  if (cell == null) {
    cell = new NativeiOSListViewCell (cellIdentifier);
  }

  if (String.IsNullOrWhiteSpace (tableItems [indexPath.Row].ImageFilename)) {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , null);
  } else {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , UIImage.FromFile ("Images/" + tableItems [indexPath.Row].ImageFilename + ".jpg"));
  }

  return cell;
}
```

這個方法會建立`NativeiOSListViewCell`每個資料列的資料將會顯示在螢幕的執行個體。 `NativeiOSCell`執行個體定義的每個資料格和儲存格的資料配置。 當資料格從由於捲動畫面消失時，資料格可供重複使用。 這可避免透過確保只有的浪費記憶體`NativeiOSCell`資料會顯示在畫面上，而不是所有的清單中的資料上的執行個體。 如需儲存格重複使用的詳細資訊，請參閱[重複使用的儲存格](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。 `GetCell`方法也會讀取`ImageFilename`屬性的每個資料列的資料，提供它存在，並讀取映像並將其做為`UIImage`執行個體，然後再更新`NativeiOSListViewCell`執行個體 （名稱、 類別和影像） 的資料資料列。

`NativeiOSListViewCell`類別定義，每個儲存格的版面配置和下列程式碼範例所示：

```csharp
public class NativeiOSListViewCell : UITableViewCell
{
  UILabel headingLabel, subheadingLabel;
  UIImageView imageView;

  public NativeiOSListViewCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
  {
    SelectionStyle = UITableViewCellSelectionStyle.Gray;

    ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);

    imageView = new UIImageView ();

    headingLabel = new UILabel () {
      Font = UIFont.FromName ("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB (127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    subheadingLabel = new UILabel () {
      Font = UIFont.FromName ("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB (38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add (headingLabel);
    ContentView.Add (subheadingLabel);
    ContentView.Add (imageView);
  }

  public void UpdateCell (string caption, string subtitle, UIImage image)
  {
    headingLabel.Text = caption;
    subheadingLabel.Text = subtitle;
    imageView.Image = image;
  }

  public override void LayoutSubviews ()
  {
    base.LayoutSubviews ();

    headingLabel.Frame = new CoreGraphics.CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
    subheadingLabel.Frame = new CoreGraphics.CGRect (100, 18, 100, 20);
    imageView.Frame = new CoreGraphics.CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

這個類別會定義用來呈現資料格的內容和其配置的控制項。 `NativeiOSListViewCell`建構函式建立的執行個體`UILabel`和`UIImageView`控制項，並初始化其外觀。 這些控制項用來顯示每個資料列的資料具有`UpdateCell`方法用來設定這項資料`UILabel`和`UIImageView`執行個體。 這些執行個體的位置會設定所覆寫`LayoutSubviews`方法，藉由指定其座標與資料格內。

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>自訂控制項上的屬性變更回應

如果`NativeListView.Items`、 屬性變更時，因為所加入的項目，或從清單中移除的自訂轉譯器需要回應所顯示的變更。 這可藉由覆寫`OnElementPropertyChanged`方法，下列程式碼範例所示：

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

方法會建立的新執行個體`NativeiOSListViewSource`類別提供資料給`UITableView`控制項，但前提是可繫結`NativeListView.Items`屬性已變更。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立的自訂轉譯器

下列程式碼範例會顯示 Android 平台的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeAndroidListViewRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidListViewRenderer : ListViewRenderer
    {
        Context _context;

        public NativeAndroidListViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // unsubscribe
                Control.ItemClick -= OnItemClick;
            }

            if (e.NewElement != null)
            {
                // subscribe
                Control.Adapter = new NativeAndroidListViewAdapter(_context as Android.App.Activity, e.NewElement as NativeListView);
                Control.ItemClick += OnItemClick;
            }
        }
        ...

        void OnItemClick(object sender, Android.Widget.AdapterView.ItemClickEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(((NativeListView)Element).Items.ToList()[e.Position - 1]);
        }
    }
}
```

原生`ListView`控制項設定的自訂轉譯器附加至新的 Xamarin.Forms 項目。 這種組態包含建立的執行個體`NativeAndroidListViewAdapter`類別提供資料給原生`ListView`控制項，然後註冊事件處理常式來處理`ItemClick`事件。 接著，將會叫用這個處理常式`ItemSelected`所提供的事件`NativeListView`自訂控制項。 `ItemClick` Xamarin.Forms 項目，轉譯器附加到變更，取消事件訂閱。

`NativeAndroidListViewAdapter`衍生自`BaseAdapter`類別並公開`Items`屬性，其中包含要顯示資料的清單，以及覆寫`Count`， `GetView`， `GetItemId`，和`this[int]`方法。 如需有關這些方法的覆寫的詳細資訊，請參閱[實作 ListAdapter](~/android/user-interface/layouts/list-view/populating.md)。 `GetView`方法傳回的每個資料列，填入資料，檢視和下列程式碼範例所示：

```csharp
public override View GetView (int position, View convertView, ViewGroup parent)
{
  var item = tableItems [position];

  var view = convertView;
  if (view == null) {
    // no view to re-use, create new
    view = context.LayoutInflater.Inflate (Resource.Layout.NativeAndroidListViewCell, null);
  }
  view.FindViewById<TextView> (Resource.Id.Text1).Text = item.Name;
  view.FindViewById<TextView> (Resource.Id.Text2).Text = item.Category;

  // grab the old image and dispose of it
  if (view.FindViewById<ImageView> (Resource.Id.Image).Drawable != null) {
    using (var image = view.FindViewById<ImageView> (Resource.Id.Image).Drawable as BitmapDrawable) {
      if (image != null) {
        if (image.Bitmap != null) {
          //image.Bitmap.Recycle ();
          image.Bitmap.Dispose ();
        }
      }
    }
  }

  // If a new image is required, display it
  if (!String.IsNullOrWhiteSpace (item.ImageFilename)) {
    context.Resources.GetBitmapAsync (item.ImageFilename).ContinueWith ((t) => {
      var bitmap = t.Result;
      if (bitmap != null) {
        view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (bitmap);
        bitmap.Dispose ();
      }
    }, TaskScheduler.FromCurrentSynchronizationContext ());
  } else {
    // clear the image
    view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (null);
  }

  return view;
}
```

`GetView`方法呼叫以傳回要轉譯的資料格為`View`，每個資料列的清單中的資料。 它會建立`View`每個資料列的資料將會顯示在畫面上，外觀的執行個體`View`配置檔案中所定義的執行個體。 當資料格從由於捲動畫面消失時，資料格可供重複使用。 這可避免透過確保只有的浪費記憶體`View`資料會顯示在畫面上，而不是所有的清單中的資料上的執行個體。 如需檢視重複使用的詳細資訊，請參閱[列檢視重複使用](~/android/user-interface/layouts/list-view/populating.md)。

`GetView`方法也會填入`View`的執行個體與資料，包括讀取影像資料中指定的檔名`ImageFilename`屬性。

原生的每個資料格 dispayed 配置`ListView`中定義`NativeAndroidListViewCell.axml`配置檔案，膨脹由`LayoutInflater.Inflate`方法。 下列程式碼範例顯示版面配置定義：

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
            android:id="@+id/Text1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/Text2"
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

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>自訂控制項上的屬性變更回應

如果`NativeListView.Items`、 屬性變更時，因為所加入的項目，或從清單中移除的自訂轉譯器需要回應所顯示的變更。 這可藉由覆寫`OnElementPropertyChanged`方法，下列程式碼範例所示：

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

方法會建立的新執行個體`NativeAndroidListViewAdapter`類別提供資料給原生`ListView`控制項，但前提是可繫結`NativeListView.Items`屬性已變更。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上建立的自訂轉譯器

下列程式碼範例示範 UWP 的自訂轉譯器：

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeUWPListViewRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPListViewRenderer : ListViewRenderer
    {
        ListView listView;

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            listView = Control as ListView;

            if (e.OldElement != null)
            {
                // Unsubscribe
                listView.SelectionChanged -= OnSelectedItemChanged;
            }

            if (e.NewElement != null)
            {
                listView.SelectionMode = ListViewSelectionMode.Single;
                listView.IsItemClickEnabled = false;
                listView.ItemsSource = ((NativeListView)e.NewElement).Items;             
                listView.ItemTemplate = App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
                // Subscribe
                listView.SelectionChanged += OnSelectedItemChanged;
            }  
        }

        void OnSelectedItemChanged(object sender, SelectionChangedEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(listView.SelectedItem);
        }
    }
}
```

原生`ListView`控制項設定的自訂轉譯器附加至新的 Xamarin.Forms 項目。 這種組態包含的設定方式與原生`ListView`控制項將回應所選取的項目控制項的外觀和內容的每個資料格，定義和註冊事件處理常式來處理所擴展的資料顯示`SelectionChanged`事件。 接著，將會叫用這個處理常式`ItemSelected`所提供的事件`NativeListView`自訂控制項。 `SelectionChanged` Xamarin.Forms 項目，轉譯器附加到變更，取消事件訂閱。

外觀和內容的每個原生`ListView`所定義的儲存格`DataTemplate`名為`ListViewItemTemplate`。 這`DataTemplate`會儲存在應用程式層級的資源字典，並在下列程式碼範例所示：

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="#DAFF7F">
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

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>自訂控制項上的屬性變更回應

如果`NativeListView.Items`、 屬性變更時，因為所加入的項目，或從清單中移除的自訂轉譯器需要回應所顯示的變更。 這可藉由覆寫`OnElementPropertyChanged`方法，下列程式碼範例所示：

```csharp
protected override void OnElementPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
    base.OnElementPropertyChanged(sender, e);

    if (e.PropertyName == NativeListView.ItemsProperty.PropertyName)
    {
        listView.ItemsSource = ((NativeListView)Element).Items;
    }
}
```

這個方法重新填入原生`ListView`控制項變更的資料，但前提是可繫結`NativeListView.Items`屬性已變更。

## <a name="summary"></a>總結

本文示範如何建立自訂轉譯器會封裝特定平台清單控制項和原生的儲存格的版面配置，允許更充分掌控原生清單控制效能。


## <a name="related-links"></a>相關連結

- [CustomRendererListView （範例）](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/listview/)
