---
標題： ' Xamarin.Forms ScrollView ' 描述： ' 本文說明如何使用 Xamarin.Forms ScrollView 類別來呈現無法只放在一個畫面上，而且有內容可讓鍵盤使用的版面配置。
assetid： ms. 技術： author： ms. 作者： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinforms-scrollview"></a>Xamarin.FormsScrollView

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView)包含版面配置，並可讓它們在外滾動。 `ScrollView`也可用來讓視圖在顯示鍵盤時，自動移至螢幕的可見部分。

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>目的

[`ScrollView`](xref:Xamarin.Forms.ScrollView)可以用來確保較大的視圖在較小的手機上顯示良好。 例如，可以在 iPhone 4s 上裁剪適用于 iPhone 6s 的版面配置。 使用 `ScrollView` 可以讓版面配置的裁剪部分顯示在較小的螢幕上。

## <a name="usage"></a>使用方式

> [!NOTE]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView)不應將物件嵌套。 此外， `ScrollView` s 不應該與提供滾動的其他控制項（例如和）加以 `ListView` 嵌套 `WebView` 。

[`ScrollView`](xref:Xamarin.Forms.ScrollView)公開 `Content` 屬性，可以設定為單一視圖或版面配置。 請以非常大的 boxView 來考慮此版面配置範例，後面接著 `Entry` ：

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

在 C# 中：

```csharp
public class ScrollingDemoCode : ContentPage
{
    public ScrollingDemoCode()
    {
        StackLayout stackLayout = new StackLayout();
        stackLayout.Children.Add(new BoxView { BackgroundColor = Color.Red, HeightRequest = 600, WidthRequest = 150 });
        stackLayout.Children.Add(new Entry());
        ScrollView scrollView = new ScrollView();
        scrollView.Content = stackLayout;
        Content = scrollView;
    }
}
```

在使用者向下滾動之前，只 `BoxView` 會顯示：

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

請注意，當使用者開始在中輸入文字時 `Entry` ，視圖會滾動，讓它在畫面上顯示：

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>屬性

[`ScrollView`](xref:Xamarin.Forms.ScrollView)定義下列屬性：

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty)取得 [`Size`](xref:Xamarin.Forms.Size) 值，表示內容的大小。
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)取得或設定 [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) 表示之滾動方向的列舉值 `ScrollView` 。
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty)取得 `double` 表示目前 X 捲軸位置的。
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty)取得 `double` 表示目前 Y 捲軸位置的。
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty)取得或設定 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) 值，表示水準捲軸何時可見。
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty)取得或設定 [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) 值，表示垂直捲動條何時可見。

> [!NOTE]
> 將屬性設定為，即可停用滾動 [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) `Neither` 。

## <a name="methods"></a>方法

[`ScrollView`](xref:Xamarin.Forms.ScrollView)提供一種 `ScrollToAsync` 方法，可用來使用座標或藉由指定應該顯示的特定視圖來滾動視圖。

使用座標時，請指定 `x` 和 `y` 座標，以及布林值，指出是否應將滾動動畫：

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> `ScrollToAsync`當屬性設定為時，方法不會產生捲軸 [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) `Neither` 。

當滾動到特定專案時， `ScrollToPosition` 列舉會指定要在 view 中的哪個位置顯示元素：

- **置** &ndash; 中將元素滾動到視圖可見部分的中央。
- **結束** &ndash;將元素滾動到視圖可見部分的結尾。
- **MakeVisible** &ndash;將元素滾動，使其可在視圖中顯示。
- **啟動** &ndash;將元素滾動到視圖可見部分的開頭。

`IsAnimated`屬性會指定視圖的滾動方式。 當設定為時 `true` ，將會使用平滑動畫，而不是立即將內容移至 view。

## <a name="events"></a>事件

[`ScrollView`](xref:Xamarin.Forms.ScrollView)只定義一個事件： `Scrolled` 。 `Scrolled`當視圖完成滾動時，會引發。 的事件處理常式 `Scrolled` 會採用 `ScrolledEventArgs` ，其具有 `ScrollX` 和 `ScrollY` 屬性。 以下示範如何使用的目前捲軸位置來更新標籤 `ScrollView` ：

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

請注意，捲軸位置可能是負面的，因為在清單的結尾處滾動時，會產生跳動效果。

## <a name="related-links"></a>相關連結

- [版面配置（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 範例（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
