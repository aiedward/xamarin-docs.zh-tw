---
title: 行動裝置軟體開發生命週期
description: 本文件描述行動軟體開發生命週期，涉及 UX 設計、UI 設計、開發、穩定、發佈和更多。
ms.prod: xamarin
ms.assetid: 420c5fdf-4610-4e71-9db5-fe894c961924
author: davidortinau
ms.author: daortin
ms.date: 11/22/2016
ms.openlocfilehash: b08293727a585ff68c4bac8a25b26d249505b1aa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016401"
---
# <a name="mobile-software-development-lifecycle"></a>行動裝置軟體開發生命週期

建置行動裝置應用程式非常簡單，您只需要開啟 Visual Studio、將一些項目放在一起、快速做一些測試，並提交到 App Store 即可，只要一個下午即可全部完成。 或者也可以是涉及下列操作的極端複雜流程：嚴苛的預先設計、可用性測試、在數以千計的裝置上進行 QA 測試、完整的 Beta 生命週期；再透過許多不同的方式進行部署。

在本文件中，我們將詳盡介紹如何建置行動應用程式，包括：

1. **流程** - 軟體開發的流程稱為軟體開發生命週期 (SDLC)。 我們會根據行動應用程式開發，檢查 SDLC 的所有階段，包括：開始、設計、開發、穩定、部署和維護。
1. **考量** - 建置行動應用程式時需要考慮許多因素，特別是與傳統的 Web 應用程式或桌面應用程式相比。 我們將介紹這些考量事項以及它們會如何影響行動應用程式開發。

本文件旨在為初學者和有經驗的應用程式開發人員，回答有關行動應用程式開發的基本問題。 採用了相當全面的方法來介紹在整個軟體開發生命週期 (SDLC) 過程中將會遇到的大多數概念。 然而，並不是每一位都適合使用本文件，如果您渴望立刻開始建置應用程式，則我們建議往前跳至[行動裝置應用程式開發簡介](~/cross-platform/get-started/introduction-to-mobile-development.md)指南，並稍後再回來使用本文件。

## <a name="mobile-development-software-lifecycle"></a>行動裝置軟體開發生命週期

行動應用程式開發的生命週期，在很大程度上與 Web 或桌面應用程式的 SDLC 並無不同。 與上述應用程式一樣，流程通常有 5 個主要部分：

1. **起始** - 所有應用程式都是從一個構想開始。 該構想通常會昇華為應用程式的堅實基礎。
1. **設計** - 設計階段包括定義應用程式的使用者體驗 (UX)，例如一般配置是什麼、如何運作，以及將該 UX 轉換為合適的使用者介面 (UI) 設計。通常是在圖形設計工具的協助下進行。
1. **開發** - 通常是資源密集程度最高的階段，本階段將會實際建置應用程式。
1. **穩定** - 開發進行到相當程度後，QA 通常會開始測試應用程式並修復 Bug。 大多數時候，應用程式會進入受限制的 Beta 階段，在此期間會讓更廣泛的使用者有機會使用，並提供意見反應和通知變更。
1. **部署**

這些階段片段有許多經常會重疊，例如：常常在使用者介面正要完成時便已開始進行開發階段了，甚至可能會通知 UI 設計。 此外，應用程式可能會在將新功能新增至新版本的同時，即進入穩定階段。

再者，這些階段可以用於任何數量的 SDLC 方法，如敏捷 (Agile)、螺旋 (Spiral)、瀑布 (Waterfall) 等。

下列各節會更詳細地說明每一個階段。

### <a name="inception"></a>起始

人們與行動裝置的互動無所不在且非常緊密，這意味著幾乎每個人都會對行動應用程式有想法。 行動裝置開啟了與電腦計算、Web、甚至是公司基礎結構進行互動的全新方式。

起始階段就是為應用程式定義和提煉構想。
若要創造出成功的應用程式，則需要提出一些基本問題，這很重要。 在一個公用 App Store 中發佈應用程式之前，需要考慮以下事項：

- **競爭優勢** - 是否已有類似的應用程式存在？ 如果是的話，此應用程式如何與其他應用程式區別？

對於將會在企業中散發的應用程式：

- **基礎結構整合** - 應用程式將會整合於或擴展於何種現有基礎結構？

此外，應用程式應在行動板形規格內容中進行評估：

