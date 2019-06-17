---
title: 在 iTunes Connect 中設定應用程式
description: 本文將介紹在 iTunes Connect 中設定和維護 Xamarin.iOS 應用程式，並在 App Store 上發行並散發所需的步驟。
ms.prod: xamarin
ms.assetid: 74587317-4b15-4904-9582-dcd914827cbc
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: ff4c72eaaf856083ab8e2e240ac8ce3eff37470c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669627"
---
# <a name="configuring-an-app-in-itunes-connect"></a>在 iTunes Connect 中設定應用程式

> [!IMPORTANT]
> Apple [已宣佈](https://developer.apple.com/ios/submit/)從 2019 年 3 月開始，提交至 App Store 的所有應用程式與更新，都必須使用 iOS 12.1 SDK 或更新版本 (包含在 Xcode 10.1 或更新版本中) 進行建置。
> 應用程式也應支援 iPhone XS 及 12.9" iPad Pro 的螢幕大小。

iTunes Connect 是一套 Web 架構工具，可在 App Store 上管理 iOS 應用程式或進行其他作業。 您必須先在 iTunes Connect 中正確安裝並設定 Xamarin.iOS 應用程式，才能將其提交給 Apple 進行檢閱，最終達到在 App Store 作為付費或免費應用程式發行的目標。

iTunes Connect 可用於下列事項：

- 設定應用程式名稱 (如 App Store 中所顯示)。
- 在支援的 iOS 裝置上，提供運作中應用程式的螢幕擷取畫面或影片。
- 提供簡明的應用程式描述，內容涵蓋其功能與終端使用者的權益。
- 提供類別和子類別來協助使用者在 App Store 中尋找應用程式。
- 提供可進一步協助使用者尋找應用程式的任何關鍵字。
- 應 Apple 要求，提供您網站的「連絡」與「支援」URL。
- 設定應用程式的分級，App Store 中的家長監護將使用此分級。
- 選取售價或指明該應用程式將免費發行。
- 設定選用的 App Store 技術，例如 Game Center 與在應用程式內購買。

此外，應用程式也應具有吸引力、高解析度圖檔，如 Apple 決定在 App Store 中特別介紹時可供使用。 如需詳細資訊，請參閱 Apple 的 [iTunes Connect Developer Guide](https://developer.apple.com/support/itunes-connect/) (iTunes Connect 開發人員指南)。

## <a name="managing-agreements-tax-and-banking"></a>管理合約、稅務和銀行業務

iTunes Connect 的**合約、稅務、銀行業務**一節，是用來提供有關 iTunes 開發者付款和稅務所需的財務資訊，並追蹤您與 Apple 所簽訂之任何協議的狀態。 在您可以在 App Store 上發行 iOS 應用程式 (免費或付費) 之前，您必須先備妥適當的合約，並同意對現有合約所進行的任何修改。

[![](itunesconnect-images/agreement01.png "管理合約、稅務和銀行業務")](itunesconnect-images/agreement01.png#lightbox)

從這裡開始，您可以：

- 提供 **Team 代理程式**並定義 iTunes Connect 帳戶的其他使用者角色，例如**管理員**或**財務人員**。
- 註冊並維護**合約**；合約可讓組織在 App Store 中散發應用程式。
- 指定**法律實體**名稱 (即 App Store 中的銷售者名稱)，此名稱用於匹配與組織相關的合約、銀行資訊和稅務資訊。
- 若要透過 App Store 銷售應用程式，需提供**銀行**和**稅務**資訊。

再次提醒：此資訊「必須」正確設定與使用，並保持為最新狀態，才能將要檢閱與發行的 iOS 應用程式提交到 iTunes Connect。 如需詳細資訊，請參閱 Apple 的 [Managing Agreements, Tax and Banking](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/ManagingContractsandBanking.html#//apple_ref/doc/uid/TP40011225-CH21-SW1) (管理合約、稅務、銀行) 文件。

<a name="creating" />

## <a name="creating-an-itunes-connect-record"></a>建立 iTunes Connect Record

您需要先在 iTunes Connect 中建立應用程式記錄，然後才可將 Xamarin.iOS 應用程式上傳到 iTunes Connect，並透過 App Store 進行散發。 此記錄包括應用程式的所有資訊 (這些資訊將視需要盡可能以多種語言在 App Store 中顯示)，以及透過散發流程來管理應用程式所需的所有資訊。 此外，此記錄也將用於設定 App Store 技術，如 iAd App Network 或 Game Center。

若要將 iOS 應用程式新增至 iTunes Connect，你必須是 **Team 代理程式**，或具有**管理員**或**技術員**角色的用戶。

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中執行下列動作：

1. 按一下 [我的應用程式]：

    [![](itunesconnect-images/add01.png "按一下 [我的應用程式]")](itunesconnect-images/add01.png#lightbox)
2. 按一下左上角的 **+**，然後選取 [New iOS App] (新增 iOS 應用程式)：

    [![](itunesconnect-images/add02.png "新增 iOS 應用程式")](itunesconnect-images/add02.png#lightbox)
3. iTunes Connect 將會顯示 [New iOS App] (新增 iOS 應用程式) 對話方塊：

    [![](itunesconnect-images/add03.png "[New iOS App] \(新增 iOS 應用程式\) 對話方塊")](itunesconnect-images/add03.png#lightbox)
4. 輸入應用程式在 App Store 中顯示的**名稱**和**版本號碼**。
5. 選取 [主要語言]。
6. 輸入 **SKU** 號碼，這是用於追踪應用程式的唯一常數識別碼。 它不會對終端使用者顯示，且應用程式建立後即「無法」更改。
7. 佈建應用程式時，為在開發人員中心中建立的應用程式選取**套件組合識別碼**。 在 Visual Studio for Mac 或 Visual Studio 中對散發組合進行簽署時，需要使用與此相同的套件組合識別碼。 如需詳細資訊，請參閱[建立散發設定檔](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile)與[在 Xamarin.iOS 專案中選取散發設定檔](~/ios/get-started/installation/device-provisioning/index.md)文件。
8. 按一下 [建立] 按鈕，為應用程式建立新的 iTunes Connect Record。

將會在 iTunes Connect 中建立新的應用程式，也將讓您填寫必要的資訊，如描述、定價、類別、分級等等：

[![](itunesconnect-images/add04.png "將會在 iTunes Connect 中建立新的應用程式")](itunesconnect-images/add04.png#lightbox)

<a name="managing" />

## <a name="managing-app-videos-and-screenshots"></a>管理應用程式的影片和螢幕擷取畫面

若要在 App Store 中成功行銷 iOS 應用程式，最重要的元素之一即為提供出色的螢幕擷取畫面或影片預覽。 使用運行中應用程式的實際檢視，醒目顯示使用者互動並展示其獨特功能。 使用應用程式預覽影片，提供使用者該應用程式使用起來的大概感受。

對於螢幕擷取畫面，Apple 提供下列建議：

- 最佳化螢幕擷取畫面，使其在應用程式支援的 iOS 裝置上提供最佳展示效果，並確保內容清晰。
- 請勿為 iOS 裝置映像中的螢幕擷取畫面設置框架。
- 使用全螢幕顯示應用程式的實際檢視，不要在螢幕擷取畫面周圍添加圖形或邊框。
- 應一律移除螢幕擷取畫面的顯示狀態列，iTunes Connect 期望螢幕擷取畫面的維度排除該區域。
- 盡可能使用真實、高解析度的 Retina iOS 硬體 (而不是在 iOS 模擬器中) 拍攝螢幕擷取畫面。
- 如果沒有可用的應用程式預覽影片，則會以第一個螢幕擷取畫面作為搜尋結果，並在 iPhone 和 iPad 上的 App Store 中顯示；因此，請將最佳的螢幕擷取畫面放在最前。
- 善加使用五個螢幕擷取畫面來介紹應用程式，同時醒目顯示應用程式的精彩瞬間。

Apple 要求提供應用程式支援之各種螢幕大小和解析度的螢幕擷取畫面和影片。 此外，依照支援的方向，應提供縱向和橫向版本。

下列的螢幕大小和解析度是目前必要的：

[!include[](~/ios/includes/table-itunesconnect.md)]

### <a name="editing-screenshots-in-itunes-connect"></a>在 iTunes Connect 中編輯螢幕擷取畫面

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中執行下列動作：

1. 按一下 [我的應用程式]。
2. 按一下應用程式的**圖示**。
3. 選取 [版本] 索引標籤。
4. 捲動到**螢幕擷取畫面**區段。
5. 選取 [影像大小] 並拖曳所需的影像 (每個螢幕大小最多 5 張)：

    [![](itunesconnect-images/screenshot01.png "選取 [影像大小] 並拖曳所需的影像")](itunesconnect-images/screenshot01.png#lightbox)
6. 為所有所需的螢幕大小重複此動作。
7. 按一下螢幕頂端的 [儲存] 按鈕以儲存變更。

> [!NOTE]
> 注意:如果螢幕擷取畫面或應用程式預覽影片不符合應用程式中目前的功能，Apple 就會拒絕您的提交。

<a name="metadata" />

## <a name="managing-name-description-whats-new-keywords-and-urls"></a>管理名稱、描述、新增功能、關鍵字和URL

＜iTunes Connect 應用程式記錄＞一節提供應用程式、功能、新版本的修改、搜尋用關鍵字、iAd 支援和任何支援之 URL 的當地語系化相關資訊。

### <a name="app-name"></a>應用程式名稱

選擇可反映出應用程式功能的描述性應用程式名稱。 盡可能保持簡潔明瞭。 應用程式的名稱扮演關鍵角色，它可以影響使用者搜尋和探索的方式，因此請使用簡單易記的名稱。 請特別留意在 iOS 裝置 (iPad、iPhone 和 iPod touch) 上檢視時，名稱的顯示方式。

Apple 建議在選擇應用程式名稱時遵循下列指導方針：

- 保持簡短、簡單又易記。
- 請確定它不會違反協力廠商的著作權或商標。
- 使其符合應用程式的功能。
- 在適當情況下，為外國市場提供當地語系化名稱。

### <a name="description"></a>說明

應用程式及其功能的描述應簡潔明瞭且資訊豐富。 前幾行最重要，因為關係到使用者的第一印象以及是否能吸引使用者。 描述應用程式的特點和相較於類似應用程式的優勢。

撰寫應用程式的描述時，Apple 提供下列建議：

- 包含一或兩個簡要的起始段落，以及主要功能的簡短項目符號清單。
- 在適當情況下，為外國市場提供當地語系化的描述。
- 如果可能，僅在最後包括使用者評論、稱讚或推薦。
- 使用分行符號和項目符號來提高可讀性。
- 注意應用程式描述在每種裝置類型之 App Store 中的顯示方式，確保描述中最重要的句子容易被看到。

### <a name="whats-new"></a>新功能

上傳新版應用程式時，**此版本的新增功能**欄位將可用，請完整地仔細填寫完成。

在填寫新增功能資訊時，Apple 建議下列指導方針：

- 新增訊息以鼓勵使用者更新。
- 按照重要性順序列出項目，並指出變更和 Bug 修正。
- 使用真實且一般的語言，而不是用術語來呈現變更。

### <a name="keywords"></a>關鍵字

與應用程式功能相關的周全、策略性關鍵字有助使用者在 App Store 中搜尋時輕鬆找到您的應用程式。 此外，如果應用程式提供 iAd 廣告，iAd App Network 在應用程式中選擇廣告時，將會使用關鍵字。

對於選擇關鍵字，Apple 提供以下建議：

- 請勿使用競爭性的應用程式名稱、公司或產品名稱，或者註冊商標名稱。
- 請勿使用通用字詞或術語。
- 避免使用不適當、令人反感的術語或無關的字詞，例如名人名稱。
- 在適當情況下，請為外國市場提供當地語系化關鍵字。

### <a name="urls"></a>URL

Apple 要求開發人員提供網站連結，當使用者遇到可能的應用程式相關問題時可提供支援。 也要求提供應用程式隱私權原則的連結 (同樣必須託管於您的網站上)。

或者，您可以提供託管於您的網站上的行銷資訊的連結，此連結用於提供比 App Store 中更詳細的應用程式相關資訊。

### <a name="editing-name-description-whats-new-keywords-and-urls-in-itunes-connect"></a>在 iTunes Connect 中編輯名稱、描述、新增功能、關鍵字和 URL

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中執行下列動作：

1. 按一下 [我的應用程式]。
2. 按一下應用程式的**圖示**。
3. 選取 [版本] 索引標籤。
4. 捲動到**名稱**區段。
5. 填寫所有必要資訊：

    [![](itunesconnect-images/name01.png "在 iTunes Connect 中編輯名稱、描述、新增功能、關鍵字和 URL")](itunesconnect-images/name01.png#lightbox)
6. 按一下螢幕頂端的 [儲存] 按鈕以儲存變更。

> [!IMPORTANT]
> 注意:若名稱、描述、新功能、關鍵字或 URL 不符合您應用程式中目前的功能，Apple 就會拒絕您的提交。

<a name="general" />

## <a name="maintaining-general-app-information"></a>維護應用程式一般資訊

＜iTunes Connect 應用程式記錄＞一節提供應用程式的唯一識別碼、應用程式所屬的類別、分級、著作權以及發行此應用程式的公司相關資訊。

### <a name="app-icon"></a>應用程式圖示

> [!IMPORTANT]
>  應用程式圖示將不再透過 iTunes Connect 提交。 必須透過專案中 **Assets.xcassets** 文件的 **AppIcon** 影像集提交。 如需詳細資訊，請參閱 [App Store 圖示](~/ios/app-fundamentals/images-icons/app-store-icon.md)指南。

應用程式圖示對使用者來說是應用程式的門面，必須容易記憶、在小尺寸下也能正常顯示。 容易記憶的圖示為乾淨、簡單、可立即辨識。

Apple 建議在設計應用程式圖示時遵循下列指導方針：

- 確保圖示適合應用程式。
- 建立與應用程式設計一致的簡單圖示。
- 圖示中避免使用文字。
- 全球思維：所有商店區域都使用單一的應用程式圖示。

在 App Store 中顯示的應用程式圖示必須有 1024 x 1024 影像像素。

如需詳細資訊，請參閱 Apple [iOS Human Interface Guidelines](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html#//apple_ref/doc/uid/TP40006556) (人性化介面指導方針) 和 [General App Information](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Appendices/Properties.html#//apple_ref/doc/uid/TP40011225-CH26-SW7) (應用程式一般資訊) 文件中的＜大型應用程式圖示＞一節

### <a name="app-id"></a>應用程式識別碼

這是在建立 iTunes Connect Record 時，Apple 指派給應用程式的唯一識別碼。 在呼叫 Apple 提供的網頁式介面 (包括網站中的 App Store 資訊) 時，可使用此號碼。

### <a name="version-number"></a>版本號碼

這是應用程式目前作用中的版本，此版本將在 App Store 中向使用者顯示。

### <a name="category-and-sub-category"></a>類別和子類別

應用程式發現性的一個重要方面是它在 App Store 中所屬的類別。 透過類別，使用者可瀏覽應用程式集合並尋找他們感興趣的應用程式。 iTunes Connect 可讓您在定義應用程式時，指定最多兩個不同類別。 請務必小心選擇描述應用程式主要功能的最佳類別。

### <a name="ratings"></a>分級

所有應用程式都需要有 App Store 中的分級。 此分級以應用程式的類型和內容為依據，用於通知家長監護並限制兒童存取。 定義您的應用程式時，iTunes Connect 將提供一個內容描述清單，用於識別該內容在應用程式中出現的頻率。 這些選取項目會轉換為分級，並在 App Store 中顯示。

建立適用於兒童的應用程式時，App Store 有一個針對 11 歲及以下兒童的特殊類別。 即使您的應用程式並非特別針對兒童，這也有助於客戶根據所提供的適當內容分級來做出正確選擇。

> [!IMPORTANT]
> 注意:如果發現任何猥褻、色情、冒犯他人或誹謗性內容，Apple 就會拒絕應用程式提交。

### <a name="copyright-and-company-information"></a>著作權和公司資訊

Apple 允許您提供應用程式的著作權資訊，並要求提供發行應用程式的公司相關資訊，例如公司地址和連絡資訊 (發行至韓國 App Store 的應用程式必須提供)。 視需要，此資訊將會在 App Store 中顯示。

### <a name="editing-general-app-information-in-itunes-connect"></a>在 iTunes Connect 中編輯應用程式一般資訊

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中執行下列動作：

1. 按一下 [我的應用程式]。
2. 按一下應用程式的**圖示**。
3. 選取 [版本] 索引標籤。
4. 捲動到**應用程式一般資訊**區段。
5. 填寫所有必要資訊：

    [![](itunesconnect-images/general01.png "在 iTunes Connect 中編輯應用程式一般資訊")](itunesconnect-images/general01.png#lightbox)
6. 按一下 [分級] 旁的 [編輯] 按鈕，設定分級資訊：

    [![](itunesconnect-images/general02.png "編輯分級")](itunesconnect-images/general02.png#lightbox)
6. 按一下螢幕頂端的 [儲存] 按鈕以儲存變更。

> [!NOTE]
> 注意:如果分類或分級不符合應用程式中目前的功能，Apple 就會拒絕您的提交。

<a name="game-center" />

## <a name="maintaining-game-center-information"></a>維護 Game Center 資訊

支援 Apple Game Center 的 iOS 遊戲應用程式如果是跨網路連線的多人遊戲，可為使用者提供如玩家排名和遊戲成就等資訊。

### <a name="editing-game-center-information-in-itunes-connect"></a>在 iTunes Connect 中編輯 Game Center 資訊

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中執行下列動作：

1. 按一下 [我的應用程式]。
2. 按一下應用程式的**圖示**。
3. 選取 [版本] 索引標籤。
4. 捲動到 **Game Center** 區段。
5. 將 **Game Center** 區段旁的開關切換到 [開] 的位置。
5. 填寫所有必要資訊：

    [![](itunesconnect-images/gamecenter01.png "在 iTunes Connect 中編輯 Game Center 資訊")](itunesconnect-images/gamecenter01.png#lightbox)
6. 按一下螢幕頂端的 [儲存] 按鈕以儲存變更。

使用 [Game Center] 索引標籤來啟用 Game Center，並維護此應用程式任何可用的**排名**或**成就**：

[![](itunesconnect-images/gamecenter02.png "啟用 Game Center")](itunesconnect-images/gamecenter02.png#lightbox)

[![](itunesconnect-images/gamecenter03.png "維護此應用程式任何可用的排名或成就")](itunesconnect-images/gamecenter03.png#lightbox)

## <a name="maintaining-app-review-information"></a>維護應用程式審核資訊

使用本節來提供必要資訊給將檢閱您應用程式的 Apple 人員，如聯絡資訊 (如果技術人員有任何問題)、任何可能需要的示範帳戶、可幫助測試人員成功完成應用程式檢閱的任何附註。

### <a name="editing-app-review-information-in-itunes-connect"></a>在 iTunes Connect 中編輯應用程式檢閱資訊

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中執行下列動作：

1. 按一下 [我的應用程式]。
2. 按一下應用程式的**圖示**。
3. 選取 [版本] 索引標籤。
4. 捲動到 [應用程式檢閱] 區段。
5. 填寫所有必要資訊：

    [![](itunesconnect-images/review01.png "在 iTunes Connect 中編輯應用程式檢閱資訊")](itunesconnect-images/review01.png#lightbox)
6. 選擇希望如何將通過檢閱的應用程式發行至 App Store：

    [![](itunesconnect-images/review02.png "在 iTunes Connect 中編輯應用程式檢閱資訊")](itunesconnect-images/review02.png#lightbox)
6. 按一下螢幕頂端的 [儲存] 按鈕以儲存變更。


## <a name="maintaining-pricing-information"></a>維護應用程式定價資訊

如果您計劃發行付費應用程式，則需要選擇一個 Apple 可用的定價層，並設定售價和給定價格的生效日期。 例如，截至編寫本文時，**第 1 層**定價如下所示：

[![](itunesconnect-images/price01.png "維護定價資訊")](itunesconnect-images/price01.png#lightbox)

### <a name="educational-discount"></a>教育折扣

如果希望以折扣價向一次購買多份的教育機構提供應用程式，請核取此方塊。 可在最新的**付費應用程式合約**中找到折扣的詳細資料，您必須簽署此合約才能向教育客戶提供此應用程式。

### <a name="custom-business-to-business-application"></a>自訂企業對企業應用程式

設定為**自訂企業對企業應用程式**的應用程式僅提供給 iTunes Connect 中指定的**大量採購方案**客戶，且僅在適用區域中可用 (例如，美國大量採購方案客戶必須使用美國的 App Store 企業版大量採購方案)。

自訂的企業對企業應用程式將不提供給教育機構或 App Store 一般客戶。 若要深入了解「App Store 企業版大量採購方案」，請前往 [Apple 的常見問題](http://vpp.itunes.apple.com/faq)頁面。 若要深入了解客戶如何註冊**大量採購方案**，請前往 Apple 的 [Deployment Programs](http://enroll.vpp.itunes.apple.com) (部署方案) 頁面。

### <a name="editing-pricing-information-in-itunes-connect"></a>在 iTunes Connect 中編輯應用程式定價資訊

在 [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) 中執行下列動作：

1. 按一下 [我的應用程式]。
2. 按一下應用程式的**圖示**。
3. 選取 [定價] 索引標籤：

    [![](itunesconnect-images/price02.png "在 iTunes Connect 中編輯應用程式定價資訊")](itunesconnect-images/price02.png#lightbox)
4. 選取 [Availability Date]) (可用日期。
5. 從 [價格區間] 下拉式清單中選取所要的價格。
5. 選擇性啟用**教育折扣**。
6. 選擇性定義應用程式為**自訂企業對企業應用程式**。
6. 按一下 [儲存] 按鈕儲存您的變更。

<a name="iap" />

## <a name="maintaining-in-app-purchase-information"></a>維護在應用程式內購買資訊

如果您計劃銷售虛擬的應用程式內產品 (例如新的遊戲級別或應用程式功能)，請使用此區段來建立和維護這些購買項目。

[![](itunesconnect-images/inapp01.png "維護在應用程式內購買資訊")](itunesconnect-images/inapp01.png#lightbox)

如需如何在 Xamarin.iOS 中使用在應用程式內購買的資訊，請參閱[在應用程式內購買](~/ios/platform/in-app-purchasing/index.md)文件。

## <a name="viewing-application-reviews"></a>檢視應用程式評論

一旦應用程式發行至 App Store 之後，購買或免費下載應用程式的使用者即可撰寫應用程式評論並評定星級。 請使用此區段來查看評論。 例如：

[![](itunesconnect-images/reviews01.png "檢視應用程式評論")](itunesconnect-images/reviews01.png#lightbox)

## <a name="summary"></a>總結

本文描述如何使用 iTunes Connect 來準備 Xamarin.iOS 應用程式，進而發行至 App Store；以及如何維護在市集中顯示的應用程式資訊。

## <a name="related-links"></a>相關連結

- [使用影像](~/ios/app-fundamentals/images-icons/index.md)
- [iOS App Development Workflow Guide:Distributing Applications](https://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [App Store 審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [iTunes Connect 開發人員指南](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/About.html#//apple_ref/doc/uid/TP40011225-CH1-SW1)
