---
title: 使用 Xamarin. Android Designer
description: 本文是 Xamarin. Android Designer 的逐步解說。 它會示範如何為小型的彩色瀏覽器應用程式建立使用者介面;這個使用者介面是完全在設計工具中建立的。
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 9387b44419af87785d45a25ab254d3361a5615a3
ms.sourcegitcommit: c75c1d2132a4f46a7b38e454d5f24705165026bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68485928"
---
# <a name="using-the-xamarinandroid-designer"></a>使用 Xamarin. Android Designer

_本文是 Xamarin. Android Designer 的逐步解說。它會示範如何為小型的彩色瀏覽器應用程式建立使用者介面;這個使用者介面是完全在設計工具中建立的。_


## <a name="overview"></a>總覽

Android 使用者介面可以使用 XML 檔案以宣告方式建立, 或藉由撰寫程式碼以程式設計方式建立。 Android Designer 可以讓開發人員以視覺化方式建立和修改宣告式配置, 而不需要手動編輯 XML 檔案。 此設計工具也會提供即時的意見反應, 讓開發人員不需要將應用程式重新部署至裝置或模擬器, 即可評估 UI 變更。 這些設計工具功能可以大幅加快 Android UI 開發的速度。
本文示範如何使用 Xamarin. Android Designer 以視覺化方式建立使用者介面。

> [!TIP]
> 較新版本的 Visual Studio 支援在 Android Designer 中開啟 .xml 檔案。
>
> Android Designer 中都支援 axml 和 .xml 檔案。

## <a name="walkthrough"></a>逐步解說

本逐步解說的目的是要使用 Android Designer 來建立範例色彩瀏覽器應用程式的使用者介面。 [色彩瀏覽器] 應用程式會顯示色彩、其名稱和 RGB 值的清單。 您將瞭解如何將 widget 新增至**Design Surface** , 以及如何以視覺化方式配置這些小工具。 之後, 您將瞭解如何在**Design Surface**上以互動方式修改 widget, 或使用設計工具的 [**屬性**] 窗格。 最後, 您會看到在裝置或模擬器上執行應用程式時, 設計的外觀。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>建立新的專案

第一個步驟是建立新的 Xamarin Android 專案。 啟動 Visual Studio, 按一下 **新增專案 ...** , 然後選擇**Visual\# C > android > android 應用程式 (Xamarin)**  範本。
將新的應用程式命名為**DesignerWalkthrough** , 然後按一下 **[確定]** 。

