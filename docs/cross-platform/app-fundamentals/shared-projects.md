---
title: 使用共用的專案共用程式碼
description: 共用的專案可讓您撰寫一些不同的應用程式專案所參考的通用程式碼。 此程式碼會編譯成每個參考專案的一部分，並且可以包含編譯器指示詞，以協助將平台專用功能併入到共用程式碼基底中。
ms.prod: xamarin
ms.assetid: 191c71fb-44a4-4e6c-af4b-7b1107dce6af
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: bd8314e0f84dd74477782a1cfdcb6b91953ecdcd
ms.sourcegitcommit: 827daa78c090bf79a1b55da45bb8012a1723b720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997516"
---
# <a name="shared-projects-code-sharing"></a>共用的專案程式碼共用

_共用的專案可讓您撰寫一些不同的應用程式專案所參考的通用程式碼。程式碼會編譯為每個參考專案的一部分，而且可以包含編譯器指示詞，以協助將平臺特定功能併入共用程式碼基底。_

共用的專案 (有時也稱為共用的資產專案) 可讓您撰寫可在多個目標專案之間共用的程式碼，包括 Xamarin 應用程式。

它們支援編譯器指示詞，因此您可以有條件地包含要編譯成參考共用專案之專案子集的平臺特定程式碼。 此外，也有 IDE 支援來協助管理編譯器指示詞，並將程式碼在每個應用程式中的外觀視覺化。

如果您過去曾使用檔案連結來共用專案之間的程式碼，共用的專案會以類似的方式運作，但支援的 IDE 也有許多改進。

## <a name="what-is-a-shared-project"></a>什麼是共用專案？

