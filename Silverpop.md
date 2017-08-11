# Overview
Silverpop is our email marketing tool, which is part of the IBM Marketing Cloud which is owned by Rihanna.  The general process involves:
* Creating database and "relational table" schemas in the SP interface
* Sending data from our database to SP via FTP and the XML API
* Marketing writes queries on the SP data to create audiences and email campaigns

Visit https://login3.silverpop.com to access the Silverpop interface.  You can (1) request credentials from Jon Hansen or the Silverpop marketing person, or (2) use the FTP username and password listed in our `service_access.yaml` file.  Option 2 will require you to validate your IP address by sending an email and pass code to software@smiledirectclub.com.  If you have access to Meldium (password manager) you can login to the email account and retrieve the pass code.

# Silverpop Data
We currently use two main data structures in SP, and these are replicated for each environment, DEV, STAGING, and PROD.  LOCAL and TEST use the same databases as DEV.

![Silverpop Database List](http://i.imgur.com/NO5V43V.png)

**SCC_MASTER**: 
This is the original (old) database created, and is referred to as a "flat table" because it is one table with lots of columns.  The dev/staging/testing replica is called 'Staging_Master' (yes, the name is confusing).  All info about a contact (email address) is listed in this single table.  To add info just add a new column.

**SDC-PROD**: 
This is the new "relational" database, which consists of a database and associated relational tables.  This basically works how you expect a database to work.  SDC-PROD has the following associated tables: PROD-LEADS, PROD-ORDERS, PROD-CASES, and PROD-ORDER-ITEMS.

**SDC-PROD-B**:
Duplication of SDC-PROD, with exact same relational tables.  The goal is to use a separate IP address when sending emails from this database, in the case that our other IP address gets marked as spam.

# Add Field to Relational Table
![screen shot 2017-08-11 at 11 21 10 am](https://user-images.githubusercontent.com/10427685/29222688-2dd4cd8a-7e89-11e7-81d5-f99cb8b2c3a1.png)
![screen shot 2017-08-11 at 11 21 54 am](https://user-images.githubusercontent.com/10427685/29222686-2dd3b058-7e89-11e7-82dd-faf381325594.png)
![screen shot 2017-08-11 at 11 23 40 am](https://user-images.githubusercontent.com/10427685/29222687-2dd496da-7e89-11e7-9d04-8da2b0680297.png)
![screen shot 2017-08-11 at 11 23 57 am](https://user-images.githubusercontent.com/10427685/29222689-2dd514c0-7e89-11e7-916f-ef5bfdc6eefc.png)
![screen shot 2017-08-11 at 11 24 13 am](https://user-images.githubusercontent.com/10427685/29222691-2dde0f4e-7e89-11e7-9ecc-c6c5ed3d6276.png)
![screen shot 2017-08-11 at 11 28 59 am](https://user-images.githubusercontent.com/10427685/29222690-2ddc00dc-7e89-11e7-896b-0d9d0966a22b.png)
![screen shot 2017-08-11 at 11 30 05 am](https://user-images.githubusercontent.com/10427685/29222692-2de1d66a-7e89-11e7-9af5-3c4ef925e53e.png)
![screen shot 2017-08-11 at 11 30 20 am](https://user-images.githubusercontent.com/10427685/29222693-2de4a2be-7e89-11e7-97ed-de1a851261b8.png)
![screen shot 2017-08-11 at 11 30 46 am](https://user-images.githubusercontent.com/10427685/29222694-2de5e494-7e89-11e7-9305-1707310d7ae1.png)


## To remove all contacts from a database

First create a new Contact List, backed by the database you want to remove contacts from.  Then go to that contact list (which starts with 0 things in it).  Click the "Add Contacts" button.  Create a query that will select all of them (i.e. Email is not empty".  Let that job run, which should import all of the contacts into the contact list.  Once you've done that, go back to the database and click "Purge".  You'll want to select to purge the e-mails located in your temporary contact list (it doesn't allow choosing itself... dumb).  Let that job run and it should have dumped everything.

Our current, as of Dec 1, 2016, triggered emails are shown in the following urls. 
For PDF copy: https://www.lucidchart.com/publicSegments/view/3499830f-22b0-4c9d-a7da-7554e01b1e0b

For editing/access (you will have to create a lucid chart account): https://www.lucidchart.com/invitations/accept/1661f3ee-0149-430e-9075-4f353924d96f