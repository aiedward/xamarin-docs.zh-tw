---
title: "解析程式庫安裝錯誤"
description: "在某些情況下，您可能會發生錯誤時安裝的 Android 支援程式庫。 本指南提供的一些常見錯誤的因應措施。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 2df101615ed512d362fc065a1bb7080f3fd3bb33
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="resolving-library-installation-errors"></a>解析程式庫安裝錯誤

_在某些情況下，您可能會發生錯誤時安裝的 Android 支援程式庫。本指南提供的一些常見錯誤的因應措施。_

## <a name="overview"></a>總覽

同時建置的 Xamarin.Android 應用程式專案，您可能會發生建置錯誤時 Visual Studio 或 Visual Studio for Mac 嘗試下載並安裝相依程式庫。 許多這些錯誤被因網路連線問題、 檔案損毀或版本控制問題。 本指南說明最常見的支援程式庫安裝錯誤，並提供解決這些問題，並取得一次建置應用程式專案的步驟。 

 
<a name="m2repository" />
 
## <a name="errors-while-downloading-m2repository"></a>下載 m2Repository 時發生錯誤

您可能會看到**m2repository**參考 Android 支援程式庫或 Google Play 服務的 NuGet 封裝時發生錯誤。 該錯誤訊息類似如下：

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

這個範例適用於**android\_m2repository\_r16**，但您可能會看到此相同的錯誤訊息為不同版本，例如**android\_m2repository\_r18**或**android\_m2repository\_r25**。 


<a name="automatic" /> 

### <a name="automatic-recovery-from-m2repository-errors"></a>從 m2repository 錯誤自動復原 

通常，這個問題可以進行補救，以免藉由刪除有問題的程式庫，並重建根據下列步驟： 

1. 瀏覽至您的電腦上的支援程式庫目錄：

    -   在 Windows 中，支援程式庫位於**c:\\使用者\\_username_\\AppData\\本機\\Xamarin**。 

    -   在 Mac OS X 上，支援程式庫位於**/Users/_username_/.local/share/Xamarin**。 

