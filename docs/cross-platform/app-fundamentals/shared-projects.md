---
title: 使用共用的專案來共用程式碼
description: 共用的專案可讓您撰寫一些不同應用程式專案所參考的通用程式碼。 程式碼會編譯為每個參考專案的一部分, 而且可以包含編譯器指示詞, 以協助將平臺特定功能併入共用的程式碼基底中。
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: ed58b0810d3c4fd3a3dd99cddd16227f9ac30273
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739064"
---
# <a name="shared-projects-code-sharing"></a>共用的專案程式碼共用

_共用的專案可讓您撰寫一些不同應用程式專案所參考的通用程式碼。程式碼會編譯為每個參考專案的一部分, 而且可以包含編譯器指示詞, 以協助將平臺特定功能併入共用的程式碼基底中。_

共用專案 (有時也稱為共用資產專案) 可讓您撰寫在多個目標專案 (包括 Xamarin 應用程式) 之間共用的程式碼。

它們支援編譯器指示詞, 因此您可以有條件地包含平臺特定的程式碼, 以編譯成參考共用專案的專案子集。 另外還有 IDE 支援, 可協助管理編譯器指示詞, 並將程式碼在每個應用程式中的外觀視覺化。

如果您過去曾用過檔案連結來共用專案之間的程式碼, 則共用的專案會以類似的方式運作, 但具備更完善的 IDE 支援。

## <a name="what-is-a-shared-project"></a>什麼是共用專案？

不同于其他大部分的專案類型, 共用專案不會有任何輸出 (DLL 格式), 而是會將程式碼編譯成參考它的每個專案。 下圖說明這一點-概念上, 共用專案的完整內容會「複製到」每個參考專案, 並以其本身的一部分進行編譯。

![](shared-projects-images/sharedassetproject.png "共用的專案架構")

共用專案中的程式碼可以包含編譯器指示詞, 這會根據應用程式專案所使用的程式碼 (由圖表中的彩色平臺方塊建議) 來啟用或停用程式碼區段。

共用的專案本身不會自行編譯, 它純粹是當做可包含在其他專案中的原始程式碼檔群組。 當另一個專案參考時, 程式碼會有效地編譯為該專案的*一部分*。 共用的專案無法參考任何其他專案類型 (包括其他共用專案)。

