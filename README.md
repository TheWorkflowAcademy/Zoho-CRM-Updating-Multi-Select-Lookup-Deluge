# Zoho-CRM-Updating-Multi-Select-Lookup-Deluge
A deluge script that updates a multi-select/ multi-user lookup field on Zoho CRM.

## Core Idea
Updating a multi-select/ multi-user lookup field via Deluge isn't as straightforward as putting record ID in the field (as you would with normal lookups). This can be achieved by;
1. Finding the Linking Module API name
2. Getting the API field names in the linking module
3. Creating a record in the linking module with the relevant details

## Understanding the Linking Module
To understand how this works, it is worth revisiting how a multi-select lookup works on the backend. When a multi-select lookup is made between two modules (eg; Contacts & Deals), a **Linking Module** (Contacts_X_Deals) is created. This linking module stores information of both Contacts & Deals, allowing a many-to-many relationship to be established.

## Finding the Linking Module
The linking module, unlike other modules in CRM, is not visible from the front-end. The way to find it is to run an API call that gets **ALL modules** in CRM. This function reveals all modules in CRM (including hidden ones) and its respective fields.
* Connections needed: scope=ZohoCRM.settings.all (or) scope=ZohoCRM.settings.modules.{operation_type} 

```javascript
response = invokeurl
[
	url: "https://www.zohoapis.com/crm/v2/settings/modules"
	type: GET
	connection: "zohocrm" //--> Change this to your Connection name
];
info response;
```

From here, you will be able to get the relevant API names you need for the update;
* Linking Module
* Lookup field to Module A
* Lookup field to Module B

## Updating the Multi-Select/ Multi-User Lookup Field
The multi-select/ multi-user lookup field can be updated by creating a record in the linking module, with the relevant IDs placed in the respective lookup fields.

```javascript
mp = Map();
mp.put("lookupfield_A", "A_record_ID");
mp.put("lookupfield_B", "B_record_ID");
create = zoho.crm.createRecord("Linking_Module_Name",mp);
```

Notes: 
* Replace "lookupfield_A" with the lookup field name to module A (eg; Contacts) and "A_record_ID" with the relevant record ID
* Replace "lookupfield_B" with the lookup field name to module B (eg; Deals) and "B_record_ID" with the relevant record ID
* Replace "Linking_Module_Name" with the linking module API name (they usually look like this: Contacts_X_Deals)
