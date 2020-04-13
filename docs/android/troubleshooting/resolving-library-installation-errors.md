---
title: 解析程式庫安裝錯誤
description: 在某些情況下,在安裝 Android 支援庫時可能會收到錯誤。 本指南提供了一些常見錯誤的解決方法。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/14/2018
ms.openlocfilehash: e99e12aa73374cc8145fad0eb5aad7f3259e0ca2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292868"
---
# <a name="resolving-library-installation-errors"></a>解析程式庫安裝錯誤

_在某些情況下,在安裝 Android 支援庫時可能會收到錯誤。本指南提供了一些常見錯誤的解決方法。_

## <a name="overview"></a>概觀

在建構 Xamarin.Android 應用專案時,當 Mac 的 Visual Studio 或 Visual Studio 嘗試下載並安裝依賴項庫時,可能會收到生成錯誤。 其中許多錯誤是由網路連接問題、文件損壞或版本控制問題引起的。 本指南介紹了最常見的支援庫安裝錯誤,並提供了解決這些問題和重新構建應用專案的步驟。

## <a name="errors-while-downloading-m2repository"></a>下載 m2 儲存函式庫時的錯誤

在引用 Android 支援庫或 Google Play 服務的 NuGet 包時,您可能會看到**m2 儲存庫**錯誤。 該錯誤訊息類似如下：

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

此示例適用於**\_android m2\_儲存庫 r16**,但您可能會看到另一個版本(如**\_android m2 儲存庫\_r18**或 android ** \_m2repository\_r25)** 的相同錯誤訊息。

### <a name="automatic-recovery-from-m2repository-errors"></a>從 m2 儲存函式庫錯誤自動復原

通常,可以通過刪除有問題的庫並根據以下步驟進行重建來解決此問題:

1. 瀏覽到電腦上的支援函式庫目錄:

    - 在 Windows 上,支援函式庫位於**\\C:\\_使用者使用者名_\\\\AppData 本地\\Xamarin**。

    - 在 Mac OS X 上,支援函式庫位於 **/User/_使用者名稱_/.local/share/Xamarin**。

