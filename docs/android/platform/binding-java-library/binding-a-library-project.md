---
title: 繫結 Eclipse 程式庫專案
description: 本演練將介紹如何使用 Xamarin.Android 專案範本綁定 Eclipse Android 庫專案。
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 2ac402bf423c9f3fe136d1ba31622d915d2e2eef
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027731"
---
# <a name="binding-an-eclipse-library-project"></a>繫結 Eclipse 程式庫專案

_本演練將介紹如何使用 Xamarin.Android 專案範本綁定 Eclipse Android 庫專案。_

## <a name="overview"></a>概觀

雖然。AAR檔正日益成為Android庫分發的常態,在某些情況下,有必要為*Android庫項目*創建綁定。 Android 庫專案是特殊的 Android 專案,其中包含可共用的代碼和資源,這些代碼和資源可以由 Android 應用程式專案引用。 通常,在 Eclipse IDE 中創建庫時,您將綁定到 Android 庫專案。
本演練提供了如何創建 Android 庫專案的範例。從 Eclipse 專案的目錄結構 ZIP。

Android 庫專案不同於常規的 Android 專案,因為它們未編譯為 APK,並且本身無法部署到設備。 相反,Android 庫專案被 Android 應用程式專案引用。 構建 Android 應用程式專案時,首先編譯 Android 庫專案。 然後,Android 應用程式專案將被吸收到編譯的 Android 庫專案中,並將代碼和資源包含在 APK 中進行分發。 由於這種差異,為Android庫項目創建綁定與為JAVA創建綁定略有不同。JAR 或 。AAR 檔。

## <a name="walkthrough"></a>逐步介紹

要在 Xamarin.Android Java 綁定專案中使用 Android 庫專案,首先有必要在 Eclipse 中構建 Android 庫專案。 以下螢幕截圖顯示了編譯後一個 Android 庫專案的範例: 

[![Eclipse 中的範例庫專案](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

請注意,Android 函式庫專案的原始碼已編譯為臨時 。JAR檔名為**android-mapview氣球.jar,** 並且資源已複製到**bin/res/crunch**資料夾。 

在 Eclipse 中編譯 Android 庫專案後,可以使用 Xamarin.Android Java 綁定專案綁定該專案。 首先. .必須建立包含 Android 庫專案的**bin**和**res**資料夾的 ZIP 檔。 請務必刪除中間**的緊縮子**目錄,以便資源駐留在**bin/res**中。下面的屏幕截圖顯示了此類內容。ZIP 檔案: 

[![Android 函式庫項目的內容 .zip](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

這。然後 ZIP 檔將新增到 Xamarin.Android Java 綁定專案,如以下螢幕截圖所示:

[![Zip 新增到 Java 繫結項目](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

請注意,的生成操作。ZIP 檔已自動設置為**庫工程Zip。**

如果有 。Android 庫專案所需的 JAR 檔,應添加到 JAVA 綁定庫專案的**Jars**資料夾和到**參考Jar**的**生成操作**集。 下面的螢幕截圖中可以看到這方面的範例: 

[![產生操作設定為參考 Jar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

完成這些步驟后,可以使用 Xamarin.Android JAva 綁定專案,如本文檔前面所述。

> [!NOTE]
> 目前不支援在其他 IDA 中編譯 Android 庫專案。 其他 ID 可能不能建立與 Eclipse 相同的目錄結構或**檔。** 

## <a name="summary"></a>總結

在本文中,我們介紹了綁定 Android 庫項目的過程。 我們在Eclipse中構建了Android庫項目,然後從Android庫專案的**bin**和**res**資料夾中創建了一個 zip 檔。 接下來,我們使用此 zip 創建了 Xamarin.安卓 Java 綁定專案。 
