# Call

```objectivec
NSString *phoneNumber = @"tel://2267002595";
NSString *phoneNumber = @"telprompt://2267002595";
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:phoneNumber]];

[[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"http://www.google.com"]];

SFSafariViewController
```

# Message

```objectivec
#import <MessageUI/MFMessageComposeViewController.h>

<MFMessageComposeViewControllerDelegate>

MFMessageComposeViewController *controller = [[MFMessageComposeViewController alloc] init];
if([MFMessageComposeViewController canSendText])
{
    controller.body = @"Body of SMS ...";
    controller.recipients = [NSArray arrayWithObjects:@"+1-226-700-2595", @"226-700-2595", nil];
    controller.messageComposeDelegate = self;
    [self presentViewController:controller animated:YES completion:^{
        NSLog(@"Present controller");
    }];
}

- (void)messageComposeViewController:(MFMessageComposeViewController *)controller didFinishWithResult:(MessageComposeResult)result
{
    [self dismissViewControllerAnimated:YES completion:^{
        NSLog(@"Dismiss controller");
    }];
    
    if (result == MessageComposeResultCancelled)
        NSLog(@"Message cancelled");
    else if (result == MessageComposeResultSent)
        NSLog(@"Message sent");
    else
        NSLog(@"Message failed");
}
```

# Contacts

	Contacts.framework
	ContactsUI.framework

	|- CNContactStore
		|- CNSaveRequest
			|- CNGroup
				|- CNMutableGroup
					|- CNContact
					|	|- CNMutableContact
					|		|- CNLabeledValue
					|		|- CNLabelPhoneNumberiPhone
					|		|- CNPhoneNumber
					|		|- CNPostalAddress
					|		|	|- CNMutablePostalAddress
					|		|- CNLabelHome
					|		|- CNSocialProfile
					|		|- CNInstantMessageAddress
					|- NSFormatter
					|	|- CNContactFormatter
					|	|- CNPostalAddressFormatter
	
	|- CNContactUI
		|- CNContactPickerViewController
			|- <CNContactViewControllerDelegate>
		|- CNContactViewController
			|- <CNContactPickerDelegate>
				
```objectivec
// Create contacts
CNMutableContact *contact = [[CNMutableContact alloc] init];
contact.givenName = @"Hello";
contact.familyName = @"World";
    
CNLabeledValue *homeEmail = [CNLabeledValue labeledValueWithLabel:CNLabelHome value:@"home@home.com"];
CNLabeledValue *workEmail =[CNLabeledValue labeledValueWithLabel:CNLabelWork value:@"work@work.com"];
contact.emailAddresses = @[homeEmail,workEmail];
    
CNLabeledValue *phoneNumber = [CNLabeledValue labeledValueWithLabel:CNLabelPhoneNumberiPhone value:[CNPhoneNumber phoneNumberWithStringValue:@"1234567890"]];
contact.phoneNumbers = @[phoneNumber];
    
CNMutablePostalAddress *homeAdress = [[CNMutablePostalAddress alloc] init];
homeAdress.street = @"Home Street";
homeAdress.city = @"Home City";
homeAdress.state = @"Home State";
homeAdress.postalCode = @"Home PostalCode";
contact.postalAddresses = @[[CNLabeledValue labeledValueWithLabel:CNLabelHome value:homeAdress]];
    
NSDateComponents *birthday = [[NSDateComponents  alloc] init];
birthday.day=11;
birthday.month=11;
birthday.year=1111;
contact.birthday=birthday;
    
CNSaveRequest *saveRequest = [[CNSaveRequest alloc] init];
[saveRequest addContact:contact toContainerWithIdentifier:nil];
    
CNContactStore *store = [[CNContactStore alloc] init];
[store executeSaveRequest:saveRequest error:nil];
    
// Format infomation
NSString *contactFormatter =[CNContactFormatter stringFromContact:contact style:CNContactFormatterStyleFullName];
NSLog(@"%@",contactFormatter);
NSString * paFormatter =[CNPostalAddressFormatter stringFromPostalAddress:homeAdress style:CNPostalAddressFormatterStyleMailingAddress];
NSLog(@"%@",paFormatter);
    
// Find contacts
NSPredicate * predicate = [CNContact predicateForContactsMatchingName:@"H"];
NSArray *contacts = [store unifiedContactsMatchingPredicate:predicate keysToFetch:@[CNContactGivenNameKey] error:nil];
    
CNContactFetchRequest * request = [[CNContactFetchRequest alloc] initWithKeysToFetch:@[CNContactPhoneticFamilyNameKey]];
[store enumerateContactsWithFetchRequest:request error:nil usingBlock:^(CNContact *contact, BOOL *stop) {
    NSLog(@"%@",contact);
}];

// Contacts UI
CNContactPickerViewController *contactPickerView = [[CNContactPickerViewController alloc] init];
[self presentViewController:contactPickerView animated:YES completion:nil];
    
<CNContactPickerDelegate>
- contactPickerDidCancel:
    
- contactPicker:didSelectContact:
- contactPicker:didSelectContactProperty:
- contactPicker:didSelectContacts:
- contactPicker:didSelectContactProperties:

    
CNContactViewController *contactView = [CNContactViewController viewControllerForContact:contact];
[self presentViewController:contactView animated:YES completion:nil];
    
<CNContactViewControllerDelegate>
- contactViewController:shouldPerformDefaultActionForContactProperty:
- contactViewController:didCompleteWithContact:
```				


