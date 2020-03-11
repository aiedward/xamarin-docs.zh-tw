---
title: 繫結 Eclipse 程式庫專案
description: 本逐步解說說明如何使用 Xamarin Android 專案範本來系結 Eclipse Android 程式庫專案。
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 2ac402bf423c9f3fe136d1ba31622d915d2e2eef
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027731"
---
# <a name="binding-an-eclipse-library-project"></a>繫結 Eclipse 程式庫專案

_本逐步解說說明如何使用 Xamarin Android 專案範本來系結 Eclipse Android 程式庫專案。_

## <a name="overview"></a>概觀

只不過.AAR 檔案逐漸成為 Android 程式庫散發的標準，在某些情況下，必須建立*android 程式庫專案*的系結。 Android 程式庫專案是特殊的 Android 專案，其中包含 Android 應用程式專案可以參考的可共用程式碼和資源。 一般而言，當您在 Eclipse IDE 中建立程式庫時，您會系結至 Android 程式庫專案。
本逐步解說提供如何建立 Android 程式庫專案的範例。從 Eclipse 專案的目錄結構 ZIP。

Android 程式庫專案與一般的 Android 專案不同之處是，它們不會編譯成 APK，而且不會自行部署至裝置。 相反地，Android 程式庫專案是要由 Android 應用程式專案參考。 建立 Android 應用程式專案時，會先編譯 Android 程式庫專案。 然後，Android 應用程式專案會吸收到已編譯的 Android 程式庫專案中，並將程式碼和資源納入 APK 進行散發。 基於這項差異，建立 Android 程式庫專案的系結與建立 JAVA 的系結稍有不同。JAR 或。AAR 檔案。

## <a name="walkthrough"></a>逐步介紹

若要在 Xamarin. Android JAVA 系結專案中使用 Android 程式庫專案，首先必須在 Eclipse 中建立 Android 程式庫專案。 下列螢幕擷取畫面顯示編譯之後一個 Android 程式庫專案的範例： 

[Eclipse 中的 ![範例程式庫專案](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

請注意，來自 Android 程式庫專案的原始程式碼已編譯為暫存。名為**android-mapviewballoons**的 jar 檔案，而且資源已經複製到**bin/res/借助電腦**資料夾。 

在 Eclipse 中編譯 Android 程式庫專案之後，就可以使用 Xamarin 的 JAVA 系結專案來系結它。 第一個。必須建立 ZIP 檔案，其中包含 Android 程式庫專案的**bin**和**res**資料夾。 請務必移除中間**借助電腦**子目錄，讓資源位於**bin/res**中。下列螢幕擷取畫面顯示其中一項的內容。ZIP 檔案： 

[![Android 程式庫專案 .zip 的內容](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

該表.然後，ZIP 檔案會新增至 Xamarin. Android JAVA 系結專案，如下列螢幕擷取畫面所示：

[已將 ![Zip 新增至 JAVA 系結專案](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

請注意，的 [建立] 動作。ZIP 檔案已自動設定為**LibraryProjectZip**。

（如果有的話）。Android 程式庫專案所需的 JAR 檔案，應新增至 JAVA 系結程式庫專案的 [ **jar** ] 資料夾，並將 [**組建] 動作**設定為 [ **ReferenceJar**]。 您可以在下列螢幕擷取畫面中看到這種情況的範例： 

[![組建動作設定為 ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

完成這些步驟之後，就可以使用如本檔稍早所述的 Xamarin JAVA 系結專案。

> [!NOTE]
> 目前不支援在其他 Ide 中編譯 Android 程式庫專案。 其他 Ide 可能不會在 [ **bin** ] 資料夾中建立與 Eclipse 相同的目錄結構或檔案。 

## <a name="summary"></a>摘要

在本文中，我們逐步解說了將 Android 程式庫專案系結的程式。 我們已在 Eclipse 中建立 Android 程式庫專案，然後從 Android 程式庫專案的**bin**和**res**資料夾建立 zip 檔案。 接下來，我們使用此 zip 來建立 Xamarin. Android JAVA 系結專案。 
