---
title: 在 Xamarin.iOS 中顯示影像
description: 本文章涵蓋包括在 Xamarin.iOS 應用程式和使用 C# 程式碼，或將它指派給 iOS 設計工具中的控制項，顯示該映像中的影像資產。
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/24/2018
ms.openlocfilehash: 6d584c45288d7e7341a4423c32a6966c6c7f21f3
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827908"
---
# <a name="displaying-an-image-in-xamarinios"></a>在 Xamarin.iOS 中顯示影像

_本文章涵蓋包括在 Xamarin.iOS 應用程式和使用 C# 程式碼，或將它指派給 iOS 設計工具中的控制項，顯示該映像中的影像資產。_

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>新增和組織中的 Xamarin.iOS 應用程式的映像

開發人員加入時使用的映像的 Xamarin.iOS 應用程式中，將會使用_資產目錄_以支援每個 iOS 裝置和應用程式所需的解析度。

在 iOS 7 中新增**資產目錄映像集**包含的所有版本或映像的支援各種裝置和縮放因數的應用程式所需的表示法。 而不是依賴映像的資產檔案名稱，**映像集**使用 Json 檔案，以指定哪一個映像屬於哪些裝置和/或解析。 這是慣用的方式來管理和支援 iOS （從 iOS 9 或更新版本) 中的映像。

## <a name="adding-images-to-an-asset-catalog-image-set"></a>將映像新增至資產目錄映像集

如上面所述**資產目錄映像集**包含的所有版本或映像的支援各種裝置和縮放因數的應用程式所需的表示法。 而不是依賴映像的資產檔案名稱，**映像集**使用 Json 檔案，以指定哪一個映像屬於哪些裝置和/或解析。

若要建立新的映像集，並將影像加入至它，執行下列作業：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在 [**方案總管] 中**，連按兩下`Assets.xcassets`檔案，以開啟它進行編輯：

    ![](displaying-an-image-images/imageset01.png "在 [方案總管] 中 Assets.xcassets")
2. 以滑鼠右鍵按一下**資產清單**，然後選取**集中新的映像**:

    ![](displaying-an-image-images/imageset02.png "加入新的映像集")
3. 選取新的映像集，並將顯示編輯器：

    ![](displaying-an-image-images/imageset03.png "映像集編輯器")
4. 從這裡開始，拖曳影像中的每個不同的裝置，以及所需的解析度。 
5. 按兩下新的映像集合**名稱**中**資產清單**對其進行編輯：![](displaying-an-image-images/imageset04.png "編輯新的映像集的名稱")

使用時**映像設定**在 iOS 設計工具中，只是集的名稱從下拉式清單選取 [屬性編輯器] 中：

![](displaying-an-image-images/imageset06.png "從下拉式清單中選取映像集的名稱")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 開啟資產目錄，從**方案總管**，然後在左上角，按一下 **加上**按鈕：

    ![](displaying-an-image-images/asset5.png "按一下加號按鈕")

2. 選取 **新增影像集**和映像設定編輯器將會顯示新的映像集合。 從這裡開始，拖曳影像中的每個不同的裝置，以及所需的解析度。 

    ![](displaying-an-image-images/asset7.png "影像編輯器 」 設定。")

### <a name="renaming-an-image-set"></a>重新命名的影像集

若要重新命名映像設定，請執行下列作業：

1. 在 [**方案總管] 中**，按兩下**資產目錄**檔案，以開啟它進行編輯：

    ![](displaying-an-image-images/rename01.png "在 [方案總管] 中的資產目錄")
2. 選取 **映像設定**重新命名：

    ![](displaying-an-image-images/rename02.png "選取 重新命名所設定的影像")
3. 在 **屬性總管**，捲動到底部，然後選取**名稱**(在**其他**一節):

    ![](displaying-an-image-images/rename03.png "選取 [其他] 區段下的名稱")
4. 輸入新**名稱**for**映像設定**並儲存變更。

-----

使用時**集中映像**程式碼中，依名稱進行參考藉由呼叫`FromBundle`方法`UIImage`類別。 例如：

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> 如果指派給映像設定的映像都未顯示正確，請確定正確的檔名，與搭配使用`FromBundle`方法 (**影像集**並不是父代**資產目錄**名稱)。 PNG 影像`.png`延伸模組，則可以省略。 對於其他的影像格式，延伸模組是必要的 （例如。 `PurpleMonkey.jpg`)。