不同于其他大部分的專案類型，共用專案不會有任何) 的輸出 (，而是會將程式碼編譯到參考它的每個專案中。 如下圖所示，在概念上，共用專案的整個內容會「複製到」每個參考專案，並編譯為它們的一部分。

![共用的專案架構](shared-projects-images/sharedassetproject.png)

共用專案中的程式碼可以包含編譯器指示詞，這些指示詞會根據應用程式專案是使用程式碼來啟用或停用程式碼區段，這是由圖表中的彩色平臺方塊所建議。

共用的專案不會單獨進行編譯，而是純粹為可包含在其他專案中的原始程式碼檔群組。 當另一個專案參考時，此程式碼會在該 *專案中有效* 編譯。 共用專案無法參考任何其他專案類型 (包括) 的其他共用專案。

請注意，Android 應用程式專案無法參考其他 Android 應用程式專案，例如 Android 單元測試專案無法參考 Android 應用程式專案。 如需這項限制的詳細資訊，請參閱此 [論壇討論](https://forums.xamarin.com/discussion/comment/98092/)。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 逐步解說

本節將逐步解說如何使用 Visual Studio for Mac 建立和使用共用專案。 如需完整範例，請參閱「 [共用專案範例](#Shared_Project_Example) 」一節。

## <a name="creating-a-shared-project"></a>建立共用專案

若要建立新的共用專案，請流覽至 [ **新增方案 >** ] (或以滑鼠右鍵按一下現有的方案，然後選擇 [ **加入新專案] > [新增專案 ...** ]) ：

[![新增共用專案](shared-projects-images/xs-newsolution-sml.png "新增方案")](shared-projects-images/xs-newsolution.png#lightbox)

在下一個畫面上，選擇專案名稱，然後按一下 [ **建立**]。

新的共用專案如下所示-請注意，沒有任何參考或元件節點;共用的專案不支援這些專案。

![空白的共用專案](shared-projects-images/xs-empty.png "空白的共用專案")

若要讓共用的專案發揮效用，至少需要有一個可進行 (的專案，例如 iOS 或 Android 應用程式或程式庫，或) 的 PCL 專案。 當共用的專案未參考任何專案時，就不會進行編譯，因此語法 (或任何其他) 錯誤都不會反白顯示，直到它被其他專案參考為止。

將參考新增至共用專案的方式，與參考一般程式庫專案的方式相同。 此螢幕擷取畫面顯示參考共用專案的 Xamarin. iOS 專案。

![共用專案的專案參考](shared-projects-images/xs-reference.png)

當另一個程式庫或應用程式參考共用專案之後，您就可以建立方案並查看程式碼中的任何錯誤。 當共用的專案由其他 _兩個或更多_ 專案參考時，[原始程式碼編輯器] 左上角會出現一個功能表，顯示哪些專案參考此檔案。

## <a name="shared-project-options"></a>共用的專案選項

當您以滑鼠右鍵按一下共用的專案，並選擇 [ **選項** ] 時，設定會比其他專案類型少。 因為共用的專案不會在自己的) 上 (編譯，所以您無法設定輸出或編譯器選項、專案設定、元件簽署或自訂命令。 共用專案中的程式碼實際上會從任何參考它們的值繼承這些值。

**選項**畫面如下所示-專案**名稱**和**預設命名空間**是您通常會變更的兩個設定。

![共用的專案選項](shared-projects-images/xs-sharedprojectoptions.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-walkthrough"></a>Visual Studio 逐步解說

本節將逐步解說如何使用 Visual Studio 建立和使用共用專案。 請參閱「 [共用專案範例](#Shared_Project_Example) 」一節，以取得完整的執行。

### <a name="creating-a-shared-project"></a>建立共用專案

若要建立新的共用專案，請**流覽至 [** 檔案  >  **新增**  >  **專案**]。

在 Visual Studio 2019 的 [**建立新專案**] 頁面上，于 [搜尋] 方塊中輸入**shared** 。 選取 **共用的專案** 範本，然後選取 **[下一步]**。 輸入專案的名稱，然後選取 [ **建立**]。

在 Visual Studio 2017 中，選取 **共用的專案** 範本，然後選擇專案的名稱。

![Visual Studio 2017 中的共用專案範本](shared-projects-images/vs-newsolution.png)

您也可以在方案檔上按一下滑鼠右鍵，然後選擇 [ **加入] > [新增專案**]，將新的共用專案加入至現有的方案。 新的共用專案如下所示 (新增) 類別檔案之後。 請注意，沒有任何參考或元件節點;共用的專案不支援這些專案。

![空白的共用專案](shared-projects-images/vs-empty.png)

若要讓共用的專案發揮效用，至少需要有一個可進行 (的專案，例如 iOS 或 Android 應用程式或程式庫，或) 的 PCL 專案。 當共用的專案未參考任何專案時，就不會進行編譯，因此語法 (或任何其他) 錯誤都不會反白顯示，直到它被其他專案參考為止。

將參考新增至共用專案的方式，與參考一般程式庫專案的方式相同。 此螢幕擷取畫面顯示參考共用專案的 Xamarin. iOS 專案。

![共用專案的專案參考](shared-projects-images/vs-reference.png)

當另一個程式庫或應用程式參考共用專案之後，您就可以建立方案並查看程式碼中的任何錯誤。 當共用的專案是由 _兩個或更多個_ 其他專案參考時，[原始程式碼編輯器] 左上角會出現一個功能表，以查看哪些專案參考目前的程式碼檔案。

### <a name="shared-project-properties"></a>共用的專案屬性

當您選取共用專案時，[屬性] 面板中的設定會比其他專案類型少。 因為共用的專案不會在自己的) 上 (編譯，所以您無法設定輸出或編譯器選項、專案設定、元件簽署或自訂命令。 共用專案中的程式碼實際上會從任何參考它們的值繼承這些值。

[ **屬性** ] 面板如下所示- **根命名空間** 是您唯一可以變更的設定。

![共用的專案屬性](shared-projects-images/vs-sharedprojectproperties.png)

-----

<a name="Shared_Project_Example"></a>

## <a name="shared-project-example"></a>共用的專案範例

[Tasky](https://github.com/xamarin/mobile-samples/tree/master/Tasky)範例會使用共用專案來包含 IOS、Android 和 Windows Phone 應用程式所使用的通用程式碼。 `SQLite.cs`和原始程式碼檔案都會利用編譯器指示詞， `TaskRepository.cs` (例如 `#if __ANDROID__`) 為參考它們的每個應用程式產生不同的輸出。

完整的解決方案結構會分別顯示在下列 (的 Visual Studio for Mac 和 Visual Studio) ：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![Visual Studio for Mac 解決方案](shared-projects-images/xs-examplesolution.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Visual Studio 解決方案](shared-projects-images/vs-examplesolution.png)

-----

您可以從 Visual Studio for Mac 內流覽 Windows Phone 專案，即使該專案類型不支援在 Visual Studio for Mac 中進行編譯也是一樣。

正在執行的應用程式如下所示：

![iOS、Android、Windows Phone 範例](shared-projects-images/example.png)

## <a name="summary"></a>總結

本檔說明了共用專案的運作方式、如何在 Visual Studio for Mac 和 Visual Studio 中建立及使用它們，並引進簡單的範例應用程式來示範共用專案的實際運作方式。

## <a name="related-links"></a>相關連結

- [Tasky 範例應用程式](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [可移植類別庫 (範例) ](~/cross-platform/app-fundamentals/pcl.md)
- [ (範例) 共用程式碼選項 ](~/cross-platform/app-fundamentals/code-sharing.md)
