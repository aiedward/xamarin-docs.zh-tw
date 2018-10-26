---
title: 使用 Xamarin.Android 設計工具
description: 這篇文章是 Xamarin.Android 設計工具的逐步解說。 它會示範如何建立使用者介面，讓小型彩色的瀏覽器應用程式;此使用者介面會完全在設計工具中建立。
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 1174fe5cb417d4977fd6519086e6c4942e74c10b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120266"
---
# <a name="using-the-xamarinandroid-designer"></a>使用 Xamarin.Android 設計工具

_這篇文章是 Xamarin.Android 設計工具的逐步解說。它會示範如何建立使用者介面，讓小型彩色的瀏覽器應用程式;此使用者介面會完全在設計工具中建立。_


## <a name="overview"></a>總覽

您可以以宣告方式建立 android 使用者介面，藉由使用 XML 檔案，或以程式設計方式撰寫的程式碼。 Xamarin.Android 設計工具可讓開發人員建立，並以視覺化的方式，而不需要手動編輯 XML 檔案的修改宣告式配置。 設計工具也提供可讓開發人員評估 UI 變更而不必重新部署至裝置或模擬器的應用程式的即時意見反應。 這些設計工具功能可以加速 Android UI 開發極大的差異。
這篇文章會示範如何使用 Xamarin.Android 設計工具以視覺化方式建立使用者介面。

## <a name="walkthrough"></a>逐步解說

本逐步解說的目的是要使用 Android Designer 來建立範例色彩瀏覽器應用程式的使用者介面。 色彩瀏覽器應用程式顯示色彩，其名稱和其 RGB 值的清單。 您將了解如何加入 widget**設計介面**以及如何以視覺化方式來配置這些小工具。 在那之後，您將了解如何修改以互動方式在 widget**設計介面**或使用設計工具的**屬性**窗格。 最後，您會看到裝置或模擬器上執行的應用程式時，設計的外觀。


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>建立新的專案

第一個步驟是建立新的 Xamarin.Android 專案。 啟動 Visual Studio 中，按一下 **新專案...**，然後選擇  **Visual C\# > Android > Android 應用程式 (Xamarin)** 範本。
將新的應用程式命名**DesignerWalkthrough**然後按一下**確定**。