2. 找到與錯誤消息對應的庫和版本資料夾。 例如,上述錯誤訊息的庫和版本資料夾位於**Android.Support.v4\\22.2.1**:

    [![22.2.1 支援函式庫的範例資料夾位置](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. 刪除版本資料夾的內容。 請務必刪除 **.zip**檔案以及此資料夾中**的內容**和**嵌入**子目錄。 對於上面顯示的範例錯誤訊息,將刪除此螢幕截圖中顯示的檔案與子目錄(**內容**,**嵌入**與**android_m2repository_r16.zip):**

    [![22.2.1 支援函式庫資料夾的範例內容](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   請注意,刪除此資料夾*的全部*內容非常重要。 儘管此資料夾最初可能包含"缺失"的**\_android m2repository\_r16.zip**檔,但此檔可能已部分下載或損壞。

4. 重建專案&ndash;將導致生成過程重新下載丟失的庫。

在大多數情況下,這些步驟將解決生成錯誤,並允許您繼續。 如果刪除此庫不能解決生成錯誤,則必須手動下載並安裝**\_android m2\_儲存庫 r_nn_.zip**檔,如下一節所述。

### <a name="manually-downloading-m2repository"></a>手動下載 m2 儲存函式庫

如果您嘗試過上述自動復原步驟,但仍有生成錯誤,您可以手動下載**android\_m2 儲存庫\_r_nn_.zip**檔(使用 Web 瀏覽器),並根據以下步驟安裝該檔。 如果您的開發電腦上沒有 Internet 訪問許可權,但您可以使用其他電腦下載存檔,則此過程也很有用。

1. 下載對應於錯誤訊息&ndash;連結的**android\_m2 儲存函式庫\_r_nn_.zip**檔案,請提供以下清單(以及每個連結網址的相應 MD5 哈希):

    - [android\_m2\_儲存庫 r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    - [android\_m2\_儲存庫 r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    - [android\_m2\_儲存庫 r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    - [android\_m2\_儲存庫 r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    - [android\_m2\_儲存庫 r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC6530E7D695C41

    - [android\_m2\_儲存庫 r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    - [android\_m2\_儲存庫 r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    - [android\_m2\_儲存庫 r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    - [android\_m2\_儲存庫 r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    - [android\_m2\_儲存庫 r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    - [android\_m2\_儲存庫 r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    - [android\_m2\_儲存庫 r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    - [android\_m2\_儲存庫 r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    - [android\_m2\_儲存庫 r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    - [android\_m2\_儲存庫 r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AE8E39460B8A5851A

    - [android\_m2\_儲存庫 r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    - [android\_m2\_儲存庫 r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    - [android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881E6

    如果此表中未顯示**m2 儲存庫**存檔,則可以通過`https://dl-ssl.google.com/android/repository/`預先掛起 要下載的**m2 儲存庫**的名稱來創建下載 URL。 例如,用於**https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip**下載**\_android m2\_儲存庫 r10.zip**。

2. 將檔案重新命名為下載 URL 的相應 MD5 哈希,如上表所示。 例如,如果您下載**了\_android m2repository\_r25.zip**,請將其重新命名為**0B3F1796C97C707339FB13AE8507AF50.zip**。 如果表中未顯示下載檔案的下載 URL 的 MD5 哈希,則可以使用[線上 MD5 產生器](http://www.webconfs.com/online-md5-generator.php)將 URL 轉換為 MD5 哈希字串。

3. 將檔案複製到 Xamarin **zips**資料夾:

    - 在 Windows 上,此資料夾位於**C:\\使用者\\\\\\\\***使用者名***\\AppData 本地 Xamarin zip**。

    - 在 Mac OS X 上,此資料夾位於 **/User/***使用者名稱***/.local/share/Xamarin/zips**。

    例如,以下螢幕截圖說明瞭**當\_Android m2repository\_r16.zip**被下載並重新命名為 Windows 下載 URL 的 MD5 哈希時的結果:

    [![r16.zip 儲存庫被重新命名為 0595E577D19D31708195A83087881E6.zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)

如果此過程不能解決生成錯誤,則必須手動下載**\_android m2repository\_r_nn_.zip**檔,解壓縮該檔,並安裝其內容,如下一節所述。

### <a name="manually-downloading-and-installing-m2repository-files"></a>手動下載及安裝 m2 儲存函式庫檔案

從**m2 儲存函式庫**錯誤中恢復的完全手動過程需要下載**\_\_android m2repository r_nn_.zip**檔案(使用 Web 瀏覽器)、解壓縮檔並將其內容複製到電腦上的支援庫目錄。 在下面的範例中,我們將從此錯誤消息中恢復:

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

使用以下步驟下載**m2 儲存函式庫**並安裝內容:

1. 刪除與錯誤消息對應的庫文件的內容。 例如,在上面的錯誤消息中,您將刪除**\\C:\\使用者***使用者名***\\AppData\\本地\\Xamarin\\Android.support.v4\\23.1.1.0**的內容。
    如前所述,您必須刪除此目錄的所有內容:

    [![從 23.1.1.0 資料夾中刪除內容、嵌入和android_m2repository資料夾](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2. 從 Google 下載與錯誤消息對應的**android\_\_m2 儲存庫 r_nn_.zip**檔(有關連結,請參閱上一節中的表)。

3. 將此 **.zip**存檔提取到任何位置(如桌面)。 這將創建對應於 **.zip**存檔名稱的目錄。 此目錄中,應找到一個子目錄,稱為**m2 儲存函式庫**:

    [![在擷取的 zip 存檔中找到 m2 儲存函式庫資料夾](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4. 在步驟 1 中清除的版本化庫目錄中,重新創建**內容和****嵌入**子目錄。 例如,以下螢幕截圖說明瞭在**\_\_android m2repository r25.zip**的**23.1.1.0**資料夾中建立的**內容和****嵌入**子目錄:

    [![在 23.1.1.0 資料夾中建立內容和嵌入資料夾](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5. 將**m2 儲存函式庫**從提取的 **.zip**複製到您在上一步中建立**的內容**目錄中:

    [![m2 儲存函式庫的螢幕擷取到 23.1.1.0/內容資料夾](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6. 在提取的 **.zip**目錄中,瀏覽到**\\m2repository com\\\\android 支援\\v4**並開啟與上面建立的版本號對應的資料夾(在此範例中為**23.1.1**):

    [![支援 v4/23.1.1 資料夾中的檔案清單](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7. 將此資料夾中的所有檔案複製到步驟 4 中建立的**嵌入式**目錄:

    [![複製到 23.1.1.0/嵌入資料夾的檔案範例](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8. 驗證是否複製了所有檔。 **內嵌入**的目錄現在應該包含檔案,如 **.jar、** **.aar**與 **.pom**。

9. 解壓縮任何提取的 **.aar**檔案的內容到**嵌入**的目錄。 在 Windows 上,將 **.zip**擴展名追加到 **.aar**檔,打開它,並將內容複製到**嵌入**的目錄。
    在 macOS 上,使用終端中的**解壓縮**命令解壓縮 **.aar**檔(例如,**解壓縮檔.aar)。**

此時,您已手動安裝缺少的元件,並且專案應生成時沒有錯誤。 如果沒有,請驗證您是否下載了與錯誤消息中的版本完全對應的**m2repository** **.zip**存檔版本,並驗證其內容是否已安裝在上述步驟中所述的正確位置。

## <a name="summary"></a>總結

本文介紹了如何在自動下載和安裝依賴項庫期間從可能發生的常見錯誤中恢復。 它描述了如何刪除有問題的庫和重建項目,作為重新下載和重新安裝庫的一種方式。
它描述了如何下載庫並將其安裝在**zips**資料夾中。 它還描述了一個涉及的手動下載和安裝必要檔的過程,作為解決無法通過自動方式解決的問題的一種方式。