請注意, Android 應用程式專案無法參考其他 Android 應用程式專案-例如, Android 單元測試專案無法參考 Android 應用程式專案。 如需這項限制的詳細資訊, 請參閱此[論壇討論](http://forums.xamarin.com/discussion/comment/98092/)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 逐步解說

本節逐步解說如何使用 Visual Studio for Mac 建立和使用共用專案。 如需完整範例, 請參閱[共用專案範例](#Shared_Project_Example)一節。

## <a name="creating-a-shared-project"></a>建立共用專案

若要建立新的共用專案, 請流覽至 [檔案] **> 新方案 ...** (或以滑鼠右鍵按一下現有的方案, 然後選擇 [**新增] > [加入新專案**]):

[![新增共用專案](shared-projects-images/xs-newsolution-sml.png "新增解決方案")](shared-projects-images/xs-newsolution.png#lightbox)

在下一個畫面上, 選擇專案名稱, 然後按一下 [**建立**]。

新的共用專案如下所示-請注意, 沒有任何參考或元件節點;這些不支援共用專案。

![空白的共用專案](shared-projects-images/xs-empty.png "空白的共用專案")

若要讓共用專案有用, 必須至少有一個可供組建專案 (例如 iOS 或 Android 應用程式或程式庫, 或 PCL 專案) 參考。 當共用的專案沒有任何參考它時, 就不會進行編譯, 因此語法 (或任何其他) 錯誤將不會反白顯示, 直到它被其他內容參考為止。

將參考加入共用專案的方式, 與參考一般程式庫專案的方法相同。 這個螢幕擷取畫面顯示了參考共用專案的 Xamarin. iOS 專案。

![](shared-projects-images/xs-reference.png "共用專案的專案參考")

當另一個程式庫或應用程式參考共用的專案之後, 您就可以建立方案, 並在程式碼中查看任何錯誤。 當_兩個或多個_其他專案參考共用的專案時, [原始程式碼編輯器] 的左上方會出現一個功能表, 顯示選擇哪些專案參考此檔案。

## <a name="shared-project-options"></a>共用的專案選項

當您以滑鼠右鍵按一下共用專案, 並選擇比其他專案類型少的設定**選項**。 由於共用的專案不會進行編譯 (本身就是), 因此您無法設定輸出或編譯器選項、專案設定、元件簽署或自訂命令。 共用專案中的程式碼會從參考它們的任何內容, 有效地繼承這些值。

[**選項**] 畫面如下所示-[專案**名稱**] 和 [**預設命名空間**] 是您通常會變更的兩個設定。

![](shared-projects-images/xs-sharedprojectoptions.png "共用的專案選項")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Visual Studio 逐步解說

本節逐步解說如何使用 Visual Studio 建立和使用共用專案。 如需完整的執行方式, 請參閱[共用專案範例](#Shared_Project_Example)一節。

### <a name="creating-a-shared-project"></a>建立共用專案

若要建立新的共用專案, 請  > 流覽至 [檔案] [**新增** > **專案**]。

在 Visual Studio 2019 的 [**建立新專案**] 頁面上, 于搜尋方塊中輸入**shared** 。 選取 [**共用專案**] 範本, 然後選取 **[下一步]** 。 輸入專案的名稱, 然後選取 [**建立**]。

在 Visual Studio 2017 中, 選取 [**共用專案**] 範本, 然後選擇專案的 [名稱]。

![Visual Studio 2017 中的共用專案範本](shared-projects-images/vs-newsolution.png)

您也可以在方案檔上按一下滑鼠右鍵, 然後選擇 [**加入] > [新增專案**], 將新的共用專案加入現有的方案中。 新的共用專案如下所示 (在加入類別檔案之後)。 請注意, 沒有任何參考或元件節點;這些不支援共用專案。

![](shared-projects-images/vs-empty.png "空白的共用專案")

若要讓共用專案有用, 必須至少有一個可供組建專案 (例如 iOS 或 Android 應用程式或程式庫, 或 PCL 專案) 參考。 當共用的專案沒有任何參考它時, 就不會進行編譯, 因此語法 (或任何其他) 錯誤將不會反白顯示, 直到它被其他內容參考為止。

將參考加入共用專案的方式, 與參考一般程式庫專案的方法相同。 這個螢幕擷取畫面顯示了參考共用專案的 Xamarin. iOS 專案。

![](shared-projects-images/vs-reference.png "共用專案的專案參考")

當另一個程式庫或應用程式參考共用的專案之後, 您就可以建立方案, 並在程式碼中查看任何錯誤。 當_兩個或多個_其他專案參考共用專案時, 原始程式碼編輯器的左上方會出現一個功能表, 以查看哪些專案參考了目前的程式碼檔案。

### <a name="shared-project-properties"></a>共用的專案屬性

當您選取共用專案時, [屬性] 面板中的設定會比其他專案類型少。 由於共用的專案不會進行編譯 (本身就是), 因此您無法設定輸出或編譯器選項、專案設定、元件簽署或自訂命令。 共用專案中的程式碼會從參考它們的任何內容, 有效地繼承這些值。

[**屬性**] 面板如下所示-**根命名空間**是您可以變更的唯一設定。

![](shared-projects-images/vs-sharedprojectproperties.png "共用的專案屬性")

-----

<a name="Shared_Project_Example"/>

## <a name="shared-project-example"></a>共用的專案範例

[Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)範例會使用共用專案來包含 IOS、Android 和 Windows Phone 應用程式所使用的通用程式碼。 `SQLite.cs` 和`TaskRepository.cs`原始程式碼檔都利用編譯器指示詞 (例如 `#if __ANDROID__`), 為每個參考它們的應用程式產生不同的輸出。

完整的解決方案結構如下所示 (分別在 Visual Studio for Mac 和 Visual Studio 中):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](shared-projects-images/xs-examplesolution.png "Visual Studio for Mac 解決方案")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](shared-projects-images/vs-examplesolution.png "Visual Studio 解決方案")

-----

Windows Phone 專案可以從 Visual Studio for Mac 內流覽, 即使 Visual Studio for Mac 中不支援該專案類型進行編譯。

執行中的應用程式如下所示:

![](shared-projects-images/example.png "iOS、Android、Windows Phone 範例")

## <a name="summary"></a>總結

本檔說明共用專案的運作方式、如何在 Visual Studio for Mac 和 Visual Studio 中建立和使用它們, 以及引進了一個簡單的範例應用程式, 示範如何在作用中執行共用的專案。

## <a name="related-links"></a>相關連結

- [Tasky 範例應用程式](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [可移植的類別庫 (範例)](~/cross-platform/app-fundamentals/pcl.md)
- [共用程式碼選項 (範例)](~/cross-platform/app-fundamentals/code-sharing.md)
