---
title: 在 Xamarin 中顯示影像
description: '本文涵蓋如何在 Xamarin iOS 應用程式中包含影像資產，以及如何使用 c # 程式碼或在 iOS 設計工具中將其指派給控制項，以顯示該影像。'
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/24/2018
ms.openlocfilehash: a7bc2b87e267e78d841217b7860e5d54c046b788
ms.sourcegitcommit: d7c09c6cc2f479b8f14910ad2d20ec76800cd9c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91248135"
---
# <a name="displaying-an-image-in-xamarinios"></a>在 Xamarin 中顯示影像

_本文涵蓋如何在 Xamarin iOS 應用程式中包含影像資產，以及如何使用 c # 程式碼或在 iOS 設計工具中將其指派給控制項，以顯示該影像。_

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>在 Xamarin iOS 應用程式中新增和組織影像

新增要在 Xamarin iOS 應用程式中使用的映射時，開發人員會使用 _資產目錄_ 來支援應用程式所需的每個 iOS 裝置和解決方案。

在 iOS 7 中新增的 **資產目錄映射集** 包含支援各種裝置及調整應用程式規模因素所需的所有映射版本或表示。 **映射集會**使用 Json 檔案來指定哪個映射屬於哪個裝置及/或解決方案，而不是依賴映射資產的檔案名。 這是在 ios 9 或更高) 版本的 iOS (中管理及支援映射的慣用方式。

## <a name="adding-images-to-an-asset-catalog-image-set"></a>將影像新增至資產目錄映射集

如上所述， **資產目錄映射集** 包含支援應用程式的各種裝置和縮放比例所需的所有映射版本或表示。 **映射集會**使用 Json 檔案來指定哪個映射屬於哪個裝置及/或解決方案，而不是依賴映射資產的檔案名。

若要建立新的映射集，並在其中新增映射，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 [ **方案總管**中，按兩下檔案 `Assets.xcassets` 以開啟它進行編輯：

    ![方案總管中的 >appicon](displaying-an-image-images/imageset01.png)
2. 以滑鼠右鍵按一下 [ **資產] 清單** ，然後選取 [ **新增映射集**：

    ![新增映射集](displaying-an-image-images/imageset02.png)
3. 選取新的映射集，系統就會顯示編輯器：

    ![影像集編輯器](displaying-an-image-images/imageset03.png)
4. 從這裡，針對每個不同的裝置和所需的解析度拖曳影像。
5. 按兩下 [**資產] 清單**中的新映射集**名稱**來編輯它： ![ 編輯新的映射集名稱](displaying-an-image-images/imageset04.png)

使用 iOS 設計工具中的 **影像集** 時，只要從屬性編輯器的下拉式清單中選取集合的名稱即可：

![從下拉式清單中選取映射集的名稱](displaying-an-image-images/imageset06.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 從 **方案總管**開啟資產目錄，然後在左上角按一下 **加號** 按鈕：

    ![按一下加號按鈕](displaying-an-image-images/asset5.png)

2. 選取 [新增 **映射集** ]，將會顯示新映射集的影像集編輯器。 從這裡，針對每個不同的裝置和所需的解析度拖曳影像。

    ![影像集編輯器](displaying-an-image-images/asset7.png)

### <a name="renaming-an-image-set"></a>重新命名映射集

若要重新命名映射集，請執行下列動作：

1. 在 [ **方案總管**中，按兩下 **資產目錄** 檔案以開啟它進行編輯：

    ![方案總管中的資產目錄](displaying-an-image-images/rename01.png)
2. 選取要重新命名的 **映射集** ：

    ![選取要重新命名的影像集](displaying-an-image-images/rename02.png)
3. 在 [**屬性] 瀏覽器**中，從 [**其他**] 區段下，選取 [ (]，然後選取 [**名稱**]) ：

    ![在 [其他] 區段下選取 [名稱]](displaying-an-image-images/rename03.png)
4. 輸入**映射集**的新**名稱**，並儲存變更。

-----

使用程式碼中的 **映射集** 時，請呼叫類別的方法，依名稱參考它 `FromBundle` `UIImage` 。 例如：

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> 如果指派給映射集的映射未正確顯示，請確認已使用正確的檔案名搭配 `FromBundle` (**映射集** 的方法，而不是父 **資產目錄** 名稱) 。 若為 PNG 影像，則 `.png` 可以省略此延伸模組。 若是其他影像格式，則需要副檔名 (例如。 `PurpleMonkey.jpg`).

### <a name="using-vector-images-in-asset-catalogs"></a>使用資產目錄中的向量影像

從 iOS 8 開始，特殊 **向量** 類別已新增至 **映射集** ，可讓開發人員在磁帶中包含 **PDF** 格式的向量影像，而不是在不同的解析度中包含個別點陣圖檔案。 使用這個方法，為解析度提供單一向量檔 `@1x` (格式為向量 PDF 檔案) ，而且檔案的 `@2x` 和 `@3x` 版本將會在編譯時期產生，並且包含在應用程式套件組合中。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![資產目錄編輯器中的向量影像](displaying-an-image-images/imageset05.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![資產目錄編輯器中的向量影像](displaying-an-image-images/asset8.png)

-----

例如，如果開發人員將檔案包含 `MonkeyIcon.pdf` 為資產目錄的向量，並解析為 150px x 150px，則在編譯時，最終的應用程式套件組合中會包含下列點陣圖資產：

- `MonkeyIcon@1x.png` -150px x 150px 解析度。
- `MonkeyIcon@2x.png` -300px x 300px 解析度。
- `MonkeyIcon@3x.png` -450px x 450px 解析度。

使用資產目錄中的 PDF 向量影像時，應考慮下列事項：

- 這並不是完整的向量支援，因為 PDF 會在編譯時期以點陣圖方式加入點陣圖，並在最終應用程式中寄出點陣圖。
- 在資產目錄中設定映射後，即無法調整映射大小。 如果開發人員嘗試以程式碼或使用自動設定和大小類別來調整影像 (，) 影像將會像其他任何點陣圖一樣失真。
- 資產目錄僅與 iOS 7 及更新版本相容，如果應用程式需要支援 iOS 6 或更低版本，則無法使用資產目錄。

## <a name="working-with-template-images"></a>使用範本映射

根據 iOS 應用程式的設計，有時開發人員可能需要自訂消費者介面內的圖示或影像，以符合色彩配置的變更 (例如，根據使用者喜好設定) 。

若要輕鬆達成此效果，請將影像資產的轉譯 _模式_ 切換為 **範本映射**：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![轉譯模式設定為範本映射](displaying-an-image-images/templateimage01.png)](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![轉譯模式設定為範本](displaying-an-image-images/templateimage01vs.png)](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

從 iOS 設計工具中，將影像資產指派給 UI 控制項，然後設定 **色調** 以將影像著色：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![設定色調以將影像著色](displaying-an-image-images/templateimage03.png)](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![設定色調以將影像著色](displaying-an-image-images/templateimage03vs.png)](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

（選擇性）您可以在程式碼中直接設定影像資產和淡色：

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

若要完全從程式碼使用範本映射，請執行下列動作：

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

因為的 `RenderMode` 屬性 `UIImage` 是唯讀的，所以請使用方法，以所需的轉譯 `ImageWithRenderingMode` 模式設定來建立映射的新實例。

透過列舉有三個可能的設定 `UIImage.RenderMode` `UIImageRenderingMode` ：

- `AlwaysOriginal` -強制將映射轉譯為原始來源影像檔案，而不進行任何變更。
- `AlwaysTemplate` -藉由標示具有指定色彩的圖元，強制將影像轉譯為範本映射 `Tint` 。
- `Automatic` -將影像轉譯為範本，或根據用於的環境轉譯為原始影像。 例如，如果在、或中使用影像， `UIToolBar` 則 `UINavigationBar` `UITabBar` `UISegmentControl` 會將它視為範本。

## <a name="adding-new-assets-collections"></a>加入新的資產集合

使用資產目錄中的影像時，有時可能會需要新的集合，而不是將應用程式的所有影像新增至 `Assets.xcassets` 集合。 例如，在設計隨選資源時。

若要將新的資產目錄新增至專案：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在**方案總管**中的**專案名稱**上按一下滑鼠右鍵，然後選取 [**加入**  >  **新**檔案]。
2. 選取 [ **iOS**  >  **資產目錄**]，輸入集合的**名稱**，然後按一下 [**新增**] 按鈕：

    ![建立新的資產目錄](displaying-an-image-images/asset01.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在方案總管中，以滑鼠右鍵按一下 [ **資產目錄** ] 資料夾，然後選取 [ **新增 > 新資產目錄**]。
2. 為其命名，然後按一下 [ **新增**]：

    ![建立新的資產目錄](displaying-an-image-images/asset1.png)

-----

從這裡開始，可以使用與專案中自動包含的預設集合相同的方式來處理集合 `Assets.xcassets` 。

## <a name="using-images-with-controls"></a>使用具有控制項的影像

除了使用影像來支援應用程式，iOS 也會使用具有應用程式控制類型的影像，例如，索引標籤列、工具列、導覽列、表格和按鈕。 在控制項上顯示影像的簡單方式，就是將 `UIImage` 實例指派給控制項的 `Image` 屬性。

### <a name="frombundle"></a>FromBundle

`FromBundle`方法呼叫是一種同步 (封鎖) 的呼叫，其中包含一些內建的影像載入和管理功能，例如快取支援和自動處理影像檔案以進行各種解析度。

下列範例顯示如何在上設定的影像 `UITabBarItem` `UITabBar` ：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

假設 `MyImage` 是已新增至上述資產目錄的影像資產名稱。 當工作資產目錄映射時，只需在 `FromBundle` 適用于 **PNG** 格式化影像的方法中指定映射的名稱：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

若為任何其他影像格式，請包含名稱的副檔名。 例如：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

如需有關圖示和影像的詳細資訊，請參閱關於 [自訂圖示的 Apple 檔和影像建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)。

## <a name="displaying-an-image-in-a-storyboard"></a>在分鏡腳本中顯示影像

使用資產目錄將映射新增至 Xamarin 專案之後，您可以使用 iOS 設計工具中的來輕鬆地在分鏡腳本中顯示映射 `UIImageView` 。 例如，如果已新增下列映射資產：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![已新增範例映射資產](displaying-an-image-images/display01.png)

執行下列動作，以將其顯示在分鏡腳本中：

1. 按兩下 `Main.storyboard` **方案總管** 中的檔案，將其開啟，以在 iOS 設計工具中進行編輯。
2. 從 [**工具箱**] 選取**影像視圖**：

     ![從 [工具箱] 選取影像視圖](displaying-an-image-images/display02.png)
3. 將影像視圖拖曳至設計介面上，並視需要調整其位置並調整其大小：

    ![Design Surface 上的新影像視圖](displaying-an-image-images/display03.png)
4. 在**屬性瀏覽器**的**Widget**區段中，選取所要顯示的**影像**資產：

    ![選取所需的影像資產以顯示](displaying-an-image-images/display04.png)
5. 在 [ **視圖** **]** 區段中，使用模式來 **控制影像調整** 大小時影像的調整大小。
6. 選取 **影像視圖** 後，再按一次以新增 **條件約束**：

    ![加入條件約束](displaying-an-image-images/display05.png)
7. 將 **影像視圖** 的每個邊緣上的 "T" 成形控點拖曳至畫面的相對應側邊，以將影像「釘選」至側面。 如此一來，當畫面調整大小時， **影像視圖** 將會縮小和放大。
8. 將變更儲存至腳本。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![已新增範例映射資產](displaying-an-image-images/display01vs.png)

執行下列動作，以將其顯示在分鏡腳本中：

1. 按兩下 `Main.storyboard` **方案總管** 中的檔案，將其開啟，以在 iOS 設計工具中進行編輯。
2. 從 [**工具箱**] 選取**影像視圖**：

     ![從 [工具箱] 選取影像視圖](displaying-an-image-images/display02vs.png)
3. 將影像視圖拖曳至設計介面上，並視需要調整其位置並調整其大小：

    ![Design Surface 上的新影像視圖](displaying-an-image-images/display03vs.png)
4. 在**屬性瀏覽器**的**Widget**區段中，選取所要顯示的**影像**資產：

    ![選取所需的影像資產以顯示](displaying-an-image-images/display04vs.png)
5. 在 [ **視圖** **]** 區段中，使用模式來 **控制影像調整** 大小時影像的調整大小。
6. 選取 **影像視圖** 後，再按一次以新增 **條件約束**：

    ![加入條件約束](displaying-an-image-images/display05vs.png)
7. 將 **影像視圖** 的每個邊緣上的 "T" 成形控點拖曳至畫面的相對應側邊，以將影像「釘選」至側面。 如此一來，當畫面調整大小時， **影像視圖** 將會縮小和放大。
8. 將變更儲存至腳本。

-----

## <a name="displaying-an-image-in-code"></a>在程式碼中顯示影像

就像在分鏡腳本中顯示影像一樣，一旦將映射新增至使用資產目錄的 Xamarin. iOS 專案之後，就可以使用 c # 程式碼輕鬆地顯示。

以下列範例為例：

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

此程式碼會建立新的 `UIImageView` ，並為其提供初始大小和位置。 然後，它會從已新增至專案的影像資產載入影像，並將新增至 `UIImageView` 父代 `UIView` 來顯示它。

## <a name="related-links"></a>相關連結

- [使用影像 (範例) ](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [ (Apple) 的影像大小和解析度 ](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
