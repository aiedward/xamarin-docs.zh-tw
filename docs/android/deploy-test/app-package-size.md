---
title: 應用程式的套件大小
description: 本文將探討 Xamarin.Android 應用程式套件的組成組件，以及在開發過程的偵錯和發行階段所能進行有效率套件部署的相關策略。
ms.prod: xamarin
ms.assetid: 8D70CDDD-3D3C-9949-8045-AB8F93D18E74
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: babe45c39f6a69dd9384f3bce8fe28ada31ebfdf
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764420"
---
# <a name="application-package-size"></a>應用程式的套件大小

_本文將探討 Xamarin.Android 應用程式套件的組成組件，以及在開發過程的偵錯和發行階段所能進行有效率套件部署的相關策略。_


## <a name="overview"></a>總覽

Xamarin.Android 使用了各種機制來將套件大小降至最低，同時維持了有效率的偵錯及發行部署過程。 在本文中，我們會查看 Xamarin.Android 的發行及偵錯部署工作流程，以及 Xamarin.Android 平台確保我們建置及發行小型應用程式套件的方式。


## <a name="release-packages"></a>發行套件

若要提供完整包含的應用程式，套件必須包含應用程式、相關聯的程式庫、內容、Mono 執行階段，以及必要的基底類別庫 (BCL) 組件。 例如，若我們使用的是預設的「Hello, World」範本，則完整套件組建的內容會像是這個樣子：

[![連結器之前的套件大小](app-package-size-images/hello-world-package-size-before-linker.png)](app-package-size-images/hello-world-package-size-before-linker.png#lightbox)

相較於我們期望的大小，15.8 MB 是一個較大的下載大小。 問題在於 BCL 程式庫，因為它們包含了 mscorlib、System 及 Mono.Android，其可提供許多執行您應用程式所需要的元件。 然而，它們同時也提供了您應用程式不會使用到的功能，因此最好是能排除這些元件。

當我們為了散發而建置應用程式時，我們會執行一個稱為「連結」的處理序，其會檢查應用程式並移除任何未直接使用到的程式碼。 這個程序與[記憶體回收](~/android/internals/garbage-collection.md)針對堆積配置記憶體所提供的功能相似。 但連結是透過您的程式碼而非物件來操作。 例如，在 System.dll 中有一整個命名空間是專門用來傳送及接收電子郵件，但若您的應用程式沒有使用到此功能，該程式碼就僅只是在浪費空間。 在 Hello World 應用程式執行連結器後，我們的套件看起來如下：

[![連結器之後的套件大小](app-package-size-images/hello-world-package-size-after-linker.png)](app-package-size-images/hello-world-package-size-after-linker.png#lightbox)

如同我們能看見的，它移除了並未使用到且數量相當龐大的 BCL。 請注意，最終 BCL 大小會取決於應用程式實際使用到的內容。 例如，若我們查看一個稱為 ApiDemo 的更龐大應用程式範例，我們可以看到 BCL 元件的大小增加，因為相較於 Hello, World，ApiDemo 使用到更多的 BCL：

![連結之後的 ApiDemo 套件大小](app-package-size-images/api-demo-package-size-after-linker.png)

如同此處所示，您的應用程式套件大小通常會比您的應用程式及其相依性大 2.9 MB。


## <a name="debug-packages"></a>偵錯套件

針對偵錯組建，處理項目的方式會有些不同。 在重複重新部署到裝置時，應用程式必須盡量快速，因此我們會針對速度而最佳化偵錯套件，而非大小。

相較之下，Android 複製及安裝套件的速度較慢，因此我們會希望套件大小能越小越好。 如同我們在上述內容中所討論，其中一個可能的方式便是透過連結器將套件大小減至最低。 然而，連結的過程相當緩慢，並且通常我們只想要部署自上一次部署之後應用程式的變更部分。 為了達到此目的，我們會將應用程式與核心 Xamarin.Android 元件區隔開來。

第一次在裝置上進行偵錯時，我們會複製兩個分別名為「共用執行階段」及「共用平台」的大型套件。 共用執行階段包含 Mono 執行階段及 BCL，共用平台則包含特定 Android API 層級的組件：

[![共用執行階段套件大小](app-package-size-images/shared-runtime-package-size.png)](app-package-size-images/shared-runtime-package-size.png#lightbox)

複製這些核心元件的過程只會執行一次。雖然這會花上一些時間，但它會允許任何後續在偵錯模式中執行的應用程式使用它們。 最後，我們會複製小而快速的實際應用程式：

![實際應用程式很小](app-package-size-images/hello-world-debug-application-no-link.png)

### <a name="fast-assembly-deployment"></a>快速組件部署

[快速部署組件] 建置選項可透過不在應用程式套件中包含組件、直接在裝置上一次性的安裝組件，然後只複製自上一次部署之後修改過的檔案，來更進一步的降低偵錯安裝套件的大小。

若要啟用 [快速部署組件]，請執行下列作業：

1.  以滑鼠右鍵按一下方案總管中的 Android 專案，然後選取 [選項]。

2.  在 [專案選項] 對話方塊中，選取 [Android 建置]：  

    ![Android 建置專案選項](app-package-size-images/fastdev0.png)

3.  勾選 [使用共用 Mono 執行階段] 核取方塊，以及 [快速部署組件] 核取方塊：  

    ![[封裝] 索引標籤下已選取的核取方塊](app-package-size-images/fastdev.png)

4.  按一下 [確定] 按鈕來儲存變更，並關閉 [專案選項] 對話方塊。


下一次應用程式針對偵錯進行建置時，組件便會直接安裝在裝置上 (若尚未安裝的話)，並且安裝在裝置上的應用程式套件將會更小 (因為其不包含組件)。 這會縮短將變更套用到應用程式及執行測試的時間。

透過忍受第一次長時間的共用執行階段與共用平台部署，之後每一次我們變更應用程式時，便可以更快速且更輕鬆的部署新版本，使得變更/部署/執行的循環變得更快。


## <a name="summary"></a>總結

在本文中，我們檢查 Xamarin.Android 發行與偵錯設定檔封裝的 Facet。 此外，我們也查看 Mono for Android 平台用來在開發過程的偵錯及發行階段協助有效率套件部署的策略。
