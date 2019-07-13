---
ms.openlocfilehash: 4e759917eac2c6d966d8cd577838c311c13b2a9e
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659765"
---
恭喜您完成此教學課程，您已學會如何：

> [!div class="checklist"]
> - 使用 NuGet 套件管理員將 Newtonsoft.Json 新增至 Xamarin.Forms 專案。
> - 建立 Web 服務類別。
> - 取用 Web 服務類別。

## <a name="next-steps"></a>後續步驟

這一系列教學課程涵蓋了使用 Xamarin.Forms 建立行動應用程式的基本概念。 若要深入了解 Xamarin.Forms 開發，請參閱下列功能：

- 有四個主要的控制項群組可用來建立 Xamarin.Forms 應用程式的使用者介面。 如需詳細資訊，請參閱[控制項參考](~/xamarin-forms/user-interface/controls/index.md)。
- 資料繫結是連結兩個物件屬性的技術，以便在其中一個屬性變更時，自動反映在另一個屬性上。 如需詳細資訊，請參閱[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。
- Xamarin.Forms 會根據使用的頁面類型，提供多種不同的網頁導覽體驗。 如需詳細資訊，請參閱[導覽](~/xamarin-forms/app-fundamentals/navigation/index.md)。
- 樣式功能有助於減少重複的標記，並可更輕鬆地變更應用程式外觀。 如需詳細資訊，請參閱[設定 Xamarin.Forms 應用程式的樣式](~/xamarin-forms/user-interface/styles/index.md)。
- XAML 標記延伸可讓您從常值文字字串以外的來源設定項目屬性，以增強 XAML 的功能和彈性。 如需詳細資訊，請參閱 [XAML 標記延伸](~/xamarin-forms/xaml/markup-extensions/index.md)。
- 資料範本可以針對支援的檢視，定義資料的呈現方式。 如需詳細資訊，請參閱[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。
- 系統會在每個平台上使用 `Renderer` 類別，以不同的方式呈現每個頁面、版面配置和檢視，進而建立原生控制項、將其排列在畫面上，然後加入在共用程式碼中指定的行為。 開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 如需詳細資訊，請參閱[自訂呈現方式](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。
- 效果也可以允許在每個平台上自訂原生控制項。 系統會在平台專屬的專案中，透過將 [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) 類別子類別化來建立效果，然後透過將其附加至適當的 Xamarin.Forms 控制項來取用。 如需詳細資訊，請參閱[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。
- 共用程式碼可以透過 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 類別存取原生功能。 如需有關詳細資訊，請參閱[透過 DependencyService 存取原生功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

或者，Charles Petzold 的書籍[_使用 Xamarin.Forms 建立行動應用程式_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)是深入了解 Xamarin.Forms 的絕佳選擇。 此書籍以 PDF 形式或多種電子書格式提供。

## <a name="related-links"></a>相關連結

- [WebServiceTutorial (範例)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Tutorials/WebServiceTutorial)
- [取用 RESTful Web 服務 (指南)](~/xamarin-forms/data-cloud/web-services/rest.md)
- [Newtonsoft.Json NuGet 套件](https://www.nuget.org/packages/Newtonsoft.Json/)