[![空白的 android 應用程式](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

在 **新的 Android 應用程式** 對話方塊中，選擇**空白應用程式**，按一下  **確定**:

[![選取空白的 Android 應用程式範本](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)


### <a name="adding-a-layout"></a>新增版面配置

下一個步驟是建立**LinearLayout**將保留使用者介面項目。 以滑鼠右鍵按一下**資源/配置**中**方案總管**，然後選取**新增 > 新增項目...**.在 **加入新項目**對話方塊中，選取**Android 配置**。 將檔案命名**list_item**然後按一下**新增**:

[![新的版面配置](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

新**list_item**配置會顯示在設計工具。 請注意兩個窗格會顯示&ndash;*設計介面*如**list_item**是顯示在左窗格中，而其 XML 來源會顯示在右窗格中。 您可以交換的位置**設計介面**並**來源**窗格，依序按一下**交換窗格**圖示位於之間的兩個窗格：

[![設計工具檢視](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

從**檢視**功能表上，按一下**其他 Windows > 文件大綱**以開啟**文件大綱**。 **文件大綱**顯示 版面配置目前包含單一**LinearLayout** widget:

[![文件大綱](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

下一個步驟是建立在這個色彩瀏覽器應用程式的使用者介面`LinearLayout`。

### <a name="creating-the-list-item-user-interface"></a>建立清單項目使用者介面

如果**工具箱**未顯示 窗格中，按一下**工具箱**左側的索引標籤。 在 **工具箱**，向下捲動至**映像與媒體**區段，並進一步向下捲動直到您找出`ImageView`:

[![找出 ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

或者，您可以輸入*ImageView*找出在搜尋列`ImageView`:

[![ImageView 搜尋](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

將此`ImageView`拖曳至設計介面 (這`ImageView`用以顯示色樣色彩瀏覽器應用程式中):

[![在畫布上的 ImageView](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

下一步，拖曳`LinearLayout (Vertical)`小工具，從**工具箱**進入設計工具。 請注意，藍色外框會指出所加入的界限`LinearLayout`。 **文件大綱**顯示它是子系`LinearLayout`下`imageView1 (ImageView)`:

[![藍色外框](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

當您選取`ImageView`括住，在設計工具中，移動藍色外框`ImageView`。 此外，將選取範圍移至`imageView1 (ImageView)`中**文件大綱**:

[![選取 ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

下一步，拖曳`Text (Large)`小工具，從**工具箱**到新加入`LinearLayout`。 請注意，設計工具會使用綠色反白顯示，表示要插入新的小工具的位置：

[![綠色反白顯示](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

接下來，新增`Text (Small)`小工具下`Text (Large)`widget:

[![新增小文字小工具](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

到目前為止，設計工具介面看起來應該像下列螢幕擷取畫面：

[![設計工具的版面配置](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

如果兩個`textView`小工具不會深入探討`linearLayout1`，您可以將它們拖曳到`linearLayout1`中**文件大綱**，然後放置在使它們顯示在上一個螢幕擷取畫面所示 (縮排在`linearLayout1`)。


### <a name="arranging-the-user-interface"></a>排列的使用者介面

下一步是修改 UI 能夠顯示`ImageView`左邊的兩個`TextView`小工具會將右邊的堆疊`ImageView`。

1.  選取 `ImageView`。

2.  在**屬性 視窗**，輸入*寬度*在搜尋 方塊，然後找出**版面配置寬度**。

3.  變更**版面配置寬度**設為  `wrap_content`:

![設定自動換行內容](designer-walkthrough-images/vs/15-wrap-content-w158.png)

若要變更的另一種方式`Width`設定為 按一下可切換至其寬度設定小工具的右邊的三角形`wrap_content`:

![若要設定寬度的拖放](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

再次按一下該三角形會傳回`Width`設為  `match_parent`。 接下來，移至**文件大綱**窗格，然後選取根目錄`LinearLayout`:

[![選取根 LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

具有根`LinearLayout`選取，返回**屬性**窗格中，輸入*方向*在搜尋方塊，然後找出**方向**設定。 變更**方向**到`horizontal`:

![選取 水平方向](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

到目前為止，設計工具介面看起來應該像下列螢幕擷取畫面。
請注意，`TextView`小工具都已移至右側`ImageView`:

[![設計工具的版面配置](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>修改間距

下一個步驟是修改在 UI 中提供更多的空間，小工具之間的邊框距離及邊界設定。 選取`ImageView`設計介面上。 在 **屬性**窗格中，輸入`min`在搜尋方塊中。 輸入`70dp`for**最小高度**並`50dp`for**最小寬度**:

[![設定高度和寬度](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

在**屬性**窗格中，輸入`padding`在搜尋 方塊，然後輸入`10dp`如**填補**。 這些`minHeight`，`minWidth`並`padding`設定加上邊框間距周圍每一邊都`ImageView`和垂直延伸它。 請注意 XML 版面配置變更為您輸入這些值：

[![設定邊框間距](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

下、 左、 以滑鼠右鍵，並可以獨立設定填補設定的頂端，輸入值**填補下方**，**填補左方**，**邊框間距右邊**，和**填補上方**欄位，分別。
比方說，設定**填補左方**欄位設為`5dp`和**填補下方**，**邊框間距右邊**，並**填補上方**欄位若要`10dp`:

[![自訂的填補設定](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

接下來，調整 位置`LinearLayout`小工具，其中包含兩個`TextView`widget。 在 **文件大綱**，選取`linearLayout1`。 在 [**屬性**] 視窗中，輸入`margin`在搜尋方塊中。 設定**版面配置邊界底部**，**版面配置邊界左邊**，並**版面配置邊界上方**至`5dp`。 設定**版面配置邊界右邊**到`0dp`:

[![設定邊界](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>移除預設的映像

因為`ImageView`用來顯示的色彩 （而非映像） 下, 一步是移除範本所加入的預設影像來源。

1.  選取 `ImageView`上**設計工具介面**。

2.  在 **屬性**，輸入*src*在搜尋方塊中。

3.  按一下右邊的小方形**Src**屬性設定，然後選取**重設**:

[![清除 ImageView src 設定](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

這會移除`android:src="@android:drawable/ic_menu_gallery"`從來源 XML 的`ImageView`。

### <a name="adding-a-listview-container"></a>加入 ListView 容器

既然**list_item**版面配置定義、 下一個步驟是新增`ListView`主要版面配置。 這`ListView`將包含一份**list_item**。 

在 **方案總管**，開啟**Resources/layout/activity_main.axml**。 在 **工具箱**，找出`ListView`小工具並拖曳至**設計介面**。 `ListView`設計工具中將會空白，但是當選取時，說明其框線的藍線。 您可以檢視**文件大綱**以確認**ListView**是否已正確新增：

[![新的 ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

根據預設，`ListView`有`Id`的值`@+id/listView1`。
雖然`listView1`仍為已選取在**文件大綱**，開啟**屬性** 窗格中，按一下**排列依據**，並選取**類別**.
開啟**Main**，找出**識別碼**屬性，其將值變更為`@+id/myListView`:

[![重新命名為 myListView 的識別碼](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

此時，使用者介面是可供使用。

### <a name="running-the-application"></a>執行應用程式

開啟**MainActivity.cs**和其程式碼取代為下列：

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

此程式碼使用自訂`ListView`配接器將載入的色彩資訊，以及剛建立的 UI 中顯示這項資料。 為了讓此範例的簡潔，色彩資訊會是硬式編碼在清單中，但無法修改配接器，從資料來源擷取色彩資訊，或即時計算出來。 如需詳細資訊`ListView`配接器，請參閱[ListView](~/android/user-interface/layouts/list-view/index.md)。

建置並執行應用程式。 下列螢幕擷取畫面是在裝置上執行時，應用程式的顯示方式範例：

[![最終的螢幕擷取畫面](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

### <a name="creating-a-new-project"></a>建立新的專案

第一個步驟是建立新的 Xamarin.Android 專案。

啟動 Visual Studio for Mac，然後按一下 [**新專案...**.選擇**Android 應用程式**範本，然後按一下**下一步]**:

[![空白的 android 應用程式](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

將新的應用程式命名**DesignerWalkthrough**。 底下**目標平台**，選取**最新及最棒**然後按一下**下一步**:

[![名稱應用程式](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

在下一步 對話方塊畫面中，按一下**建立**。

### <a name="adding-a-layout"></a>新增版面配置

下一個步驟是建立**LinearLayout**將保留使用者介面項目。

在 Visual Studio for Mac，請以滑鼠右鍵按一下**資源/配置**中**解決方案**填補，並選取**新增 > 新增檔案...**.在 **新的檔案**對話方塊中，選取**Android > 配置**。 將檔案命名**list_item**然後按一下**新增**:

[![新的版面配置](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

加入此檔案之後，新**list_item**版面配置會顯示在**設計介面**(如果您看到訊息，*此專案包含未成功，編譯的資源可能會影響轉譯*，按一下**建置 > 全部建置**來建置專案):

[![設計工具檢視](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

按一下 **來源**底部的 設計工具來檢視 XML 來源，這個版面配置 索引標籤。 當您按一下 **文件大綱**索引標籤右邊，它會顯示配置目前包含單一**LinearLayout** widget:

[![設計工具 XML](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

下一個步驟是建立色彩的瀏覽器應用程式的使用者介面。

### <a name="creating-the-list-item-user-interface"></a>建立清單項目使用者介面

按一下 [**設計工具**下方的 [返回] 畫面] 索引標籤**設計工具介面**。 在 **工具箱**窗格在右側，向下捲動至**映像與媒體**區段，然後找出`ImageView`:

[![找出 ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

或者，您可以輸入*ImageView*找出在搜尋列`ImageView`:

[![ImageView 搜尋](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

將此`ImageView`拖曳至**設計介面**(這`ImageView`用以顯示色樣色彩瀏覽器應用程式中):

[![在畫布上的 ImageView](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

下一步，拖曳`LinearLayout (Vertical)`小工具，從**工具箱**成**設計介面**。 請注意，藍色外框會指出所加入的界限`LinearLayout`。 **文件大綱**顯示它是子系`LinearLayout`位於以下`imageView1 (ImageView)`:

[![藍色外框](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

當您選取`ImageView`括住，在設計工具中，移動藍色外框`ImageView`。 此外，將選取範圍移至`imageView1 (ImageView)`中**文件大綱**:

[![選取 ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

下一步，拖曳`Text (Large)`小工具，從**工具箱**到新加入`LinearLayout`。 請注意，當您拖曳到滑鼠**設計介面**，它會反白顯示新的 widget 插入的位置。
`Text (Large)`小工具應位於`linearLayout1`如下所示：

[![新增大型文字小工具](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

接下來，新增`Text (Small)`小工具下`Text (Large)`小工具。 在此時**設計介面**應該類似下列螢幕擷取畫面：

[![新增小文字小工具](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

如果兩個`textView`小工具不會深入探討`linearLayout1`，您可以將它們拖曳到`linearLayout1`中**文件大綱**並將其置放，使其顯示在上一個螢幕擷取畫面所示 (縮排下`linearLayout1`).


### <a name="arranging-the-user-interface"></a>排列的使用者介面

下一步是修改 UI 能夠顯示`ImageView`左邊的兩個`TextView`小工具會將右邊的堆疊`ImageView`。

1.  具有`ImageView`選取，按一下**屬性** 索引標籤。

2.  正下方**屬性**索引標籤上，按一下**版面配置**。

3.  向下捲動至**ViewGroup**並變更`Width`設為  `wrap_content`:

[![設定自動換行內容](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

若要變更的另一種方式`Width`設定為 按一下可切換至其寬度設定小工具的右邊的三角形`wrap_content`:

[![若要設定寬度的拖放](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

再次按一下該三角形會傳回`Width`設為  `match_parent`。 接下來，移至**文件大綱**窗格，然後選取根目錄`LinearLayout`:

[![選取根 LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

具有根`LinearLayout`選取，返回**屬性**索引標籤，然後按一下**Widget**。 變更`Orientation`設為  `horizontal` ，如下所示。 在此時**設計介面**應該類似下列螢幕擷取畫面。 請注意，`TextView`小工具都已移至右側`ImageView`:

[![選取 水平方向](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)


### <a name="modifying-the-spacing"></a>修改間距

下一個步驟是修改的邊框距離及邊界設定，以提供更多的空間之間的小工具 UI 中。 選取  `ImageView` ，按一下 **版面配置**索引標籤下**屬性**。 變更`Min Width`來`50dp`，則`Min Height`來`70dp`，而`Padding`來`10dp`。
這適用於周圍每一邊都填補`ImageView`和垂直 elongates 它：

[![設定邊框間距](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

可以藉由輸入值分別設定 頂端、 右側、 底部、 與左側填補設定`Top`， `Right`， `Bottom`，和`Left`填補欄位，分別。 例如，設定`Left`填補值，以`5dp`並`Top`， `Right`，和`Bottom`填補值，以`10dp`。 請注意，`Padding`設定會變更為這些值的逗號分隔清單：

[![自訂的填補設定](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

接下來，調整 位置`LinearLayout`小工具，其中包含兩個`TextView`widget。 在 **文件大綱**，選取`linearLayout1`。 在 [**屬性**窗格中，選取**版面配置**] 索引標籤。向下捲動至**ViewGroup**區段，並設定`Left`， `Top`， `Right`，和`Bottom`邊界來`5dp`， `5dp`， `0dp`，以及`5dp`分別：

[![設定邊界](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>移除預設的映像

因為`ImageView`用來顯示的色彩 （而非映像） 下, 一步是移除範本所加入的預設影像來源。

1.  選取 `ImageView`。

2.  按一下  **Widget**索引標籤下**屬性**。

3.  清除`Src`設定使其空白：

[![清除 ImageView src 設定](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

這會移除`android:src="@android:drawable/ic_menu_gallery"`從來源 XML 的`ImageView`。

### <a name="adding-a-listview-container"></a>加入 ListView 容器

既然**list_item**版面配置定義、 下一個步驟是新增`ListView`主要版面配置。 這`ListView`將包含一份**list_item**。 

在 **方案總管**，開啟**Resources/layout/Main.axml**。
按一下 `Button`小工具 （如果有的話） 並將它刪除。 在 **工具箱**，找出`ListView`小工具並拖曳至**設計介面**。
`ListView`設計工具中將會空白，但是當選取時，說明其框線的藍線。 您可以檢視**文件大綱**以確認**ListView**是否已正確新增：

[![新的 ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

根據預設，`ListView`有`Id`的值`@+id/listView1`。
雖然`listView1`仍為已選取在**文件大綱**，開啟**屬性** 窗格中，按一下**排列依據**，並選取**類別**.
開啟**Main**，找出**識別碼**屬性，其將值變更為`@+id/myListView`:

[![重新命名為 myListView 的識別碼](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

此時，使用者介面是可供使用。

### <a name="running-the-application"></a>執行應用程式

開啟**MainActivity.cs**和其程式碼取代為下列：

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

此程式碼使用自訂`ListView`配接器將載入的色彩資訊，以及剛建立的 UI 中顯示這項資料。 為了讓此範例的簡潔，色彩資訊會是硬式編碼在清單中，但無法修改配接器，從資料來源擷取色彩資訊，或即時計算出來。 如需詳細資訊`ListView`配接器，請參閱[ListView](~/android/user-interface/layouts/list-view/index.md)。

建置並執行應用程式。 下列螢幕擷取畫面是在裝置上執行時，應用程式的顯示方式範例：

[![最終的螢幕擷取畫面](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----


## <a name="summary"></a>總結

本文章會逐步完成使用 Xamarin.Android 的設計工具在 Visual Studio 中建立基本的應用程式的使用者介面的程序。
課程中示範了如何在清單中，建立單一項目介面，它說明如何新增 widget 和它們以視覺化方式來配置。
它也會說明如何將資源指派，然後在這些小工具設定各種屬性。
