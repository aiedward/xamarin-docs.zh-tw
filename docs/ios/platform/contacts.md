---
title: 連絡人和 ContactsUI
description: 本文涵蓋了使用新的連絡人和連絡人 UI Xamarin.iOS 應用程式中的架構。 這些架構會取代現有的通訊錄和 iOS 的先前版本中使用位址活頁簿的 UI。
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: ca4235f7fb67c26ade6171d91870e74407aedbd3
ms.sourcegitcommit: 4f646dc5c51db975b2936169547d625c78a22b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2018
---
# <a name="contacts-and-contactsui"></a>連絡人和 ContactsUI

_本文涵蓋了使用新的連絡人和連絡人 UI Xamarin.iOS 應用程式中的架構。這些架構會取代現有的通訊錄和 iOS 的先前版本中使用位址活頁簿的 UI。_

IOS 9 的簡介，與 Apple 已釋放這兩個新的架構，`Contacts`和`ContactsUI`、 取代現有的地址通訊錄和 iOS 8 及更早版本所使用的位址活頁簿的 UI 架構。

這兩個新的架構包含下列功能：

- [**連絡人**](#contacts) -提供存取使用者的連絡人清單資料。
    因為大部分的應用程式只需要唯讀存取權，此架構已經過最佳化的執行緒安全的唯讀存取。

- [**ContactsUI** ](#contactsui) -提供 Xamarin.iOS UI 項目，若要顯示，編輯、 選取和 iOS 裝置上建立的連絡人。

[![](contacts-images/add01.png "IOS 裝置上縮圖工作表範例")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> 現有`AddressBook`和`AddressBookUI`架構使用 iOS 8 （且之前） iOS 9 中已被取代，並應該取代成新`Contacts`和`ContactsUI`儘速的任何現有的 Xamarin.iOS 應用程式的架構。 新的應用程式應撰寫針對新的架構。




在下列章節中，我們將探討這些新的架構以及如何實作它們 Xamarin.iOS 應用程式中。

<a name="contacts" />

## <a name="the-contacts-framework"></a>連絡人架構

連絡人架構提供 Xamarin.iOS 存取使用者的連絡資訊。 因為大部分的應用程式只需要唯讀存取權，此架構已經過最佳化的執行緒安全的唯讀存取。

<a name="Contact_Objects" />

### <a name="contact-objects"></a>連絡人物件

`CNContact`類別提供執行緒安全的唯讀存取連絡人的內容，例如名稱、 地址或電話號碼。 `CNContact` 之類的函式`NSDictionary`且包含多個唯讀集合的屬性 （例如地址或電話號碼）：

[![](contacts-images/contactobjects.png "連絡人物件概觀")](contacts-images/contactobjects.png#lightbox)

可以有多個值 （例如電子郵件地址或電話號碼） 的所有屬性，它們會表示為陣列`NSLabeledValue`物件。 `NSLabeledValue` 是唯讀組標籤所組成的執行緒安全的 tuple，值標籤用來定義使用者 （例如家用或工作電子郵件） 的值。 連絡人 framework 提供的預先定義的標籤 (透過`CNLabelKey`和`CNLabelPhoneNumberKey`靜態類別)，您可以使用您的應用程式中，或您已選擇定義自訂標籤，針對您的需求。

需要調整現有連絡人的值 （或建立新的） 任何 Xamarin.iOS 應用程式中，使用`NSMutableContact`版本的類別和其子類別 (例如`CNMutablePostalAddress`)。

例如，下列程式碼會建立新的連絡人，並將它加入至 使用者連絡人的集合：

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Add email addresses
var homeEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@mac.com"));
var workEmail = new CNLabeledValue<NSString>(CNLabelKey.Work, new NSString("john.appleseed@apple.com"));
contact.EmailAddresses = new CNLabeledValue<NSString>[] { homeEmail, workEmail };

// Add phone numbers
var cellPhone = new CNLabeledValue<CNPhoneNumber>(CNLabelPhoneNumberKey.iPhone, new CNPhoneNumber("713-555-1212"));
var workPhone = new CNLabeledValue<CNPhoneNumber>("Work", new CNPhoneNumber("408-555-1212"));
contact.PhoneNumbers = new CNLabeledValue<CNPhoneNumber>[] { cellPhone, workPhone };

// Add work address
var workAddress = new CNMutablePostalAddress()
{
    Street = "1 Infinite Loop",
    City = "Cupertino",
    State = "CA",
    PostalCode = "95014"
};
contact.PostalAddresses = new CNLabeledValue<CNPostalAddress>[] { new CNLabeledValue<CNPostalAddress>(CNLabelKey.Work, workAddress) };

// Add birthday
var birthday = new NSDateComponents()
{
    Day = 1,
    Month = 4,
    Year = 1984
};
contact.Birthday = birthday;

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

// Attempt to save changes
NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error))
{
    Console.WriteLine("New contact saved");
}
else
{
    Console.WriteLine("Save error: {0}", error);
}
```

如果 iOS 9 裝置上執行此程式碼時，新的連絡人會加入至使用者的集合。 例如: 

[![](contacts-images/add01.png "新的連絡人新增至使用者的集合")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>連絡人的格式化和當地語系化

連絡人架構包含數個物件和方法，可協助您格式化和當地語系化向使用者顯示的內容。 例如，下列程式碼會正確格式化的連絡人名稱和郵寄地址，以便顯示：

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

屬性要在您的應用程式 UI 中顯示的標籤，請連絡 framework 使用的當地語系化以及這些字串的方法。 同樣地，這根據目前的地區設定的 iOS 裝置執行應用程式。 例如: 

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>擷取現有的連絡人

所使用的執行個體`CNContactStore`類別，您可以從使用者的連絡人資料庫擷取連絡資訊。 `CNContactStore`包含所有提取或更新連絡人和群組從資料庫所需的方法。 因為這些方法是同步的建議您執行該背景執行緒，若要防止封鎖 UI。

使用述詞 (從建置`CNContact`類別)，您可以篩選從資料庫擷取連絡人時傳回的結果。 若要擷取包含字串的連絡人`Appleseed`，使用下列程式碼：

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> 連絡人 framework 不支援泛型和複合述詞。

例如，若要限制為只有提取**GivenName**和**FamilyName**連絡人、 屬性，請使用下列程式碼：

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

最後，若要搜尋資料庫，並傳回結果，請使用下列程式碼：

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

如果在使用範例，我們在建立之後執行此程式碼**連絡人物件**上一節，它就會傳回"John Appleseed"之連絡人的剛才所建立。

### <a name="contact-access-privacy"></a>連絡人存取隱私權

使用者可以授與或拒絕存取他們的連絡資訊的每個應用程式為基礎，因為第一次您對進行呼叫`CNContactStore`，將會顯示對話方塊，要求他們允許應用程式的存取。

權限要求只會一次、 第一次應用程式會執行，及後續執行，或呼叫`CNContactStore`將會使用使用者選取在當時的權限。

您應該設計您的應用程式，以便依正常程序會處理拒絕存取其連絡人資料庫的使用者。

#### <a name="fetching-partial-contacts"></a>擷取部分連絡人

A_部分連絡_是從連絡市集已經提取的只有部分可用屬性的連絡人。 如果您嘗試存取未先前擷取的屬性，它會導致例外狀況。

您可以輕鬆地檢查指定的連絡人是否想要的屬性使用`IsKeyAvailable`或`AreKeysAvailable`方法`CNContact`執行個體。 例如: 

```csharp
// Does the contact contain the requested key?
if (!contact.IsKeyAvailable(CNContactOption.PostalAddresses)) {
    // No, re-request to pull required info
    var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName, CNContactKey.PostalAddresses};
    var store = new CNContactStore();
    NSError error;
    contact = store.GetUnifiedContact(contact.Identifier, fetchKeys, out error);
}
```

> [!IMPORTANT]
> `GetUnifiedContact`和`GetUnifiedContacts`方法`CNContactStore`類別_只_傳回部分連絡限制在要求中提供的擷取索引鍵的屬性。

### <a name="unified-contacts"></a>統一的連絡人

使用者可能會有不同 （例如 iCloud，Facebook 或 Google 郵件) 及其連絡資料庫中的單一人員連絡資訊的來源。 在 iOS 和 OS X 應用程式中，此連絡人的資訊自動會連結在一起並顯示以單一使用者_統一連絡_:

[![](contacts-images/unified01.png "統一的連絡人概觀")](contacts-images/unified01.png#lightbox)

此整合連絡是的暫存記憶體中檢視會提供它自己唯一的識別項 （這應該用來重新擷取連絡人，視需要） 的連結連絡資訊。 根據預設，連絡人 framework 將會傳回統一連絡盡可能。

### <a name="creating-and-updating-contacts"></a>建立和更新連絡人

如我們所見中[連絡物件](#Contact_Objects)使用上一節，`CNContactStore`和執行個體`CNMutableContact`來建立新的連絡人，然後寫入使用者的連絡資料庫使用`CNSaveRequest`:

```csharp
// Create a new Mutable Contact (read/write)
var contact = new CNMutableContact();

// Set standard properties
contact.GivenName = "John";
contact.FamilyName = "Appleseed";

// Save new contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.AddContact(contact, store.DefaultContainerIdentifier);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("New contact saved");
} else {
    Console.WriteLine("Save error: {0}", error);
}
```

A`CNSaveRequest`也可用來快取多個連絡人和群組變更成單一作業與批次這些修改`CNContactStore`。

若要更新不可變動的連絡人，取自 fetch 作業，您必須先要求的可變動的副本，然後修改並儲存連絡人的存放區。 例如: 

```csharp
// Get mutable copy of contact
var mutable = contact.MutableCopy() as CNMutableContact;
var newEmail = new CNLabeledValue<NSString>(CNLabelKey.Home, new NSString("john.appleseed@xamarin.com"));

