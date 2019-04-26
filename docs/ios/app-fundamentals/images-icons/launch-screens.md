---
title: Xamarin.iOS 應用程式啟動畫面
description: 這篇文章說明如何在任何解析度和方向，使用單一的統一的分鏡腳本建立所有的 iOS 裝置的應用程式啟動畫面。
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2018
ms.openlocfilehash: 0ec1defa29a4fe85c4ae3e809d8733e68cc268ac
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087409"
---
# <a name="launch-screens-for-xamarinios-apps"></a>Xamarin.iOS 應用程式啟動畫面

_這篇文章說明如何在任何解析度和方向，使用單一的統一的分鏡腳本建立所有的 iOS 裝置的應用程式啟動畫面。_

IOS 8 之前建立的 iOS 應用程式啟動畫面需要開發人員的各種裝置外型規格和應用程式無法執行的解決方法的每個提供的影像資產。 IOS 8 的推出，不過，已經可以建立一個啟動的畫面看起來正確無誤，在所有情況下，使用單一的統一的分鏡腳本。

這個簡短的逐步解說描述如何使用新的專案中的預設提供的其中一個分鏡腳本，或使用手動新增至現有的專案分鏡腳本建立啟動螢幕。 然後，它會示範如何使用 iOS 設計工具將映像檢視和標籤新增到分鏡腳本，以設定條件約束，這些檢視，並確認 分鏡腳本會尋找正確的各種裝置和方向。

<a name="storyboard" />

## <a name="managing-launch-screens-with-storyboards"></a>管理分鏡腳本與啟動畫面

在 iOS 8 （和更新版本） 中，開發人員可以建立特殊統一的腳本，以提供啟動畫面，而不是使用一或多個靜態的啟動影像。 建立時啟動分鏡腳本 iOS 設計工具中，使用大小類別和自動版面配置來定義不同的顯示環境的不同版面配置。 使用大小類別和自動版面配置，可以建立單一的啟動螢幕看起來沒問題，在所有裝置上，開發人員，並將其顯示環境中。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac，請選取建立新的專案**檔案 > 新的解決方案**，然後選擇**單一檢視應用程式**: 

    ![新增專案] 視窗的 [使用選取的單一檢視應用程式](launch-screens-images/launch01.png)

    - 根據預設，新的專案包含**LaunchScreen.storyboard**定義啟動畫面介面檔案。 
    - 若要改為新增至現有的專案的啟動 Screen 分鏡腳本，以滑鼠右鍵按一下中的專案名稱**Solution Pad** ，然後選擇 **新增 > 新的檔案...** ，然後選取**啟動畫面**:

    ![新的檔案] 視窗的 [ios 啟動畫面選取](launch-screens-images/launch01b.png)

    - 將檔案命名**LaunchScreen**或您選擇的另一個名稱。

2. 設定專案以使用適當的分鏡腳本，其啟動畫面：

    - 按兩下**Info.plist**中的檔案**Solution Pad**以開啟它進行編輯。
    - 在 **啟動映像**區段中，請確定**啟動畫面**設為適當的分鏡腳本的名稱：

    ![在 Info.plist 中的 啟動畫面選擇器](launch-screens-images/launch02.png)

    - 根據預設，新的專案設定為使用**LaunchScreen.storyboard**做為其啟動螢幕。

