---
title: 使用 Xamarin. Android Designer
description: 本文是 Xamarin. Android Designer 的逐步解說。 它會示範如何建立小型色彩瀏覽器應用程式的使用者介面;此使用者介面完全是在設計工具中建立。
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: 1eed9691684bffbc9367c9b411a83876a52cf72f
ms.sourcegitcommit: 63029dd7ea4edb707a53ea936ddbee684a926204
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98609337"
---
# <a name="using-the-xamarinandroid-designer"></a>使用 Xamarin. Android Designer

_本文是 Xamarin. Android Designer 的逐步解說。它會示範如何建立小型色彩瀏覽器應用程式的使用者介面;此使用者介面完全是在設計工具中建立。_

## <a name="overview"></a>概觀

您可以使用 XML 檔案，或以程式設計方式撰寫程式碼，以宣告方式建立 Android 使用者介面。 Xamarin. Android Designer 可讓開發人員以視覺化方式建立和修改宣告式版面配置，而不需要手動編輯 XML 檔案。 設計工具也會提供即時意見反應，讓開發人員不需要將應用程式重新部署至裝置或模擬器，就能評估 UI 的變更。 這些設計工具功能可以大幅加快 Android UI 開發的速度。
本文示範如何使用 Xamarin Android Designer 以視覺化方式建立使用者介面。

> [!TIP]
> 較新版的 Visual Studio 支援在 Android Designer 中開啟 .xml 檔案。
>
> Android Designer 同時支援 .axml 和 .xml 檔案。

## <a name="walkthrough"></a>逐步介紹

本逐步解說的目的是要使用 Android Designer 建立範例色彩瀏覽器應用程式的使用者介面。 色彩瀏覽器應用程式會顯示色彩清單、其名稱和 RGB 值。 您將瞭解如何將 widget 新增至 **Design Surface** 以及如何以視覺化方式配置這些小工具。 之後，您將瞭解如何在 **Design Surface** 上以互動方式修改小工具，或使用設計工具的 [ **屬性** ] 窗格。 最後，您會看到在裝置或模擬器上執行應用程式時，設計的外觀。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>建立新的專案

第一個步驟是建立新的 Xamarin. Android 專案。 啟動 Visual Studio，按一下 [ **新增專案**]，然後選擇 **Visual C \# > Android > Android 應用程式 (Xamarin)** 範本。
將新的應用程式命名為 **DesignerWalkthrough** ，然後按一下 **[確定]**。

