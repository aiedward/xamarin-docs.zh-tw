---
ms.assetid: 4D47185C-8998-4903-AE64-7E2A67F9DF7A
title: UI 控制項比較
description: 本檔提供 Xamarin、Windows Forms 和 WPF 之間的 UI 控制項比較。 它也會連結至比較 WPF 與 Xamarin 格式的其他檔。
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: b4cffd9e95f24dea9fc5fed5a6badeec624a4e25
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453086"
---
# <a name="ui-controls-comparison"></a>UI 控制項比較

以下是使用 Windows Forms 和 WPF 的 Xamarin 控制項（根據 [此資料表](/dotnet/framework/wpf/advanced/windows-forms-controls-and-equivalent-wpf-controls)）的比較。

深入瞭解 [WPF 和 Xamarin 之間的相似之處和差異](wpf.md) ，以協助更新您的行動應用程式開發的桌面知識。

|Windows Forms|WPF|Xamarin.Forms|
|--- |--- |--- |
|[BindingNavigator](/dotnet/api/system.windows.forms.bindingnavigator)|-|-|
|[BindingSource](/dotnet/api/system.windows.forms.bindingsource)|[CollectionViewSource](/dotnet/api/system.windows.data.collectionviewsource)|Binding 屬性，例如 BindingCoNtext|
|[Button](/dotnet/api/system.windows.forms.button)|[Button](/dotnet/api/system.windows.controls.button)|按鈕|
|[CheckBox](/dotnet/api/system.windows.forms.checkbox)|[CheckBox](/dotnet/api/system.windows.controls.checkbox)|參數|
|[CheckedListBox](/dotnet/api/system.windows.forms.checkedlistbox)|包含組合的[ListBox](/dotnet/api/system.windows.controls.listbox) 。|包含組合的 ListView。|
|[ColorDialog](/dotnet/api/system.windows.forms.colordialog)|-|-|
|[ComboBox](/dotnet/api/system.windows.forms.combobox)|[ComboBox](/dotnet/api/system.windows.controls.combobox) (不支援自動完成) |Picker|
|[ContextMenuStrip](/dotnet/api/system.windows.forms.contextmenustrip)|[ContextMenu](/dotnet/api/system.windows.controls.contextmenu)|-|
|[DataGridView](/dotnet/api/system.windows.forms.datagridview)|[DataGrid](/dotnet/api/system.windows.controls.datagrid)|-|
|[DateTimePicker](/dotnet/api/system.windows.forms.datetimepicker)|[DatePicker](/dotnet/api/system.windows.controls.datepicker)|DatePicker & TimePicker|
|[DomainUpDown](/dotnet/api/system.windows.forms.domainupdown)|[TextBox](/dotnet/api/system.windows.controls.textbox) 和兩個 [RepeatButton](/dotnet/api/system.windows.controls.primitives.repeatbutton) 控制項。|Stepper|
|[ErrorProvider](/dotnet/api/system.windows.forms.errorprovider)|-|-|
|[FlowLayoutPanel](/dotnet/api/system.windows.forms.flowlayoutpanel)|[WrapPanel](/dotnet/api/system.windows.controls.wrappanel) 或 [StackPanel](/dotnet/api/system.windows.controls.stackpanel)|StackLayout 或 FlexLayout|
|[FolderBrowserDialog](/dotnet/api/system.windows.forms.folderbrowserdialog)|-|-|
|[FontDialog](/dotnet/api/system.windows.forms.fontdialog)|-|-|
|[表單](/dotnet/api/system.windows.forms.form)|[Window](/dotnet/api/system.windows.window)|頁面|
|[GroupBox](/dotnet/api/system.windows.forms.groupbox)|[GroupBox](/dotnet/api/system.windows.controls.groupbox)|-|
|[HelpProvider](/dotnet/api/system.windows.forms.helpprovider)|沒有對等的控制項 (使用工具提示) 。|-|
|[HScrollBar](/dotnet/api/system.windows.forms.hscrollbar)|滾動[條](/dotnet/api/system.windows.controls.primitives.scrollbar) (滾動內建在容器控制項中) |使用 ScrollView|
|[ImageList](/dotnet/api/system.windows.forms.imagelist)|-|-|
|[標籤](/dotnet/api/system.windows.forms.label)|[標籤](/dotnet/api/system.windows.controls.label)|標籤|
|[LinkLabel](/dotnet/api/system.windows.forms.linklabel)|沒有對等的控制項 (您可以使用 [超連結](/dotnet/api/system.windows.documents.hyperlink) 類別，在 flow 內容) 內裝載超連結。|-|
|[ListBox](/dotnet/api/system.windows.forms.listbox)|[ListBox](/dotnet/api/system.windows.controls.listbox)|使用 ListView|
|[ListView](/dotnet/api/system.windows.forms.listview)|[ListView](/dotnet/api/system.windows.controls.listview)|ListView|
|[MaskedTextBox](/dotnet/api/system.windows.forms.maskedtextbox)|-|-|
|[MenuStrip](/dotnet/api/system.windows.forms.menustrip)|[功能表](/dotnet/api/system.windows.controls.menu)|考慮 MasterDetailPage 或 TabbedPage|
|[MonthCalendar](/dotnet/api/system.windows.forms.monthcalendar)|[Calendar](/dotnet/api/system.windows.controls.calendar)|-|
|[NotifyIcon](/dotnet/api/system.windows.forms.notifyicon)|-|-|
|[NumericUpDown](/dotnet/api/system.windows.forms.numericupdown)|[TextBox](/dotnet/api/system.windows.controls.textbox) 和兩個 [RepeatButton](/dotnet/api/system.windows.controls.primitives.repeatbutton) 控制項。|Stepper|
|[OpenFileDialog](/dotnet/api/system.windows.forms.openfiledialog)|[OpenFileDialog](/dotnet/api/microsoft.win32.openfiledialog)|-|
|[PageSetupDialog](/dotnet/api/system.windows.forms.pagesetupdialog)|-|-|
|[Panel](/dotnet/api/system.windows.forms.panel)|[畫布](/dotnet/api/system.windows.controls.canvas)|View 或 AbsoluteLayout|
|[PictureBox](/dotnet/api/system.windows.forms.picturebox)|[映像](/dotnet/api/system.windows.controls.image)|映像|
|[PrintDialog](/dotnet/api/system.windows.forms.printdialog)|[PrintDialog](/dotnet/api/system.windows.controls.printdialog)|-|
|[PrintDocument](/dotnet/api/system.drawing.printing.printdocument)|-|-|
|[PrintPreviewControl](/dotnet/api/system.windows.forms.printpreviewcontrol)|[DocumentViewer](/dotnet/api/system.windows.controls.documentviewer)|-|
|[PrintPreviewDialog](/dotnet/api/system.windows.forms.printpreviewdialog)|-|-|
|[進度列](/dotnet/api/system.windows.forms.progressbar)|[ProgressBar](/dotnet/api/system.windows.controls.progressbar)|進度列|
|[PropertyGrid](/dotnet/api/system.windows.forms.propertygrid)|-|-|
|[RadioButton](/dotnet/api/system.windows.forms.radiobutton)|[RadioButton](/dotnet/api/system.windows.controls.radiobutton)|-|
|[RichTextBox](/dotnet/api/system.windows.forms.richtextbox)|[RichTextBox](/dotnet/api/system.windows.controls.richtextbox)|編輯器不支援豐富的 (格式的) 文字、單一行文字的專案|
|[SaveFileDialog](/dotnet/api/system.windows.forms.savefiledialog)|[SaveFileDialog](/dotnet/api/microsoft.win32.savefiledialog)|-|
|[ScrollableControl](/dotnet/api/system.windows.forms.scrollablecontrol)|[ScrollViewer](/dotnet/api/system.windows.controls.scrollviewer)|ScrollView|
|[SoundPlayer](/dotnet/api/system.media.soundplayer)|[MediaPlayer](/dotnet/api/system.windows.media.mediaplayer)|-|
|[SplitContainer](/dotnet/api/system.windows.forms.splitcontainer)|[GridSplitter](/dotnet/api/system.windows.controls.gridsplitter)|考慮 MasterDetailPage|
|[StatusStrip](/dotnet/api/system.windows.forms.statusstrip)|[StatusBar](/dotnet/api/system.windows.controls.primitives.statusbar)|-|
|[TabControl](/dotnet/api/system.windows.forms.tabcontrol)|[TabControl](/dotnet/api/system.windows.controls.tabcontrol)|TabbedPage|
|[TableLayoutPanel](/dotnet/api/system.windows.forms.tablelayoutpanel)|[方格](/dotnet/api/system.windows.controls.grid)|Grid|
|[TextBox](/dotnet/api/system.windows.forms.textbox)|[TextBox](/dotnet/api/system.windows.controls.textbox)|編輯器不支援豐富的 (格式) 文字|
|[計時器](/dotnet/api/system.windows.forms.timer)|[DispatcherTimer](/dotnet/api/system.windows.threading.dispatchertimer)|Device. StartTime ( # A1|
|[ToolStrip](/dotnet/api/system.windows.forms.toolstrip)|[工具 欄](/dotnet/api/system.windows.controls.toolbar)|ToolbarItems 和 ToolbarItem|
|[ToolStripContainer](/dotnet/api/system.windows.forms.toolstripcontainer)、 [ToolStripDropDown](/dotnet/api/system.windows.forms.toolstripdropdown)、 [ToolStripDropDownMenu](/dotnet/api/system.windows.forms.toolstripdropdownmenu)、 [ToolStripPanel](/dotnet/api/system.windows.forms.toolstrippanel)|包含組合的[工具列](/dotnet/api/system.windows.controls.toolbar)。|具有組合的 ToolbarItems 和 ToolbarItem|
|[ToolTip](/dotnet/api/system.windows.forms.tooltip)|[ToolTip](/dotnet/api/system.windows.controls.tooltip)|使用協助工具功能|
|[TrackBar](/dotnet/api/system.windows.forms.trackbar)|[滑桿](/dotnet/api/system.windows.controls.slider)|滑桿|
|[TreeView](/dotnet/api/system.windows.forms.treeview)|[TreeView](/dotnet/api/system.windows.controls.treeview)|考慮 NavigationPage 中的階層式 ListView|
|[UserControl](/dotnet/api/system.windows.forms.usercontrol)|[UserControl](/dotnet/api/system.windows.controls.usercontrol)|View 和自訂轉譯器|
|[VScrollBar](/dotnet/api/system.windows.forms.vscrollbar)|[ScrollBar](/dotnet/api/system.windows.controls.primitives.scrollbar)|使用 ScrollView|
|[WebBrowser](/dotnet/api/system.windows.forms.webbrowser)|[WebBrowser](/dotnet/api/system.windows.controls.webbrowser)|WebView|