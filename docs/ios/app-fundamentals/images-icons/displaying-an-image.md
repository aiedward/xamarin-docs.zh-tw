---
title: 在 Xamarin 中顯示影像
description: 本文說明如何在 Xamarin iOS 應用程式中包含影像資產, 並使用C#程式碼或在 iOS 設計工具中將它指派給控制項, 以顯示該影像。
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/24/2018
ms.openlocfilehash: 2c69c995317d5121f203946cf1a4196bce9b4177
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643081"
---
# <a name="displaying-an-image-in-xamarinios"></a>在 Xamarin 中顯示影像

_本文說明如何在 Xamarin iOS 應用程式中包含影像資產, 並使用C#程式碼或在 iOS 設計工具中將它指派給控制項, 以顯示該影像。_

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>在 Xamarin iOS 應用程式中新增和組織影像

新增要在 Xamarin iOS 應用程式中使用的映射時, 開發人員會使用_資產目錄_來支援每個 iOS 裝置, 以及應用程式所需的解決方案。

在 iOS 7 中新增的**資產目錄影像集**包含支援應用程式的各種裝置和調整因素所需之映射的所有版本或標記法。 **映射集**不依賴映射資產檔案名, 而是使用 Json 檔案來指定哪個映射屬於哪些裝置和/或解析度。 這是在 iOS (iOS 9 或更新版本) 中管理和支援影像的慣用方式。

## <a name="adding-images-to-an-asset-catalog-image-set"></a>將影像新增至資產目錄映射集

如上所述,**資產目錄影像集**包含映射的所有版本或標記法, 以支援應用程式的各種裝置和調整因素。 **映射集**不依賴映射資產檔案名, 而是使用 Json 檔案來指定哪個映射屬於哪些裝置和/或解析度。

若要建立新的映射集, 並將影像新增至其中, 請執行下列動作:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在 **方案總管**中, 按兩下`Assets.xcassets`檔案以開啟它進行編輯:

    ![](displaying-an-image-images/imageset01.png "方案總管中的資產. assets.xcassets")
2. 以滑鼠右鍵按一下 [**資產] 清單**, 然後選取 [**新增映射集**]:

    ![](displaying-an-image-images/imageset02.png "加入新的映射集")
3. 選取新的映射集, 就會顯示編輯器:

    ![](displaying-an-image-images/imageset03.png "影像集編輯器")
4. 從這裡, 為每個不同的裝置和所需的解析度拖曳影像。 
5. 按兩下 [**資產] 清單**中的新映射集**名稱**以進行編輯:![](displaying-an-image-images/imageset04.png "編輯新影像集的名稱")

在 iOS 設計工具中使用**映射集**時, 只要從屬性編輯器的下拉式清單中選取集合的名稱即可:

![](displaying-an-image-images/imageset06.png "從下拉式清單中選取映射集的名稱")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 從 **方案總管**開啟 資產目錄, 然後在左上角, 按一下**加號**按鈕:

    ![](displaying-an-image-images/asset5.png "按一下加號按鈕")

2. 選取 [新增**映射集**], 就會顯示新影像集的 [影像集編輯器]。 從這裡, 為每個不同的裝置和所需的解析度拖曳影像。 

    ![](displaying-an-image-images/asset7.png "影像集編輯器")

### <a name="renaming-an-image-set"></a>重新命名映射集

若要重新命名映射集, 請執行下列動作:

1. 在 **方案總管**中, 按兩下**資產目錄**檔案以開啟它進行編輯:

    ![](displaying-an-image-images/rename01.png "方案總管中的資產目錄")
2. 選取要重新命名的**映射集**:

    ![](displaying-an-image-images/rename02.png "選取要重新命名的映射集")
3. 在 [**屬性] 瀏覽器**中, 流覽至底部, 然後選取 [**名稱**] (在 [**其他**] 區段下):

    ![](displaying-an-image-images/rename03.png "選取 [其他] 區段下的 [名稱]")
4. 輸入**映射集**的新**名稱**, 然後儲存變更。

-----

在程式碼中使用**影像集**時, 藉由呼叫`FromBundle` `UIImage`類別的方法, 依名稱參考它。 例如：

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> 如果指派給映射集的映射未正確顯示, 請確定使用`FromBundle`方法 (**映射集**, 而不是父**資產目錄**名稱) 搭配正確的檔案名。 若是 PNG 影像, `.png`可以省略副檔名。 若是其他影像格式, 則需要擴充功能 (例如 `PurpleMonkey.jpg`)。