### <a name="using-vector-images-in-asset-catalogs"></a>在 資產目錄中使用向量影像

從 iOS 8 中，特殊開始**向量**類別也已經加入至**映像集**，可讓開發人員包含**PDF**格式改為包括紙匣中的向量映像以不同的解析度的個別點陣圖檔案。 使用此方法中，提供單一的向量檔案以供`@1x`解析度 （格式為向量的 PDF 檔案） 和`@2x`和`@3x`會產生編譯時期和應用程式的組合中所包含的檔案版本。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "在 資產目錄編輯器的向量影像")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "在 資產目錄編輯器的向量影像")

-----

例如，如果開發人員包含`MonkeyIcon.pdf`150px x 150px，下列點陣圖編譯時，會在最終的應用程式套件組合中包含資產的解析度的媒介的資產目錄的檔案：

- `MonkeyIcon@1x.png` -150px x 150px 解析。
- `MonkeyIcon@2x.png` -300px x 300px 解析。
- `MonkeyIcon@3x.png` -450px x 450px 解析。

下列應該列入考量時使用 PDF 向量影像資產目錄中：

- 這不是完整的向量支援 PDF 將會在編譯時期和最終的應用程式中隨附的點陣圖的點陣圖的點陣化。
- 設定資產目錄中之後，就無法調整影像的大小。 如果開發人員嘗試調整大小的映像 （無論是在程式碼，或使用自動版面配置和大小類別） 將會扭曲映像，就像任何其他的點陣圖。
- 資產目錄僅相容於 iOS 7 和更新版本，如果應用程式需要支援 iOS 6 或更低，所以不能使用資產目錄。

## <a name="working-with-template-images"></a>使用範本映像

IOS 應用程式的設計為基礎，可能的有時候開發人員需要自訂圖示或影像內的使用者介面，以比對色彩配置 （例如，根據使用者喜好設定） 中的變更。

若要輕鬆地達成此效果，請切換_呈現模式_的映像資產**範本映像**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "轉譯模式設定為範本映像")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "轉譯模式設定為範本")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

IOS 設計工具中，將影像資產指派至 UI 控制項，然後設定**濃淡**以顏色標示影像：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "設定以顏色標示影像濃淡")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "設定以顏色標示影像濃淡")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

（選擇性） 的影像資產和濃淡可以直接在程式碼會設定：

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

若要使用完全從程式碼的範本映像，執行下列作業：

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

因為`RenderMode`的屬性`UIImage`是唯讀的請使用`ImageWithRenderingMode`方法用來建立包含所需的轉譯模式設定的映像的新執行個體。

有三個可能的設定`UIImage.RenderMode`透過`UIImageRenderingMode`列舉：

* `AlwaysOriginal` -強制要轉譯為原始的來源映像檔，而不需要任何變更的映像。
* `AlwaysTemplate` -強制藉由使用指定的像素的色彩標示，做為範本映像轉譯之影像`Tint`色彩。
* `Automatic` -其中呈現影像，因為範本或原始根據環境中使用。 例如，如果映像會用於`UIToolBar`， `UINavigationBar`，`UITabBar`或`UISegmentControl`它將會被視為範本。

## <a name="adding-new-assets-collections"></a>加入新的資產集合

使用資產目錄中的映像時可能會當新的集合會的是必要項目，而不是新增的所有應用程式的映像至`Assets.xcassets`集合。 例如，在設計上隨選資源時。

將新的資產目錄加入至專案：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 以滑鼠右鍵按一下**專案名稱**中**方案總管**，然後選取**新增** > **新的檔案...**
2. 選取  **iOS** > **資產目錄**，輸入**名稱**集合，然後按一下 **新增**按鈕：

    ![](displaying-an-image-images/asset01.png "建立新的資產目錄")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 [方案總管] 中，以滑鼠右鍵按一下**資產目錄**資料夾，然後選取**新增 > 新的資產目錄**。
2. 給它的名稱，然後按一下**新增**:

    ![](displaying-an-image-images/asset1.png "建立新的資產目錄")

-----

從這裡開始，集合可以從事為預設值相同的方式`Assets.xcassets`自動包含在專案中的集合。

## <a name="using-images-with-controls"></a>使用映像搭配控制項