3. 新增映像**appicon**資產目錄，如此就可供使用，在 [啟動] 畫面。 如需詳細資訊，請參閱 <<c0> [ 新增至資產目錄映像設定的映像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)一節[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南。

4. 開啟**LaunchScreen.storyboard**編輯按兩下**Solution Pad**。

5. 選擇裝置和要預覽啟動 Screen 分鏡腳本 iOS 設計工具中的方向。 開啟在底部工具列的 [裝置選取項目] 面板，然後選取**iPhone 4 秒**並**直向**。

    ![[裝置] 選取項目工具列](launch-screens-images/launch05.png)

    - 請注意，選取 裝置和方向只變更 iOS 設計工具預覽設計的方式。 此處所做的選取範圍，無論新加入的條件約束會套用在所有裝置和方向，除非**編輯特性**已用來指定其他的按鈕。 

6. 設定**背景**檢視控制器的主要檢視的色彩。 選取 [檢視]，請按一下中間檢視控制器，並調整背景色彩 using **Properties Pad**:

    ![單一檢視具有紫色的背景色彩](launch-screens-images/launch06.png)

7. 新增**映像檢視**來啟動畫面，並設定其來源**映像**:

    - 拖曳**映像檢視**從**Toolbox Pad**至檢視的中心。
    - 與**映像檢視**已選取**Widget**一節**Properties Pad**設定**映像**映像設定的屬性加入至**appicon**資產目錄。 重新調整位置和大小**映像檢視**視需要而定：
    
    ![映像具有檢視其映像 屬性設定](launch-screens-images/launch07.png)

8. 新增**標籤**下方**映像檢視**並用**Properties Pad**來設定其屬性： 

    ![其文字和色彩設定標籤](launch-screens-images/launch08.png)

9. 使用中的右側按鈕切換至限制式編輯模式**限制式工具列**:
    
    ![限制式編輯模式按鈕](launch-screens-images/launch09.png)

10. 將條件約束加入**映像檢視**、 設定其高度和寬度和水平及垂直置中：

    ![映像具有檢視版面配置條件約束](launch-screens-images/launch10.png)

    - 如需如何新增條件約束的詳細資訊，請參閱 <<c0> [ 自動配置使用適用於 iOS 的 Xamarin Designer](~/ios/user-interface/designer/designer-auto-layout.md)。

11. 將條件約束加入**標籤**、 將它在水平置中、 高度和寬度，讓它和定位固定從垂直距離**映像檢視**:

    ![具有版面配置條件約束的標籤](launch-screens-images/launch11.png)

12. 測試其他裝置和確認設計看起來如預期般在所有情況下的方向。 在需要針對特定裝置或方向進行調整的情況下，使用**編輯特性**按鈕以新增特定的大小類別的條件約束：

    ![轉譯為 X 使用橫向 iPhone 啟動畫面](launch-screens-images/launch12.png)

13. 將變更儲存到分鏡腳本。 在模擬器或裝置上執行應用程式，並啟動畫面會顯示，因為在啟動應用程式。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 建立新的專案。 在 Visual Studio 中，選取**檔案 > 新增 > 專案 > Visual C# > iPhone 與 iPad > iOS 應用程式 (Xamarin)**:

    ![[新增專案] 視窗，使用 iOS 應用程式 (Xamarin) 選取](launch-screens-images/launch01.w157.png)

    選取 **單一檢視應用程式**範本，然後再按一下**確定**:

    ![單一檢視應用程式範本](launch-screens-images/launch01-2.w157.png)

2. 如果**資源 > LaunchScreen.xib**存在於**方案總管**，刪除該檔案上按一下滑鼠右鍵，然後選擇**刪除**。 此檔案將取代下一個步驟中的分鏡腳本。

3. 建立分鏡腳本，做為啟動螢幕。 在 **方案總管 中**，以滑鼠右鍵按一下專案，然後選擇 **新增 > 新增項目...** 後面接著**空白分鏡腳本**。 命名這個分鏡腳本**LaunchScreen.storyboard**然後按一下**新增**:

    ![[加入新項目] 視窗中，使用選取的空白分鏡腳本](launch-screens-images/launch03.w157.png)

4. 設定專案以使用**LaunchScreen.storyboard**作為其啟動 Screen 分鏡腳本：

    - 在 [方案總管] 中，按兩下 [Info.plist] 檔案以開啟它進行編輯。 
    - 在 **視覺效果資產**索引標籤上，設定**啟動畫面**來**LaunchScreen**。

    ![在 Info.plist 中的 啟動畫面選擇器](launch-screens-images/launch04-vs.png)

5. 如此就可供使用，在啟動畫面上，請將影像加入專案中的資產目錄：

    - 在 [**方案總管] 中**，以滑鼠右鍵按一下**資產目錄**，然後選取**新增資產目錄**。 命名此新的資產目錄**資產**:

    ![[加入新項目] 視窗中，使用選取的資產目錄](launch-screens-images/launch05.w157.png)

    - 新增新映像設定為**資產**資產目錄中所述[新增至資產目錄映像設定的映像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)一節[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南。

6. 開啟**LaunchScreen.storyboard**編輯按兩下**方案總管 中**。

    - 若要編輯分鏡腳本檔案，Visual Studio 需要作用中的連線到 Mac 組建主機。 請參閱[連線到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南，如需詳細資訊。

7. 選擇裝置和要預覽啟動 Screen 分鏡腳本 iOS 設計工具中的方向。 開啟在底部工具列的 [裝置選取項目] 面板，然後選取**iPhone 4 秒**並**直向**: 
 
    ![[裝置] 選取項目工具列](launch-screens-images/launch07-vs.png)

    - 請注意，選取 裝置和方向只變更 iOS 設計工具預覽設計的方式。 此處所做的選取範圍，無論新加入的條件約束會套用在所有裝置和方向，除非**編輯特性**已用來指定其他的按鈕。 

8. 新增**檢視控制器**藉由拖曳其中一個從分鏡腳本**工具箱**拖曳至設計介面： 

    ![空的檢視控制器加入至設計介面](launch-screens-images/launch08-vs.png)

9. 設定**背景**檢視控制器的主要檢視的色彩。 選取 檢視，請按一下中間檢視控制器，並調整背景色彩 using**屬性 視窗**:
    
    ![單一檢視具有紫色的背景色彩](launch-screens-images/launch09-vs.png)

10. 新增**映像檢視**來啟動畫面，並設定其來源**映像**:

    - 拖曳**映像檢視**從**工具箱**至檢視的中心。
    - 與**映像檢視**仍在選取**Widget**一節**屬性 視窗**設定**映像**映像設定的屬性已新增至**資產**資產目錄。 重新調整位置和大小**映像檢視**視需要而定：
    
    ![映像具有檢視其映像 屬性設定](launch-screens-images/launch10-vs.png)

11. 新增**標籤**下方**映像檢視**:

    - 拖曳**標籤**從**工具箱**拖曳至設計介面中，將它放下方**影像檢視**。
    - 設定屬性**標籤**使用**屬性 視窗**:

    ![其文字和色彩設定標籤](launch-screens-images/launch11-vs.png) 

12. 使用中的右側按鈕切換至限制式編輯模式**限制式工具列**:
    
    ![限制式編輯模式按鈕](launch-screens-images/launch12-vs.png) 

13. 將條件約束加入**映像檢視**、 設定其高度和寬度和水平及垂直置中：

    ![映像具有檢視版面配置條件約束](launch-screens-images/launch13-vs.png) 

    - 新增條件約束的相關資訊，請參閱[自動配置使用適用於 iOS 的 Xamarin Designer](~/ios/user-interface/designer/designer-auto-layout.md)。

14. 將條件約束加入**標籤**、 將它在水平置中、 高度和寬度，讓它和定位固定從垂直距離**映像檢視**:
    
    ![具有版面配置條件約束的標籤](launch-screens-images/launch14-vs.png) 

15. 測試其他裝置和確認設計看起來如預期般在所有情況下的方向。 在需要針對特定裝置或方向進行調整的情況下，使用**編輯特性**按鈕以新增特定的大小類別的條件約束：

    ![轉譯為 X 使用橫向 iPhone 啟動畫面](launch-screens-images/launch15-vs.png) 

16. 將變更儲存到分鏡腳本。 在模擬器或裝置上執行應用程式，並啟動畫面會顯示，因為在啟動應用程式。

-----

> [!NOTE]
> 做為啟動螢幕的分鏡腳本_必須_包含只是簡單、 內建的 UI 項目並**無法**執行任何計算，或衍生自的自訂類別。

如需使用統一的分鏡腳本建立啟動螢幕的詳細資訊，請參閱[動態啟動畫面](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens)一節[統一的分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)指南。

## <a name="migrating-to-launch-screen-storyboards"></a>移轉至啟動螢幕的分鏡腳本

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在更新現有的應用程式来用於其啟動畫面上的分鏡腳本，以滑鼠右鍵按一下**專案名稱**中**方案總管**，然後選取**新增** > **新檔...**.選取  **iOS** > **啟動螢幕**，按一下 **新增**按鈕：

![](launch-screens-images/storyboard02.png "選取 iOS 啟動畫面")

接下來，按兩下`Info.plist`檔案中**方案總管 中**以開啟它進行編輯。 底下**啟動畫面**，選取先前建立的新分鏡腳本檔案。

![](launch-screens-images/storyboard09.png "選取上面所建立的新分鏡腳本檔案")


若要使用新的分鏡腳本，做為啟動螢幕，執行下列作業：

1. 按兩下`Info.plist`檔案中**方案總管 中**以開啟它進行編輯。
2. 捲動到 [**通用的啟動影像**編輯器，開啟] 區段**啟動畫面**下拉式清單中，然後選取上面建立的分鏡腳本名稱： 

    ![](launch-screens-images/storyboard08.png "設定啟動螢幕到分鏡腳本")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 中的專案名稱上按一下滑鼠右鍵**方案總管**，然後選取**新增** > **新檔...**: 

    ![](launch-screens-images/image012.png "加入新的檔案")
2. 輸入啟動螢幕的名稱，然後按一下**新增**按鈕： 

    ![](launch-screens-images/image013.png "輸入啟動螢幕的名稱")
3. 在 [**方案總管] 中**，按兩下新建立的分鏡腳本檔案，以開啟它進行編輯。
4. 請確認**大小類別**設為**任何： 任何**並**檢視方式**是**泛型**: 

    ![](launch-screens-images/image016.png "請確定大小類別設定為任何： 任何以及檢視為泛型")
5. 組件大小類別，簡單的 UI 項目從啟動畫面 (例如`UIImageView`) 和應用程式的套件組合中所含的映像： 

    ![](launch-screens-images/image017.png "組件中 「 iOS 設計工具在啟動畫面")
6. 將變更儲存到分鏡腳本。

-----

## <a name="related-links"></a>相關連結

- [動態啟動畫面 （範例）](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [統一的分鏡腳本](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 設計工具基本概念](~/ios/user-interface/designer/index.md)
- [新增至資產目錄映像的映像設定](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [使用適用於 iOS 的 Xamarin 設計工具的自動版面配置](~/ios/user-interface/designer/designer-auto-layout.md)
- [人性化介面指導方針：啟動畫面](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)
