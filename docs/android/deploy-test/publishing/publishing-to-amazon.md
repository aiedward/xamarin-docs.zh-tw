---
title: 發行至 Amazon App Store
ms.prod: xamarin
ms.assetid: A3E9EAC7-2968-8891-CDF2-B73FC0013EC9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 288ff00b35c369581e50b8e8777f85b7b6119590
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021310"
---
# <a name="publishing-to-the-amazon-app-store"></a>發行至 Amazon App Store

Amazon Mobile App Distribution Program (Amazon 行動應用程式散發計劃) 可讓行動應用程式開發人員在 Amazon 上散發其應用程式。 本節將簡要說明 Amazon App Store for Android。 

[![亞馬遜應用商店螢幕](publishing-to-amazon-images/amazon-app-store.png)](publishing-to-amazon-images/amazon-app-store.png#lightbox)

Amazon 並無限制 APK 大小。 不過，如果 APK 大於 30 MB，就會使用 FTP 來散發，而不會使用「Amazon 行動應用程式散發入口網站」。

## <a name="submitting-apps-binary-info"></a>提交應用程式：二進位檔資訊

將應用程式提交給 Amazon App Store 的流程與將應用程式提交給 Google Play 的流程類似。 由 Amazon 散發的應用程式需要具備下列資產： 

- **圖示** &ndash;   這是背景透明的 114 x 114 .png 檔案。 此為必要。
- **縮圖** &ndash;   這是上述圖示的較大版本。 這是背景透明的 512 x 512 像素檔案。 此圖示也是必要項目。
- **螢幕擷取畫面** &ndash;   Amazon 會要求最少 3 個、最多 10 個螢幕擷取畫面。 螢幕擷取畫面必須是 1024 (寬) x 600 (高) 像素或 800 (寬) x 480 (高) 像素。 可接受 .png 和 .jpg 格式。
- **宣傳影像** &ndash;   為了讓應用程式成為宣傳位置 (例如首頁) 上的精選項目，您可以視需要提交宣傳影像。 此影像應該是 1024 (寬) x 500 (高) 像素的橫向 .png 或 .jpg 檔案。 其中不可包含任何動畫。
- 可以提供五個影片的更新。

## <a name="approval-process"></a>核准流程

提交應用程式之後，會經過一個核准流程。
Amazon 會審查您的應用程式，以確保其運作方式如產品描述所述、不會讓客戶資料遭受風險，且不會妨礙裝置運作。 在核准流程完成之後，Amazon 將會傳送通知並散發應用程式。