[![Android 空白應用程式](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

在 [**新增 Android app** ] 對話方塊中, 選擇 [**空白應用程式**], 然後按一下 **[確定]** :

[![選取 Android [空白應用程式] 範本](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)


### <a name="adding-a-layout"></a>新增版面配置

下一個步驟是建立會保存使用者介面元素的**LinearLayout** 。 以滑鼠右鍵按一下**方案總管**中的 [**資源/** 配置], 然後選取 [**加入 > 新增專案**...]。在 [**加入新專案**] 對話方塊中, 選取 [ **Android 版面**配置]。 將檔案命名為**list_item** , 然後按一下 [**新增**]:

[![新版面配置](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

新的**list_item**版面配置會顯示在設計工具中。 請注意, 在左窗格&ndash;中顯示**list_item**的*Design Surface* , 而在右窗格中顯示其 XML 來源時, 會看到兩個窗格。 您可以按一下位於兩個窗格之間的 [**交換窗格**] 圖示, 交換 [ **Design Surface** ] 和 [**來源**] 窗格的位置:

[![設計工具視圖](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

從 [ **View** ] 功能表中, 按一下 [**其他視窗 > 檔大綱**] 以開啟 [**檔大綱**]。 [**檔大綱**] 顯示版面配置目前包含單一**LinearLayout**小工具:

[![檔大綱](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

下一個步驟是在這個`LinearLayout`中建立色彩瀏覽器應用程式的使用者介面。

### <a name="creating-the-list-item-user-interface"></a>建立清單專案使用者介面

如果沒有顯示 [**工具箱**] 窗格, 請按一下左側的 [**工具箱**] 索引標籤。 在 [**工具箱**] 中, 向下 **& [媒體**] 區段中上下移動, 直到`ImageView`您找到下列各項:

[![找出 ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

或者, 您可以在搜尋列中輸入*ImageView* , 以找`ImageView`出:

[![ImageView 搜尋](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

將此`ImageView`拖曳至 Design Surface ( `ImageView`這將用來在 色彩瀏覽器 應用程式中顯示色樣):

[![畫布上的 ImageView](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

接下來, 將`LinearLayout (Vertical)` widget 從 [**工具箱**] 拖曳至設計工具。 請注意, 藍色外框表示已加入`LinearLayout`的界限。 [**檔大綱**] 顯示它是的子`LinearLayout`系, 位於`imageView1 (ImageView)`下列位置:

[![藍色外框](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

當您`ImageView`在設計工具中選取時, 藍色外框會移動以`ImageView`環繞。 此外, 選取範圍會移至`imageView1 (ImageView)` **檔大綱**中的:

[![選取 ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

接下來, 將`Text (Large)` widget 從 [**工具箱**] 拖曳至新加入`LinearLayout`的。 請注意, 設計工具會使用綠色的反白顯示來指出將插入新 widget 的位置:

[![綠色反白顯示](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

接下來, 在`Text (Small)` `Text (Large)`小工具下方新增 widget:

[![新增小型文字小工具](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

此時, 設計工具介面應該類似下列螢幕擷取畫面:

[![設計工具版面配置](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

如果兩個`textView`小工具不在`linearLayout1`內, 您可以將它們`linearLayout1`拖曳至**檔大綱**中, 並放置它們, 讓它們如先前的螢幕擷取畫面所示`linearLayout1`顯示 (縮排在底下)。


### <a name="arranging-the-user-interface"></a>排列使用者介面

下一個步驟是修改 UI, 以`ImageView`在左側顯示, 並將兩個`TextView`小工具堆疊`ImageView`到的右邊。

1.  選取 `ImageView`。

2.  在 **屬性視窗**的 搜尋 方塊中, 輸入*width* , 並找出 **版面配置寬度**。

3.  將 [**版面配置寬度**] `wrap_content`設定變更為:

![設定自動換行內容](designer-walkthrough-images/vs/15-wrap-content-w158.png)

另一種變更`Width`設定的方式是按一下 widget 右手邊的三角形, 將其寬度設定切換為: `wrap_content`

![拖曳以設定寬度](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

再次`Width`按一下三角形, 會將設定傳回`match_parent`給。 接下來, 移至 [**檔大綱**] 窗格, 然後`LinearLayout`選取 [根]:

[![選取根 LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

選取`LinearLayout`根之後, 返回 [**屬性**] 窗格, 在 [搜尋] 方塊中輸入*方向*, 並找出 [**方向**] 設定。 將**方向**變更`horizontal`為:

![選取水準方向](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

此時, 設計工具介面應該類似下列螢幕擷取畫面。
請注意, `TextView` widget 已移至右側: `ImageView`

[![設計工具版面配置](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>修改間距

下一步是修改 UI 中的填補和邊界設定, 以在 widget 之間提供更多空間。 選取設計`ImageView`介面上的。 在 [**屬性**] 窗格的`min` [搜尋] 方塊中, 輸入。 輸入`70dp` [**最小高度**] 和`50dp` [**最小寬度**]:

[![設定高度和寬度](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

在 [**屬性**] 窗格中`padding` , 于搜尋方塊中輸入`10dp` , 並輸入**填補**。 這些`minHeight`和`minWidth` `ImageView`設定會在的所有側邊加上填補, 並以垂直方式 elongate。 `padding` 請注意, 當您輸入這些值時, 版面配置 XML 會變更:

[![設定填補](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

下、左、右和上填補設定可以個別設定, 其方式是將值輸入到**下填補**、**左邊填補**、**右填補**和**填補頂端**欄位。
例如, 將 [填補]**左邊**欄位設定`5dp`為, 並將 [**右**填補]、[填補] 和`10dp`[**填補] 頂端**欄位設為:

[![自訂填補設定](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

接下來, 調整包含兩個`LinearLayout` `TextView`小工具的 widget 位置。 在 [**檔大綱**] 中`linearLayout1`, 選取。 在 [**屬性**] 視窗的`margin` [搜尋] 方塊中, 輸入。 將 [**版面配置邊界**]、[**版面**配置邊界] 和 [版面配置**邊界**] 設定為`5dp`。 將配置**邊界**設定為`0dp`[右]:

[![設定邊界](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>移除預設映射

`ImageView`由於是用來顯示色彩 (而非影像), 因此下一個步驟是移除範本所新增的預設影像來源。

1.  在設計`ImageView`工具**介面**上, 選取。

2.  在 [**屬性**] 的 [搜尋] 方塊中, 輸入*src* 。

3.  按一下**Src**屬性設定右邊的小方形, 然後選取 [**重設**]:

[![清除 ImageView src 設定](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

這會`android:src="@android:drawable/ic_menu_gallery"`從的來源 XML 移除該`ImageView`。

### <a name="adding-a-listview-container"></a>新增 ListView 容器

現在已定義**list_item**配置, 下一個步驟是將加入`ListView`至主要版面配置。 這`ListView`會包含**list_item**的清單。 

在**方案總管**中, 開啟**Resources/layout/activity_main. axml**。 在 [**工具箱**] 中找`ListView`出小工具, 並將它拖曳至 [ **Design Surface**]。 設計`ListView`工具中的會是空白, 但在選取時, 會將框線外框的藍色線條除外。 您可以查看**檔大綱**, 確認已正確新增**ListView** :

[![新增 ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

根據預設, `ListView`會`Id`提供的值`@+id/listView1`給。
當`listView1`您仍在 [**檔大綱**] 中選取時, 開啟 [**屬性**] 窗格, 按一下 [**排列依據**], 然後選取 [**類別**]。
開啟 [ **Main**], 找到**Id**屬性, 並將其值`@+id/myListView`變更為:

[![將識別碼重新命名為 myListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

此時, 使用者介面已準備好可供使用。

### <a name="running-the-application"></a>執行應用程式

開啟**MainActivity.cs** , 並將其程式碼取代為下列內容:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using Android.Support.V7.App;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}

```

這段程式碼會`ListView`使用自訂介面卡來載入色彩資訊, 並在剛建立的 UI 中顯示此資料。 為了讓此範例保持簡短, 色彩資訊會以硬式編碼方式放入清單中, 但是可以修改介面卡以從資料來源解壓縮色彩資訊, 或即時計算。 如需`ListView`介面卡的詳細資訊, 請參閱[ListView](~/android/user-interface/layouts/list-view/index.md)。

建置並執行應用程式。 下列螢幕擷取畫面是應用程式在裝置上執行時的顯示方式範例:

[![最後的螢幕擷取畫面](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

### <a name="creating-a-new-project"></a>建立新的專案

第一個步驟是建立新的 Xamarin Android 專案。

啟動 Visual Studio for Mac, 然後按一下 [**新增專案**...]。選擇 [ **Android 應用程式**] 範本, 然後按 **[下一步]** :

[![Android 空白應用程式](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

將新的應用程式命名為**DesignerWalkthrough**。 在 [**目標平臺**] 底下, 選取 [**最新] 和 [最大** **]** , 然後按

[![名稱應用程式](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

在下一個對話方塊畫面中, 按一下 [**建立**]。

### <a name="adding-a-layout"></a>新增版面配置

下一個步驟是建立會保存使用者介面元素的**LinearLayout** 。

在 Visual Studio for Mac 中, 以滑鼠右鍵按一下**Solution** pad 中的 [**資源/版面**配置], 然後選取 [**新增 > 新**檔案 ...]。在 [**新增**檔案] 對話方塊中, 選取 [ **Android > 版面**配置]。 將檔案命名為**list_item** , 然後按一下 [**新增**]:

[![新版面配置](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

新增此檔案之後, 會在**Design Surface**上顯示新的**list_item**配置 (如果您看到訊息,*表示此專案包含未成功編譯的資源, 轉譯可能會受到影響*, 請按一下 [**建立] >組建全部**以建立專案):

[![設計工具視圖](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

按一下設計工具底部的 [**來源**] 索引標籤, 以查看此版面配置的 XML 來源。 當您按一下右側的 [**檔大綱**] 索引標籤時, 它會顯示版面配置目前包含單一**LinearLayout**小工具:

[![設計工具 XML](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

下一個步驟是建立色彩瀏覽器應用程式的使用者介面。

### <a name="creating-the-list-item-user-interface"></a>建立清單專案使用者介面

按一下畫面底部的 [**設計**工具] 索引標籤, 返回**設計工具介面**。 在右側的 [**工具箱**] 窗格中, 向下 & [媒體] 區段中向`ImageView`下流覽至 [**影像**], 並尋找:

[![找出 ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

或者, 您可以在搜尋列中輸入*ImageView* , 以找`ImageView`出:

[![ImageView 搜尋](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

將此`ImageView`拖曳至  **Design Surface** ( `ImageView`這將用來在 色彩瀏覽器 應用程式中顯示色樣):

[![畫布上的 ImageView](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

接下來, 將`LinearLayout (Vertical)` widget 從 [**工具箱**] 拖曳至 [ **Design Surface**]。 請注意, 藍色外框表示已加入`LinearLayout`的界限。 [**檔大綱**] 顯示它是的子`LinearLayout`系, 位於下列`imageView1 (ImageView)`位置:

[![藍色外框](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

當您`ImageView`在設計工具中選取時, 藍色外框會移動以`ImageView`環繞。 此外, 選取範圍會移至`imageView1 (ImageView)` **檔大綱**中的:

[![選取 ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

接下來, 將`Text (Large)` widget 從 [**工具箱**] 拖曳至新加入`LinearLayout`的。 請注意, 當您將滑鼠拖曳至  **Design Surface**時, 它會反白顯示新 widget 的插入位置。
Widget 應該位於中`linearLayout1` , 如下所示: `Text (Large)`

[![新增大型文字小工具](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

接下來, 在`Text (Small)` `Text (Large)`小工具下方新增 widget。 此時, **Design Surface**應該類似下列螢幕擷取畫面:

[![新增小型文字小工具](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

如果兩個`textView`小工具不在`linearLayout1`內, 您可以將它們`linearLayout1`拖曳至**檔大綱**中, 並放置它們, 使其如先前的螢幕擷取畫面所示`linearLayout1`顯示 (縮排在底下)。


### <a name="arranging-the-user-interface"></a>排列使用者介面

下一個步驟是修改 UI, 以`ImageView`在左側顯示, 並將兩個`TextView`小工具堆疊`ImageView`到的右邊。

1.  使用選取`ImageView`的, 按一下 [**屬性**] 索引標籤。

2.  在 [**屬性**] 索引標籤正下方, 按一下 [配置]。

3.  向下**ViewGroup** , 並將`Width`設定變更為: `wrap_content`

[![設定自動換行內容](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

另一種變更`Width`設定的方式是按一下 widget 右手邊的三角形, 將其寬度設定切換為: `wrap_content`

[![拖曳以設定寬度](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

再次`Width`按一下三角形, 會將設定傳回`match_parent`給。 接下來, 移至 [**檔大綱**] 窗格, 然後`LinearLayout`選取 [根]:

[![選取根 LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

選取根`LinearLayout`之後, 返回 [**屬性**] 索引標籤, 然後按一下 [ **Widget**]。 將設定變更為`horizontal` , 如下所示。 `Orientation` 此時, **Design Surface**應該類似下列螢幕擷取畫面。 請注意, `TextView` widget 已移至右側: `ImageView`

[![選取水準方向](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)


### <a name="modifying-the-spacing"></a>修改間距

下一步是修改 UI 中的填補和邊界設定, 以在 widget 之間提供更多空間。 選取, 然後按一下 [**屬性**] 底下的 [配置] 索引標籤。  `ImageView` `50dp` `10dp`將變更`Min Width`為、 `Min Height`設為`70dp`,並`Padding`將變更為。
這會在的`ImageView`所有側邊套用填補, 並以垂直方式 elongates:

[![設定填補](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

您可以分別`Top`在、 `Right`、 `Bottom`和`Left`填補欄位中輸入值, 以獨立設定頂端、右側、底部和左側填補設定。 `Left`例如, 將填補值設為`5dp` , 並`Top`將、 `Right`和`Bottom`填補值設定為`10dp`。 請注意, `Padding`設定會變更為這些值的逗號分隔清單:

[![自訂填補設定](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

接下來, 調整包含兩個`LinearLayout` `TextView`小工具的 widget 位置。 在 [**檔大綱**] 中`linearLayout1`, 選取。 在 [**屬性**] 窗格中, 選取 [**版面**配置] 索引標籤。向下流覽至 **[ViewGroup** ] 區段, `Left`並`Top`將`Right` `Bottom` 、、和邊界`5dp`分別設定`0dp`為、 `5dp` `5dp`、和:

[![設定邊界](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>移除預設映射

`ImageView`由於是用來顯示色彩 (而非影像), 因此下一個步驟是移除範本所新增的預設影像來源。

1.  選取 `ImageView`。

2.  按一下 [**屬性**] 底下的 [ **Widget** ] 索引標籤。

3.  `Src`清除設定, 使其空白:

[![清除 ImageView src 設定](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

這會`android:src="@android:drawable/ic_menu_gallery"`從的來源 XML 移除該`ImageView`。

### <a name="adding-a-listview-container"></a>新增 ListView 容器

現在已定義**list_item**配置, 下一個步驟是將加入`ListView`至主要版面配置。 這`ListView`會包含**list_item**的清單。 

在**方案總管**中, 開啟**Resources/layout/Main. axml**。
`Button`按一下 widget (如果有的話), 並將它刪除。 在 [**工具箱**] 中找`ListView`出小工具, 並將它拖曳至 [ **Design Surface**]。
設計`ListView`工具中的會是空白, 但在選取時, 會將框線外框的藍色線條除外。 您可以查看**檔大綱**, 確認已正確新增**ListView** :

[![新增 ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

根據預設, `ListView`會`Id`提供的值`@+id/listView1`給。
當`listView1`您仍在 [**檔大綱**] 中選取時, 開啟 [**屬性**] 窗格, 按一下 [**排列依據**], 然後選取 [**類別**]。
開啟 [ **Main**], 找到**Id**屬性, 並將其值`@+id/myListView`變更為:

[![將識別碼重新命名為 myListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

此時, 使用者介面已準備好可供使用。

### <a name="running-the-application"></a>執行應用程式

開啟**MainActivity.cs** , 並將其程式碼取代為下列內容:

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", MainLauncher = true)]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.Main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}
```

這段程式碼會`ListView`使用自訂介面卡來載入色彩資訊, 並在剛建立的 UI 中顯示此資料。 為了讓此範例保持簡短, 色彩資訊會以硬式編碼方式放入清單中, 但是可以修改介面卡以從資料來源解壓縮色彩資訊, 或即時計算。 如需`ListView`介面卡的詳細資訊, 請參閱[ListView](~/android/user-interface/layouts/list-view/index.md)。

建置並執行應用程式。 下列螢幕擷取畫面是應用程式在裝置上執行時的顯示方式範例:

[![最後的螢幕擷取畫面](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----


## <a name="summary"></a>總結

本文逐步解說在 Visual Studio 中使用 Xamarin. Android Designer 的程式, 以建立基本應用程式的使用者介面。
其中示範了如何為清單中的單一專案建立介面, 並說明如何加入 widget, 並以視覺化方式將其版面配置。
它也會說明如何指派資源, 然後在那些 widget 上設定各種屬性。
