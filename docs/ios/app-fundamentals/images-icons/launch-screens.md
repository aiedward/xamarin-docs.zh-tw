---
title: 針對 Xamarin iOS 應用程式啟動畫面
description: 本文說明如何使用單一的統一分鏡腳本，為所有 iOS 裝置建立應用程式啟動畫面（任何解析度和方向）。
ms.prod: xamarin
ms.assetid: 31A489CA-756B-4B9B-B386-4BADF18EDD33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2018
ms.openlocfilehash: f4f24ee8bfc6bdde0becb9539ff9e2f532d06381
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432025"
---
# <a name="launch-screens-for-xamarinios-apps"></a>針對 Xamarin iOS 應用程式啟動畫面

_本文說明如何使用單一的統一分鏡腳本，為所有 iOS 裝置建立應用程式啟動畫面（任何解析度和方向）。_

在 iOS 8 之前，建立 iOS 應用程式的啟動畫面需要開發人員針對各種裝置外型規格提供映射資產，以及應用程式可以執行的解決方案。 不過，自 iOS 8 發行之後，您就可以使用單一的統一分鏡腳本來建立在所有案例中都正確的啟動畫面。

這個簡短的逐步解說將說明如何使用新專案中預設提供的分鏡腳本，或以手動方式新增至現有專案的分鏡腳本，來建立啟動畫面。 接著，它會示範如何使用 iOS 設計工具將影像視圖和標籤新增至腳本、在這些視圖上設定條件約束，以及驗證腳本的各種裝置和方向是否正確。

<a name="storyboard"></a>

## <a name="managing-launch-screens-with-storyboards"></a>使用分鏡腳本管理啟動畫面

在 iOS 8 (和更新版本) 中，開發人員可以建立特殊的統一分鏡腳本，以提供啟動畫面，而不是使用一或多個靜態啟動影像。 在 iOS 設計工具中建立啟動分鏡腳本時，請使用大小類別和自動設定來定義不同顯示環境的不同版面配置。 藉由使用大小類別和自動設定，開發人員可以建立在所有裝置和顯示環境中都能美觀的單一啟動畫面。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中，選取 [檔案] **> [新方案** ]，然後選擇 [ **單一視圖應用程式**]，以建立新的專案： 

    ![[新增單一視圖] 應用程式的 [新增專案] 視窗](launch-screens-images/launch01.png)

    - 依預設，新的專案包含定義啟動畫面介面的**LaunchScreen。** 
    - 若要改為將啟動畫面分鏡腳本新增至現有的專案，請以滑鼠右鍵按一下 **Solution Pad** 中的專案名稱，然後選擇 [ **加入 > 新** 檔案]，然後選取 [ **啟動畫面**]：

    ![已選取 iOS 啟動畫面的 [新增檔案] 視窗](launch-screens-images/launch01b.png)

    - 將檔案命名為 **LaunchScreen** 或您選擇的其他名稱。

2. 將專案設定為針對其啟動畫面使用適當的分鏡腳本：

    - 按兩下**Solution Pad**中的**plist**檔案，將其開啟以供編輯。
    - 在 [ **啟動映射** ] 區段中，確定 [ **啟動畫面** ] 設定為適當分鏡腳本的名稱：

    ![Plist 中的啟動畫面選取器](launch-screens-images/launch02.png)

    - 依預設，新的專案會設定為使用 **LaunchScreen** 作為其啟動畫面。

