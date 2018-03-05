---
title: "使用 Android 設計工具"
description: "本主題是 Xamarin.Android 設計工具的逐步解說。 它會示範如何建立使用者介面的小型色彩瀏覽器應用程式中;完全在設計工具中建立此使用者介面。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/29/2018
ms.openlocfilehash: b199e4370e93712211db125e427fb61da39cb296
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="using-the-android-designer"></a>使用 Android 設計工具

_本主題是 Xamarin.Android 設計工具的逐步解說。它會示範如何建立使用者介面的小型色彩瀏覽器應用程式中;完全在設計工具中建立此使用者介面。_

<a name="Overview" />

## <a name="overview"></a>總覽

Android 使用者介面可以宣告建立 XML 檔案，或以程式設計方式撰寫的程式碼。 Xamarin.Android 設計工具可讓開發人員建立和修改宣告式配置以視覺化的方式，而不需要手動編輯 XML 檔案的 tedium 處理。 在設計工具也提供即時意見反應，讓開發人員，而不必重新部署應用程式到裝置或模擬器來評估 UI 變更。 這可以大幅加速 Android UI 開發。 在本文中，我們提供的逐步解說，示範如何使用 Xamarin.Android 設計工具以視覺化方式建立的使用者介面。

<a name="Walkthrough" />

## <a name="walkthrough"></a>逐步解說

本逐步解說的目標是使用 Android 設計工具建立使用者介面會顯示色彩，其名稱，以及它們的 RGB 值清單的範例色彩瀏覽器應用程式。 我們會探討如何將 widget 新增至設計介面，以及如何以視覺方式來配置這些小工具。 在這之後，我們將說明如何修改設計介面上以互動方式或使用設計工具的 widget**屬性板**。 最後，我們會看到我們執行應用程式時，我們的設計的外觀。

讓我們開始吧 ！

<a name="Creating_a_New_Project" />

### <a name="creating-a-new-project"></a>建立新專案

第一個步驟是建立新的 Xamarin.Android 專案。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

啟動 Visual Studio，然後按一下**新的專案...**然後選擇  **Visual C\# > Android > 空白應用程式 (Android)**範本：

