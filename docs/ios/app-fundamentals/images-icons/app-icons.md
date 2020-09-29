---
title: Xamarin 中的應用程式圖示
description: 本檔說明如何在 Xamarin 中使用各種應用程式圖示：應用程式圖示本身、焦點圖示、設定圖示，以及 iTunes 作品。
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/22/2017
ms.openlocfilehash: 8bf0df03419d3705d72b679cfbfca427b0ac1a93
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435644"
---
# <a name="application-icons-in-xamarinios"></a>Xamarin 中的應用程式圖示

將會詳細介紹下列主題：

- [應用程式、焦點和設定圖示](#icon-types) -iOS 應用程式所需的不同圖示類型。
- 使用資產目錄[管理圖示](#managing)-使用資產目錄管理應用程式圖示。
- [Itunes 圖稿](#itunes) -為提供您應用程式的臨機操作方法提供必要的 iTunes 作品。

<a name="icon-types"></a>

## <a name="application-spotlight-and-settings-icons"></a>應用程式、焦點和設定圖示

與 Xamarin 應用程式可以使用 UI 控制項的影像資產，以及做為檔圖示的方式一樣，影像資產也可以用來提供應用程式圖示。 下列來自 iPad 的螢幕擷取畫面說明 iOS 中圖示的三種用途：

- **應用程式圖示** -每個 iOS 應用程式都必須定義應用程式圖示。 這是使用者將從 iOS 主畫面上點擊以啟動應用程式的圖示。 此外，Game Center 也會使用此圖示（如果適用）。 範例：

    [![應用程式圖示](app-icons-images/000.png)](app-icons-images/000-full.png#lightbox)
- **焦點圖示** -每當使用者在焦點搜尋中輸入應用程式的名稱時，就會顯示此圖示。 範例：

    [![焦點圖示](app-icons-images/000a.png)](app-icons-images/000a-full.png#lightbox)
- **設定圖示** -如果使用者在其 iOS 裝置上輸入「 **設定** 」應用程式，則會在應用程式的 **設定** 清單結尾顯示此圖示。 範例：

    [![設定圖示](app-icons-images/000b.png)](app-icons-images/000b-full.png#lightbox)

需要下列影像資產大小和解析度，才能支援以 iOS 5 至 iOS 9 (或更高) 的 Xamarin iOS 應用程式所需的所有圖示類型：

### <a name="iphone-icon-sizes"></a>iPhone 圖示大小

- **iPhone： iOS 9 & 10 (iPhone 6 & 7 Plus) **

    |圖示|3倍|
    |---|---|
    |應用程式圖示|180x180|
    |焦點|120x120|
    |設定|87x87|

- **iPhone： iOS 7 & 8**

    |圖示|1 倍|2x|
    |---|---|---|
    |應用程式圖示|iphones<sup>1</sup>|120x120|
    |焦點|40x40<sup>2</sup>|80x80|
    |設定|-|-|

- **iPhone： iOS 5 & 6**

    |圖示|1 倍|2x|
    |---|---|---|
    |應用程式圖示|57x57|114x114|
    |焦點|29x29|58x58|
    |設定|29x29<sup>3、4</sup>|58x58<sup>3、4</sup>|

### <a name="ipad-icon-sizes"></a>iPad 圖示大小

- **iPad： iOS 9 & 10**

    |圖示|2x (iPad Pro) |
    |---|---|
    |應用程式圖示|167x167<sup>6</sup>|
    |焦點|120x120<sup>6</sup>|
    |設定|58x58<sup>5</sup>|

- **iPad： iOS 7 & 8**

    |圖示|1 倍|2x|
    |---|---|---|
    |應用程式圖示|76x76|為152x152|
    |焦點|40x40|80x80|
    |設定|-|-|

- **iPad： iOS 5 & 6**

    |圖示|1 倍|2x|
    |---|---|---|
    |應用程式圖示|72x72|144x144|
    |焦點|50x50|100x100|
    |設定|29x29<sup>3，5</sup>|58x58<sup>3，5</sup>|

 1. Visual Studio for Mac 和 Xcode 不再支援設定適用于 iOS 7 的1x 映射。
 2. 使用資產目錄時，不支援設定 iOS 7 的1x 映射。
 3. iOS 7 & 8 使用與 iOS 5 & 6 相同的映射大小。
 4. 使用與焦點圖示相同的影像和大小。
 5. 使用與 iPhone 相同的大小圖示。
 6. 僅支援資產目錄映射集。

 如需圖示的詳細資訊，請參閱 Apple 的 [圖示和影像大小](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) 檔。

<a name="managing"></a>

## <a name="managing-icons-with-asset-catalogs"></a>使用資產目錄管理圖示

針對圖示， `AppIcon` 可以 `Assets.xcassets` 在應用程式專案的檔案中新增特殊的影像集。 所有支援所有解析度所需的映射版本都包含在 _xcasset_ 中，並群組在一起。 Visual Studio for Mac 中的特殊編輯器，可讓開發人員以圖形方式包含及設定這些影像。

若要使用資產目錄，請遵循下列步驟：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下 `Info.plist` **方案總管** 中的檔案，將它開啟以供編輯。
2. 向下卷到 [ **IPhone 圖示** ] 區段。
3. 按一下 [ **遷移至資產目錄** ] 按鈕：

    ![確定已選取 [AppIcon]](app-icons-images/migrate01.png)

4. 在 [ **方案總管**中，按兩下檔案 `Assets.xcassets` 以開啟它進行編輯：

    ![方案總管中的 >appicon 檔案](app-icons-images/asset01.png)

5. `AppIcon`從資產清單中選取以顯示 `Icon Editor` ：

    ![AppIcon 編輯器](app-icons-images/asset02.png)

6. 按一下指定的圖示類型，然後選取所需類型/大小的影像檔案，或從資料夾中拖曳影像，並將它放在所需的大小。
7. 按一下 [ **開啟** ] 按鈕，將影像包含在專案中，並在 xcasset 中進行設定。
8. 針對所有需要的映射重複執行。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 按兩下 [資訊]。  * * **方案總管**中的檔案：

    ![選取 Info. plist](app-icons-images/icon01w.png)

2. 按一下 [**視覺資產**] 索引標籤，然後按一下**應用程式圖示**底下的 [**使用資產目錄**] 按鈕：

    ![選取 [視覺資產] 索引標籤](app-icons-images/icon02w.png)

    如果沒有任何按鈕，而是下拉式清單，則表示資產目錄已新增至此專案。

3. 從 **方案總管**中，展開 [ **資產目錄** ] 資料夾：

    ![展開資產目錄資料夾](app-icons-images/image009.png)

4. 按兩下 **媒體** 檔案，以在編輯器中開啟它：

    ![在編輯器中開啟媒體檔案](app-icons-images/image010.png)

5. 在 [ **屬性] Explorer** 下，開發人員可以選取不同類型和所需圖示的大小。
6. 按一下指定的圖示類型，然後選取所需類型/大小的影像檔案。
7. 按一下 [ **開啟** ] 按鈕，將影像包含在專案中，並在 xcasset 中進行設定。
8. 針對所有需要的映射重複執行。

-----

這是慣用的方法，包含和管理將用來為應用程式提供應用程式、焦點和設定圖示的影像資產。

<a name="itunes"></a>

## <a name="itunes-artwork"></a>iTunes 插圖

如果使用提供應用程式的臨機操作方法 (針對公司使用者或在實際裝置上進行搶鮮版（Beta）的測試) ，開發人員也必須包含512x512 和1024x1024 映射，以用來代表 iTunes 中的應用程式。

若要指定 iTunes 插圖，請執行下列動作：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 按兩下 `Info.plist` **方案總管** 中的檔案，將它開啟以供編輯。
2. 滾動至編輯器的 [ **ITunes 圖稿** ] 區段：

    ![滾動至編輯器的 iTunes 圖稿區段](app-icons-images/itunes01.png)
3. 針對任何遺失的影像，按一下編輯器中的縮圖，從 [開啟檔案] 對話方塊中選取所需 iTunes 圖稿的影像檔案，然後按一下 [ **確定]** 按鈕。
4. 重複此步驟，直到為應用程式指定所有所需的映射為止。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 按兩下 `Info.plist` **方案總管** 中的檔案，將它開啟以供編輯。

2. 按一下 [ **視覺資產** ] 索引標籤，然後展開 **iTunes 圖稿**：

    ![在 Visual Studio 中編輯 iTunes 圖稿](app-icons-images/itunes01w.png)
3. 針對任何遺失的影像，按一下編輯器中的縮圖，從 [開啟檔案] 對話方塊中選取所需 iTunes 圖稿的影像檔案，然後按一下 [ **開啟** ] 按鈕。
4. 重複此步驟，直到為應用程式指定所有所需的映射為止。

-----

## <a name="related-links"></a>相關連結

- [使用影像 (範例) ](/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和映射建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)) 