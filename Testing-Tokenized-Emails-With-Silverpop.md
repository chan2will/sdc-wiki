# Overview

Since adding the ability for a user to finish an account via email, most of our emails generated in silverpop have a token attached to them as a query parameter. For some features, we are using this token to temporarily treat the customer as an authenticated user. In order to test these features, we need to be able to send tokenized emails from silverpop as an entry point into a given feature making use of that token. This document will outline a process for generating those emails for a testing environment.

## Create a query for your email recipients

1. Once you login, navigate to Data -> Queries -> View from the main nav. 
2. Select the shared tab on the right of the main content. 
3. Navigate to the "Test Queries" folder.
4. Click the "Test Tokenized Email" query
5. Click "Edit Query"
6. Add criteria for a range of emails. It helps here if you can predefine a string to be included in all test emails, so that you can filter based on that. You should be able to pull accounts here from any QA or staging environment.
7. Click "Save and Calculate"
8. Submit the data job
9. Click "Back to Query Summary"
10. Click the search tab to see the results of your query. At this point you can manually exclude emails by selecting them and clicking "Opt-Out Contacts"

## Load the query into a test email template

1. From the main nav, navigate Content -> Templates
2. Click the shared tab
3. Currently we have two templates built for testing. "Test Tokenized Book Scan - STAGING" and "Test Tokenized Book Scan - QA3". You should be able to use these for any feature, because ultimately we only care about the CTA in the email going to the correct URL. Select one of these templates.
4. If you need to make sure the email contains a CTA to a specific location, you can modify the existing buttons in the template by clicking the "Hyperlinks" button on the right side. Change the URL to go to the desired location. You do not need to worry about appending the token to this URL, that will be added by the silverpop middleware. Make sure the URL is pointing to the environment you care about.
5. Once you have the template setup, from the main nav navigate to Test Options -> Normal Test.
6. You may have in intermediary screen here where you confirm the mailing to process.
7. Confirm any updates to the template if a modal pops up
8. Find "Contact Source" near the top and confirm that it is reading from the test query you built. Select the query manually if needed.
9. Click "Send Test Now" at the bottom to send your test emails.
