---
title: 解析程式庫安裝錯誤
description: 在某些情況下, 您可能會在安裝 Android 支援程式庫時收到錯誤。 本指南提供一些常見錯誤的因應措施。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/14/2018
ms.openlocfilehash: d3face5db37e22fe7acceaa975c3327bb02b78ab
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523330"
---
# <a name="resolving-library-installation-errors"></a>解析程式庫安裝錯誤

_在某些情況下, 您可能會在安裝 Android 支援程式庫時收到錯誤。本指南提供一些常見錯誤的因應措施。_

## <a name="overview"></a>總覽

建立 Xamarin Android 應用程式專案時, 您可能會在 Visual Studio 或 Visual Studio for Mac 嘗試下載並安裝相依性程式庫時收到組建錯誤。 這些錯誤中的許多都是由網路連線問題、檔案損毀或版本設定問題所造成。 本指南說明最常見的支援程式庫安裝錯誤, 並提供解決這些問題的步驟, 並讓您的應用程式專案重新建立。 

 
 
## <a name="errors-while-downloading-m2repository"></a>下載 m2Repository 時發生錯誤

參考 Android 支援程式庫或 Google Play 服務的 NuGet 套件時, 您可能會看到**m2repository**錯誤。 該錯誤訊息類似如下：

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

此範例適用于**android\_m2repository\_r16**, 但您可能會看到不同版本的相同錯誤訊息, 例如 **\_android m2repository\_r18**或**android\_m2repository\_r25**。 



### <a name="automatic-recovery-from-m2repository-errors"></a>從 m2repository 錯誤自動復原 

通常, 您可以藉由刪除有問題的程式庫並根據下列步驟重建, 來補救此問題: 

1. 流覽至電腦上的支援程式庫目錄:

    - 在 Windows 上, 支援程式庫位於**C:\\Users\\ _username_ \\AppData\\Local\\Xamarin**。 

    - 在 Mac OS X 上, 支援程式庫位於 **/Users/_username_/.local/share/Xamarin**。 