3. 將影像新增至 **>appicon** 資產目錄，讓它可在啟動畫面上使用。 如需詳細資訊，請參閱[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南的將[影像新增至資產目錄映射集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)一節。

4. 開啟 **LaunchScreen** ，在 **Solution Pad**中按兩下以進行編輯。

5. 選擇要在 iOS 設計工具中預覽啟動畫面分鏡腳本的裝置和方向。 開啟底部工具列上的 [裝置選擇] 面板，然後選取 [ **IPhone 4S** 和 **縱向**]。

    ![裝置選取工具列](launch-screens-images/launch05.png)

    - 請注意，選取裝置和方向只會變更 iOS 設計工具預覽設計的方式。 無論在這裡所做的選擇為何，除非已使用 [ **編輯特性** ] 按鈕來指定，否則新加入的條件約束會套用到所有裝置和方向。 

6. 設定 View Controller 主視圖的 **背景** 色彩。 按一下視圖控制器的中間，並使用 **Properties Pad**來調整背景色彩，以選取此視圖：

    ![具有紫色背景色彩的單一視圖](launch-screens-images/launch06.png)

7. 將 **影像視圖** 新增至啟動畫面並設定其來源 **影像**：

    - 將 **影像視圖** 從 [ **工具箱] 面板** 拖曳到視圖的中央。
    - 選取 **影像視圖** 之後，在 [ **Widget** ] 區段中 **Properties Pad** 將 **影像** 屬性設定為已新增至 **>appicon** 資產目錄的映射集。 視需要調整 **影像視圖** 的位置並調整其大小：
    
    ![影像屬性集的影像視圖](launch-screens-images/launch07.png)

8. 在**影像視圖**下方新增**標籤**，並使用**Properties Pad**來設定其屬性： 

    ![具有其文字和色彩集的標籤](launch-screens-images/launch08.png)

9. 使用 [ **條件約束] 工具列**中的右手邊按鈕，切換至條件約束編輯模式：
    
    ![條件約束編輯模式按鈕](launch-screens-images/launch09.png)

10. 將條件約束新增至 **影像視圖**、設定其高度和寬度，並以水準和垂直方式將其置中：

    ![具有版面配置條件約束的影像視圖](launch-screens-images/launch10.png)

    - 如需有關如何新增條件約束的詳細資訊，請參閱 [使用 Xamarin Designer for iOS 的自動版面](~/ios/user-interface/designer/designer-auto-layout.md)配置。

11. 將條件約束新增至 **標籤**、水準置中、為其提供高度和寬度，並將其定位於從 **影像視圖**垂直固定的距離：

    ![具有版面配置條件約束的標籤](launch-screens-images/launch11.png)

12. 測試其他裝置和方向，以確認設計在所有案例中都看起來如預期。 在需要針對特定裝置或方向進行調整的情況下，請使用 [ **編輯特性** ] 按鈕來新增特定大小類別的條件約束：

    ![使用橫向方向轉譯為 iPhone X 的啟動畫面](launch-screens-images/launch12.png)

13. 將變更儲存至腳本。 在模擬器或裝置上執行應用程式，當應用程式啟動時，就會顯示啟動畫面。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 建立新專案。 在 Visual Studio 中，選取 [檔案] **> [新的 > 專案] > [Visual c # > iPhone] & [IOS 應用程式 > Xamarin (**：

    ![已選取 iOS 應用程式 (Xamarin) 的 [新增專案] 視窗](launch-screens-images/launch01.w157.png)

    選取 [ **單一視圖應用程式** ] 範本，然後按一下 **[確定]**：

    ![單一視圖應用程式範本](launch-screens-images/launch01-2.w157.png)

2. 如果**方案總管**中有**資源 > LaunchScreen** ，請在檔案上按一下滑鼠右鍵，然後選擇 [**刪除**]，將它刪除。 在下一個步驟中，將會以分鏡腳本取代此檔案。

3. 建立分鏡腳本來做為啟動畫面。 在 [ **方案總管**中，以滑鼠右鍵按一下專案，然後選擇 [ **加入 > 新專案** ]，然後再按 **空白**分鏡腳本。 將此腳本命名為 **LaunchScreen** ，然後按一下 [ **新增**]：

    ![[加入新專案] 視窗，已選取空白分鏡腳本](launch-screens-images/launch03.w157.png)

4. 將專案設定為使用 **LaunchScreen** 作為其啟動畫面分鏡腳本：

    - 在 [方案總管]**** 中，按兩下 [Info.plist]**** 檔案以開啟它進行編輯。 
    - 在 [ **視覺資產** ] 索引標籤上，將 **啟動畫面** 設定為 [ **LaunchScreen**]。

    ![Plist 中的啟動畫面選取器](launch-screens-images/launch04-vs.png)

5. 將影像新增至專案中的資產目錄，讓它可在啟動畫面上使用：

    - 在 **方案總管**中，以滑鼠右鍵按一下 [ **資產目錄** ]，然後選取 [ **新增資產目錄**]。 命名此新資產目錄 **資產**：

    ![[加入新專案] 視窗，已選取 [資產目錄]](launch-screens-images/launch05.w157.png)

    - 將新的映射集新增至**資產**資產目錄，如[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)指南的將[影像新增至資產目錄映射集](~/ios/app-fundamentals/images-icons/displaying-an-image.md)一節中所述。

6. 開啟 **LaunchScreen** ，在 **方案總管**中按兩下以進行編輯。

    - 若要編輯分鏡腳本檔案，Visual Studio 需要使用 Mac 組建主機的使用中連接。 如需詳細資訊，請參閱 [連接到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) 指南。

7. 選擇要在 iOS 設計工具中預覽啟動畫面分鏡腳本的裝置和方向。 開啟底部工具列上的 [裝置選擇] 面板，然後選取 [ **IPhone 4S** 和 **縱向**： 

    ![裝置選取工具列](launch-screens-images/launch07-vs.png)

    - 請注意，選取裝置和方向只會變更 iOS 設計工具預覽設計的方式。 無論在這裡所做的選擇為何，除非已使用 [ **編輯特性** ] 按鈕來指定，否則新加入的條件約束會套用到所有裝置和方向。 

8. 將 **View Controller** 從 **工具箱** 拖曳到設計介面上，以將它新增至腳本： 

    ![已新增至設計介面的空白 View 控制器](launch-screens-images/launch08-vs.png)

9. 設定 View Controller 主視圖的 **背景** 色彩。 按一下視圖控制器的中間，並使用 [ **屬性] 視窗**調整背景色彩，以選取此視圖：
    
    ![具有紫色背景色彩的單一視圖](launch-screens-images/launch09-vs.png)

10. 將 **影像視圖** 新增至啟動畫面並設定其來源 **影像**：

    - 將 **影像視圖** 從 [ **工具箱** ] 拖曳到視圖的中央。
    - 在仍選取**影像視圖**的情況下，在 [**屬性] 視窗**的 [ **Widget** ] 區段中，將 [**影像**] 屬性設定為已新增至**資產**資產目錄的映射集。 視需要調整 **影像視圖** 的位置並調整其大小：
    
    ![影像屬性集的影像視圖](launch-screens-images/launch10-vs.png)

11. 在**影像視圖**下方新增**標籤**：

    - 將 **標籤** 從 [ **工具箱** ] 拖曳至設計介面，放在 **影像視圖**下方。
    - 使用 [**屬性] 視窗**設定**標籤**的屬性：

    ![具有其文字和色彩集的標籤](launch-screens-images/launch11-vs.png) 

12. 使用 [ **條件約束] 工具列**中的右手邊按鈕，切換至條件約束編輯模式：
    
    ![條件約束編輯模式按鈕](launch-screens-images/launch12-vs.png) 

13. 將條件約束新增至 **影像視圖**、設定其高度和寬度，並以水準和垂直方式將其置中：

    ![具有版面配置條件約束的影像視圖](launch-screens-images/launch13-vs.png) 

    - 如需新增條件約束的詳細資訊，請參閱 [使用 Xamarin Designer for iOS 的自動](~/ios/user-interface/designer/designer-auto-layout.md)配置。

14. 將條件約束新增至 **標籤**、水準置中、為其提供高度和寬度，並將其定位於從 **影像視圖**垂直固定的距離：
    
    ![具有版面配置條件約束的標籤](launch-screens-images/launch14-vs.png) 

15. 測試其他裝置和方向，以確認設計在所有案例中都看起來如預期。 在需要針對特定裝置或方向進行調整的情況下，請使用 [ **編輯特性** ] 按鈕來新增特定大小類別的條件約束：

    ![使用橫向方向轉譯為 iPhone X 的啟動畫面](launch-screens-images/launch15-vs.png) 

16. 將變更儲存至腳本。 在模擬器或裝置上執行應用程式，當應用程式啟動時，就會顯示啟動畫面。

-----

> [!NOTE]
> 當做啟動畫面使用的分鏡腳本 _必須_ 只包含簡單的內建 UI 元素，而且 **無法** 進行任何計算或衍生自自訂類別。

如需有關使用統一分鏡腳本建立啟動畫面的詳細資訊，請參閱[統一](~/ios/user-interface/storyboards/unified-storyboards.md)的分鏡腳本指南的[動態啟動畫面](~/ios/user-interface/storyboards/unified-storyboards.md#dynamic-launch-screens)一節。

## <a name="migrating-to-launch-screen-storyboards"></a>遷移至啟動畫面分鏡腳本

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

當更新現有的應用程式以使用分鏡腳本來啟動畫面時，請以滑鼠右鍵按一下**方案總管**中的**專案名稱**，**然後選取 [**  >  **新增檔案 ...**]。選取 [ **iOS**  >  **啟動畫面**]，然後按一下 [**新增**] 按鈕：

![選取 iOS 啟動畫面](launch-screens-images/storyboard02.png)

接著，按兩下方案總管中的檔案， `Info.plist` 將**Solution Explorer**它開啟以供編輯。 在 [ **啟動畫面**] 下，選取上面建立的新分鏡腳本檔案。

![選取上面建立的新分鏡腳本檔案](launch-screens-images/storyboard09.png)

若要使用新的腳本做為啟動畫面，請執行下列動作：

1. 按兩下 `Info.plist` **方案總管** 中的檔案，將它開啟以供編輯。
2. 在編輯器的 [ **通用啟動映射** ] 區段中，開啟 [ **啟動畫面** ] 下拉式清單，然後選取上面所建立的分鏡腳本名稱： 

    ![將啟動畫面設定為分鏡腳本](launch-screens-images/storyboard08.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 在**方案總管**中的專案名稱上按一下滑鼠右鍵，然後選取 [**加入**  >  **新檔案 ...**： 

    ![新增檔案](launch-screens-images/image012.png)
2. 輸入啟動畫面的名稱，然後按一下 [ **新增** ] 按鈕： 

    ![輸入啟動畫面的名稱](launch-screens-images/image013.png)
3. 在 **方案總管**中，按兩下新建立的分鏡腳本檔案以開啟它進行編輯。
4. 確定 [ **大小] 類別** 設定為 [ **任何]： [任何** ]，並將 [ **View** ] 設定為 [ **一般**]： 

    ![確定 [大小] 類別設定為 [任何]： [任何]，並以泛型形式顯示](launch-screens-images/image016.png)
5. 從大小類別、簡單的 UI 元素 (例如 `UIImageView`) 和您已包含在應用程式套件組合中的影像來組裝啟動畫面： 

    ![在 iOS 設計工具中元件啟動畫面](launch-screens-images/image017.png)
6. 將變更儲存至腳本。

-----

## <a name="related-links"></a>相關連結

- [動態啟動畫面 (範例) ](/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [整合的 Storyboard](~/ios/user-interface/storyboards/unified-storyboards.md)
- [iOS 設計工具基本概念](~/ios/user-interface/designer/index.md)
- [將影像新增至資產目錄映射集](~/ios/app-fundamentals/images-icons/displaying-an-image.md#adding-images-to-an-asset-catalog-image-set)
- [使用 Xamarin Designer for iOS 的自動版面配置](~/ios/user-interface/designer/designer-auto-layout.md)
- [人類介面指導方針：啟動畫面](https://developer.apple.com/ios/human-interface-guidelines/icons-and-images/launch-screen/)