// Append new email
var emails = new NSObject[mutable.EmailAddresses.Length+1];
mutable.EmailAddresses.CopyTo(emails,0);
emails[mutable.EmailAddresses.Length+1] = newEmail;
mutable.EmailAddresses = emails;

// Update contact
var store = new CNContactStore();
var saveRequest = new CNSaveRequest();
saveRequest.UpdateContact(mutable);

NSError error;
if (store.ExecuteSaveRequest(saveRequest, out error)) {
    Console.WriteLine("Contact updated.");
} else {
    Console.WriteLine("Update error: {0}", error);
}
```

### <a name="contact-change-notifications"></a>連絡人的變更通知

每當修改連絡人，請連絡存放區文章`CNContactStoreDidChangeNotification`預設通知中心。 如果您已快取或目前正在顯示任何連絡人，您必須重新整理這些物件從連絡人儲存區 (`CNContactStore`)。

### <a name="containers-and-groups"></a>容器和群組

使用者的連絡人可以存在，或在本機使用者的裝置上為從一或多個伺服器帳戶 （例如 Facebook 或 Google） 同步處理到裝置的連絡人。 連絡人的每個集區都有它自己_容器_和指定的連絡人只能存在一個容器中。

[![](contacts-images/containers01.png "容器和群組概觀")](contacts-images/containers01.png#lightbox)

某些容器允許的排列到一或多個連絡人_群組_或_子群組_。 這個行為會視指定容器的備份存放區。 例如，icloud 的功能有只有一個容器，但可以有多個群組 （但有任何子群組）。 相反地，Microsoft Exchange 不支援群組，但可以有多個容器 （一個用於每個 Exchange 資料夾）。

[![](contacts-images/containers02.png "在中容器和群組重疊")](contacts-images/containers02.png#lightbox)

<a name="contactsui" />

## <a name="the-contactsui-framework"></a>ContactsUI 架構

其中您的應用程式不需要呈現自訂使用者介面的情況下，您可以使用 ContactsUI 架構來呈現 UI 項目，顯示、 編輯、 選取和 Xamarin.iOS 應用程式中建立的連絡人。

藉由使用 Apple 的內建控制項，您不僅減少您必須建立 Xamarin.iOS 應用程式，支援連絡人的程式碼數量，但您的應用程式使用者呈現一致的介面。

### <a name="the-contact-picker-view-controller"></a>連絡人選擇器檢視控制器

連絡人選擇器檢視控制器 (`CNContactPickerViewController`) 管理標準的連絡人選擇器檢視，可讓使用者選取從使用者的連絡人資料庫的連絡人或連絡人屬性。 使用者可以選取 （根據其使用量） 的一或多個連絡人，以及連絡選擇器檢視控制器不會再顯示選擇器的權限的提示。

之前先呼叫`CNContactPickerViewController`類別，定義使用者可以選取並定義述詞來控制顯示和選取的連絡人屬性的屬性。

使用繼承自類別的執行個體`CNContactPickerDelegate`回應使用者互動，使用選擇器。 例如: 

```csharp
using System;
using System.Linq;
using UIKit;
using Foundation;
using Contacts;
using ContactsUI;

