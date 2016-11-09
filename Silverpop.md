# Overview
Silverpop is our email marketing tool, which is part of the IBM Marketing Cloud.  The general process involves:
* Creating database and "relational table" schemas in the SP interface
* Sending data from our database to SP via FTP and the XML API
* Marketing writes queries on the SP data to create audiences and email campaigns

To get access to the Silverpop interface you can request credentials from Jon Hansen or Josh Michael.  It is also possible to use the FTP username and password listed in our `service_access.yaml` file, but it will require you to validate your IP address by sending an email and pass code to software@smiledirectclub.com.  If you have access to Meldium (password manager) you can login to the email account and retrieve the pass code.

# Silverpop Data
We currently use two main data structures in SP, and these are replicated for each environment, DEV, STAGING, and PROD.  LOCAL and TEST use the same databases as DEV.

![Silverpop Database List](file:///Users/jmhansen/Desktop/Screen%20Shot%202016-11-09%20at%2010.39.48%20AM.png)

*SCC_MASTER*
This is the original (old) database created, and is referred to as a "flat table" because it is one table with lots of columns.  The dev/staging/testing replica is called 'Staging_Master' (yes, the name is confusing).  All info about a contact (email address) is listed in this single table.  To add info just add a new column.

*SDC_PROD*
This is the new "relational" database, which consists of a database and associated relational tables.  This basically works how you expect a database to work.  SDC_PROD has the following associated tables: PROD_LEADS, PROD_ORDERS, PROD_CASES, and PROD_ORDER_ITEMS.


## To remove all contacts from a database

First create a new Contact List, backed by the database you want to remove contacts from.  Then go to that contact list (which starts with 0 things in it).  Click the "Add Contacts" button.  Create a query that will select all of them (i.e. Email is not empty".  Let that job run, which should import all of the contacts into the contact list.  Once you've done that, go back to the database and click "Purge".  You'll want to select to purge the e-mails located in your temporary contact list (it doesn't allow choosing itself... dumb).  Let that job run and it should have dumped everything.