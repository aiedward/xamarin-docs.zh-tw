---
ms.openlocfilehash: e46c86def8a22450cf8087cf36a6bb05dd24ef70
ms.sourcegitcommit: 4d260b655cb52b990dda79c239a9721f2e964625
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570839"
---
在嘗試此教學課程之前，您應該已成功完成：

- [建置您的第一個 Xamarin.Forms 應用程式](~/get-started/first-app/index.md)快速入門。
- [StackLayout](~/get-started/tutorials/stacklayout/index.yml) 教學課程。
- [標籤](~/get-started/tutorials/label/index.yml)教學課程。
- [按鈕](~/get-started/tutorials/button/index.yml)教學課程。
- [CollectionView](~/get-started/tutorials/collectionview/index.yml) 教學課程。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> - 使用 NuGet 套件管理員將 Newtonsoft.Json 新增至 Xamarin.Forms 專案。
> - 建立 Web 服務類別。
> - 取用 Web 服務類別。

您將會使用 Visual Studio 2019 或 Visual Studio for Mac 來建立能示範如何從 GitHub Web API 擷取.NET存放庫資料的簡單應用程式。 擷取的資料會顯示在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 中。 下列螢幕擷取畫面顯示的是最終的應用程式：

[![iOS 和 Android 上 GitHub .NET 存放庫的螢幕擷取畫面](../images/consume-web-service.png)](../images/consume-web-service-large.png#lightbox)
