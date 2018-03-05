---
title: "繫結的 Eclipse 程式庫專案"
description: "這個逐步解說將說明如何使用繫結的 Eclipse Android 程式庫專案的 Xamarin.Android 專案範本。"
ms.topic: article
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/14/2017
ms.openlocfilehash: 2048056415e0969e13e305b1dbba8bdb7ffabd30
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="binding-an-eclipse-library-project"></a>繫結的 Eclipse 程式庫專案

_這個逐步解說將說明如何使用繫結的 Eclipse Android 程式庫專案的 Xamarin.Android 專案範本。_

<a name=overview />

## <a name="overview"></a>總覽

雖然。AAR 檔案會越來越 Android 程式庫發佈範中，在某些情況下會需要建立的繫結*Android 程式庫專案*。 Android 程式庫專案會包含可共用的程式碼的特殊 Android 專案和 Android 的應用程式專案可以參考的資源。 一般而言，您建立繫結至 Android 程式庫的專案程式庫 Eclipse IDE 中。
本逐步解說提供有關如何建立 Android 程式庫專案的範例。ZIP 從 Eclipse 專案的目錄結構。

Android 程式庫專案會不同於一般的 Android 專案，因為它們不會編譯為 APK，而不是，在他們自己，可部署至裝置。 相反地，Android 程式庫專案的目的在於 Android 應用程式專案所參考。 建立 Android 應用程式專案時，就會先編譯 Android 程式庫專案。 Android 應用程式專案接著被吸收至已編譯的 Android 程式庫專案，並將程式碼和資源包含發佈的 APK。 由於此差別，建立 Android 程式庫專案的繫結是稍有不同於建立 Java 的繫結。JAR 或。AAR 檔案。


<a name="Walkthrough" />

## <a name="walkthrough"></a>逐步解說

Xamarin.Android Java 繫結的專案中使用的 Android 程式庫專案中，它是建置 Android 程式庫專案，在 Eclipse 中的第一個所需。 下列螢幕擷取畫面會顯示一個 Android 程式庫專案的範例，在編譯之後： 

[ ![在 Eclipse 中的範例程式庫專案](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png)

請注意，Android 程式庫專案中的原始程式碼編譯為暫存。名為 JAR 檔案**android mapviewballoons.jar**，且資源已複製到**無暇/bin/res**資料夾。 

在 Eclipse 中編譯 Android 程式庫專案之後，它可以再繫結使用的繫結 Xamarin.Android Java 專案。 第一次。必須建立 ZIP 檔，其中包含**bin**和**res** Android 程式庫專案的資料夾。 請務必移除的中介**無暇**子目錄，讓資源位於**bin/res**。下列螢幕擷取畫面顯示內容的其中一個這類。ZIP 檔案： 

[ ![Android 程式庫專案.zip 的內容](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png)

這。ZIP 檔案然後將新增至繫結 Xamarin.Android Java 專案，如下列螢幕擷取畫面所示：

[ ![Zip 加入 Java 繫結的專案](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png)

請注意，建置動作。ZIP 檔案會自動設定為**LibraryProjectZip**。

如果有的話。JAR 檔案所需的 Android 程式庫專案，應該將它們加入**Jar** Java 繫結的程式庫專案的資料夾和**建置動作**設**ReferenceJar**. 下列螢幕擷取畫面中，可以看到此動作的範例： 

[ ![建置動作設定為 ReferenceJar](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png)

完成這些步驟之後，繫結 Xamarin.Android Java 專案可以當做本文件稍早所述。

> [!NOTE]
> **請注意**： 此時不支援在其他 Ide 編譯 Android 程式庫專案。 相同的目錄結構或中的檔案，可能無法建立其他 Ide **bin** Eclipse 的資料夾。 

<a name="Summary" /> 

## <a name="summary"></a>總結

在本文中，我們逐步進行作業的繫結的 Android 程式庫專案的程序。 我們已經建置 Android 程式庫專案在 Eclipse 中，則我們建立 zip 檔案，從**bin**和**res** Android 程式庫專案的資料夾。 接下來，我們可以使用這個 zip 建立 Xamarin.Android Java 繫結的專案。 

