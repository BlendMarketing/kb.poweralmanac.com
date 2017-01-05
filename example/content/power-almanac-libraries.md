Power Almanac Libraries

**This is in it's original form, and is pretty old, dated 12/28/2010. However, it may still be useful**
The screenshots are just conceptual representations of where some of the content will be used after calling
the appropriate libraries or routines. The look and feel will definitely change.
**Assumptions:**
Server session variables or similar will be used to determined if a user is logged in or not. Any un‐registered or
non‐logged in user performing most of the functions described here (other than Share a Search or basic
searches) will be redirected to register or log in.
The same technology can be used to keep track of the latest search parameters used so it can be saved or
shared easily.
1. **Home Page (Public Search)**
    * Search Summary
        * Save a Search
        * Power Almanac Government Summary
        * Power Almanac Government Official Summary
        * Government Summary
        * Government Official Summary
        * Government Overview (link)
        * Government Official Overview (link)
    * Search Results
        * Download Records (link)
        * Government Details (link)
        * Government Official Details (link)
    * Top Saved Searches
        * Show top 5 searches (parameters used)
        * Re‐run search (link)
![roles](images/poweralmanaclibraries1.png)
![roles](images/poweralmanaclibraries2.png)
![roles](images/poweralmanaclibraries3.png)
![roles](images/poweralmanaclibraries4.png)




*Libraries Needed*
## Search Summary
``power_Almanac->DatabaseSummary()`` Returns a PHP record containing numGovtRecords, numGovtOfficialRecords, numRecords, numEmails, etc. in the Power Almanac database. 

``user_Account->SaveCurrentSearch($userID,$namedSearch)`` User must be Registered AND Logged In to save search parameters into Saved Searches
table. 

``search_Results->SearchSummary($searchParams,$powerUser)`` 
Returns a PHP record containing numMatchedGovtRecords,
numMatchedGovtOfficialRecords, numMatchedRecords, numMatchedEmails, etc. from the
Power Almanac database.
Matched record count to be displayed to the user on mouse-click. Summary pop-up or
overlay nicely formatted summary display. 

``search_Results->SearchSummaryTiered($searchParams,$powerUser,$summaryType)``
Returns a PHP record containing breakdown of Government or Government Officials
(summary type) by Government Type, Government Location, Population, etc. Power
Users will also show breakdown by budgets, demographics, etc. 

## Search Results

``user_Account->DownloadStatus($userID)``
Returns a PHP record of download reserves, downloads used, download pricing, etc.

``search_Results->DownloadResultsCount()``
Returns a count of the number of records to download for comparison to reserves and calculating costs if needed. 

``user_Account->DownloadRequest($userID)``
Check Download Credits/Reserves greater than or equal to DownloadResultsCount.
Enterprise (unlimited downloads) users will bypass this step.
If Less Than, Invoke PayPal (see separate section). Return to first step after top up success with PayPal. If More Than or Equal, proceed to download records.

``search_Results->DownloadResults($userID,$powerUser)``
Returns a CSV/XLS zip archive of records to user (HTTP). Back-end server developer
to implement sustainable Download Infrastructure - maybe use Amazon Simple Queue
Service (SQS)?
Save download archive into Saved Downloads table. Actual Zip archive file saved in
Amazon S3?
If a Saved Search is associated with this download, save info into Saved Searches
table.

``search_Results->SearchDetails($numRecords2Show,$searchParams,$powerUser)``
Returns a PHP record containing search results including Government, Type, Record
ID, etc. from the Power Almanac database.

``power_Almanac->RecordDetails($recordID,$powerUser)``
Returns a PHP record containing the full database columns (some fields masked out)
to be displayed to the user on mouse-click of some search results table columns
like Government and Last Name.
Profile pop-up or overlay nicely formatted display. 