2. 尋找對應至錯誤訊息的程式庫和版本的資料夾。 例如，上述錯誤訊息的程式庫及版本資料夾是位於**Android.Support.v4\\22.2.1**:

    [![支援程式庫的 22.2.1 的範例資料夾位置](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png)

3. 刪除版本資料夾的內容。 請務必移除**.zip**檔案以及**內容**和**內嵌**這個資料夾內的子目錄。 範例錯誤訊息如上所示，檔案和子目錄這個螢幕擷取畫面所示 (**內容**，**內嵌**，和**android_m2repository_r16.zip**) 要刪除：

    [![範例內容 22.2.1 支援文件庫資料夾](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png)

   請注意，若要刪除*整個*此資料夾的內容。 雖然此資料夾可能一開始包含 「 遺失 」 **android\_m2repository\_r16.zip**檔案，這個檔案可能已部分下載或已損毀。

4. 重建專案&ndash;這樣做會導致建置處理序重新下載遺漏的程式庫。

在大部分情況下，這些步驟將解決建置錯誤，並讓您繼續進行。 如果刪除此程式庫無法解決建置錯誤，則您必須手動下載並安裝**android\_m2repository\_r_nn_.zip**檔案下一節中所述。 


<a name="download" /> 

### <a name="manually-downloading-m2repository"></a>手動下載 m2repository

如果您嘗試使用上面的自動復原步驟，並仍然有建置錯誤，可以手動下載**android\_m2repository\_r_nn_.zip**檔案 （使用網頁瀏覽器），並根據進行安裝下列步驟。 如果在開發電腦上沒有網際網路存取，但是您可以下載封存使用不同的電腦，此程序也很有用。 

1.  下載**android\_m2repository\_r_nn_.zip**對應至錯誤訊息檔案&ndash;（以及每個連結的對應的 MD5 雜湊表中會提供連結URL):

    -   [android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    -   [android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    -   [android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    -   [android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    -   [android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    -   [android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    -   [android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    -   [android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    -   [android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    -   [android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    -   [android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    -   [android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    -   [android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    -   [android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    -   [android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    -   [android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    -   [android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    -   [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    如果**m2repository**封存不會顯示在此資料表中，您可以建立的下載 URL 前面加上**https://dl-ssl.google.com/android/repository/**名稱**m2repository**下載。 例如，使用**https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip**下載**android\_m2repository\_r10.zip**.

2.  將檔案重新命名的上表所示的下載 URL 對應的 MD5 雜湊。 例如，如果您已經下載**android\_m2repository\_r25.zip**，它重新命名為**0B3F1796C97C707339FB13AE8507AF50.zip**。 如果下載檔案的下載 URL 的 MD5 雜湊不會顯示在資料表中，您可以使用[線上 MD5 產生器](http://www.webconfs.com/online-md5-generator.php)將 URL 轉換成 MD5 雜湊字串。 

3.  將檔案複製到在 Xamarin**送達**資料夾： 

    -   在 Windows 中，此資料夾位於**c:\\使用者\\***username***\\AppData\\本機\\Xamarin\\送達**。 

    -   在 Mac OS X 上，此資料夾位於**/Users/***username***/.local/share/Xamarin/zips**。 

    例如，下列螢幕擷取畫面說明結果時**android\_m2repository\_r16.zip**會下載並重新命名為其下載 URL，在 Windows 上的 MD5 雜湊：

    [![正在重新命名為 0595E577D19D31708195A83087881EE6.zip r16.zip 儲存機制的範例](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png)


如果此程序無法解決建置錯誤，您必須手動下載**android\_m2repository\_r_nn_.zip**檔案，解壓縮，並安裝它的內容下, 一節中所述。 


<a name="install" /> 

### <a name="manually-downloading-and-installing-m2repository-files"></a>手動下載及安裝 m2repository 檔案

完全手動的程序，從復原**m2repository**錯誤需要下載**android\_m2repository\_r_nn_.zip**檔案 （使用網頁瀏覽器），解壓縮它，並將它的內容複製到您的電腦上的支援程式庫目錄。 在下列範例中，我們會從這個錯誤訊息中復原： 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

使用下列步驟來下載**m2repository**並安裝它的內容：

1.  刪除對應至錯誤訊息之程式庫資料夾的內容。 例如，上述錯誤訊息中您要刪除的內容**c:\\使用者\\***username***\\AppData\\本機\\Xamarin\\Android.Support.v4\\23.1.1.0**。 
    如先前所述，您必須刪除這個目錄的完整內容：

    [![刪除內容，內嵌和 23.1.1.0 android_m2repository 資料夾的資料夾](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png)

2.  下載**android\_m2repository\_r_nn_.zip**檔案，將來自 Google 對應至錯誤訊息 （請參閱連結的前一節中的資料表）。

3.  擷取此**.zip**封存至任何位置 （例如桌上型電腦）。 這必須建立對應至名稱的目錄**.zip**封存。 在此目錄中，您應該找到子目錄呼叫**m2repository**: 

    [![擷取的 zip 封存中找到的 m2repository 資料夾](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png)

4.  在您清除在步驟 1 中已建立版本的程式庫目錄中，重新建立**內容**和**內嵌**子目錄。 例如，下列螢幕擷取畫面說明**內容**和**內嵌**子目錄中建立**23.1.1.0**資料夾**android\_m2repository\_r25.zip**: 

    [![建立內容和內嵌的資料夾中 23.1.1.0 資料夾](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png)

5.  複製**m2repository**從解壓縮**.zip**到**內容**您在上一個步驟中建立的目錄： 

    [![複製到 23.1.1.0/content 資料夾 m2repository 的螢幕擷取畫面](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)

6.  在解壓縮**.zip**目錄中，瀏覽至**m2repository\\com\\android\\支援\\支援 v4** ，然後開啟對應的資料夾上面所建立的版本號碼 (在此範例中， **23.1.1**):

    [![Support-v4/23.1.1 資料夾中包含檔案的範例清單](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png)

7.  將所有檔案複製到這個資料夾中**內嵌**步驟 4 中建立目錄：

    [![23.1.1.0/embedded 資料夾複製檔案的範例](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png)

8.  請確認所有檔案會都遭覆寫。 **內嵌**目錄現在應該會包含檔案這類**d**， **.aar**，和**.pom**。

此時，您已手動安裝遺失的元件，應該會無誤建置您的專案。 如果沒有，請確認您已下載**m2repository** **.zip**封存完全對應於錯誤訊息中的版本的版本，並確認您已安裝在其內容上述步驟中所述，請更正的位置。 


<a name="summary" /> 

## <a name="summary"></a>總結 

本文說明如何從期間自動下載並安裝相依程式庫可以進行的常見錯誤復原。 它說明如何刪除有問題的程式庫，並重建專案做為重新下載，並重新安裝此程式庫的方式。 它說明如何下載文件庫，並將它安裝在**送達**資料夾。 它也說明手動下載及安裝必要的檔案，以便解決問題，無法解析透過自動代表更為複雜的程序。 