namespace iOS9Contacts
{
    public class ContactPickerDelegate: CNContactPickerDelegate
    {
        #region Constructors
        public ContactPickerDelegate ()
        {
        }

        public ContactPickerDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ContactPickerDidCancel (CNContactPickerViewController picker)
        {
            Console.WriteLine ("User canceled picker");

        }

        public override void DidSelectContact (CNContactPickerViewController picker, CNContact contact)
        {
            Console.WriteLine ("Selected: {0}", contact);
        }

        public override void DidSelectContactProperty (CNContactPickerViewController picker, CNContactProperty contactProperty)
        {
            Console.WriteLine ("Selected Property: {0}", contactProperty);
        }
        #endregion
    }
}
```

若要允許使用者選取從其資料庫中的連絡人電子郵件地址，您可以使用下列程式碼：

```csharp
// Create a new picker
var picker = new CNContactPickerViewController();

// Select property to pick
picker.DisplayedPropertyKeys = new NSString[] {CNContactKey.EmailAddresses};
picker.PredicateForEnablingContact = NSPredicate.FromFormat("emailAddresses.@count > 0");
picker.PredicateForSelectionOfContact = NSPredicate.FromFormat("emailAddresses.@count == 1");

// Respond to selection
picker.Delegate = new ContactPickerDelegate();