## Search Critera
Simple structured searches only - no wildcard or keywords.
Search **parameters** will be sent to the database request.
**NOTE:**
There are some special cases where an AND/OR logic is required and I'm looking at that in
detail next. This will be inmcorporated into the parameters passed to:
``search_Results->SearchDetails($numRecords2Show,$searchParams,$powerUser)``
**Top Saved Searches**
``power_Almanac->TopSavedSearches($numSearches2Show)``
Returns a PHP record containing all data mined saved searches from all registered
users and link to re-run search.
``power_Almanac->TopSearches($numSearches2Show)``
Returns a PHP record containing all data mined search parameters used for all
searches done on Power Almanac and link to re-run search. 

2. Download Records Request
    * Logged In User
    * Download Reserves
    * Download Pricing
    * Retry Download Request
![roles](images/poweralmanaclibraries7.png)

**Libraries Needed**
``user_Account->LoggedIn()`` Return status if a user is logged in or not
``user_Account->PowerUser($userID)`` Return status if power user or not
``user_Account->userID()`` Returns current logged in user ID 

3. New User Management
    * User Creation
    * User Validation (link)
![roles](images/poweralmanaclibraries8.png)

**Libraries Needed**
``user_Creation->InsertUserInfo($eMail,$passWord,$firstName,$lastName,$companyName)``
Insert registration info to Registered Users database. Generate unique newUserCode.
Set status flag to Unverified.

``user_Creation->newUserCode()``
Returns unique identifier to validate new registered user email account.

``user_Creation->Validate_User($newUserCode)``
Validate account in Registered Users database. Set status flag to Verified.

``user_Creation->SweepRegistrationDB()``
Returns a PHP record containing all un-verified accounts so they can be alerted via
follow-up (multiple) emails.

``user_Creation->CleanRegistrationDB()``
Remove all un-verified accounts older than one month (or less).

4. Current User Management
    * Log In (Verification)
    * Log Out (link)
![roles](images/poweralmanaclibraries9.png)

**Libraries Needed**
``user_Account->LogOut($userID)``
Log user out. Reset server session variables.

``user_Account->ValidateLogIn($username,$password)``
Validate and log user in. Set server session variables.
Return a success/fail code. 

5. Account Dashboard
    * Power Almanac Snapshot
    * Account Snapshot
    * Frequent Searches
    * My Alerts
![roles](images/poweralmanaclibraries10.png)

**Libraries Needed**
Power Almanac snapshot same as described in Home Page (power_Almanac->DatabaseSummary).
``user_Account->TopSearches($numSearches2Show,$userID)``
Returns a PHP record containing top saved search names (most run) and link to rerun search.

``user_Account->AccountSnapshot($userID)``
Returns a PHP record for a quick summary/snapshot of the basic information in the user account like the number saved searches, saved downloads, download reserves, subcriptions info, etc.

``user_Account->SavedDownloadsAlerts($userID)``
Detect changes (time stamp comparison of records/columns) in Power Almanac database.
Offline daily cron job?

``user_Account->SavedSearchesAlerts($userID)``
Detect changes (time stamp comparison of records/columns) in Power Almanac database.
Offline daily cron job?

6. My Searches
    * Table of Saved Searches
    * Saved Search management
![roles](images/poweralmanaclibraries11.png)

**Libraries Needed**
``user_Account->SavedSearches($userID)``
Returns a PHP record containing the list of saved searches in the Saved Searches
table including its unique ID (searchID), time/date, etc.

``user_Account->RemoveSavedSearch($searchID)``
Delete entry from the Saved Searches table.

``user_Account->RenameSavedSearch($searchID)``
Modify entry from the Saved Searches table.

``user_Account->RunSavedSearch($searchID)``
Re-run a full search results. 

7. My Downloads
    * Table of Downloads
    * Downloads management
![roles](images/poweralmanaclibraries12.png)

**Libraries Needed**
Can Amazon Simple Queue Service (SQS) and Amazon S3 be used here?

