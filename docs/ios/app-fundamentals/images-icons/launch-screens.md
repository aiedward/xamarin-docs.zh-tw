---
title: Xamarin.iOS 應用程式啟動畫面
description: 本文說明如何在任何解析度和方向，使用單一的統一分鏡腳本建立所有的 iOS 裝置的應用程式啟動螢幕。
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2018
ms.openlocfilehash: 0fbd38b643e7806932ba262becc72be2bae6ba4b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784610"
---
# <a name="launch-screens-for-xamarinios-apps"></a>Xamarin.iOS 應用程式啟動畫面

_本文說明如何在任何解析度和方向，使用單一的統一分鏡腳本建立所有的 iOS 裝置的應用程式啟動螢幕。_

IOS 8 之前, 建立的 iOS 應用程式啟動螢幕需要開發人員提供的各種裝置的表單係數和應用程式無法執行的解決方法的每個影像資產。 IOS 8 的版本，不過，已可使用單一的統一分鏡腳本建立看起來正確在所有情況下啟動螢幕。

這個簡短的逐步解說描述如何使用新的專案中的預設提供任一分鏡腳本或腳本，並手動新增至現有的專案，建立啟動螢幕。 接著，它會示範如何使用 iOS 設計工具，以加入到分鏡腳本，設定這些檢視中，條件約束，並確認 分鏡腳本看起來正確的各種裝置和方向的影像檢視和標籤。

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>管理與分鏡腳本的啟動螢幕

在 iOS 8 （和更新版本） 中，開發人員可以建立特殊統一分鏡腳本提供啟動螢幕，而不是使用一或多個靜態啟動映像。 建立時啟動分鏡腳本 iOS 設計工具中，使用大小類別和自動配置來定義不同的顯示環境的不同版面配置。 使用大小類別和自動配置，開發人員可以建立單一的啟動螢幕看起來好所有裝置上和顯示的環境。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在適用於 Mac 的 Visual Studio 中建立新的專案選取**檔案 > 新的方案**，然後選擇**單一檢視應用程式**: 

    ![新增專案 視窗中，使用選取的單一檢視應用程式](launch-screens-images/launch01.png)

    - 根據預設，新的專案包括**LaunchScreen.storyboard**定義的啟動螢幕介面的檔案。 
    - 若要改為加入至現有的專案的啟動螢幕分鏡腳本，以滑鼠右鍵按一下專案名稱中**方案板**選擇**新增 > 新的檔案...** ，然後選取 **啟動螢幕**:

    ![新檔案 視窗，請選取啟動螢幕 iOS](launch-screens-images/launch01b.png)

    - 將檔案命名**LaunchScreen**或您選擇另一個名稱。

2. 設定專案以使用適當的分鏡腳本的啟動螢幕：

    - 按兩下**Info.plist**檔案**方案板**開啟進行編輯。
    - 在**啟動映像**區段中，請確定**啟動螢幕**會設為適當的分鏡腳本的名稱：

    ![啟動螢幕中的選取器 Info.plist](launch-screens-images/launch02.png)

    - 根據預設，新的專案設定為使用**LaunchScreen.storyboard**做為其啟動螢幕。