### <a name="using-vector-images-in-asset-catalogs"></a>在資產目錄中使用向量影像

從 iOS 8 開始, 已新增至**映射集**的特殊**向量**類別, 可讓開發人員在卡帶中包含**PDF**格式的向量影像, 而不會在不同的解析度上包括個別的點陣圖檔案。 使用此方法時, 請提供單一向量檔案以`@1x`供解析 (格式化為向量 PDF 檔案), 而且`@2x`檔案`@3x`的和版本將會在編譯時期產生, 並包含在應用程式的組合中。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "資產目錄編輯器中的向量影像")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "資產目錄編輯器中的向量影像")

-----

例如, 如果開發人員將檔案包含`MonkeyIcon.pdf`為資產目錄的向量, 而其解析為 150px x 150px, 則下列點陣圖資產會在編譯時包含在最終的應用程式套件組合中:

- `MonkeyIcon@1x.png`-150px x 150px 解析。
- `MonkeyIcon@2x.png`-300px x 300px 解析。
- `MonkeyIcon@3x.png`-450px x 450px 解析。

在資產目錄中使用 PDF 向量影像時, 應該考慮下列事項:

- 這不是完整的向量支援, 因為在編譯時期, PDF 會柵格化至點陣圖, 而在最後一個應用程式中傳送的點陣圖。
- 在資產目錄中設定映射後, 就無法調整影像大小。 如果開發人員嘗試調整影像大小 (在程式碼中, 或使用自動設定和大小類別), 影像就會扭曲, 就像任何其他點陣圖一樣。
- 資產目錄僅與 iOS 7 及更新版本相容, 如果應用程式需要支援 iOS 6 或更低版本, 則無法使用資產目錄。

## <a name="working-with-template-images"></a>使用範本映射

根據 iOS 應用程式的設計, 開發人員有時可能需要自訂使用者介面內的圖示或影像, 以符合色彩配置中的變更 (例如, 根據使用者喜好設定)。

若要輕鬆達成此效果, 請將影像資產的轉譯_模式_切換至**範本映射**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "呈現模式設定為範本影像")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "呈現模式設定為範本")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

從 iOS 設計工具中, 將影像資產指派給 UI 控制項, 然後設定**色調**以著色影像:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "設定色調以著色影像")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "設定色調以著色影像")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

(選擇性) 您可以直接在程式碼中設定影像資產和淡色:

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

若要從程式碼完全使用範本映射, 請執行下列動作:

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

因為的`RenderMode`屬性`UIImage` 是`ImageWithRenderingMode`唯讀的, 所以請使用方法, 以所需的呈現模式設定來建立影像的新實例。

透過`UIImageRenderingMode`列舉有三個可能`UIImage.RenderMode`的設定:

* `AlwaysOriginal`-強制影像轉譯為原始來源影像檔案, 而不進行任何變更。
* `AlwaysTemplate`-藉由使用指定`Tint`的色彩上色圖元, 強制將影像轉譯為範本映射。
* `Automatic`-會將影像轉譯為範本, 或根據所用的環境, 以原始形式呈現。 `UIToolBar`例如, 如果在`UINavigationBar` `UITabBar` 、或`UISegmentControl`中使用影像, 則會將它視為範本。

## <a name="adding-new-assets-collections"></a>加入新的資產集合

使用資產目錄中的影像時, 有時可能需要新的集合, 而不是將所有應用程式的映射新增至`Assets.xcassets`集合。 例如, 在設計隨選資源時。

若要將新的資產目錄新增至專案:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 以滑鼠右鍵按一下**方案總管**中的**專案名稱**, 然後選取 **[加入** > **新檔案 ...** ]。
2. 選取 [ **iOS**  > **資產目錄**], 輸入集合的**名稱**, 然後按一下 [**新增**] 按鈕:

    ![](displaying-an-image-images/asset01.png "建立新的資產目錄")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 方案總管中, 以滑鼠右鍵按一下 **資產目錄** 資料夾, 然後選取 **新增 > 新資產目錄**。
2. 為其命名, 然後按一下 [**新增**]:

    ![](displaying-an-image-images/asset1.png "建立新的資產目錄")

-----

從這裡開始, 可以使用與專案中自動包含的預設`Assets.xcassets`集合相同的方式來處理集合。

## <a name="using-images-with-controls"></a>使用影像搭配控制項

除了使用影像來支援應用程式, iOS 也會使用影像與應用程式控制類型, 例如索引標籤列、工具列、導覽列、表格和按鈕。 讓影像出現在控制項上的簡單方式, 就是將`UIImage`實例指派給控制項的`Image`屬性。

