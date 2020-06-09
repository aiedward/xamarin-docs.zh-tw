---
title: Xamarin 中的連絡人和 ContactsUI
description: 本文涵蓋在 Xamarin iOS 應用程式中使用新的「連絡人」和「連絡人」 UI 架構。 這些架構會取代舊版 iOS 中使用的現有通訊錄和通訊錄 UI。
ms.prod: xamarin
ms.assetid: 7b6fb66a-5e19-4a5a-9ed2-f6b02af099af
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 918030120e6b7d0e22abdf5ea3e57f3849b86616
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572568"
---
# <a name="contacts-and-contactsui-in-xamarinios"></a>Xamarin 中的連絡人和 ContactsUI

_本文涵蓋在 Xamarin iOS 應用程式中使用新的「連絡人」和「連絡人」 UI 架構。這些架構會取代舊版 iOS 中使用的現有通訊錄和通訊錄 UI。_

隨著 iOS 9 的推出，Apple 發行了兩個新的架構， `Contacts` 並 `ContactsUI` 取代了 iOS 8 和更早版本所使用的現有通訊錄和通訊錄 UI 架構。

這兩個新的架構包含下列功能：

- [**連絡人**](#contacts)-提供使用者連絡人清單資料的存取權。
  因為大部分的應用程式只需要唯讀存取，所以此架構已針對安全線程的唯讀存取進行優化。

- [**ContactsUI**](#contactsui) -提供 XAMARIN ios UI 元素，以顯示、編輯、選取及建立 ios 裝置上的連絡人。

[![](contacts-images/add01.png "An example Contact Sheet on an iOS device")](contacts-images/add01.png#lightbox)

> [!IMPORTANT]
> Ios `AddressBook` `AddressBookUI` 8 （和更早版本）所使用的現有和架構已于 ios 9 中淘汰，而且應該儘快以新的和架構取代 `Contacts` `ContactsUI` 現有的任何 Xamarin ios 應用程式。 新的應用程式應針對新的架構撰寫。

在下列各節中，我們將探討這些新的架構，以及如何在 Xamarin iOS 應用程式中加以執行。

<a name="contacts"></a>

## <a name="the-contacts-framework"></a>連絡人架構

Contacts 架構會提供 Xamarin iOS 存取權給使用者的連絡人資訊。 因為大部分的應用程式只需要唯讀存取，所以此架構已針對安全線程的唯讀存取進行優化。

<a name="Contact_Objects"></a>

### <a name="contact-objects"></a>Contact 物件

`CNContact`類別提供安全線程的唯讀存取權給連絡人的屬性，例如名稱、位址或電話號碼。 `CNContact`之類的函 `NSDictionary` 式包含多個唯讀的屬性集合（例如位址或電話號碼）：

[![](contacts-images/contactobjects.png "Contact Object overview")](contacts-images/contactobjects.png#lightbox)

對於可以有多個值的任何屬性（例如電子郵件地址或電話號碼），它們會以物件的陣列表示 `NSLabeledValue` 。 `NSLabeledValue`是由一組唯讀的標籤和值組成的安全線程元組，其中標籤會定義使用者的值（例如，首頁或公司電子郵件）。 Contacts 架構提供預先定義的標籤（透過 `CNLabelKey` 和 `CNLabelPhoneNumberKey` 靜態類別）供您在應用程式中使用，或者您可以選擇為您的需求定義自訂標籤。

針對需要調整現有連絡人的值（或建立新的）的任何 Xamarin 應用程式，請使用 `NSMutableContact` 類別的版本及其子類別（例如 `CNMutablePostalAddress` ）。

例如，下列程式碼會建立新的連絡人，並將它新增至使用者的連絡人集合：

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

如果此程式碼是在 iOS 9 裝置上執行，則會將新的連絡人新增至使用者的集合。 例如：

[![](contacts-images/add01.png "A new contact added to the user's collection")](contacts-images/add01.png#lightbox)

### <a name="contact-formatting-and-localization"></a>連絡人的格式和當地語系化

Contacts 架構包含數個物件和方法，可協助您格式化和當地語系化要顯示給使用者的內容。 例如，下列程式碼會正確地格式化連絡人名稱和郵件標籤以供顯示：

```csharp
Console.WriteLine(CNContactFormatter.GetStringFrom(contact, CNContactFormatterStyle.FullName));
Console.WriteLine(CNPostalAddressFormatter.GetStringFrom(workAddress, CNPostalAddressFormatterStyle.MailingAddress));
```

針對您將在應用程式的 UI 中顯示的屬性標籤，Contact framework 也有方法可當地語系化這些字串。 同樣地，這是以執行應用程式的 iOS 裝置目前的地區設定為基礎。 例如：

```csharp
// Localized properties
Console.WriteLine(CNContact.LocalizeProperty(CNContactOptions.Nickname));
Console.WriteLine(CNLabeledValue<NSString>.LocalizeLabel(CNLabelKey.Home));
```

### <a name="fetching-existing-contacts"></a>正在提取現有的連絡人

藉由使用類別的實例 `CNContactStore` ，您可以從使用者的「連絡人」資料庫提取連絡人資訊。 `CNContactStore`包含從資料庫提取或更新連絡人和群組所需的所有方法。 由於這些方法是同步的，因此建議您在背景執行緒上執行它們，以避免封鎖 UI。

藉由使用述詞（從 `CNContact` 類別建立），您可以篩選從資料庫提取連絡人時所傳回的結果。 若只要提取包含字串的連絡人 `Appleseed` ，請使用下列程式碼：

```csharp
// Create predicate to locate requested contact
var predicate = CNContact.GetPredicateForContacts("Appleseed");
```

> [!IMPORTANT]
> Contacts 架構不支援泛型和複合述詞。

例如，若要將提取限制為只有連絡人的**GivenName**和**FamilyName**屬性，請使用下列程式碼：

```csharp
// Define fields to be searched
var fetchKeys = new NSString[] {CNContactKey.GivenName, CNContactKey.FamilyName};
```

最後，若要搜尋資料庫並傳回結果，請使用下列程式碼：

```csharp
// Grab matching contacts
var store = new CNContactStore();
NSError error;
var contacts = store.GetUnifiedContacts(predicate, fetchKeys, out error);
```

如果此程式碼是在我們于上述**Contact 物件**一節中建立的範例之後執行，則會傳回我們剛才建立的「John Appleseed」連絡人。

### <a name="contact-access-privacy"></a>連絡人存取隱私權

因為終端使用者可以根據每個應用程式來授與或拒絕其連絡人資訊的存取權，所以當您第一次呼叫時 `CNContactStore` ，將會顯示一個對話方塊，要求他們允許應用程式的存取權。

許可權要求只會顯示一次，第一次執行應用程式時，後續執行或呼叫 `CNContactStore` 將會使用使用者在該時間選取的許可權。

您應該設計您的應用程式，讓它能夠正常地處理使用者拒絕其連絡人資料庫的存取權。

#### <a name="fetching-partial-contacts"></a>提取部分連絡人

_部分連絡人_是指只從連絡人存放區提取部分可用屬性的連絡人。 如果您嘗試存取先前未提取的屬性，將會導致例外狀況。

您可以使用實例的或方法，輕鬆檢查指定的連絡人是否具有所需的 `IsKeyAvailable` 屬性 `AreKeysAvailable` `CNContact` 。 例如：

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
> `GetUnifiedContact`類別的和 `GetUnifiedContacts` 方法只會傳回 `CNContactStore` 部分連絡人，限制為從提供的提取金鑰要求的屬性。 _only_

### <a name="unified-contacts"></a>整合連絡人

使用者在其連絡人資料庫（例如 iCloud、Facebook 或 Google Mail）中，可能會有一個人連絡人資訊的不同來源。 在 iOS 和 OS X 應用程式中，此連絡人資訊會自動連結在一起，並以單一_整合連絡人_的身分向使用者顯示：

[![](contacts-images/unified01.png "Unified Contacts overview")](contacts-images/unified01.png#lightbox)

此整合連絡人是連結連絡人資訊的暫時性記憶體內部觀點，會提供自己唯一的識別碼（應該用來在必要時重新擷取連絡人）。 根據預設，Contact 架構會盡可能傳回整合的連絡人。

### <a name="creating-and-updating-contacts"></a>建立和更新連絡人

如上述的「[連絡人物件](#Contact_Objects)」一節所示，您可以使用 `CNContactStore` 和的實例 `CNMutableContact` 來建立新的連絡人，然後使用下列方式，將它們寫入至使用者的連絡人資料庫 `CNSaveRequest` ：

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

`CNSaveRequest`也可以用來將多個連絡人和群組變更快取成單一作業，然後將這些修改批次到 `CNContactStore` 。

若要更新從提取作業取得的不可變動連絡人，您必須先要求可變動的複本，然後再修改並儲存回 contact store。 例如：

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

### <a name="contact-change-notifications"></a>連絡人變更通知

每次修改連絡人時，Contact Store 都會張貼 `CNContactStoreDidChangeNotification` 到預設的通知中心。 如果您已經快取或目前正在顯示任何連絡人，就必須從連絡人存放區（）重新整理這些物件 `CNContactStore` 。

### <a name="containers-and-groups"></a>容器和群組

使用者的連絡人可以存在於使用者的裝置本機上，或是從一或多個伺服器帳戶（例如 Facebook 或 Google）同步至裝置的連絡人。 每個連絡人集區都有自己的_容器_，而指定的連絡人只能存在於一個容器中。

[![](contacts-images/containers01.png "Containers and Groups overview")](contacts-images/containers01.png#lightbox)

某些容器可讓連絡人排列成一或多個_群組_或_子群組_。 此行為取決於指定容器的備份存放區。 例如，iCloud 只有一個容器，但可以有許多群組（但沒有子群組）。 另一方面，Microsoft Exchange 並不支援群組，但可以有多個容器（每個 Exchange 資料夾各一個）。

[![](contacts-images/containers02.png "Overlap within Containers and Groups")](contacts-images/containers02.png#lightbox)

<a name="contactsui"></a>

## <a name="the-contactsui-framework"></a>ContactsUI 架構

對於您的應用程式不需要呈現自訂 UI 的情況，您可以使用 ContactsUI 架構來呈現 UI 元素，以顯示、編輯、選取及建立您的 Xamarin iOS 應用程式中的連絡人。

藉由使用 Apple 的內建控制項，您不僅可以減少必須建立的程式碼數量，以支援您的 Xamarin iOS 應用程式中的連絡人，還能為應用程式的使用者提供一致的介面。

### <a name="the-contact-picker-view-controller"></a>連絡人選擇器視圖控制器

連絡人選擇器視圖控制器（ `CNContactPickerViewController` ）會管理標準連絡人選擇器視圖，讓使用者可以從使用者的連絡人資料庫中選取連絡人或連絡人屬性。 使用者可以選取一或多個連絡人（根據其使用方式），而連絡人選擇器視圖控制器在顯示選擇器之前，不會提示您提供許可權。

在您呼叫 `CNContactPickerViewController` 類別之前，您可以定義使用者可選取的屬性，並定義述詞以控制連絡人屬性的顯示和選取。

使用繼承自的類別實例， `CNContactPickerDelegate` 以回應使用者與選擇器的互動。 例如：

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

若要允許使用者從其資料庫的連絡人中選取電子郵件地址，您可以使用下列程式碼：

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

### <a name="the-contact-view-controller"></a>Contact View 控制器

Contact View Controller （ `CNContactViewController` ）類別提供一個控制器，向終端使用者呈現標準連絡人的觀點。 連絡人視圖可以顯示新的新的、未知或現有的連絡人，而且必須先指定類型，才能藉由呼叫正確的靜態函式（ `FromNewContact` ， `FromUnknownContact` ，）來顯示視圖 `FromContact` 。 例如：

```csharp
// Create a new contact view
var view = CNContactViewController.FromContact(contact);

// Display the view
PresentViewController(view, true, null);
```

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin iOS 應用程式中使用 Contact 和 Contact UI 架構。 首先，它涵蓋了 Contact framework 提供的不同物件類型，以及您如何使用它們來建立新的或存取現有的連絡人。 它也會檢查連絡人 UI 架構，以選取現有的連絡人並顯示連絡人資訊。

## <a name="related-links"></a>相關連結

- [連絡人範例](https://docs.microsoft.com/samples/xamarin/ios-samples/contacts/)
- [IOS 9 的新功能](https://developer.apple.com/library/content/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Contacts 架構參考](https://developer.apple.com/documentation/contacts?language=objc)
- [ContactsUI 架構參考](https://developer.apple.com/documentation/contactsui?language=objc)