[![Android 空白應用程式](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

在 [**新增 Android 應用程式**] 對話方塊中，選擇 [**空白應用程式**]，然後按一下 **[確定]**

[![選取 Android 空白應用程式範本](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)

### <a name="adding-a-layout"></a>新增版面配置

下一步是建立會保存使用者介面元素的 **LinearLayout** 。 以滑鼠右鍵按一下 **方案總管** 中的 [**資源]/** [配置]，然後選取 [**加入 > 新專案**...]。在 [**加入新專案**] 對話方塊中，選取 [ **Android 版面** 配置]。 將檔案命名為 **list_item** ，然後按一下 [ **新增**]：

[![新版面配置](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

新的 **list_item** 版面配置會顯示在設計工具中。 請注意，有兩個窗格會顯示在 &ndash; 左窗格中顯示 **list_item** 的 *DESIGN SURFACE* ，而其 XML 來源顯示在右窗格中。 您可以按一下位於兩個窗格之間的 [**交換窗格**] 圖示，以交換 **Design Surface** 和 **來源** 窗格的位置：

[![設計工具視圖](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

在 [ **View** ] 功能表中，按一下 [ **其他 Windows > 檔大綱** ] 以開啟 [ **檔大綱**]。 **檔大綱** 顯示版面配置目前包含單一 **LinearLayout** 小工具：

[![檔大綱](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

下一步是在這個中建立色彩瀏覽器應用程式的使用者介面 `LinearLayout` 。

### <a name="creating-the-list-item-user-interface"></a>建立清單專案使用者介面

如果未顯示 [ **工具箱** ] 窗格，請按一下左側的 [ **工具箱** ] 索引標籤。 在 [ **工具箱**] 中，向下 **& 媒體** ] 區段中向下移動，並在您找出 `ImageView` ：

[![尋找 >imageview](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

或者，您可以在搜尋列中輸入 *>imageview* ，以找出 `ImageView` ：

[![>imageview 搜尋](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

將此拖曳至 `ImageView` Design Surface (這 `ImageView` 會用來在色彩瀏覽器應用程式) 中顯示色樣：

[![畫布上的 >imageview](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

接下來， `LinearLayout (Vertical)` 從 [ **工具箱** ] 將 Widget 拖曳至設計工具。 請注意，藍色外框表示已新增的界限 `LinearLayout` 。 **檔大綱** 顯示它是的子系 `LinearLayout` ，位於 `imageView1 (ImageView)` ：

[![藍色外框](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

當您 `ImageView` 在設計工具中選取時，藍色外框會移至周圍 `ImageView` 。 此外，選取範圍會移至 `imageView1 (ImageView)` **檔大綱** 中：

[![選取 >imageview](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

接下來，將 `Text (Large)` 小工具從 [ **工具箱** ] 拖曳至新加入的 `LinearLayout` 。 請注意，設計工具會使用綠色醒目提示來指出將插入新小工具的位置：

[![綠色醒目顯示](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

接下來， `Text (Small)` 在 widget 下方新增 `Text (Large)` widget：

[![新增小型文字小工具](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

此時，設計工具介面應該類似下列螢幕擷取畫面：

[![螢幕擷取畫面顯示 [工具箱]、[檔大綱] 和 [版面配置區域] 的設計工具介面，且選取了小型文字。](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

如果兩個 widget `textView` 都不在內部 `linearLayout1` ，您可以將它們拖曳到 `linearLayout1` **檔大綱** 中，並將它們放在上方的螢幕擷取畫面中， (縮排在 `linearLayout1`) 下方。

### <a name="arranging-the-user-interface"></a>排列使用者介面

下一步是修改 UI `ImageView` ，以在左邊顯示兩個小工具，並將這兩個 `TextView` 小工具堆疊到右邊 `ImageView` 。

1. 選取 `ImageView`。

2. 在 [ **屬性視窗**] 的 [搜尋] 方塊中輸入 *width* ，然後找出 [ **版面配置寬度**]。

3. 將 **版面配置寬度** 設定變更為 `wrap_content` ：

![設定自動換行內容](designer-walkthrough-images/vs/15-wrap-content-w158.png)

變更設定的另一種方式 `Width` 是按一下 widget 右邊的三角形，將其寬度設定切換為 `wrap_content` ：

![拖曳以設定寬度](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

再次按一下三角形會將 `Width` 設定傳回至 `match_parent` 。 接下來，移至 [ **檔大綱** ] 窗格，然後選取根目錄 `LinearLayout` ：

[![選取根 LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

選取根時 `LinearLayout` ，返回 [ **屬性** ] 窗格，在 [搜尋] 方塊中輸入 *方向* ，並找出 [ **方向** ] 設定。 將 **方向** 變更為 `horizontal` ：

![選取水準方向](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

此時，設計工具介面應該類似下列螢幕擷取畫面。
請注意， `TextView` 小工具已移至的右邊 `ImageView` ：

[![螢幕擷取畫面顯示 [工具箱]、[檔大綱] 和 [版面配置區域] 的設計工具介面。](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>修改間距

下一步是在 UI 中修改填補和邊界設定，以提供小工具之間的空間。 選取 `ImageView` 設計介面上的。 在 [ **屬性** ] 窗格中，于 `min` [搜尋] 方塊中輸入。 輸入 `70dp` **最小高度** 和 `50dp` **最小寬度**：

[![設定高度與寬度](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

在 [ **屬性** ] 窗格中，于 [搜尋] 方塊中輸入， `padding` 並輸入 `10dp` **填補**。 這些 `minHeight` `minWidth` 和 `padding` 設定會在的所有兩側加上邊框間距， `ImageView` 並以垂直方式 elongate。 請注意，當您輸入這些值時，版面配置 XML 會變更：

[![設定填補](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

底部、左方、右邊和頂端填補設定可以個別設定，方法是將值分別輸入到 **下填補**、 **填補左邊**、 **填補右邊**，以及 **填補頂端** 欄位。
例如，將 [**左邊填補**] 欄位設定為，並將 [靠右填補] `5dp` 、[**右填補**] 和[**填補頂端**] 欄位設為 `10dp` ：

[![自訂填補設定](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

接下來，調整 `LinearLayout` 包含這兩個小工具的 widget 位置 `TextView` 。 在 [ **檔大綱**] 中，選取 `linearLayout1` 。 在 [ **屬性** ] 視窗中，于 `margin` [搜尋] 方塊中輸入。 將 **版面配置邊界底部**、配置 **左邊界** 和 **版面配置邊界頂端** 設定為 `5dp` 。 將 **版面配置邊界** 設定為 `0dp` ：

[![設定邊界](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>移除預設影像

由於 `ImageView` 是用來顯示 (而非影像) 的色彩，因此下一步是移除範本新增的預設影像來源。

1. 選取 `ImageView` **設計工具介面** 上的。

2. 在 [ **屬性**] 的 [搜尋] 方塊中輸入 *src* 。

3. 按一下 [ **Src** ] 屬性設定右邊的小方塊，然後選取 [ **重設**]：

[![清除 >imageview src 設定](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

這 `android:src="@android:drawable/ic_menu_gallery"` 會從的來源 XML 中移除該的 `ImageView` 。

### <a name="adding-a-listview-container"></a>新增 ListView 容器

現在已定義 **list_item** 配置，下一個步驟是將加入 `ListView` 至主要版面配置。 這 `ListView` 會包含 **list_item** 的清單。 

在 **方案總管** 中，開啟 **Resources/layout/activity_main. .axml**。 在 [ **工具箱**] 中，找出 `ListView` widget，然後將它拖曳至 **Design Surface**。 設計工具中的是空白的， `ListView` 但在選取它的框線外，會有藍色線條。 您可以查看 [ **檔大綱** ]，確認已正確新增 **ListView** ：

[![新 ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

依預設， `ListView` 會提供的 `Id` 值 `@+id/listView1` 。
當 `listView1` 仍在 **檔大綱** 中選取時，請開啟 [ **屬性** ] 窗格，按一下 [ **排列依據**]，然後選取 [ **類別目錄**]。
開啟 **Main**，找出 **識別碼** 屬性，並將其值變更為 `@+id/myListView` ：

[![將識別碼重新命名為 myListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

此時，使用者介面已可供使用。

### <a name="running-the-application"></a>執行應用程式

開啟 **MainActivity.cs** ，並將其程式碼取代為下列內容：

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

此程式碼會使用自訂 `ListView` 介面卡來載入色彩資訊，並在剛建立的 UI 中顯示此資料。 為了讓這個範例保持簡短，色彩資訊是以硬式編碼的方式在清單中，但您可以修改介面卡，以從資料來源取出色彩資訊，或是即時計算。 如需介面卡的詳細資訊 `ListView` ，請參閱 [ListView](~/android/user-interface/layouts/list-view/index.md)。

建置並執行應用程式。 下列螢幕擷取畫面是在裝置上執行應用程式時，應用程式顯示方式的範例：

[![最後一個螢幕擷取畫面](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

### <a name="creating-a-new-project"></a>建立新的專案

第一個步驟是建立新的 Xamarin. Android 專案。

啟動 Visual Studio for Mac，然後按一下 [ **新增專案**]。選擇 [ **Android 應用程式** ] 範本，然後按 **[下一步]**：

[![Android 空白應用程式](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

將新應用程式命名為 **DesignerWalkthrough**。 在 [ **目標平臺**] 之下，選取 [ **最新] 和** [最大 **]**，然後按

[![命名應用程式](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

在下一個對話方塊畫面中，按一下 [ **建立**]。

### <a name="adding-a-layout"></a>新增版面配置

下一步是建立會保存使用者介面元素的 **LinearLayout** 。

在 Visual Studio for Mac 中，以滑鼠右鍵按一下 **Solution** pad 中的 [**資源]/[版面** 配置]，然後選取 [**新增 > 新** 檔案 ...]。在 [**新增** 檔案] 對話方塊中，選取 [ **Android > 版面** 配置]。 將檔案命名為 **list_item** ，然後按一下 [ **新增**]：

[![新版面配置](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

加入這個檔案之後，新的 **list_item** 配置會顯示在 **Design Surface** (如果您看到訊息， *表示此專案包含未成功編譯的資源，轉譯可能會受到影響*，請按一下 [ **組建] > [組建** ]，以建立專案) ：

[![設計工具視圖](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

按一下設計工具底部的 [ **來源** ] 索引標籤，以查看此版面配置的 XML 來源。 當您按一下右邊的 [ **檔大綱** ] 索引標籤時，它會顯示版面配置目前包含單一 **LinearLayout** 小工具：

[![設計工具 XML](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

下一步是建立色彩瀏覽器應用程式的使用者介面。

### <a name="creating-the-list-item-user-interface"></a>建立清單專案使用者介面

按一下畫面底部的 [ **設計** 工具] 索引標籤，以返回 **設計工具介面**。 在右側的 [ **工具箱** ] 窗格中，向下 **& 媒體** ] 區段中向下滾動，然後找出 `ImageView` ：

[![尋找 >imageview](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

或者，您可以在搜尋列中輸入 *>imageview* ，以找出 `ImageView` ：

[![>imageview 搜尋](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

將此拖曳至 `ImageView` **Design Surface** (這 `ImageView` 會用來在色彩瀏覽器應用程式) 中顯示色樣：

[![畫布上的 >imageview](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

接下來， `LinearLayout (Vertical)` 從 [ **工具箱** ] 將 widget 拖曳至 **Design Surface**。 請注意，藍色外框表示已新增的界限 `LinearLayout` 。 **檔大綱** 顯示它是的子系 `LinearLayout` ，位於以下位置 `imageView1 (ImageView)` ：

[![藍色外框](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

當您 `ImageView` 在設計工具中選取時，藍色外框會移至周圍 `ImageView` 。 此外，選取範圍會移至 `imageView1 (ImageView)` **檔大綱** 中：

[![選取 >imageview](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

接下來，將 `Text (Large)` 小工具從 [ **工具箱** ] 拖曳至新加入的 `LinearLayout` 。 請注意，當您將滑鼠拖曳到 **Design Surface** 時，它會反白顯示新小工具的插入位置。
`Text (Large)`Widget 應位於以下所示的內部 `linearLayout1` ：

[![新增大型文字小工具](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

接下來， `Text (Small)` 在 widget 下方新增 `Text (Large)` widget。 此時， **Design Surface** 應該類似下列螢幕擷取畫面：

[![新增小型文字小工具](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

如果兩個 widget `textView` 都不在內部 `linearLayout1` ，您可以將它們拖曳到 `linearLayout1` **檔大綱** 中，並將它們放在上方的螢幕擷取畫面中， (在) 下縮排 `linearLayout1` 。

### <a name="arranging-the-user-interface"></a>排列使用者介面

下一步是修改 UI `ImageView` ，以在左邊顯示兩個小工具，並將這兩個 `TextView` 小工具堆疊到右邊 `ImageView` 。

1. 使用 `ImageView` 選取的，按一下 [ **屬性** ] 索引標籤。

2. **在 [** 內容] 索引標籤的正下方，按一下 [**版面** 配置]。

3. 向下滾動以 **再 viewgroup** ，並將 `Width` 設定變更為  `wrap_content` ：

[![設定自動換行內容](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

變更設定的另一種方式 `Width` 是按一下 widget 右邊的三角形，將其寬度設定切換為 `wrap_content` ：

[![拖曳以設定寬度](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

再次按一下三角形會將 `Width` 設定傳回至 `match_parent` 。 接下來，移至 [ **檔大綱** ] 窗格，然後選取根目錄 `LinearLayout` ：

[![選取根 LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

選取根時 `LinearLayout` ，返回 [ **屬性** ] 索引標籤，然後按一下 [ **Widget**]。 將 `Orientation` 設定變更為， `horizontal` 如下所示。 此時， **Design Surface** 應該類似下列螢幕擷取畫面。 請注意， `TextView` 小工具已移至的右邊 `ImageView` ：

[![選取水準方向](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)

### <a name="modifying-the-spacing"></a>修改間距

下一步是修改 UI 中的填補和邊界設定，以提供小工具之間的空間。 選取 `ImageView` ，然後按一下 [**屬性**] 底下的 [**版面** 配置] 索引標籤。 將變更 `Min Width` 為、將變更為 `50dp` `Min Height` `70dp` ，並 `Padding` 將變更為 `10dp` 。
這會將填補套用至周圍的所有邊 `ImageView` ，並垂直 elongates：

[![設定填補](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

您可以分別將值輸入 `Top` 、 `Right` 、 `Bottom` 和 `Left` 填補欄位，以獨立設定頂端、右邊、下和左邊的填補設定。 例如，將 `Left` 填補值設定為 `5dp` ，並將 `Top` 、 `Right` 和 `Bottom` 填補值設定為 `10dp` 。 請注意，此 `Padding` 設定會變更為這些值的逗號分隔清單：

[![自訂填補設定](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

接下來，調整 `LinearLayout` 包含這兩個小工具的 widget 位置 `TextView` 。 在 [ **檔大綱**] 中，選取 `linearLayout1` 。 在 [ **屬性** ] 窗格中，選取 [ **版面** 配置] 索引標籤。向下 **再 viewgroup** 區段，並將、、 `Left` `Top` `Right` 和 `Bottom` 邊界分別設定為 `5dp` 、、 `5dp` `0dp` 和 `5dp` ：

[![設定邊界](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>移除預設影像

由於 `ImageView` 是用來顯示 (而非影像) 的色彩，因此下一步是移除範本新增的預設影像來源。

1. 選取 `ImageView`。

2. 按一下 [**屬性**] 底下的 [ **Widget** ] 索引標籤。

3. 清除 `Src` 設定，使其空白：

[![清除 >imageview src 設定](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

這 `android:src="@android:drawable/ic_menu_gallery"` 會從的來源 XML 中移除該的 `ImageView` 。

### <a name="adding-a-listview-container"></a>新增 ListView 容器

現在已定義 **list_item** 配置，下一個步驟是將加入 `ListView` 至主要版面配置。 這 `ListView` 會包含 **list_item** 的清單。 

在 **方案總管** 中，開啟 **Resources/layout/Main. .axml**。
`Button`如果有任何) ，請按一下 widget (，然後將它刪除。 在 [ **工具箱**] 中，找出 `ListView` widget，然後將它拖曳至 **Design Surface**。
設計工具中的是空白的， `ListView` 但在選取它的框線外，會有藍色線條。 您可以查看 [ **檔大綱** ]，確認已正確新增 **ListView** ：

[![新 ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

依預設， `ListView` 會提供的 `Id` 值 `@+id/listView1` 。
當 `listView1` 仍在 **檔大綱** 中選取時，請開啟 [ **屬性** ] 窗格，按一下 [ **排列依據**]，然後選取 [ **類別目錄**]。
開啟 **Main**，找出 **識別碼** 屬性，並將其值變更為 `@+id/myListView` ：

[![將識別碼重新命名為 myListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

此時，使用者介面已可供使用。

### <a name="running-the-application"></a>執行應用程式

開啟 **MainActivity.cs** ，並將其程式碼取代為下列內容：

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

此程式碼會使用自訂 `ListView` 介面卡來載入色彩資訊，並在剛建立的 UI 中顯示此資料。 為了讓這個範例保持簡短，色彩資訊是以硬式編碼的方式在清單中，但您可以修改介面卡，以從資料來源取出色彩資訊，或是即時計算。 如需介面卡的詳細資訊 `ListView` ，請參閱 [ListView](~/android/user-interface/layouts/list-view/index.md)。

建置並執行應用程式。 下列螢幕擷取畫面是在裝置上執行應用程式時，應用程式顯示方式的範例：

[![最後一個螢幕擷取畫面](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----

## <a name="summary"></a>摘要

本文逐步解說使用 Android Designer 在 Visual Studio 中建立基本應用程式的使用者介面的流程。
它示範了如何為清單中的單一專案建立介面，並說明如何加入 widget 並以視覺化方式呈現它們。
此外也會說明如何指派資源，然後在這些小工具上設定各種屬性。