// Display picker
PresentViewController(picker,true,null);
```

### <a name="the-contact-view-controller"></a>連絡檢視控制站

請連絡檢視控制器 (`CNContactViewController`) 類別會提供向使用者呈現標準的 [連絡人] 檢視的控制站。 連絡檢視可以顯示新的新增 」、 「 未知或 「 現有連絡人並檢視顯示藉由呼叫正確的靜態建構函式之前，必須指定的類型 (`FromNewContact`， `FromUnknownContact`， `FromContact`)。 例如：

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>總結

這篇文章已取得使用 Xamarin.iOS 應用程式中的連絡人和連絡人 UI 架構的詳細的檢視。 首先，它涵蓋不同類型的連絡人 framework 提供的物件以及您如何使用它們來建立新的或存取現有的連絡人。 它也會檢查連絡 UI 架構，以選取現有的連絡人，然後顯示的連絡資訊。


## <a name="related-links"></a>相關連結

- [QuickContacts 範例](https://developer.xamarin.com/samples/monotouch/ios9/QuickContacts/)
- [功能在 iOS 9 中的新功能](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [連絡人 Framework 參考](https://developer.apple.com/documentation/contacts?language=objc)
- [ContactsUI Framework 參考](https://developer.apple.com/documentation/contactsui?language=objc)