### <a name="frombundle"></a>FromBundle

`FromBundle`方法呼叫是一種同步 (封鎖) 呼叫, 其中包含數個內建的影像載入和管理功能, 例如快取支援和自動處理各種解析度的影像檔案。  

下列範例顯示如何`UITabBarItem` `UITabBar`在上設定的影像:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

`MyImage`假設是已新增至上述資產目錄的影像資產名稱。 當工作資產目錄映射時, 只需在適用于**PNG**格式影像的`FromBundle`方法中指定映射集的名稱:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

針對任何其他影像格式, 請包含名稱為的延伸模組。 例如：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

如需有關圖示和影像的詳細資訊, 請參閱關於[自訂圖示和映射建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)的 Apple 檔。

## <a name="displaying-an-image-in-a-storyboards"></a>在分鏡腳本中顯示影像

一旦使用資產目錄將映射新增至 Xamarin iOS 專案, 就可以`UIImageView`在 ios 設計工具中使用, 輕鬆地在分鏡腳本上顯示。 例如, 如果已新增下列影像資產:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](displaying-an-image-images/display01.png "已新增範例影像資產")

執行下列動作, 以將它顯示在分鏡腳本上:

1. 按兩下 **方案總管**中`Main.storyboard`的檔案, 在 iOS 設計工具中開啟檔案進行編輯。
2. 從 [**工具箱**] 中選取**影像視圖**:

     ![](displaying-an-image-images/display02.png "從 [工具箱] 選取影像視圖")
3. 將 [影像視圖] 拖曳至設計介面上, 並視需要調整其位置和大小:

    ![](displaying-an-image-images/display03.png "Design Surface 上的新影像視圖")
4. 在 [**屬性瀏覽器**] 的 [ **Widget** ] 區段中, 選取要顯示的所需**影像**資產:

    ![](displaying-an-image-images/display04.png "選取要顯示的所需影像資產")
5. 在 [ **View** ] 區段中, 使用**模式**來控制影像**視圖**調整大小時影像大小的調整方式。
6. 選取**影像視圖**後, 再次按一下以新增**條件約束**:

    ![](displaying-an-image-images/display05.png "加入條件約束")
7. 將**影像視圖**的每個邊緣上的 "T" 形狀控點拖曳至畫面的對應側邊, 以將影像「釘選」到側邊。 如此一來, 當畫面調整大小時,**影像視圖**就會縮小並成長。
8. 將變更儲存至分鏡腳本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "已新增範例影像資產")

執行下列動作, 以將它顯示在分鏡腳本上:

1. 按兩下 **方案總管**中`Main.storyboard`的檔案, 在 iOS 設計工具中開啟檔案進行編輯。
2. 從 [**工具箱**] 中選取**影像視圖**:

     ![](displaying-an-image-images/display02vs.png "從 [工具箱] 選取影像視圖")
3. 將 [影像視圖] 拖曳至設計介面上, 並視需要調整其位置和大小:

    ![](displaying-an-image-images/display03vs.png "Design Surface 上的新影像視圖")
4. 在 [**屬性瀏覽器**] 的 [ **Widget** ] 區段中, 選取要顯示的所需**影像**資產:

    ![](displaying-an-image-images/display04vs.png "選取要顯示的所需影像資產")
5. 在 [ **View** ] 區段中, 使用**模式**來控制影像**視圖**調整大小時影像大小的調整方式。
6. 選取**影像視圖**後, 再次按一下以新增**條件約束**:

    ![](displaying-an-image-images/display05vs.png "加入條件約束")
7. 將**影像視圖**的每個邊緣上的 "T" 形狀控點拖曳至畫面的對應側邊, 以將影像「釘選」到側邊。 如此一來, 當畫面調整大小時,**影像視圖**就會縮小並成長。
8. 將變更儲存至分鏡腳本。

-----

## <a name="displaying-an-image-in-code"></a>在程式碼中顯示影像

就像在分鏡腳本中顯示影像一樣, 一旦使用資產目錄將影像新增至 Xamarin iOS 專案, 就可以使用C#程式碼輕鬆地顯示映射。

請使用以下範例：

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

這段程式碼會`UIImageView`建立新的, 並為它提供初始大小和位置。 然後, 它會從新增至專案的影像資產載入影像, 並將`UIImageView`其新增至`UIView`父代以顯示它。

## <a name="related-links"></a>相關連結

- [使用影像 (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [影像大小和解析度 (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
