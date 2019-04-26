---
title: 繫結 Eclipse 程式庫專案
description: 本逐步解說說明如何使用 Xamarin.Android 專案範本繫結 Eclipse Android 程式庫專案。
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a5887748eda8af89b9215e3e121db592dfa73935
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957629"
---
# <a name="binding-an-eclipse-library-project"></a>繫結 Eclipse 程式庫專案

_本逐步解說說明如何使用 Xamarin.Android 專案範本繫結 Eclipse Android 程式庫專案。_


## <a name="overview"></a>總覽

雖然。AAR 檔案越來越容易成為 Android 程式庫發佈的範數，在某些情況下就必須建立的繫結*Android 程式庫專案*。 Android 程式庫專案會包含可共用的程式碼的特殊 Android 專案和 Android 應用程式專案可以參考的資源。 一般而言，您繫結至 Android 程式庫的專案在 Eclipse IDE 中建立程式庫時。
本逐步解說提供如何建立 Android 程式庫專案的範例。從 Eclipse 專案的目錄結構壓縮。

Android 程式庫專案會不同於一般的 Android 專案，因為它們不會編譯的 apk，，則不會，靠自己，可部署至裝置。 相反地，Android 程式庫專案的目的在於 Android 應用程式專案所參考。 建置 Android 應用程式專案時，就會先編譯 Android 程式庫專案。 Android 應用程式專案接著被吸收到編譯的 Android 程式庫專案並散發 APK 中納入程式碼和資源。 由於此差別，建立繫結 Android 程式庫專案會稍有不同於建立適用於 Java 的繫結。JAR 或。AAR 檔案。



## <a name="walkthrough"></a>逐步解說

若要使用 Xamarin.Android Java 繫結的專案中的 Android 程式庫專案就來建置 Android 程式庫專案在 Eclipse 中的第一個必要。 下列螢幕擷取畫面會顯示一個 Android 程式庫專案的範例在編譯之後： 

[![在 Eclipse 中的範例程式庫專案](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

請注意，Android 程式庫專案中的原始程式碼編譯為暫存。名為 JAR 檔案**android mapviewballoons.jar**，且資源已複製到**bin/res/無暇**資料夾。 

一旦編譯 Android 程式庫專案在 Eclipse 中，它可以再繫結使用 Xamarin.Android Java 繫結的專案。 第一次。必須建立 ZIP 檔案，其中包含**筒**並**res** Android 程式庫專案的資料夾。 很重要，您就會移除中間**無暇**子目錄，讓資源位於**bin/res**。下列螢幕擷取畫面顯示內容的其中一個這類。ZIP 檔案： 

[![Android 程式庫專案的.zip 的內容](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

這。然後，Xamarin.Android Java 繫結的專案，來新增 ZIP 檔案，如下列螢幕擷取畫面所示：

[![新增至 Java 繫結專案的 zip](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

請注意，建置動作。ZIP 檔案會自動設定為**LibraryProjectZip**。

如果有的話。Android 程式庫專案所需的 JAR 檔案，應該將它們加入**Jar** Java 繫結程式庫專案資料夾並**建置動作**設定為**ReferenceJar**. 以下螢幕擷取畫面中，就可以看到這類範例： 

[![建置動作設為 ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

完成這些步驟之後，Xamarin.Android Java 繫結專案便能以稍早在本文中所述。

> [!NOTE]
> 目前不支援在其他 Ide 編譯 Android 程式庫專案。 相同的目錄結構或中的檔案，可能無法建立其他 Ide **bin** Eclipse 的資料夾。 


## <a name="summary"></a>總結

在本文中，我們逐一檢視過的 Android 程式庫專案的繫結程序。 我們已經建置 Android 程式庫專案在 Eclipse 中，則我們建立的 zip 檔案**筒**並**res** Android 程式庫專案的資料夾。 接下來，我們可以使用這個 zip 建立 Xamarin.Android Java 繫結的專案。 

