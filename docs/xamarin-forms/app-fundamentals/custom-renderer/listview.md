---
title： "自訂 ListView" 描述： " Xamarin.Forms ListView 是將資料集合顯示為垂直清單的視圖。 本文示範如何建立自訂轉譯器，以封裝平臺特定清單控制項和原生資料格配置，讓您對原生清單控制效能有更大的控制權。」
assetid： 2FBCB8C8-4F32-45E7-954F-63AD29D5F1B5 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：11/29/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="customizing-a-listview"></a>自訂 ListView

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-listview)

_Xamarin.FormsListView 是將資料集合顯示為垂直清單的視圖。本文示範如何建立自訂轉譯器，以封裝平臺特定清單控制項和原生資料格配置，讓您對原生清單控制效能有更大的控制權。_

每 Xamarin.Forms 個視圖都會針對每個建立原生控制項實例的平臺，隨附一個轉譯器。 當 [`ListView`](xref:Xamarin.Forms.ListView) Xamarin.Forms 應用程式轉譯時，在 iOS 中， `ListViewRenderer` 類別會具現化，然後再具現化原生 `UITableView` 控制項。 在 Android 平台上，`ListViewRenderer` 類別會具現化原生 `ListView` 控制項。 在通用 Windows 平台 (UWP) 上，`ListViewRenderer` 類別會具現化原生 `ListView` 控制項。 如需控制項對應之轉譯器和原生控制項類別的詳細資訊 Xamarin.Forms ，請參閱轉譯器[基類和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

下圖說明 [`ListView`](xref:Xamarin.Forms.ListView) 控制項和執行它的對應原生控制項之間的關聯性：

![](listview-images/listview-classes.png "Relationship Between the ListView Control and the Implementing Native Controls")

您可以 [`ListView`](xref:Xamarin.Forms.ListView) 在每個平臺上建立的自訂轉譯器，利用呈現程式來執行平臺特定的自訂。 執行這項作業的流程如下：

1. [建立](#creating-the-custom-listview-control) Xamarin.Forms 自訂控制項。
1. [使用](#consuming-the-custom-control)來自的自訂控制項 Xamarin.Forms 。
1. 在每個平台上[建立](#creating-the-custom-renderer-on-each-platform)控制項的自訂轉譯器。

現在可以依次討論每個項目，以實作利用平台特定清單控制項和原生資料格配置的 `NativeListView` 轉譯器。 在移植包含清單和可重複使用之資料格程式碼的現有原生應用程式時，此案例可提供協助。 此外，也允許您精細自訂可能會影響效能的清單控制項功能，例如資料虛擬化。

## <a name="creating-the-custom-listview-control"></a>建立自訂 ListView 控制項

您可以子類別化類別來建立自訂 [`ListView`](xref:Xamarin.Forms.ListView) 控制項 `ListView` ，如下列程式碼範例所示：

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

`NativeListView` 會在 .NET Standard 程式庫專案中建立，並定義自訂控制項的 API。 此控制項會公開 `Items` 屬性用於將資料填入 `ListView`，且可以是用於顯示用途的資料繫結。 其也會公開 `ItemSelected` 事件；在平台特定的原生清單控制項中選取項目時，就會觸發該事件。 如需有關資料繫結的詳細資訊，請參閱[資料繫結基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)。

## <a name="consuming-the-custom-control"></a>使用自訂控制項

您可以宣告控制項的位置命名空間並使用控制項上的命名空間前置詞，在 .NET Standard 程式庫專案中的 XAML 參考 `NativeListView` 自訂控制項。 下列程式碼範例示範 XAML 頁面如何使用 `NativeListView` 自訂控制項：

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

`local` 命名空間前置詞沒有命名限制。 不過，`clr-namespace` 和 `assembly` 值必須符合自訂控制項的詳細資料。 一旦宣告命名空間，即會使用前置詞來參考自訂控制項。

下列程式碼範例示範 C# 頁面如何使用 `NativeListView` 自訂控制項：

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

`NativeListView` 自訂控制項使用平台特定的自訂轉譯器來顯示資料清單，該資料清單會透過 `Items` 屬性填入。 清單中的每個資料列都包含三個資料項目 – 名稱、類別和影像檔案名稱。 清單中每個資料列的配置，由平台特定的自訂轉譯器定義。

> [!NOTE]
> 由於 `NativeListView` 自訂控制項將會使用包含滾動功能的平臺特定清單控制項來呈現，因此自訂控制項不應裝載在可滾動的版面配置控制項中，例如 [`ScrollView`](xref:Xamarin.Forms.ScrollView) 。

自訂轉譯器現在可以新增至每個應用程式專案，來建立平台特定的清單控制項和原生資料格配置。

## <a name="creating-the-custom-renderer-on-each-platform"></a>在每個平台上建立自訂轉譯器

建立自訂轉譯器類別的程序如下：

1. 建立轉譯自訂控制項之 `ListViewRenderer` 類別的子類別。
1. 覆寫轉譯自訂控制項的 `OnElementChanged` 方法，並撰寫自訂方法的邏輯。 當對應的建立時，會呼叫這個方法 Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) 。
1. 將 `ExportRenderer` 屬性新增至自訂轉譯器類別，以指定它將用來呈現 Xamarin.Forms 自訂控制項。 這個屬性是用來向註冊自訂轉譯器 Xamarin.Forms 。

> [!NOTE]
> 您可以選擇在每個平台專案中提供自訂轉譯器。 如果自訂轉譯器尚未註冊，則會使用資料格基底類別的預設轉譯器。

下圖說明範例應用程式中每個專案的責任，以及它們之間的關聯性：

![](listview-images/solution-structure.png "NativeListView Custom Renderer Project Responsibilities")

`NativeListView` 自訂控制項是由平台特定轉譯器類別轉譯，其全部衍生自各平台的 `ListViewRenderer` 類別。 這會導致每個 `NativeListView` 自訂控制項都使用平台特定的清單控制項轉譯，如下列螢幕擷取畫面所示：

![](listview-images/screenshots.png "NativeListView on each Platform")

`ListViewRenderer`類別會公開 `OnElementChanged` 方法，這會在 Xamarin.Forms 建立自訂控制項以轉譯對應的原生控制項時呼叫。 此方法會接受 `ElementChangedEventArgs` 參數，其中包含 `OldElement` 和 `NewElement` 屬性。 這些屬性代表轉譯器 Xamarin.Forms 附加到的*was*專案，以及轉譯器 Xamarin.Forms 附加至的元素。 *is* 在範例應用程式中，`OldElement` 屬性會是 `null`，而 `NewElement` 屬性會包含 `NativeListView` 執行個體的參考。

在每個平台特定轉譯器類別中，`OnElementChanged` 方法的覆寫版本是執行原生控制項自訂的位置。 平台上所使用的原生控制項具型別參考可透過 `Control` 屬性存取。 此外，您 Xamarin.Forms 可以透過屬性取得所要轉譯之控制項的參考 `Element` 。

在 `OnElementChanged` 方法中訂閱事件處理常式時必須留意，如下列程式碼範例中所示：

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

只有在自訂轉譯器附加至新的專案時，才會設定原生控制項和訂閱的事件處理常式 Xamarin.Forms 。 同樣地，您應該只在轉譯器附加到的項目變更時，才取消訂閱任何已訂閱的事件處理常式。 採用這個方法將有助於建立不會發生記憶體流失的自訂轉譯器。

`OnElementPropertyChanged`在每個平臺特定轉譯器類別中，方法的覆寫版本是回應自訂控制項上可系結屬性變更的位置 Xamarin.Forms 。 因為此覆寫會呼叫多次，所以請一律檢查變更的屬性。

每個自訂轉譯器類別都會以向註冊轉譯器的 `ExportRenderer` 屬性裝飾 Xamarin.Forms 。 屬性會採用兩個參數– Xamarin.Forms 所呈現之自訂控制項的類型名稱，以及自訂轉譯器的類型名稱。 屬性的 `assembly` 前置詞會指定套用至整個組件的屬性。

下列各節會討論每個平台特定自訂轉譯器類別的實作。

### <a name="creating-the-custom-renderer-on-ios"></a>在 iOS 上建立自訂轉譯器

下列程式碼範例示範適用於 iOS 平台的自訂轉譯器：

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

藉 `UITableView` 由建立類別的實例來設定控制項 `NativeiOSListViewSource` ，前提是自訂轉譯器已附加至新的專案 Xamarin.Forms 。 此類別藉由從 `UITableViewSource` 類別覆寫 `RowsInSection` 和 `GetCell` 方法，以及公開包含要顯示之資料清單的 `Items` 屬性，來提供資料給 `UITableView` 控制項。 此類別也會透過叫用由 `NativeListView` 自訂控制項提供的 `ItemSelected` 事件，來提供 `RowSelected` 方法覆寫。 如需方法覆寫的詳細資訊，請參閱[子類別化 UITableViewSource](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。 `GetCell` 方法會傳回以清單中每個資料列之資料填入的 `UITableCellView`，如下列程式碼範例所示：

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

此方法會為將顯示於螢幕上的每個資料列建立 `NativeiOSListViewCell` 執行個體。 `NativeiOSCell` 執行個體會定義每個資料格的配置，以及資料格的資料。 當資料格因捲動而從畫面消失時，資料格將可重複使用。 這可藉由確保僅有資料的 `NativeiOSCell` 執行個體顯示在畫面上 (而非清單中的所有資料) 來避免浪費記憶體。 如需資料格重複使用的詳細資訊，請參閱[資料格重複使用](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)。 `GetCell` 方法也會讀取每個資料列的 `ImageFilename` 屬性 (如存在)，並讀取映像且將儲存為 `UIImage` 執行個體，然後使用資料列的資料 (名稱、類別和映像) 來更新 `NativeiOSListViewCell` 執行個體。

`NativeiOSListViewCell` 類別定義每個資料格的配置，如下列程式碼範例所示：

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

這個類別會定義用來轉譯資料格內容的控制項及其配置。 `NativeiOSListViewCell` 建構函式會建立 `UILabel` 的執行個體和 `UIImageView` 控制項，並初始化其外觀。 這些控制項會用於顯示每個資料列的資料，`UpdateCell` 方法會用於在 `UILabel` 和 `UIImageView` 執行個體上設定這項資料。 這些執行個體的位置，會由覆寫的 `LayoutSubviews` 方法透過指定其資料格內的座標來設定。

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>回應自訂控制項上的屬性變更

如果 `NativeListView.Items` 屬性變更，由於正在新增項目至清單或從清單中移除項目，因此自訂轉譯器必須透過顯示變更來回應。 這可藉由覆寫 `OnElementPropertyChanged` 方法來完成，如下列程式碼範例所示：

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

此方法會建立 `NativeiOSListViewSource` 類別的新執行個體，該類別會提供資料給 `UITableView` 控制項，前提是可繫結的 `NativeListView.Items` 屬性已變更。

### <a name="creating-the-custom-renderer-on-android"></a>在 Android 上建立自訂轉譯器

下列程式碼範例示範適用於 Android 平台的自訂轉譯器：

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

`ListView`如果自訂轉譯器已附加至新的專案，則會設定原生控制項 Xamarin.Forms 。 此組態包含建立提供資料給原生 `ListView` 控制項的 `NativeAndroidListViewAdapter` 類別執行個體，並註冊事件處理常式來處理 `ItemClick` 事件。 接著，此處理常式將會叫用由 `NativeListView` 自訂控制項所提供的 `ItemSelected` 事件。 如果轉譯器附加的專案變更，則 `ItemClick` 會取消訂閱事件 Xamarin.Forms 。

`NativeAndroidListViewAdapter` 衍生自 `BaseAdapter` 類別並會公開 `Items` 屬性，該屬性包含要顯示的資料清單，且會覆寫 `Count`、`GetView`、`GetItemId` 和 `this[int]` 方法。 如需這些方法覆寫的詳細資訊，請參閱[實作 ListAdapter](~/android/user-interface/layouts/list-view/populating.md)。 `GetView` 方法會傳回每資料列的檢視，其由資料填入，如下列程式碼範例所示：

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

會呼叫 `GetView` 方法以傳回清單中每個資料列要轉譯的資料格作為 `View`。 會為將顯示於畫面上的每個資料列建立 `View` 執行個體，並在配置檔案中定義 `View` 執行個體的外觀。 當資料格因捲動而從畫面消失時，資料格將可重複使用。 這可藉由確保僅有資料的 `View` 執行個體顯示在畫面上 (而非清單中的所有資料) 來避免浪費記憶體。 如需檢視重複使用的詳細資訊，請參閱[資料列檢視重複使用](~/android/user-interface/layouts/list-view/populating.md)。

`GetView` 方法也會將資料填入 `View` 執行個體，資料包括從 `ImageFilename` 屬性中指定的檔案名稱來讀取映像資料。

由原生 `ListView` 顯示之每個資料格的配置，都由 `NativeAndroidListViewCell.axml` 配置檔案定義，該檔案會由 `LayoutInflater.Inflate` 方法擴大。 下列程式碼範例示範配置定義：

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

此配置指定顯示資料格內容所用的兩個 `TextView` 控制項和一個 `ImageView` 控制項。 兩個 `TextView` 控制項在 `LinearLayout` 控制項內是垂直方向，而且所有控制項都包含在 `RelativeLayout` 內。

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>回應自訂控制項上的屬性變更

如果 `NativeListView.Items` 屬性變更，由於正在新增項目至清單或從清單中移除項目，因此自訂轉譯器必須透過顯示變更來回應。 這可藉由覆寫 `OnElementPropertyChanged` 方法來完成，如下列程式碼範例所示：

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

此方法會建立 `NativeAndroidListViewAdapter` 類別的新執行個體，該類別會提供資料給原生 `ListView` 控制項，前提是可繫結的 `NativeListView.Items` 屬性已變更。

### <a name="creating-the-custom-renderer-on-uwp"></a>在 UWP 上建立自訂轉譯器

下列程式碼範例示範適用於 UWP 的自訂轉譯器：

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

`ListView`如果自訂轉譯器已附加至新的專案，則會設定原生控制項 Xamarin.Forms 。 此組態包含設定原生 `ListView` 控制項將如何回應所選取的項目、填入由控制項顯示的資料、定義每個資料格的外觀，並註冊事件處理常式來處理 `SelectionChanged` 事件。 接著，此處理常式將會叫用由 `NativeListView` 自訂控制項所提供的 `ItemSelected` 事件。 如果轉譯器附加的專案變更，則 `SelectionChanged` 會取消訂閱事件 Xamarin.Forms 。

每個原生 `ListView` 資料格的外觀和內容，由名為 `ListViewItemTemplate` 的 `DataTemplate` 所定義。 `DataTemplate` 儲存在應用程式層級的資源字典中，如下列程式碼範例所示：

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

`DataTemplate` 指定顯示資料格內容及其配置和外觀所用的控制項。 透過資料繫結使用兩個 `TextBlock` 控制項和一個 `Image` 控制項來顯示資料格的內容。 此外，使用 `ConcatImageExtensionConverter` 的執行個體將 `.jpg` 副檔名串連到每個影像檔案名稱。 這可確保 `Image` 控制項在設定了 `Source` 屬性後，可以載入及轉譯影像。

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>回應自訂控制項上的屬性變更

如果 `NativeListView.Items` 屬性變更，由於正在新增項目至清單或從清單中移除項目，因此自訂轉譯器必須透過顯示變更來回應。 這可藉由覆寫 `OnElementPropertyChanged` 方法來完成，如下列程式碼範例所示：

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

方法會使用變更的資料重新填入原生 `ListView` 控制項，前提是可繫結的 `NativeListView.Items` 屬性已變更。

## <a name="summary"></a>總結

本文示範了如何建立自訂轉譯器，其會封裝平台特定清單控制項和原生資料格配置，讓您對原生清單控制效能擁有更多掌控權。

## <a name="related-links"></a>相關連結

- [CustomRendererListView (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-listview)