``user_Account->SavedDownloads($userID)``
Returns a PHP record containing the list of downloads in the Saved Downloads table
including its unique ID (downloadID), time/date, etc.

``user_Account->DownloadRecords($downloadID)``
Re-download previously downloaded records. Actual Zip archive file saved in Amazon
S3?

8. Account Details
    * Membership Status
    * Log In Info
    * Download Status
    * Communication Settings
    * Corporate Account
        * List of Users
        * User Info
        * Sub‐User Creation & Management
![roles](images/poweralmanaclibraries13.png)

**Libraries Needed**
``user_Account->MembershipStatus($userID)``
Returns a PHP record of membership type, start date, end date, etc.

``user_Account->LogInDetails($userID)``
Returns a PHP record of email address (cannot change) and password.

``user_Account->UpdatePassword($userID)``
Update password.

``user_Account->DownloadStatus($userID)``
Returns a PHP record of download reserves, downloads used, download pricing, etc.

``user_Account->CommunicationSettings($userID)``
Returns a PHP record of opt-in settings for alert and newsletter.

``user_Account->UpdateCommunicationSettings($userID,$settings)``
Update opt-in settings.

``user_Account->ListSubUser($userID)``
 Returns a PHP record of all sub-users details including sub-user ID (subUserID).
 
``user_Account->CreateSubUser($eMail,$passWord,$firstName,$lastName,$downloadLimits,$userID)``
Insert sub-user registration info to Registered Users database

``user_Account->RemoveSubUser($subUserID)``
Remove a sub user from the Registered Users database. 

9. Research
    * Row, Column, Calculation
    * Generate tables
    * Saved tables management
![roles](images/poweralmanaclibraries14.png)

**Libraries Needed**
Wizard to select Row and Column. Select Calculation. Then display tables. Save generated
tables.
Offline weekly cron job to pre-calculate or pre-generate all tables. For example,
Table ID 1: Uses Row = 'Government Type', Column = 'Population Served', Calculation =
'Count'
Table ID 2: Uses Row = 'Government Type', Column = 'Population Served', Calculation =
'Percentage'
Fixed number of tables.
``power_Almanac->DisplayTable($row,$column,$calculation)``
Display the pre-generated table
Function like saved searches but if pre-calculated, then save pointer or Power Almanac
Table ID ($tableID) by storing simple table IDs in Saved Tables database:
``user_Account->SaveCurrentTable($userID)``
``user_Account->SavedTables($userID)``
``user_Account->RemoveSavedTable($userID,$tableID)``
``user_Account->RenameSavedTable($userID,$tableID)``
``user_Account->ShowSavedTable($tableID)``

10. Share a Search
![roles](images/poweralmanaclibraries15.png)

**Libraries Needed**
``search_Results->ShareASearch()``
Similar to save search parameters into My Searches table but create a Shared
Searches lookup table.
Search parameters stored to Shared Searches table.
Returns a unique identifier (uniqueID) to the lookup table.
Could be data mined later for popular shared searches.

``shared_Searches->Lookup($uniqueID)``
Returns a full search parameter set to re-create or re-run a full search results.

11. PayPal Interface
    * Membership Subscription (recurring annual charges)
    * On Demand Record Purchase (one time)
    * Error handling

**Libraries Needed**
user_Account->eMailAddress($userID)
Return logged in user email address
* ``paypal_API->CheckoutDL($amount2Charge,$userEmail,$returnURL)``
    * amount2Charge calculated (numRecord X pricePerRecord) - (reserves)
    * transaction type is one time
    * Pre-fill user email (in case they already have a PayPal account)
    * LogRequest
    * Submit to PayPal

* ``paypal_API->CheckoutSUB($amount2Charge,$userEmail,$returnURL)``
    * amount2Charge based on subscription level choice
    * transaction type is annual recurring
    * Pre-fill user email (in case they already have a PayPal account)
    * LogRequest
    * Submit to PayPal
