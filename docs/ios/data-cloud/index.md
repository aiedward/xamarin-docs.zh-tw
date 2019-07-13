---
title: 資料和雲端服務，在 Xamarin.iOS 應用程式
description: 本文件所連結說明如何在 Xamarin.iOS 應用程式中使用本機資料、 iCloud、 和 CloudKit 的指南。
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 930ff3df8266053ab5de09c01d82d8d583ffac81
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865559"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>資料和雲端服務，在 Xamarin.iOS 應用程式

## <a name="data-accessiosdata-clouddataindexmd"></a>[資料存取](~/ios/data-cloud/data/index.md)

大部分的應用程式有一些需求，將資料儲存在本機裝置上。 除非資料總量很微小，這通常需要的資料庫和資料層應用程式來管理資料庫存取權。 iOS 已經 「 內建 」 的 Sqlite 資料庫引擎和 Xamarin 平台的 SQLite 資料提供者所隨附的簡化資料的存取權。

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

在 iOS 5 中的 iCloud 儲存體 API 可讓應用程式，將使用者文件和應用程式專屬資料儲存到中央位置，然後從所有使用者的裝置存取這些項目。

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

此 CloudKit 架構簡化了應用程式的開發該存取 iCloud。 這包括擷取應用程式資料和資產的權限，以及能夠安全地儲存應用程式資訊。 此套件可讓使用者的匿名層藉由允許其 iCloud 識別碼的應用程式的存取，而不需要共用個人資訊。
