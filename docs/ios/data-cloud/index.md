---
title: 在 Xamarin iOS 應用程式中的資料和雲端服務
description: 本檔連結的指南會說明如何在 Xamarin iOS 應用程式中使用本機資料、iCloud 和 CloudKit。
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/13/2017
ms.openlocfilehash: 1c60c1631fd3ecdccf4a91840cfca9ee6116a367
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289893"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>在 Xamarin iOS 應用程式中的資料和雲端服務

## <a name="data-accessiosdata-clouddataindexmd"></a>[資料存取](~/ios/data-cloud/data/index.md)

大部分的應用程式都需要將資料儲存在本機裝置上。 除非完整的資料量很小，否則應用程式通常需要資料庫和資料層來管理資料庫存取。 iOS 的 Sqlite 資料庫引擎為「內建」，而 Xamarin 的平臺簡化了資料的存取，其隨附于 SQLite Data Provider。

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

IOS 5 中的 iCloud 儲存體 API 可讓應用程式將使用者檔和應用程式特定的資料儲存到中央位置，並從所有使用者的裝置存取這些專案。

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

CloudKit 架構可簡化存取 iCloud 之應用程式的開發。 這包括抓取應用程式資料和資產許可權，以及能夠安全地儲存應用程式資訊。 此套件可讓使用者透過其 iCloud 識別碼存取應用程式，而不需要共用個人資訊，以提供匿名層。
