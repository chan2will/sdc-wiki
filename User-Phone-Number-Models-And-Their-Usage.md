### Index:
---
1. Index
2. Purpose
3. Overview
4. Collection
5. Storage
6. Usage
7. UserPhone model

### Purpose:
---
The purpose of this article is to detail the inner workings of the scc-api project with respect to the collection, storage, and usage of user phone numbers. 

### Overview:
---
Currently, in our database phone numbers are stored as part of the following tables:
* cases_patient_address
* profiles_user_address
* profiles_user_phone

The phone numbers are collected at the same point for each of these tables (account creation). However, only the profiles_user_phone table is referenced to display phone numbers in the staff portal. 

### Collection
---
As far as the ecommerce site is concerned, a customer's phone number is collected during impression-kit purchase or scan appointment scheduling as a part of initial user account creation. 
The code which we are interested in here is located in sites/website/forms/checkout_form_mixins_v3.py
The following block is a portion of the class ImpressionKitCheckoutMixin
---
                address_objects = []
                addresses_data = {
                    'SHIPPING': {
                        'contact_name': '{} {}'.format(user_data.first_name, user_data.last_name),
                        'email': user_data.email,
                        'postal_code': user_data.postal_code,
                        'region': self.cleaned_data.get('state', ''),
                        'locality': self.cleaned_data.get('city', ''),
                        'street1': self.cleaned_data.get('address_1', ''),
                        'street2': self.cleaned_data.get('address_2', ''),
                        'phone': user_data.phone,
                    }
                }

                # if the customer has a billing address different from their shipping address
                if not self.cleaned_data.get('is_billing_address_same'):
                    addresses_data['BILLING'] = {
                        'contact_name': '{} {}'.format(user_data.first_name, user_data.last_name),
                        'email': user_data.email,
                        'postal_code': self.cleaned_data.get('billing_postal_code', ''),
                        'region': self.cleaned_data.get('billing_state', ''),
                        'locality': self.cleaned_data.get('billing_city', ''),
                        'street1': self.cleaned_data.get('billing_address_1', ''),
                        'street2': self.cleaned_data.get('billing_address_2', ''),
                        'phone': self.cleaned_data.get('billing_phone', ''),
                    }

                try:
                    UserPhone.add(request_user=user, user=user, phone=user_data.phone, label='OTHER')
                    for address_kind, address_data in addresses_data.items():
                        # set common vars
                        address_data['user'] = user
                        address_data['country'] = 'US'
                        address_data['ignore_errors'] = True
                        address_data['residential'] = True
                        address_data['kind'] = address_kind

                        # We have to double-check these because we might be deriving them
                        # from the postal code. There's a chance they'll be null.
                        if not (address_data['region'] or address_data['locality']):
                            error_field = '{}_postal_code'.format(address_kind.lower())
                            if address_kind == 'SHIPPING':
                                error_field = 'postal_code'
                            return self.add_error(error_field, 'The postal code you entered is not valid.')

                        user_address = UserAddress.get_or_create(request_user=user, **address_data)
                        preference_key = '{}_address'.format(address_kind).lower()
                        user.create_or_update_preference(request_user=user, key=preference_key, value=user_address.uuid)
                        address_objects.append(user_address)
---

The above block creates a new user in the database during checkout form submission if one does not already exist.
We can see from the first half of the above block that the phone number entered by the customer into the checkout form is included with the rest of the information from that form into a dictionary called addresses_data. This dictionary is used at the end of the block to populate the fields of an entry in the profiles_user_address table. 

The phone number of the user is also used here to create an entry in a completely different table profiles_user_phone. It is at present unclear as to why a phone number is recorded in two different places.

It is worth noting here that a similar process happens in the staff portal. See sites/staff/wizards/helpers.py reconcile_user_addresses.

### Storage
---
So knowing the above it seems that the original intention was to collect customer phone numbers and store them in a way which would parallel the way that addresses were stored. However at some point, maybe even initially, phone numbers were also included as a column in the address tables. At this time the two tables do not appear to be linked such that the phone numbers in each are forced to match. It is entirely possible that confusion could occur if one number is edited and the other does not change accordingly.

### Usage
---
Currently, even though we store the phone numbers of our users in two separate places, this author struggles to find a place where the phone number stored in the profiles_user_address table is displayed/used. In all of the noted appearances of a user's phone number in the staff portal, the information is being pulled from the profiles_user_phone table. See sites/staff/views/cases.py CaseDetailView and the associated template for a display case. 

### UserPhone
---
UserPhone is the name of the model which describes the database table profiles_user_phone

The model is located in core/profiles/models.py

The most interesting property to look at for this model is 'can_receive_sms'. It doesn't appear to be used at all outside of a single test written specifically to test this functionality. This is interesting because this field already exists in the database and thus can be leveraged to provide more functionality for future SMS related projects without requireing a migration.  