3. 將映像加入**Assets.xcassets**資產目錄，使其可用於啟動螢幕。 如需詳細資訊，請參閱[加入影像資產目錄映像設定](~/ios/app-fundamentals/images-icons/displaying-an-image.md)區段[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南。

4. 開啟**LaunchScreen.storyboard**編輯按兩下**方案板**。

5. 選擇裝置和用來預覽啟動螢幕分鏡腳本設計工具上的 ios 的方向。 開啟裝置選取項目面板底端工具列上的，然後選取**iPhone 4S**和**縱向**。

    ![裝置選取項目工具列](launch-screens-images/launch05.png)

    - 請注意，選取 裝置和方向只變更 iOS 設計工具預覽設計的方式。 這裡所做的選擇，不論新加入的所有裝置和方向會都套用條件約束，除非**編輯特性**按鈕已用來指定，否則。 

6. 設定**背景**檢視控制站的主要檢視的色彩。 選取檢視，請按一下中間檢視控制器，並調整背景色彩使用**屬性板**:

    ![在單一檢視具有紫色背景色彩](launch-screens-images/launch06.png)

7. 新增**影像檢視**至啟動螢幕，並設定其來源**映像**:

    - 拖曳**影像檢視**從**工具箱板**至檢視的中心。
    - 與**影像檢視**已選取**Widget**區段**屬性板**設定**映像**設映像已經屬性加入至**Assets.xcassets**資產目錄。 重新定位並調整大小**影像檢視**視需要而定：
    
    ![檢視其映像屬性設定，映像](launch-screens-images/launch07.png)

8. 新增**標籤**下方**影像檢視**並用**屬性板**設定其屬性： 

    ![其文字和色彩設定標籤](launch-screens-images/launch08.png)

9. 切換編輯模式，條件約束使用中的右按鈕**條件約束工具列**:
    
    ![條件約束的編輯模式按鈕](launch-screens-images/launch09.png)

10. 加入條件約束，以**影像檢視**、 設定它的高度和寬度和水平及垂直置中：

    ![檢視具有版面配置的條件約束，映像](launch-screens-images/launch10.png)

    - 如需有關如何加入條件約束的詳細資訊，請參閱[與 Xamarin 設計工具，適用於 iOS 的自動配置](~/ios/user-interface/designer/designer-auto-layout.md)。

11. 加入條件約束，以**標籤**、 將水平置中、 高度和寬度，並賦予它和定位固定從垂直距離**影像檢視**:

    ![包含配置條件約束的標籤](launch-screens-images/launch11.png)

12. 測試其他裝置和以確認設計看起來，如預期般在所有情況下的方向。 在需要針對特定裝置或方向進行調整的情況，使用**編輯特性**按鈕即可加入針對特定大小類別條件約束：

    ![轉譯為 X 使用橫向 iPhone 啟動螢幕](launch-screens-images/launch12.png)

13. 將變更儲存到分鏡腳本。 執行應用程式模擬器或裝置，並啟動螢幕會顯示為應用程式正在啟動。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 建立新的專案。 在 Visual Studio 中，選取**檔案 > 新增 > 專案 > Visual C# > iPhone 和 iPad > iOS 應用程式 (Xamarin)**:

    ![新的專案，使用 iOS 應用程式 (Xamarin) 選取視窗](launch-screens-images/launch01.w157.png)

    選取**單一檢視應用程式**範本，然後再按一下**確定**:

    ![單一檢視應用程式範本](launch-screens-images/launch01-2.w157.png)

2. 如果**資源 > LaunchScreen.xib**存在於**方案總管 中**，刪除該檔案上按一下滑鼠右鍵，然後選擇**刪除**。 這個檔案會取代下一個步驟中的分鏡腳本。

3. 建立要做為啟動螢幕的分鏡腳本。 在**方案總管] 中**，以滑鼠右鍵按一下專案，然後選擇 [**新增 > 新的項目...** 後面**空分鏡腳本**。 將這個分鏡腳本**LaunchScreen.storyboard**按一下**新增**:

    ![加入新項目] 視窗，請選取 [空白的分鏡腳本](launch-screens-images/launch03.w157.png)

4. 設定專案以使用**LaunchScreen.storyboard**作為其啟動螢幕分鏡腳本：

    - 在 [方案總管] 中，按兩下 [Info.plist] 檔案以開啟它進行編輯。 
    - 在**視覺資產**索引標籤上，設定**啟動螢幕**至**LaunchScreen**。

    ![啟動螢幕中的選取器 Info.plist](launch-screens-images/launch04-vs.png)

5. 使其可用於啟動畫面，請將影像加入專案中的資產目錄：

    - 在**方案總管 中**，以滑鼠右鍵按一下**資產目錄**並選取**加入資產目錄**。 將這個新的資產目錄**資產**:

    ![加入新項目 視窗，請選取的資產目錄](launch-screens-images/launch05.w157.png)

    - 加入新映像設定為**資產**資產目錄中所述[加入影像資產目錄映像設定](~/ios/app-fundamentals/images-icons/displaying-an-image.md)區段[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南。

6. 開啟**LaunchScreen.storyboard**編輯按兩下**方案總管 中**。

    - 若要編輯分鏡腳本檔案，Visual Studio 需要 Mac 組建主機的使用中連接。 請參閱[連接到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南以取得詳細資料。

7. 選擇裝置和用來預覽啟動螢幕分鏡腳本設計工具上的 ios 的方向。 開啟裝置選取項目面板底端工具列上的，然後選取**iPhone 4S**和**縱向**: 
 
    ![裝置選取項目工具列](launch-screens-images/launch07-vs.png)

    - 請注意，選取 裝置和方向只變更 iOS 設計工具預覽設計的方式。 這裡所做的選擇，不論新加入的所有裝置和方向會都套用條件約束，除非**編輯特性**按鈕已用來指定，否則。 

8. 新增**檢視控制器**藉由拖曳其中一個從分鏡腳本**工具箱**拖曳至設計介面： 

    ![空白檢視控制器加入至設計介面](launch-screens-images/launch08-vs.png)

9. 設定**背景**檢視控制站的主要檢視的色彩。 選取檢視，請按一下中間檢視控制器，並調整背景色彩使用**屬性 視窗**:
    
    ![在單一檢視具有紫色背景色彩](launch-screens-images/launch09-vs.png)

10. 新增**影像檢視**至啟動螢幕，並設定其來源**映像**:

    - 拖曳**影像檢視**從**工具箱**至檢視的中心。
    - 與**影像檢視**仍已選取**Widget**區段**屬性 視窗**設定**映像**映像設定的屬性已加入至**資產**資產目錄。 重新定位並調整大小**影像檢視**視需要而定：
    
    ![檢視其映像屬性設定，映像](launch-screens-images/launch10-vs.png)

11. 新增**標籤**下方**影像檢視**:

    - 拖曳**標籤**從**工具箱**拖曳至設計介面中，將此硬碟置於下列**影像檢視**。
    - 設定屬性**標籤**使用**屬性 視窗**:

    ![其文字和色彩設定標籤](launch-screens-images/launch11-vs.png) 

12. 切換編輯模式，條件約束使用中的右按鈕**條件約束工具列**:
    
    ![條件約束的編輯模式按鈕](launch-screens-images/launch12-vs.png) 

13. 加入條件約束，以**影像檢視**、 設定它的高度和寬度和水平及垂直置中：

    ![檢視具有版面配置的條件約束，映像](launch-screens-images/launch13-vs.png) 

    - 新增條件約束的相關資訊，請參閱[與 Xamarin 設計工具，適用於 iOS 的自動配置](~/ios/user-interface/designer/designer-auto-layout.md)。

14. 加入條件約束，以**標籤**、 將水平置中、 高度和寬度，並賦予它和定位固定從垂直距離**影像檢視**:
    
    ![包含配置條件約束的標籤](launch-screens-images/launch14-vs.png) 

15. 測試其他裝置和以確認設計看起來，如預期般在所有情況下的方向。 在需要針對特定裝置或方向進行調整的情況，使用**編輯特性**按鈕即可加入針對特定大小類別條件約束：

    ![轉譯為 X 使用橫向 iPhone 啟動螢幕](launch-screens-images/launch15-vs.png) 

16. 將變更儲存到分鏡腳本。 執行應用程式模擬器或裝置，並啟動螢幕會顯示為應用程式正在啟動。

-----

> [!NOTE]
> 做為啟動螢幕的分鏡腳本_必須_包含只有簡單、 內建的 UI 項目和**無法**執行任何計算，或衍生自的自訂類別。

如需有關使用統一的分鏡腳本建立啟動螢幕的詳細資訊，請參閱[動態啟動螢幕](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens)區段[統一分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)指南。

## <a name="migrating-to-launch-screen-storyboards"></a>移轉啟動螢幕的分鏡腳本

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在更新現有的應用程式用於其啟動螢幕的分鏡腳本時，以滑鼠右鍵按一下**專案名稱**中**方案總管 中**選取**新增** > **新的檔案...**.選取**iOS** > **啟動螢幕**按一下**新增**按鈕：

![](launch-screens-images/storyboard02.png "選取 iOS 啟動螢幕")

接下來，按兩下`Info.plist`檔案**方案總管 中**開啟進行編輯。 在下**啟動螢幕**，選取上面所建立的新分鏡腳本檔案。

![](launch-screens-images/storyboard09.png "選取上面所建立的新分鏡腳本檔案")


若要使用新的分鏡腳本，做為啟動螢幕，執行下列作業：

1. 按兩下`Info.plist`檔案**方案總管 中**開啟進行編輯。
2. 捲動到**通用啟動映像**編輯器，開啟區段**啟動螢幕**下拉式清單中，然後選取上面建立的分鏡腳本名稱： 

    ![](launch-screens-images/storyboard08.png "設定啟動螢幕到分鏡腳本")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 中的專案名稱上按一下滑鼠右鍵**方案總管 中**選取**新增** > **新的檔案...**: 

    ![](launch-screens-images/image012.png "加入新檔案")
2. 輸入啟動螢幕的名稱，然後按一下**新增**按鈕： 

    ![](launch-screens-images/image013.png "輸入啟動螢幕的名稱")
3. 在**方案總管 中**，按兩下新建立的分鏡腳本檔案，以開啟它進行編輯。
4. 請確認**大小類別**設**任何： 任何**和**檢視為**是**泛型**: 

    ![](launch-screens-images/image016.png "請確定大小類別設定為任何： 任何以及檢視為泛型")
5. 啟動螢幕大小的類別，簡單的 UI 項目從組件 (例如`UIImageView`) 及您具有應用程式的組合中所包含的映像： 

    ![](launch-screens-images/image017.png "組件在 iOS 設計工具中啟動螢幕")
6. 將變更儲存到分鏡腳本。

-----

## <a name="related-links"></a>相關連結

- [動態啟動螢幕 （範例）](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [統一的分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 設計工具基本概念](~/ios/user-interface/designer/index.md)
- [設定加入至資產目錄映像的影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md#asset-catalogs)
- [使用 Xamarin 設計工具，適用於 iOS 的自動配置](~/ios/user-interface/designer/designer-auto-layout.md)
- [人性化介面指導方針： 啟動螢幕](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