- **價值** - 此應用程式會為使用者帶來什麼價值？ 他們會如何使用它？
- **形式/行動性** - 此應用程式將會如何以行動板形規格進行工作？ 如何用定位知悉、相機等行動技術來為應用程式增加價值？

為了協助設計應用程式功能，定義執行者和[使用案例](https://en.wikipedia.org/wiki/Use_case)可能會十分有用。 執行者是應用程式中的角色，通常是使用者。 使用案例通常是動作或意向。

比方說，追踪應用程式的一項工作可能會有兩位執行者：「使用者」和「好友」。 使用者可以「建立工作」、與好友「共用工作」。 在此情況下，建立工作和共用工作是兩個完全不同的案例，這兩個案例與執行者串聯，將會告知哪些畫面需要建置、哪些商務實體和邏輯需要開發。

一旦擷取了適量的使用案例和執行者之後，開始設計應用程式便會容易許多。 開發即可以專注於如何建立應用程式，而不需專注於應用程式的定位和用途。

### <a name="designing-mobile-applications"></a>設計行動裝置應用程式

一旦確定了應用程式的特性和功能之後，下一步是開始嘗試解決使用者體驗 (UX)。

#### <a name="ux-design"></a>UX 設計

UX 通常會透過使用任何一種[設計工具組](https://docs.microsoft.com/windows/uwp/design/downloads/)的框線或原型來完成。 使用 UX 原型來設計 UX 可以不必擔心實際的 UI 設計：

 [![](introduction-to-mobile-sdlc-images/balsamiq.png "UX is usually done via wireframes or mockups using tools such as Balsamiq")](introduction-to-mobile-sdlc-images/balsamiq.png#lightbox)

建立 UX 原型時，請務必注意應用程式所針對的各種平台介面指導方針。 應用程式應在每種平台上都「有家的感覺」。 每種平台的官方設計指導方針如下：

1. **Apple** -  [Human Interface Guidelines](https://developer.apple.com/ios/human-interface-guidelines/overview/themes/) (人性化介面指導方針)
1. **Android** -  [Design Guidelines](https://developer.android.com/design/index.html) (設計指導方針)
1. **UWP** –  [UWP 設計基本概念](https://docs.microsoft.com/windows/uwp/design/basics/)

例如，每個應用程式都有一個用來在應用程式各區段間進行切換的隱喻。 iOS 使用畫面底部的索引標籤，Android 使用畫面頂部的索引標籤，而 UWP 則使用[樞紐或索引標籤](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/tabs-pivot)檢視。

此外，硬體本身也會限定 UX 決策。 例如，iOS 裝置沒有實體「返回」鍵，因此引入了導覽控制器隱喻：

 ![](introduction-to-mobile-sdlc-images/01-navigation-controller.png "iOS devices have no physical back button, and therefore introduce the Navigation Controller metaphor")

此外，板型規格也會影響 UX 決策。 平板電腦具有更大的空間，因此可以顯示更多資訊。 通常在手機上需要以多張畫面顯示的內容，在平板電腦可壓縮到一張畫面中：

 [![](introduction-to-mobile-sdlc-images/iphone-vs-ipad.png "Often what needs multiple screens on a phone is compressed into one for a tablet")](introduction-to-mobile-sdlc-images/iphone-vs-ipad.png#lightbox)

由於有無數種板形規格存在，您可能要針對中等大小的板形規格 (介於手機與平板電腦之間)。

#### <a name="user-interface-ui-design"></a>使用者介面 (UI) 設計

一旦決定了 UX 之後，下一個步驟就是建立 UI 設計。 雖然 UX 通常只是黑白原型，但是在 UI 設計階段中會引入並完成顏色、圖形等。 花費時間來設計良好的 UI 是非常重要的；一般來說，最受歡迎的應用程式具有專業的設計。

如同 UX 一樣，請務必了解每種平台都有自己的設計語言，因此即使是設計良好的應用程式，在每種平台上的外觀可能仍有不同：

 [![](introduction-to-mobile-sdlc-images/multiplatform-1.png "A well-designed application may still look different on each platform")](introduction-to-mobile-sdlc-images/multiplatform-1.png#lightbox)

### <a name="development"></a>開發

開發階段通常非常早開始。 事實上，當構想在概念和靈感階段中有了一定的成熟度之後，通常即會開發出一個工作原型，用以驗證功能、假設、協助了解工作範圍。

在本教學課程的其餘部分，我們將主要聚焦於開發階段。

### <a name="stabilization"></a>穩定

穩定是解決應用程式中 Bug 的過程。 不僅僅是從功能 (例如：「它在我按一下這個按鈕時當機了」) 的角度來看，也涉及了可用性和效能。 最好在非常早期的開發過程中就開始進行穩定，以便在成本提高之前進行修正。 一般來說，應用程式會進入「原型」、*Alpha*、*Beta* 及「候選版」等階段。 不同的人會以不同方式定義這些階段，但通常會遵循下列模式：

1. **原型** – 應用程式仍處於概念證明階段，僅有核心功能或應用程式的特定部分能運作。 存在主要 Bug。
1. **Alpha** – 核心功能通常已完成程式碼 (已建置但尚未完整測試)。 仍有主要 Bug，外圍功能可能尚不存在。
1. **Beta** – 大多數功能已完成，至少進行了輕度測試和 Bug 修正。 可能仍然存在主要的已知問題。
1. **候選版** – 所有功能都已完成並經過測試。 除了新 Bug 之外，應用程式是可發行的候選版。

愈早開始測試應用程式愈好。 比方說，如果在原型階段中發現主要問題，仍可以修改應用程式的 UX 以便容納。 如果在初始階段中發現效能問題，則可以在大量程式碼建構於錯誤假設上之前，及早修改架構。

一般而言，當應用程式在生命週期中進一步移動時，將會開放給更多人試用、測試、提供意見反應等等。比方說，原型應用程式可能只會顯示或提供給重要的專案關係人，而發行候選應用程式可能會散發給註冊早期存取的客戶。

針對早期測試和部署到相對較少的裝置，通常直接從開發電腦進行部署便足夠了。 但是，隨著受眾的擴大，可能很快會變得十分麻煩。 因此，有許多測試部署選項讓您可以邀請他人加入測試集區、透過 Web 發行組建、提供可以進行使用者意見反應的工具，大大降低此流程的難度。

針對測試與部署，您可以使用 [App Center](https://appcenter.ms/) 持續對應用程式進行建置、測試、發行和監視。

### <a name="distribution"></a>散發

一旦應用程式處於穩定狀態，即可以散發出去。 有許多不同的散發選項，視平台而定。

#### <a name="ios"></a>iOS

Xamarin.iOS 應用程式和 Objective-C 應用程式採用完全相同的方式進行散發：

1. **Apple App Store** – Apple 的 App Store 是全球可用的線上應用程式存放庫，透過 iTunes 建置於 Mac OS X 中。 它是迄今最受歡迎的應用程式散發方法，讓開發人員毫不費力即可在線上將應用程式投入市場和進行散發。
1. **內部部署** – 內部部署旨在用於不能透過 App Store 公開提供的企業應用程式內部散發。
1. **臨機操作部署** – 臨機操作部署主要用於開發和測試，讓您可以部署到數量有限、經過正確佈建的裝置。 當您透過 Xcode 或 Visual Studio for Mac 部署到裝置時，即為臨機操作部署。

#### <a name="android"></a>Android

所有 Android 應用程式都必須在散發之前進行簽署。 開發人員使用其受憑證保護的私密金鑰對應用程式進行簽署。 此憑證可以提供真確性鏈結，該鏈結會將應用程式開發人員綁定至該開發人員所建置和發行的應用程式。
請注意，雖然適用於 Android 的開發憑證可以由受認可的憑證授權單位進行簽署，但大多數開發人員不會選擇利用這些服務，且會自行簽署其憑證。 憑證的主要用途是區分不同的開發人員和應用程式。
Android 使用此資訊協助您在 Android OS 內運行的應用程式和元件之間執行權限委派。

與其他常用的行動平台不同，Android 採用非常開放的應用程式散發方法。 裝置不會鎖定於已核准的單一應用程式市集。
相反地，任何人都可以自由建立應用程式市集，而大多數 Android 手機允許從這些第三方應用程式市集安裝應用程式。

這可讓開發人員將應用程式散發於可能更大、更複雜的散發管路。 [Google Play](https://play.google.com/store?hl=en) 是 Google 的官方應用程式市集，但還有許多其他應用程式市集。 幾個常見的有：

1. [AppBrain](https://www.appbrain.com/)
1. [適用於 Android 的 Amazon App Store](https://www.amazon.com/mobile-apps/b?ie=UTF8&amp;node=2350149011)
1. [Handango](https://www.handango.com/)
1. [GetJar](https://www.getjar.com/)

#### <a name="uwp"></a>UWP

UWP 應用程式會透過 Microsoft Store 散發給使用者。 開發人員會提交其應用程式來進行准核，之後應用程式就會出現在市集中。 如需發行 Windows 應用程式的詳細資訊，請參閱 UWP 的[發行](https://docs.microsoft.com/windows/uwp/publish/)文件。

## <a name="mobile-development-considerations"></a>行動裝置開發考量

雖然開發行動應用程式與開發傳統 Web/桌面應用程式在流程或架構方面沒有根本性差異，不過需要注意一些考量事項。

### <a name="common-considerations"></a>一般考量

#### <a name="multitasking"></a>多工

行動裝置上的多工 (一次運行多個應用程式) 面臨兩個艱鉅挑戰。 首先，由於螢幕空間有限，難以同時顯示多個應用程式。 因此，在行動裝置上一次只能有一個應用程式處於前景。 其次，開啟多個應用程式並執行工作可能會快速耗盡電池電量。

每種平台都以不同的方式處理多工，我們將會對此做一些探討。

#### <a name="form-factor"></a>板型規格

行動裝置通常可分為兩種類別：手機和平板電腦；另外也有一些介於兩者之間的裝置。 針對這些板型規格進行的開發通常很類似；但是，應用程式在設計上可能會非常不同。
手機的螢幕空間非常有限；而平板電腦雖然螢幕較大，仍然屬於行動裝置，螢幕比大多數的筆記型電腦還小。 因此，行動平台 UI 控制項經過特別設計，以便在較小的板型規格上能有效使用。

#### <a name="device-and-operating-system-fragmentation"></a>裝置與作業系統片段

請務必在整個軟體開發生命週期中將不同裝置納入考量：

1. **概念化和規劃** – 請記住，硬體和功能因裝置而異，依賴某些功能的應用程式可能無法在某些裝置上正常運作。 例如，並非所有裝置都具備相機，如果您建置視訊訊息應用程式，某些裝置或許能播放影片，但不能拍攝影片。
1. **設計** – 設計應用程式的使用者體驗 (UX) 時，請注意裝置間的不同螢幕比例和大小。 此外，設計應用程式的使用者介面 (UI ) 時，應考慮不同的螢幕解析度。
1. **開發** – 以程式碼使用某項功能時，應一律先測試測試該功能是否存在。 比方說，在使用某項裝置功能 (如相機) 前，請一律先在 OS 中查詢該功能是否存在。 接著，在初始化功能/裝置時，請確保從 OS 請求該裝置目前支援的資訊，再使用這些組態設定。
1. **測試** – 請務必及早並經常在實際裝置上測試應用程式。 即使是有相同硬體規格的裝置，行為差異也可能很大。

#### <a name="limited-resources"></a>有限的資源

行動裝置持續變得越來越強大，但與桌上型或筆記型電腦相比，仍然是功能有限的行動裝置。 比方說，桌上型開發人員通常不必擔心記憶體容量，他們已習慣擁有豐富的實體和虛擬記憶體；而在行動裝置上，僅僅是載入少量高畫質圖片便可能會快速消耗所有可用記憶體。

此外，大量佔用處理器的應用程式 (如遊戲或文字辨識) 可能會使行動 CPU 負擔過重，並對裝置效能產生負面影響。

基於這些考量，請務必明智地編寫程式碼，並儘早且且經常在實際裝置上部署，以驗證回應性。

### <a name="ios-considerations"></a>iOS 考量

#### <a name="multitasking"></a>多工

多工在 iOS 中受到非常嚴格的控制；當其他應用程式移到前景時，您的應用程式必須符合某些規則和行為，否則 iOS 會終止您的應用程式。

#### <a name="device-specific-resources"></a>裝置專用資源

在特定板型規格中，不同型號間的硬體可能會有很大差異。 比方說，某些裝置具有後方相機，某些裝置具有前方相機，而某些裝置則沒有相機。

某些較舊裝置 (iPhone 3G 和更舊版本) 甚至不允許多工。

基於這些裝置型號間的差異，請務必在嘗試使用某項功能前檢查它是否存在。

#### <a name="os-specific-constraints"></a>OS 專用條件約束

為確保應用程式回應迅速且安全可靠，iOS 強制應用程式必須遵守的某些規則。 除了與多工有關的規則，還有一些事件方法規定您的應用程式必須在特定時間內傳回，否則 iOS 會將它終止。

另外值得一提的是，應用程式在所謂的「沙箱」中運行，這個環境會強制執行條件約束，限制應用程式能存取的內容。 例如，應用程式可以讀取和寫入自己的目錄，但如果它嘗試寫入其他應用程式目錄，則會受到終止。

### <a name="android-considerations"></a>Android 考量

#### <a name="multitasking"></a>多工

Android 中的多工有兩個元件；第一個是活動生命週期。 Android 應用程式中的每張畫面都由一個活動表示；當應用程式位於背景或移到前景時，會發生一組特定事件。 應用程式必須遵循此生命週期以建立反應迅速、行為良好的應用程式。 如需詳細資訊，請參閱[活動生命週期](~/android/app-fundamentals/activity-lifecycle/index.md)指南。

Android 中多工的第二個元件，是使用「服務」。
服務是長期運行的流程，獨立於應用程式之外存在，用於在應用程式處於背景時執行流程。 如需詳細資訊，請參閱[建立服務](~/android/app-fundamentals/services/index.md)指南。

#### <a name="many-devices-and-many-form-factors"></a>許多裝置與許多板型規格

Google 不會對於可在 Android OS 上執行的做出任何限制。 這種開放式架構，導致產品環境包含大量具有截然不同的硬體、螢幕解析度和比例、裝置特性和功能的不同裝置。

由於 Android 裝置極為分散，大多數人會選擇最受歡迎的 5 種或 6 種裝置進行設計和測試，並優先處理。

#### <a name="security-considerations"></a>安全性考量

所有 Android OS 中的應用程式都在有限權限的不同獨立身分識別中運行。 根據預設，應用程式可以做的事非常少。 比方說，如果沒有特殊權限，應用程式不能發送簡訊、確定手機狀態、甚至是存取網際網路！ 若要存取這些功能，應用程式必須在其資訊清單檔中指定所需的權限；在安裝時，OS 會讀取這些權限，並通知使用者該應用程式正在請求這些權限，然後允許使用者繼續或取消安裝。
由於 Android 採用開放式的應用程式市集模型 (例如：應用程式的管理方式與 iOS 不同)，因此這在 Android 散發模型中是必要的步驟。 如需應用程式權限的清單，請參閱 [Manifest Permissions](https://developer.android.com/reference/android/Manifest.permission.html) (資訊清單權限) 中的＜Android 文件＞參考文章。

### <a name="windows-considerations"></a>Windows 考量

#### <a name="multitasking"></a>多工

UWP 中的多工也分為兩個部分：頁面與應用程式的生命週期和背景流程。 應用程式中，每張畫面都是頁面類別的實例，具有與設為使用中或非使用中狀態建立關聯的事件 (具有用於處理非使用中狀態或「刪除標記」的特殊規則)。

第二個部分：即使應用程式不在前景運行時，為處理工作提供背景代理程式。

#### <a name="device-capabilities"></a>裝置功能

雖然 UWP 硬體的同質性相當高，但仍有選擇性元件，因此在編碼時需要特殊考量。 選擇性硬體功能包括相機、指南針和陀螺儀。 還有一個需要特別考量的低記憶體 (256 MB) 特殊類別，或是開發人員也可以選擇不支援低記憶體。

#### <a name="security-considerations"></a>安全性考量

如需 UWP 中重要安全性考量的資訊，請參閱[安全性](https://docs.microsoft.com/windows/uwp/security/)文件。

## <a name="summary"></a>總結

本指南提供了 SDLC 簡介，因其與行動開發相關。 其介紹有關建置行動應用程式的一般考量，並檢視平台特定的考量，包括設計、測試和部署。

## <a name="next-steps"></a>後續步驟

- [什麼是 Xamarin？](~/cross-platform/get-started/introduction-to-mobile-development.md)
- [Xamarin 使用者入門](~/get-started/index.yml)
- [跨平台共用程式碼](~/cross-platform/app-fundamentals/index.md)
