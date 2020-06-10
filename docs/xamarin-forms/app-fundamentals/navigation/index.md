---
title： " Xamarin.Forms 導覽" 描述： "本指南說明如何在應用程式中執行流覽 Xamarin.Forms 。 Xamarin.Forms會根據使用的頁面類型，提供多種不同的頁面導覽體驗。」
assetid： BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：12/01/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-navigation"></a>Xamarin.Forms導覽

_Xamarin.Forms 會根據使用的頁面類型，提供多種不同的網頁導覽體驗。_

![](images/page-types.png "Xamarin.Forms Page Types")

或者， Xamarin.Forms Shell 應用程式會使用以 URI 為基礎的導覽體驗，而不會強制執行設定的導覽階層。 如需詳細資訊，請參閱[ Xamarin.Forms Shell 導覽](~/xamarin-forms/app-fundamentals/shell/navigation.md)。

## <a name="hierarchical-navigation"></a>[階層式導覽](hierarchical.md)

[`NavigationPage`](xref:Xamarin.Forms.NavigationPage)類別提供階層式導覽體驗，讓使用者能夠視需要，向前和向後流覽頁面。 類別會將導覽實作為物件的後進先出（LIFO）堆疊 [`Page`](xref:Xamarin.Forms.Page) 。

## <a name="tabbedpage"></a>[TabbedPage](tabbed-page.md)

包含索引標籤 Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 清單和較大的詳細資料區域，每個索引標籤都會將內容載入至詳細資料區域。

## <a name="carouselpage"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) 是一種頁面，使用者可以從側邊滑動以流覽內容頁面，例如圖庫。

## <a name="masterdetailpage"></a>[MasterDetailPage](master-detail-page.md)

Xamarin.Forms [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 是管理兩個相關資訊頁面的頁面–顯示專案的主版頁面，以及顯示主版頁面上專案相關詳細資料的詳細資料頁面。

## <a name="modal-pages"></a>[強制回應頁面](modal.md)

Xamarin.Forms也提供模式頁面的支援。 強制回應頁面鼓勵使用者完成各自獨立且無法離開的工作，直到完成或取消工作為止。
