# be.aivl.streetimport
Native CiviCRM extension for Amnesty International Flanders (AIVL) to import street recruitment and welcome call csvfiles into CiviCRM.
The extension was created initially by Erik Hommel (CiviCooP) and Björn Endres (Systopia) for AIVL.

## Basic functionality ##
AIVL use street recruitment to get new donors and SEPA Direct Debits (SDD). The actual street recruitment is done by a supplier, who follows up the recruitment with a welcoming call to the new donor within a week of recruitment.
Daily they will put a csv file with either street recruitment data or welcome call data on the server, which will be imported by this extension. The records will contain:
* data identifying the recruiting organization and the recruiter
* donor data (name, address, phones, email, birth date, gender, newsletter Y/N, become member, follow up call)
* SEPA mandate data (bank account, frequency, amount, mandate reference, start date, end date)

Depending on the configuration the **street recruitment** record import will automatically do the following:
* a new contact for the recruiter if it does not exist yet with the contact sub type 'recruiter' and an active relationship 'recruiter for' with the recruiting organziation
* a new contact created for the donor, also storing the donorID at the recruitment organization in a custom group
* the contact will be added to the newsletter group if appropriate
* a membership will be created for the contact if appropriate
* an activity of the type 'Street Recruitment' will be added, with the recruiter as the activity source and the donor as the target contact with status scheduled. In a custom group linked to this activity type all the imported data will be recorded as a snapshot of the street recruitment.
* an SDD is generated (a recurring contribution wtih SEPA mandate and a specific campaign, the mandate reference being generated by the recruiting organization)
* if appropriate, an activity of the type 'Follow Up Call' will be generated with the status scheduled, with the recruiter as the source contact, the donor as the target and the fundraiser in the settings (see section Import Settings) as the assignee contact
* if any error occurred in the process, the data will still be imported if possible but an activity of the type 'Import Error' will be created with the flagged problem, assigned to the error handling employee specified in the settings (see section Import Settings)

Depending on the configuration the **welcome call** record import will automatically do the following:
* a new contact for the recruiter if it does not exist yet with the contact sub type 'recruiter' and an active relationship 'recruiter for' with the recruiting organziation
* it will lookup the donor with the donorID of the recruiting organization, using the custom group where the donorID was stored at Street Recruitment
* it will add phone number if there is one for the donor that is not in CiviCRM yet
* it will update email, address, birth date and gender if it has to
* it will add a bank account if not there yet
* the contact will be added to the newsletter group if appropriate if it not there yet
* a membership will be created for the contact if appropriate and not existing yet
* an activity of the type 'Welcome Call' will be added, with the recruiter as the activity source and the donor as the target contact with status scheduled. In a custom group linked to this activity type all the imported data will be recorded as a snapshot of the welcome call.
* the SDD will be updated or even deleted if required. If the SDD can not be removed because stuff has been sent to the bank, the SDD will be ended and a new one will be added with a generated mandate reference
* if appropriate, an activity of the type 'Follow Up Call' will be generated with the status scheduled, with the recruiter as the source contact, the donor as the target and the fundraiser in the settings (see section Import Settings) as the assignee contact
* if any error occurred in the process, the data will still be imported if possible but an activity of the type 'Import Error' will be created with the flagged problem, assigned to the error handling employee specified in the settings (see section Import Settings)

Once the complete file is processed, the file will be moved to a folder specified in the settings.

## Installation ##
You can install the extension by downloading a zip file from GitHub or by pulling, fetching or cloning the repository. You can then use the CiviCRM manage extensions menu option to install the extension.

In the resources folder are a couple of CiviCRM entities that will be created upon installation of the extension (or whenever the extension is referenced in CiviCRM). This is done in the *CRM_Streetimport_Config* class constructor. The Config object is instantiated in the install hook of the *streetimport.php* file. The entities are in JSON-files:
 * activity_types.json
 * contact_sub_types.json
 * custom_data.json
 * groups.json
 * option_Groups.json
 * relationship_types.json
 
 You can adapt these files to suit your needs, but please check the *CRM_Streetimport_Config* to understand what the impact is. This extension is created specifically for AIVL in their context. You are quite welcome to use and change this extension for your own needs but you will have to ensure you understand the structure before you do :-)
 
## Import Settings ##
The settings used in the import process are stored in a JSON file *import_settings.json* in the *resources* folder of the extension. You can manipulate the JSON file to update the settings, but there is also an option in the CiviCRM menu Administer/CiviContribute with the name *AIVL Import Settings*.
If you click on this option you will see all the import settings. When you hit the save button, the results will be stored in the JSON file *import_settings.json* in the *resources* folder.
All settings will be discussed below (you might get Dutch headings if you have a Dutch CiviCRM installation but we think you will understand anyway).

### Employee handling errors ###
During the import whenever an error is logged, an activity of the type Import Error will be created. In this setting you select the contact that will be set as one the activity will be assigned to. In the select list you will get a list of all contacts in your database that are considered as an employee (based on the setting *Relationship types for other/employee* as AIVL has more than one relationship type that can signify an employee in the sense for this setting.

### Employee doing follow up call ###
### Folder to get CSV files from ###
### Folder to move processed CSV files to ###
### Newsletter group ###
### Membership type ###
### Offset days for SDD ###
### Phone type for landlines ###
### Phone type for mobiles ###
### Location type for address and 1st phone ###
### Location type for additional phones ###
### Default country ###
### Default financial type for SDD ###
### Prefix for individual/household ###
### Gender for female import ###
### Gender for male import ###
### Gender for unknown import ###
### Relationship types for other/employee ###