[ ![Android 的空白應用程式](designer-walkthrough-images/vs/01-android-app-sml.png)](designer-walkthrough-images/vs/01-android-app.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

啟動 Visual Studio 用於 Mac 和按一下**新方案...**.選擇**Android 應用程式**範本，然後按一下**下一步**:

[ ![Android 的空白應用程式](designer-walkthrough-images/xs/01-android-app-sml.png)](designer-walkthrough-images/xs/01-android-app.png)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

將新的應用程式命名**DesignerWalkthrough**按一下**確定**。

[ ![名稱的應用程式](designer-walkthrough-images/vs/02-name-app-sml.png)](designer-walkthrough-images/vs/02-name-app.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

將新的應用程式命名**DesignerWalkthrough**。 在下**目標平台**，選取**最新且最大**按一下**下一步**:

[ ![名稱的應用程式](designer-walkthrough-images/xs/02-designer-walkthrough-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough.png)

在下一步] 對話方塊畫面中，按一下 [**建立**。

-----


<a name="Adding_a_Layout" />

### <a name="adding-a-layout"></a>加入版面配置

讓我們來建立**線性**我們將使用來保留使用者介面項目。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在 Visual Studio 中，以滑鼠右鍵按一下**資源/配置**中**方案總管 中**選取**新增 > 新的項目...**.在**加入新項目**對話方塊中，選取**Android 配置**。 將檔案命名**ListItem.axml**按一下**新增**:

[ ![新的版面配置](designer-walkthrough-images/vs/03-new-layout-sml.png)](designer-walkthrough-images/vs/03-new-layout.png)

新**ListItem**配置會顯示在設計工具：

[ ![設計工具檢視](designer-walkthrough-images/vs/04-designer-view-sml.png)](designer-walkthrough-images/vs/04-designer-view.png)

按一下**來源**底部的設計工具，以檢視 XML 來源，這個版面配置 索引標籤：

[ ![XML 設計工具](designer-walkthrough-images/vs/05-designer-xml-sml.png)](designer-walkthrough-images/vs/05-designer-xml.png)

從**檢視**功能表上，按一下 **其他視窗 > 文件大綱**開啟**文件大綱**。 **文件大綱**顯示版面配置目前包含單一**線性**widget:

[ ![文件大綱](designer-walkthrough-images/vs/06-document-outline-sml.png)](designer-walkthrough-images/vs/06-document-outline.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在 Visual Studio for Mac，以滑鼠右鍵按一下**資源/配置**中**方案**填補，並選取**新增 > 新的檔案...**.在**新檔案**對話方塊中，選取**Android > 配置**。 將檔案命名**ListItem**按一下**新增**:

[ ![新的版面配置](designer-walkthrough-images/xs/03-new-layout-sml.png)](designer-walkthrough-images/xs/03-new-layout.png)

新**ListItem**配置會顯示在設計工具：

[ ![設計工具檢視](designer-walkthrough-images/xs/04-designer-view-sml.png)](designer-walkthrough-images/xs/04-designer-view.png)

按一下**來源**底部的設計工具，以檢視 XML 來源，這個版面配置 索引標籤。 當您按一下**文件大綱** 索引標籤右邊，它會顯示配置目前包含單一**線性**widget:

[ ![XML 設計工具](designer-walkthrough-images/xs/05-designer-xml-sml.png)](designer-walkthrough-images/xs/05-designer-xml.png)

-----


<a name="Creating_the_List_Item_User_Interface" />

### <a name="creating-the-list-item-user-interface"></a>建立清單項目使用者介面

接下來，我們將建立色彩瀏覽器應用程式的使用者介面。
按一下**設計師**索引標籤，以返回設計介面。
在**工具箱**，向下捲動至**映像 （& s) 媒體**區段，並瀏覽每個項目，直到您找出*ImageView*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![找出 ImageView](designer-walkthrough-images/vs/07-locate-imageview-sml.png)](designer-walkthrough-images/vs/07-locate-imageview.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![找出 ImageView](designer-walkthrough-images/xs/06-locate-imageview-sml.png)](designer-walkthrough-images/xs/06-locate-imageview.png)

-----

或者，您可以輸入*ImageView*找出在搜尋列`ImageView`widget:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![ImageView 搜尋](designer-walkthrough-images/vs/08-imageview-search-sml.png)](designer-walkthrough-images/vs/08-imageview-search.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![ImageView 搜尋](designer-walkthrough-images/xs/07-imageview-search-sml.png)](designer-walkthrough-images/xs/07-imageview-search.png)

-----

將此`ImageView`拖曳至設計介面：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![在畫布上 ImageView](designer-walkthrough-images/vs/09-imageview-on-canvas-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![在畫布上 ImageView](designer-walkthrough-images/xs/08-imageview-on-canvas-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas.png)

-----

這`ImageView`將用於在我們的色彩瀏覽器應用程式中顯示色彩樣本。

下一步，拖曳`LinearLayout (Vertical)`從 widget**工具箱**進入設計工具。 請注意，藍色的外框表示加入的界限`LinearLayout`，而**文件大綱**它位於正下方顯示`imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![藍色大綱](designer-walkthrough-images/vs/10-blue-outline-sml.png)](designer-walkthrough-images/vs/10-blue-outline.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![藍色大綱](designer-walkthrough-images/xs/10-blue-outline-sml.png)](designer-walkthrough-images/xs/10-blue-outline.png)

-----

當您選取`ImageView`在設計師中，藍色的外框移至圍繞`ImageView`; 在**文件大綱**，選取範圍移至`imageView1 (ImageView)`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![選取 ImageView](designer-walkthrough-images/vs/11-select-imageview-sml.png)](designer-walkthrough-images/vs/11-select-imageview.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![選取 ImageView](designer-walkthrough-images/xs/11-select-imageview-sml.png)](designer-walkthrough-images/xs/11-select-imageview.png)

-----

下一步，拖曳`Text (Large)`從 widget**工具箱**到新加入`LinearLayout`。 請注意，在設計工具會使用綠色反白顯示，表示要插入的新小工具的位置：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![綠色反白顯示](designer-walkthrough-images/vs/12-green-highlight-sml.png)](designer-walkthrough-images/vs/12-green-highlight.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![綠色反白顯示](designer-walkthrough-images/xs/12-green-highlight-sml.png)](designer-walkthrough-images/xs/12-green-highlight.png)

-----

接下來，加入`Text (Small)`下方的小工具`Text (Large)`widget:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![將小型文字 widget](designer-walkthrough-images/vs/13-add-small-text-sml.png)](designer-walkthrough-images/vs/13-add-small-text.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![將小型文字 widget](designer-walkthrough-images/xs/13-add-small-text-sml.png)](designer-walkthrough-images/xs/13-add-small-text.png)

-----

此時，設計工具應該類似下列的螢幕擷取畫面：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![設計工具的版面配置](designer-walkthrough-images/vs/14-raw-layout-sml.png)](designer-walkthrough-images/vs/14-raw-layout.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![設計工具的版面配置](designer-walkthrough-images/xs/14-raw-layout-sml.png)](designer-walkthrough-images/xs/14-raw-layout.png)

-----

如果兩個`textView`widget 不是內部`linearLayout1`，您可以將它們拖曳到`linearLayout1`中**文件大綱**和位置，它們會出現在上一個螢幕擷取畫面所示 (底下縮排`linearLayout1`)。


<a name="Arranging_The_User_Interface" />

### <a name="arranging-the-user-interface"></a>排列的使用者介面

我們要修改 UI，以顯示`ImageView`左邊的兩個`TextView`右邊的堆疊 widget `ImageView`。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  選取 `ImageView`。

2.  在**屬性] 視窗**，按一下 [**分類**排序圖示，然後向下的捲動至**配置-檢視**> 一節。

3.  變更`layout_width`設`wrap_content`:

![設定自動換行內容](designer-walkthrough-images/vs/15-wrap-content.png "組換行內容")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  與`ImageView`選取，按一下**屬性** 索引標籤。

2.  正下方**屬性**索引標籤上，按一下 **配置**。

3.  向下捲動至**檢視**並變更`Width`設`wrap_content`:

[ ![設定自動換行內容](designer-walkthrough-images/xs/15-wrap-content-sml.png)](designer-walkthrough-images/xs/15-wrap-content.png)

-----

若要變更的另一種方式`Width`設定為 按一下可切換至其寬度設定 widget 的右邊的三角形`wrap_content`:

[ ![拖曳來設定寬度](designer-walkthrough-images/xs/16-width-arrow-sml.png)](designer-walkthrough-images/xs/16-width-arrow.png)

再次按一下該三角形傳回`Width`設`match_parent`。

接下來，切換至**文件大綱**並選取根目錄`LinearLayout`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![選取根線性](designer-walkthrough-images/vs/16-root-linearlayout-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![選取根線性](designer-walkthrough-images/xs/17-root-linearlayout-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout.png)

-----
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

與根`LinearLayout`選取，返回**屬性**視窗中，按一下 **字母順序**排序圖示，然後捲動，直到您找到`orientation`。 變更`orientation`設`horizontal`:

![選取 水平方向](designer-walkthrough-images/vs/17-horizontal-orientation.png "選取水平方向")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

與根`LinearLayout`選取，返回**屬性**索引標籤上，按一下  **Widget**。 變更`Orientation`設`horizontal`:

[ ![選取 水平方向](designer-walkthrough-images/xs/18-horizontal-orientation-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation.png)

-----

此時，設計工具應該類似下列的螢幕擷取畫面：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![設計工具的版面配置](designer-walkthrough-images/vs/18-designer-layout-sml.png)](designer-walkthrough-images/vs/18-designer-layout.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![設計工具的版面配置](designer-walkthrough-images/xs/19-designer-layout-sml.png)](designer-walkthrough-images/xs/19-designer-layout.png)

-----

<a name="Modifying_the_Spacing" />

### <a name="modifying-the-spacing"></a>修改間距

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

接下來，我們將修改在 UI，以提供更多空間 widget 之間的填補和邊界設定。 選取`ImageView`，按一下 **分類**中的搜尋圖示**屬性**視窗，並向下的捲動至**配置**> 一節。 變更`Min Height`至`70dp`、`Min Width`至`50dp`，而`padding`至`10dp`。 這適用於周圍每一邊的填補`ImageView`和垂直 elongates 它：

[ ![設定填補](designer-walkthrough-images/vs/19-padding-widths-sml.png)](designer-walkthrough-images/vs/19-padding-widths.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

接下來，我們將修改在 UI，以提供更多空間 widget 之間的填補和邊界設定。 選取`ImageView`按一下**配置**索引標籤底下**屬性**。 變更`Padding`至`10dp`、`Min Width`至`50dp`，而`Min Height`至`70dp`。 這適用於周圍每一邊的填補`ImageView`和垂直 elongates 它：

[ ![設定填補](designer-walkthrough-images/xs/20-padding-widths-sml.png)](designer-walkthrough-images/xs/20-padding-widths.png)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

底部、 左邊，以滑鼠右鍵，並可以獨立設定填補設定的頂端，輸入值`paddingBottom`， `paddingLeft`， `paddingRight`，和`paddingTop`分別欄位。
例如，設定`paddingLeft`欄位設為`5dp`和`paddingBottom`， `paddingRight`，和`paddingTop`欄位`10dp`:

[ ![自訂的填補設定](designer-walkthrough-images/vs/20-custom-padding-sml.png)](designer-walkthrough-images/vs/20-custom-padding.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

可藉由輸入值入獨立設定 上方、 右側、 下方，與左填補設定`Top`， `Right`， `Bottom`，和`Left`分別填補欄位。 例如，設定`Left`填補值`5dp`和`Top`， `Right`，和`Bottom`填補值`10dp`。 請注意，`Padding`設定變更為以逗號分隔的清單，這些值：

[ ![自訂的填補設定](designer-walkthrough-images/xs/21-custom-padding-sml.png)](designer-walkthrough-images/xs/21-custom-padding.png)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

接下來，調整的位置`LinearLayout`widget，其中包含兩個`TextView`widget。 在**文件大綱**，選取`linearLayout1`。 在**屬性** 視窗中，捲動到**配置-檢視**> 一節。 設定`layout_marginBottom`， `layout_marginLeft`， `layout_marginRight`，和`layout_marginTop`至`5dp`， `5dp`， `0dp`，和`5dp`分別：

[ ![設定邊界](designer-walkthrough-images/vs/21-margins-sml.png)](designer-walkthrough-images/vs/21-margins.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

接下來，調整的位置`LinearLayout`widget，其中包含兩個`TextView`widget。 在**文件大綱**，選取`linearLayout1`。 在**屬性**窗格中，選取**配置** 索引標籤。向下捲動至**檢視**區段，並設定`Left`， `Top`， `Right`，和`Bottom`邊界來`5dp`， `5dp`， `0dp`，和`5dp`分別：

[ ![設定邊界](designer-walkthrough-images/xs/22-margins-sml.png)](designer-walkthrough-images/xs/22-margins.png)

-----


<a name="Removing_the_Default_Image" />

### <a name="removing-the-default-image"></a>移除預設的映像

因為我們會使用`ImageView`顯示色彩 （而不是影像），請讓我們先移除範本加入的預設影像來源。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  選取 `ImageView`。

2.  在**屬性**，尋找`src`欄位。

3.  清除`src`設定，好讓它是空白：

![清除 ImageView src 設定](designer-walkthrough-images/vs/22-clear-img-src.png "清除 ImageView src 設定")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  選取 `ImageView`。

2.  按一下**Widget**索引標籤底下**屬性**。

3.  清除`Src`設定，好讓它是空白：

[ ![清除 ImageView src 設定](designer-walkthrough-images/xs/23-clear-src-sml.png)](designer-walkthrough-images/xs/23-clear-src.png)

-----

<a name="Adding_a_ListView" />

### <a name="adding-a-listview-container"></a>加入清單檢視容器

既然**ListItem**定義版面配置，我們將新增`ListView`Main 版面配置。 這`ListView`將包含一份**ListItems**。
在**工具箱**，找出`ListView`widget 並將它拖曳到設計介面上。 `ListView`會空白，藍線概述框線時選取它，但是設計工具中。 您可以檢視**文件大綱**可讓您確認**ListView**是否已正確新增：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![新的 ListView](designer-walkthrough-images/vs/23-new-listview.png "新 ListView")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![新的 ListView](designer-walkthrough-images/xs/24-new-listview-sml.png)](designer-walkthrough-images/xs/24-new-listview.png)

-----

根據預設，`ListView`指定`Id`值`@+id/listView1`。
開啟**Widget**索引標籤底下**屬性**並變更`Id`至`@+id/myListView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![將識別碼重新命名為 myListView](designer-walkthrough-images/vs/24-change-id.png "myListView 重新命名識別碼")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[ ![重新命名為 myListView 的識別碼](designer-walkthrough-images/xs/25-change-id-sml.png)](designer-walkthrough-images/xs/25-change-id.png)

-----

此時，我們的使用者介面是可供使用。


<a name="Running_the_Application" />

### <a name="running-the-application"></a>執行應用程式


開啟**Weatherapp**和其程式碼替換成下列：

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity (Label = "DesignerWalkthrough", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem> ();
        ListView listView;

        protected override void OnCreate (Bundle savedInstanceState)
        {
            base.OnCreate (savedInstanceState);

            SetContentView (Resource.Layout.Main);
            listView = FindViewById<ListView> (Resource.Id.myListView);

            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.DarkRed,
                                               ColorName = "Dark Red", Code = "8B0000" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.SlateBlue,
                                               ColorName = "Slate Blue", Code = "6A5ACD" });
            colorItems.Add (new ColorItem () { Color = Android.Graphics.Color.ForestGreen,
                                               ColorName = "Forest Green", Code = "228B22" });

            listView.Adapter = new ColorAdapter (this, colorItems);
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
        public override View GetView (int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.ListItem, null);
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

此程式碼使用自訂`ListView`載入色彩資訊和我們剛建立的 UI 中顯示這項資料的配接器。 為了讓這個簡短的範例，色彩資訊硬式編碼在清單中，但無法修改配接器，才能從資料來源擷取色彩資訊或即時計算出來。 如需有關`ListView`配接器，請參閱[Listview 和配接器](~/android/user-interface/layouts/list-view/index.md)。

建置並執行應用程式。 下列螢幕擷取畫面是在裝置上執行時，應用程式的顯示方式的範例：

[ ![最終螢幕擷取畫面](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png)


<a name="Summary" />

## <a name="summary"></a>總結

在本文中，我們逐步解說如何使用適用於 Mac 的 Visual Studio 中的 Xamarin.Android 設計工具來建立使用者介面。 接下來，我們會示範如何在清單中建立單一項目介面。
過程中，我們討論了如何新增 widget，以及如何進行配置以視覺化的方式，以及如何指派資源，以及如何設定這些小工具上的各種屬性。 結論，我們將說明我們在設計工具所建立的介面中的範例應用程式的執行方式。