除了使用映像，以支援應用程式，iOS 也會使用映像應用程式控制項類型，例如索引標籤列、 工具列、 導覽列中，資料表和按鈕。 將影像出現在控制項上的簡單方式是將指派`UIImage`控制項的執行個體`Image`屬性。

### <a name="frombundle"></a>FromBundle

`FromBundle`方法呼叫是同步的 （封鎖） 呼叫，有許多的內建功能，例如快取支援及各種不同的解析度的影像檔的自動處理映像載入和管理功能。  

下列範例示範如何設定的映像`UITabBarItem`上`UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

假設`MyImage`影像資產新增到上述的資產目錄的名稱。 當使用資產目錄映像，只需指定名稱中設定的映像`FromBundle`方法**PNG**格式化映像：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

任何其他映像格式的名稱包含副檔名。 例如:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

如需有關圖示和影像的詳細資訊，請參閱 Apple 文件上[自訂圖示和影像建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)。

## <a name="displaying-an-image-in-a-storyboards"></a>分鏡腳本中顯示影像

映像新增至 Xamarin.iOS 專案使用資產目錄之後, 就可以輕鬆地顯示在分鏡腳本，使用`UIImageView`iOS 設計工具中。 例如，如果已加入下列的影像資產：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](displaying-an-image-images/display01.png "已新增範例映像資產")

執行下列命令以顯示在分鏡腳本：

1. 按兩下`Main.storyboard`檔案中**方案總管 中**開啟以在 iOS 設計工具中編輯。
2. 選取 **映像檢視**從**工具箱**:

     ![](displaying-an-image-images/display02.png "從 [工具箱] 中選取映像檢視")
3. 影像檢視拖曳至設計介面和位置，並將其所需的大小：

    ![](displaying-an-image-images/display03.png "在設計介面上的新映像檢視")
4. 在**Widget**一節**屬性總管**選取想要**映像**要顯示的資產：

    ![](displaying-an-image-images/display04.png "選取要顯示想要的映像資產")
5. 在**檢視**區段中，使用**模式**來控制如何將映像將會調整大小的時機**影像檢視**重新調整大小。
6. 具有**映像檢視**選取，再按一下按鈕可新增**條件約束**:

    ![](displaying-an-image-images/display05.png "新增條件約束")
7. 拖曳"T"形狀控點，每個邊緣**映像檢視**到螢幕上以 「 釘選 」 側邊的映像的對應端。 如此一來，**映像檢視**會壓縮，並隨著螢幕重新調整大小。
8. 將變更儲存到分鏡腳本。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "已新增範例映像資產")

執行下列命令以顯示在分鏡腳本：

1. 按兩下`Main.storyboard`檔案中**方案總管 中**開啟以在 iOS 設計工具中編輯。
2. 選取 **映像檢視**從**工具箱**:

     ![](displaying-an-image-images/display02vs.png "從 [工具箱] 中選取映像檢視")
3. 影像檢視拖曳至設計介面和位置，並將其所需的大小：

    ![](displaying-an-image-images/display03vs.png "在設計介面上的新映像檢視")
4. 在**Widget**一節**屬性總管**選取想要**映像**要顯示的資產：

    ![](displaying-an-image-images/display04vs.png "選取要顯示想要的映像資產")
5. 在**檢視**區段中，使用**模式**來控制如何將映像將會調整大小的時機**影像檢視**重新調整大小。
6. 具有**映像檢視**選取，再按一下按鈕可新增**條件約束**:

    ![](displaying-an-image-images/display05vs.png "新增條件約束")
7. 拖曳"T"形狀控點，每個邊緣**映像檢視**到螢幕上以 「 釘選 」 側邊的映像的對應端。 如此一來，**映像檢視**會壓縮，並隨著螢幕重新調整大小。
8. 將變更儲存到分鏡腳本。

-----

## <a name="displaying-an-image-in-code"></a>在程式碼中顯示影像

就像在分鏡腳本中顯示影像，影像新增至 Xamarin.iOS 專案使用資產目錄之後, 它可以輕鬆地顯示使用 C# 程式碼。

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

此程式碼會建立新`UIImageView`並為其提供的初始大小和位置。 然後再載入映像從映像資產加入至專案，並將`UIImageView`父系`UIView`來顯示它。

## <a name="related-links"></a>相關連結

- [使用映像 （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [映像大小和解析度 (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