* ``paypal_API->LogRequest($userID,$amount2Charge,$transactionType,$userEmail)``
    * Log Request into PayPal Transactions table
    * paypal_API->LogResponse($userID,$IPN_Response)
    * Log Response into PayPal Transactions table
* ``paypal_API->ProcessIPN($payPalPayload)``
    * LogResponse
    * Process PayPal response
    * Display PayPal status to user (both Verified/Success and Errors)
    * Proceed to Download if Verified for download - extract from PayPal payload.
    * Update account reserves.
    * Proceed to Dashboard if Verified for membership - extract from PayPal payload.
    * Set account expiration (new) or extend account expiration (renew/upgrade).
    * Proceed to Retry if Errors.

## Summary
**PHP Routines/Libraries:**
```
power_Almanac->DatabaseSummary()
power_Almanac->RecordDetails($recordID,$powerUser)
power_Almanac->TopSearches($numSearches2Show)
power_Almanac->TopSavedSearches($numSearches2Show)
power_Almanac->DisplayTable($row,$column,$calculation)
user_Account->eMailAddress($userID)
user_Account->LogOut($userID)
user_Account->ValidateLogIn($username,$password)
user_Account->LoggedIn()
user_Account->PowerUser($userID)
user_Account->userID()
user_Account->DownloadStatus($userID)
user_Account->DownloadRequest($userID)
user_Account->SavedDownloads($userID)
user_Account->DownloadRecords($downloadID)
user_Account->SaveCurrentSearch($userID,$namedSearch)
user_Account->TopSearches($numSearches2Show,$userID)
user_Account->SavedSearches($userID)
user_Account->RemoveSavedSearch($searchID)
user_Account->RenameSavedSearch($searchID)
user_Account->RunSavedSearch($searchID)
user_Account->SavedDownloadsAlerts($userID)
user_Account->SavedSearchesAlerts($userID)
user_Account->AccountSnapshot($userID)
user_Account->MembershipStatus($userID)
user_Account->LogInDetails($userID)
user_Account->UpdatePassword($userID)
user_Account->CommunicationSettings($userID)
user_Account->UpdateCommunicationSettings($userID,$settings)
user_Account->ListSubUser($userID)
user_Account->CreateSubUser($eMail,$passWord,$firstName,$lastName,$downloadLimits,$userID)
user_Account->RemoveSubUser($subUserID)
user_Account->SaveCurrentTable($userID)
user_Account->SavedTables($userID)
user_Account->RemoveSavedTable($userID,$tableID)
user_Account->RenameSavedTable($userID,$tableID)
user_Account->ShowSavedTable($tableID)
user_Creation->InsertUserInfo($eMail,$passWord,$firstName,$lastName,$companyName)
user_Creation->newUserCode()
user_Creation->Validate_User($newUserCode)
user_Creation->SweepRegistrationDB()
user_Creation->CleanRegistrationDB() 
search_Results->ShareASearch()
search_Results->SearchSummary($searchParams,$powerUser)
search_Results->SearchSummaryTiered($searchParams,$powerUser,$summaryType)
search_Results->SearchDetails($numRecords2Show,$searchParams,$powerUser)
search_Results->DownloadResultsCount() 
search_Results->DownloadResults($userID,$powerUser)
shared_Searches->Lookup($uniqueID)
paypal_API->CheckoutDL($amount2Charge,$userEmail,$returnURL)
paypal_API->CheckoutSUB($amount2Charge,$userEmail,$returnURL)
paypal_API->LogRequest($userID,$amount2Charge,$transactionType,$userEmail)
paypal_API->LogResponse($userID,$IPN_Response)
paypal_API->ProcessIPN($payPalPayload)
```
## Application Database Tables:
This is separate from the core Power Almanac content database tables.
1. Saved Searches
2. Saved Downloads
3. Saved Tables
4. Registered Users
5. Shared Searches
6. PayPal Transactions

Actual download (Zip archives) files may be saved in Amazon S3?


