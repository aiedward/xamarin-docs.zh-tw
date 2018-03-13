---
title: "顯示影像"
description: "本文涵蓋包括 Xamarin.iOS 應用程式和使用 C# 程式碼，或將其指派給 iOS 設計工具中的控制項，顯示該映像中的影像資產。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 1ca537ef47f48886483e99bb33b0d524d16593f8
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="displaying-an-image"></a>顯示影像

_本文涵蓋包括 Xamarin.iOS 應用程式和使用 C# 程式碼，或將其指派給 iOS 設計工具中的控制項，顯示該映像中的影像資產。_

本文會詳細說明下列主題：

- [加入和組織 Xamarin.iOS 應用程式中的映像](#adding-assets)-涵蓋的影像資產和方式，可以是包含的組織及管理 Xamarin.iOS 專案內。
- [將影像加入至資產目錄](#asset-catalogs)-管理資產目錄的映像。
    - [使用向量影像資產類別目錄中](#Using-Vector-Images-in-Asset-Catalogs)-提供所有映像大小的單一向量。
- [使用範本映像](#Working-with-Template-Images)-藉由設定影像資產是範本映像，開發人員可以輕鬆地以色彩標示它以符合應用程式的 UI 佈景主題變更藉由設定 映像的`Tint`屬性。
- [控制項搭配使用影像](#controls)-涵蓋使用 Xamarin.iOS 專案中使用 UI 控制項，例如包含影像資產`UIButton`和`UIImageView`及如何處理與在 C# 中使用的映像`UIImage`物件的[FromBundle](#frombundle)方法。
- [顯示映像中的分鏡腳本](#Displaying-an-Image-in-a-Storyboards)-提供一個範例顯示使用分鏡腳本映像。
- [在程式碼中顯示影像](#Displaying-an-Image-in-Code)-提供一個範例顯示使用 C# 程式碼映像。

<a name="adding-assets" />

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>加入和組織 Xamarin.iOS 應用程式中的映像

加入時使用的映像 Xamarin.iOS 應用程式中，開發人員會使用_資產目錄_以支援每個 iOS 裝置和應用程式所需的解析度。

新增 iOS 7**資產目錄映像集**包含所有版本或映像的支援各種裝置和規模因素的應用程式所需的表示法。 而不是依賴影像資產檔案名稱 (請參閱[解析度獨立影像和影像術語表](~/ios/app-fundamentals/images-icons/displaying-an-image.md))，**映像集**指定哪一個映像屬於哪些裝置和/或解析使用 Json 檔案. 這是慣用的方式來管理和支援 iOS (iOS 9 或更新版本) 中的映像。

<a name="asset-catalogs" />

## <a name="adding-images-to-an-asset-catalog-image-set"></a>設定加入至資產目錄映像的影像

如上所述，**資產目錄映像集**包含所有版本或映像的支援各種裝置和規模因素的應用程式所需的表示法。 而不是依賴影像資產檔案名稱，**映像集**指定哪一個映像屬於哪些裝置和/或解析使用 Json 檔案。

若要建立新的映像集，並將影像加入至它，執行下列作業：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在**方案總管 中**，連按兩下`Assets.xcassets`檔案，以開啟它進行編輯：

    ![](displaying-an-image-images/imageset01.png "在 [方案總管] 中 Assets.xcassets")
2. 以滑鼠右鍵按一下**資產清單**選取**新映像設定**:

    ![](displaying-an-image-images/imageset02.png "加入新的映像集")
3. 選取新的映像集，編輯器會隨即出現：

    ![](displaying-an-image-images/imageset03.png "設定影像編輯器")
4. 從這裡拖曳影像中的每個不同的裝置和與所需的解決方案。 (注意： 會解決方法比對中指定的解決方式[映像大小和檔案名稱](~/ios/app-fundamentals/images-icons/displaying-an-image.md)文件。)
5. 按兩下新的映像集**名稱**中**資產清單**對其進行編輯： ![ ](displaying-an-image-images/imageset04.png "編輯新的映像集名稱")

當使用**映像設定**在 iOS 設計工具中，只需選取集的名稱 」 從下拉式清單在屬性編輯器中：

![](displaying-an-image-images/imageset06.png "從下拉式清單中選取映像集名稱")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 開啟從資產目錄**方案總管] 中**，然後在左上角，按一下 [**加上**按鈕：

    ![](displaying-an-image-images/asset5.png "按一下加號按鈕")
2. 選取**新增映像設定**和映像設定編輯器 將會顯示新的映像集。 從這裡拖曳影像中的每個不同的裝置和與所需的解決方案。 (注意： 會解決方法比對中指定的解決方式[映像大小和檔案名稱](~/ios/app-fundamentals/images-icons/displaying-an-image.md)文件):

    ![](displaying-an-image-images/asset7.png "影像編輯器設定。")

### <a name="renaming-an-image-set"></a>重新命名映像組

若要重新命名設定映像，執行下列作業：

1. 在**方案總管 中**，連按兩下**資產目錄**檔案，以開啟它進行編輯：

    ![](displaying-an-image-images/rename01.png "在 [方案總管] 中的 「 資產類別")
2. 選取**映像設定**重新命名：

    ![](displaying-an-image-images/rename02.png "選取要重新命名集映像")
3. 在**屬性總管**，捲動到底部，然後選取**名稱**(下**其他**> 一節):

    ![](displaying-an-image-images/rename03.png "選取 [其他] 區段下的名稱")
4. 輸入新**名稱**如**映像設定**並儲存變更。

-----

當使用**映像設定**程式碼中，它來按照名稱參考呼叫`FromBundle`方法`UIImage`類別。 例如：

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> **請注意**： 如果指派給設定映像的映像都未顯示正確，請確定正確的檔名，與搭配使用`FromBundle`方法 (**映像設定**並不是父代**資產目錄**名稱)。 為 PNG 影像`.png`副檔名可以省略。 其他的影像格式，副檔名是必要的 （例如。 `PurpleMonkey.jpg`)。

<a name="Using-Vector-Images-in-Asset-Catalogs" />

### <a name="using-vector-images-in-asset-catalogs"></a>使用向量影像資產類別目錄中

從 iOS 8 特殊開始**向量**類別也已經加入至**映像集**，可讓開發人員加入**PDF**格式化向量改為包括匣中的映像個別的點陣圖檔案，在不同的解析度。 使用這個方法提供的單一向量檔案`@1x`解析度 （向量 PDF 檔案格式） 和`@2x`和`@3x`會產生編譯時期，應用程式的組合中所包含檔案的版本。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](displaying-an-image-images/imageset05.png "向量影像資產目錄編輯器中")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](displaying-an-image-images/asset8.png "向量影像資產目錄編輯器中")

-----

例如，如果開發人員包含`MonkeyIcon.pdf`檔案儲存為資產類別 」 的向量 150px x 150px，編譯時，會在最終的應用程式套件組合中包含資產的下列點陣圖的解析度：

- `MonkeyIcon@1x.png` -150px x 150px 解析度。
- `MonkeyIcon@2x.png` -300px x 300px 解析度。
- `MonkeyIcon@3x.png` -450px x 450px 解析度。

下列應該列入考量時使用 PDF 向量影像資產類別目錄中：

- 當 PDF 將會在編譯階段和出貨最終的應用程式中的點陣圖的點陣圖點陣化，這不是支援完整的向量。
- 無法調整影像大小，一旦設定資產類別中。 如果開發人員嘗試調整影像 （無論是在程式碼或使用自動配置和大小類別） 的大小影像會扭曲就像任何其他的點陣圖。
- 資產的類別目錄，才與 iOS 7 和更大，如果應用程式需要支援 iOS 6 或更低，它不能使用資產目錄。

<a name="Working-with-Template-Images" />

## <a name="working-with-template-images"></a>使用範本映像

根據設計的 iOS 應用程式，可能會有開發人員必須自訂圖示或在使用者介面，以符合的變更 （例如，根據使用者偏好設定） 的色彩配置內的映像的時間。

若要輕鬆地達成這個效果，切換_呈現模式_的影像資產**範本映像**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](displaying-an-image-images/templateimage01.png "轉譯模式設定為範本映像")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](displaying-an-image-images/templateimage01vs.png "轉譯模式設定為範本")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

從 iOS 設計工具中，將影像資產指派至 UI 控制項，然後設定**濃淡**來標示映像：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![](displaying-an-image-images/templateimage03.png "設定來標示影像濃淡")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](displaying-an-image-images/templateimage03vs.png "設定來標示影像濃淡")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

（選擇性） 影像資產及濃淡可以直接在程式碼會設定：

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

若要使用範本映像完全從程式碼，執行下列作業：

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

因為`RenderMode`屬性`UIImage`是唯讀的請使用`ImageWithRenderingMode`方法來建立具有所需的呈現模式設定的新執行個體的映像。

有三個可能設定`UIImage.RenderMode`透過`UIImageRenderingMode`列舉：

* `AlwaysOriginal` -強制的影像會轉譯為原始的來源映像檔案，不進行任何變更。
* `AlwaysTemplate` -強制要轉譯的色彩標示與指定的像素做為範本映像的映像`Tint`色彩。
* `Automatic` -請呈現影像，作為範本或原始環境中使用。 例如，如果映像是用`UIToolBar`， `UINavigationBar`，`UITabBar`或`UISegmentControl`會視為範本。

<a name="Adding-new-Assets-Collections" />

## <a name="adding-new-assets-collections"></a>加入新資產集合

使用資產目錄中的映像時可能會當新的集合會的是必要項目，而不是將所有應用程式的映像，以新增`Assets.xcassets`集合。 例如，在設計上要求資源時。

若要將新的資產目錄加入至專案：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 以滑鼠右鍵按一下**專案名稱**中**方案總管 中**選取**新增** > **新的檔案...**
2. 選取**iOS** > **資產目錄**，輸入**名稱**集合，然後按一下**新增**按鈕：

    ![](displaying-an-image-images/asset01.png "建立新的資產目錄")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 [方案總管] 中，以滑鼠右鍵按一下**資產目錄**資料夾，然後選取**新增 > 新的資產目錄**。
2. 它提供一個名稱，然後按一下**新增**:

    ![](displaying-an-image-images/asset1.png "建立新的資產目錄")

-----


從這裡集合即可使用相同的方式做為預設`Assets.xcassets`自動包含在專案中的集合。

<a name="controls" />

## <a name="using-images-with-controls"></a>使用控制項的影像

除了使用映像，以支援應用程式，iOS 也會使用映像應用程式等索引標籤列、 工具列、 導覽列中，資料表和按鈕的控制項類型。 會出現在控制項上的映像的簡單方法是將指派`UIImage`至控制項的執行個體`Image`屬性。

<a name="frombundle" />

### <a name="frombundle"></a>FromBundle

`FromBundle`方法呼叫是同步的 （封鎖） 呼叫具有許多內建功能，例如快取支援，並針對各種解析度的影像檔的自動處理映像載入和管理功能。  

下列範例示範如何設定的映像`UITabBarItem`上`UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

假設`MyImage`影像資產加入至上述 Asset 類別目錄的名稱。 當使用資產目錄映像時，只需要指定名稱中的映像設定`FromBundle`方法**PNG**格式化影像：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

對於其他任何映像格式，包含名稱的擴充功能。 例如: 

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

如需圖示和映像的詳細資訊，請參閱 Apple 文件上[自訂圖示和映像建立指導方針](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)。

<a name="Displaying-an-Image-in-a-Storyboards" />

## <a name="displaying-an-image-in-a-storyboards"></a>分鏡腳本中顯示影像

一旦使用資產目錄 Xamarin.iOS 專案加入映像，它可以輕鬆地顯示在分鏡腳本使用`UIImageView`iOS 設計工具中。 例如，如果已加入下列的影像資產：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](displaying-an-image-images/display01.png "已新增範例影像資產")

執行下列命令顯示腳本上：

1. 按兩下`Main.storyboard`檔案**方案總管 中**開啟 iOS 設計工具中進行編輯。
2. 選取**影像檢視**從**工具箱**:

     ![](displaying-an-image-images/display02.png "從 [工具箱] 中選取映像的檢視")
3. 影像檢視拖曳至設計介面和位置和大小，視需要：

    ![](displaying-an-image-images/display03.png "在設計介面上新的映像檢視")
4. 在**Widget**區段**屬性總管**選取所需**映像**来顯示的資產：

    ![](displaying-an-image-images/display04.png "選取要顯示所需的影像資產")
5. 在**檢視**區段中，使用**模式**控制映像將會如何調整大小的時機**影像檢視**重新調整大小。
6. 與**影像檢視**選取，按一次，以新增**條件約束**:

    ![](displaying-an-image-images/display05.png "加入條件約束")
7. 在每個邊緣拖曳"T"形狀**影像檢視**來對應在螢幕側邊"釘選 」 兩側的映像。 如此一來，**影像檢視**會壓縮及成長螢幕調整大小時。
8. 將變更儲存到分鏡腳本。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](displaying-an-image-images/display01vs.png "已新增範例影像資產")

執行下列命令顯示腳本上：

1. 按兩下`Main.storyboard`檔案**方案總管 中**開啟 iOS 設計工具中進行編輯。
2. 選取**影像檢視**從**工具箱**:

     ![](displaying-an-image-images/display02vs.png "從 [工具箱] 中選取映像的檢視")
3. 影像檢視拖曳至設計介面和位置和大小，視需要：

    ![](displaying-an-image-images/display03vs.png "在設計介面上新的映像檢視")
4. 在**Widget**區段**屬性總管**選取所需**映像**来顯示的資產：

    ![](displaying-an-image-images/display04vs.png "選取要顯示所需的影像資產")
5. 在**檢視**區段中，使用**模式**控制映像將會如何調整大小的時機**影像檢視**重新調整大小。
6. 與**影像檢視**選取，按一次，以新增**條件約束**:

    ![](displaying-an-image-images/display05vs.png "加入條件約束")
7. 在每個邊緣拖曳"T"形狀**影像檢視**來對應在螢幕側邊"釘選 」 兩側的映像。 如此一來，**影像檢視**會壓縮及成長螢幕調整大小時。
8. 將變更儲存到分鏡腳本。

-----


<a name="Displaying-an-Image-in-Code" />

## <a name="displaying-an-image-in-code"></a>在程式碼中顯示映像

就像在腳本中顯示映像，一旦映像新增至 Xamarin.iOS 專案使用資產目錄中，它可以輕鬆地顯示使用 C# 程式碼。

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

此程式碼會建立新`UIImageView`，並讓它的初始大小和位置。 然後它載入影像的影像資產加入至專案，並新增`UIImageView`父系`UIView`加以顯示。

## <a name="related-links"></a>相關連結

- [使用映像 （範例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和映像建立指導方針](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