2. 找出對應于錯誤訊息的程式庫和版本資料夾。 例如, 上述錯誤訊息的程式庫和版本資料夾位於**Android。支援\\v4 22.2.1**:

    [![22.2.1 支援程式庫的範例資料夾位置](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. 刪除版本資料夾的內容。 請務必移除 **.zip**檔案以及此資料夾中的**內容**和**內嵌**子目錄。 如上所示的範例錯誤訊息, 將會刪除這個螢幕擷取畫面中所顯示的檔案和子目錄 (**content**、 **embedded**和**android_m2repository_r16 .zip**):

    [![22.2.1 支援程式庫資料夾的範例內容](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   請注意, 刪除此資料夾的*整個*內容是很重要的。 雖然此資料夾一開始可能會包含「遺失 **」\_的\_android m2repository r16**檔案, 但此檔案可能已部分下載或損毀。

4. 重建專案&ndash;執行此動作會導致組建程式重新下載遺失的程式庫。

在大部分情況下, 這些步驟會解決組建錯誤, 並允許您繼續進行。 如果刪除此媒體櫃無法解決組建錯誤, 您必須手動下載並安裝 **\_android m2repository\_r_nn_** , 如下一節所述。 



### <a name="manually-downloading-m2repository"></a>手動下載 m2repository

如果您嘗試使用上述自動復原步驟, 但仍有組建錯誤, 您可以手動下載**android\_m2repository\_r_nn_ .zip**檔案 (使用網頁瀏覽器), 並根據下列步驟進行安裝. 如果您的開發電腦上沒有網際網路存取, 但是您可以使用不同的電腦下載封存, 這個程式也很有用。 

1. 下載與下列清單中所提供之錯誤訊息&ndash;連結對應的**android\_m2repository\_r_nn_** 檔案 (以及每個連結 URL 的對應 MD5 雜湊):

    - [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    - [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    - [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    - [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    - [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    - [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    - [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    - [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    如果**m2repository**封存未顯示在此表格中, 您可以在要下載的**m2repository**名稱前面 **https://dl-ssl.google.com/android/repository/** 加上, 以建立下載 URL。 例如，使用 **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** 下載 **android\_ m2repository\_ r10.zip**。

2. 將檔案重新命名為下載 URL 的對應 MD5 雜湊, 如上表所示。 例如, 如果您下載了**android\_m2repository\_r25**, 請將它重新命名為**0B3F1796C97C707339FB13AE8507AF50 .zip**。 如果下載的檔案之下載 URL 的 MD5 雜湊不會顯示在表格中, 您可以使用[線上 MD5](http://www.webconfs.com/online-md5-generator.php)產生器, 將 url 轉換成 MD5 雜湊字串。 

3. 將檔案複製到 Xamarin **zips**資料夾: 

    - 在 Windows 上, 此資料夾位於**C:\\Users\\ ***username*** \\AppData\\ \\Local Xamarin\\zips**。 

    - 在 Mac OS X 上, 此資料夾位於 **/Users/***username***/.local/share/Xamarin/zips**。 

    例如, 下列螢幕擷取畫面說明當 **\_android m2repository\_r16**下載並重新命名為 Windows 上下載 URL 的 MD5 雜湊時的結果:

    [![要重新命名為0595E577D19D31708195A83087881EE6 的 r16 存放庫範例](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)


如果此程式無法解決組建錯誤, 您必須手動下載**android\_m2repository\_r_nn_ .zip**檔案, 將它解壓縮, 然後安裝其內容, 如下一節所述。 


### <a name="manually-downloading-and-installing-m2repository-files"></a>手動下載並安裝 m2repository 檔案

從**m2repository**錯誤復原的完整手動程式需要下載**android\_ \_m2repository r_nn_ .zip**檔案 (使用網頁瀏覽器)、將其解壓縮, 並將其內容複寫到支援電腦上的媒體櫃目錄。 在下列範例中, 我們將從這個錯誤訊息中復原: 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

使用下列步驟來下載**m2repository**並安裝其內容:

1. 刪除對應于錯誤訊息之程式庫資料夾的內容。 例如, 在上述錯誤訊息中, 您會刪除 **\\C: Users 使用者 \\ \\名稱 AppData\\本機\\Xamarin\\Android. 支援 v4的內容23.1.1.0\\** 。 
    如先前所述, 您必須刪除此目錄的完整內容:

    [![從23.1.1.0 資料夾刪除 content、embedded 和 android_m2repository 資料夾](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. 從 Google 下載與錯誤訊息對應的**android\_m2repository\_r_nn_ .zip**檔案 (請參閱上一節中有關連結的表格)。

3. 將此 **.zip**封存檔解壓縮到任何位置 (例如桌面)。 這應該會建立一個對應至 **.zip**封存名稱的目錄。 在此目錄中, 您應該會找到名為**m2repository**的子目錄: 

    [![在解壓縮的 zip 封存中找到 m2repository 資料夾](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. 在您于步驟1中清除的版本程式庫目錄中, 重新建立**內容**和**內嵌**的子目錄。 例如, 下列螢幕擷取畫面說明在 **\_android m2repository\_r25**的**23.1.1.0**資料夾中建立的**內容**和**內嵌**的子目錄: 

    [![在23.1.1.0 資料夾中建立內容和內嵌資料夾](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. 將**m2repository**從解壓縮的 **.zip**複製到您在上一個步驟中建立的**content**目錄: 

    [![複製到 23.1.1.0/content 資料夾的 m2repository 螢幕擷取畫面](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. 在解壓縮的 **.zip**目錄中, 流覽至**m2repository\\com\\android\\支援\\-v4** , 並開啟對應于上方所建立之版本號碼的資料夾 (在此範例中為**23.1.1**):

    [![支援 v4/23.1.1 資料夾中所含檔案的範例清單](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. 將此資料夾中的所有檔案複製到步驟4中建立的**embedded**目錄:

    [![複製到 23.1.1.0/embedded 資料夾的檔案範例](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. 確認已複製所有檔案。 **內嵌**目錄現在應該包含檔案, 例如 **.jar**、 **. aar**和**pom**。

9. 將任何已解壓縮的**aar**檔案的內容解壓縮至**內嵌**目錄。 在 Windows 上, 將 **.zip**副檔名附加至**aar**檔案, 將它開啟, 然後將內容複寫到**內嵌**目錄。
    在 macOS 上, 使用終端機中的 [**解壓縮**] 命令 (例如,**解壓縮檔案. aar**) 將**aar**檔案解壓縮。

此時, 您已手動安裝遺失的元件, 而您的專案應該會建立而不會發生錯誤。 如果不是, 請確認您已下載與錯誤訊息中的版本完全對應的**m2repository**封存版本, 並確認您已在正確的位置安裝其內容, 如上述步驟所述。 



## <a name="summary"></a>總結 

本文說明如何從自動下載和安裝相依性程式庫期間可能發生的常見錯誤中復原。 文中說明了如何刪除有問題的程式庫, 並重建專案以重新下載並重新安裝程式庫。 文中說明如何下載程式庫, 並將它安裝在**zips**資料夾中。 另外也說明了手動下載和安裝必要檔案的程式, 以解決無法透過自動方式解決的問題。 
