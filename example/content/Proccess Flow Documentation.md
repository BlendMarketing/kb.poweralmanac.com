## Process Flows
**Mainly authored by Nick with few edits**  
* **A process flow documentation of an order (what are all the routines called, steps people go through, part of
its branching, one set versus another based on amount of credits, when you get kicked out to PayPal to buy
more credits etc.)**
The documentation that follows is based on how I(Nick) implemented this a few years back - Jon might have changed
the process flow and more...  
Each of the four use cases are documented as much as I can remember and flow charts drawn in the following
sections. IF you need even deeper details on each of these use cases, please ask.  
**NOTE:**
Messaging between my PHP code and Sagi’s Stored Procedures is via the standard JSON format. The
json_decode() function is used extensively throughout the system.
Production database is **government** and it also includes many dynamic user generated tables based on their
searches, payments (pay_*), and downloads (,s_dl*, i_dl*, u_dl*)  

## Use Case 1 (UC1): Power Almanac New Account Registration/Subscription
![roles](images/nick1.png)

After filling in the necessary registration forms, the final page will transfer the user to the PayPal hosted
checkout page. Processing is done securely (https) on PayPal servers. Upon completion from PayPal, there are
two possible outcomes: SUCCESS or FAILURE (this is processed asynchronously via the PayPal API and initiated
by PayPal).
The user can also cancel the transaction on PayPal. In this case, the user is taken back to the Power Almanac and
notified. User can continue to explore Power Almanac more and trying subscribing again.
FAILURE – User is bounced back to Power Almanac’s checkout page and asked to try and complete payment
with PayPal server again.
SUCCESS – Handshaking with the PayPal API, the certified XML text document from the transaction is sent to
Power Almanac, validated with the information received from PayPal versus what’s sent, and processed
accordingly. A copy of the XML certificate is also stored in the PayPal log table. After processing completes, the
user subscription level is changed from TRIAL to the level purchased and the equivalent starting amount of DL
credits is initialized or added to the outstanding DL count if not zero. The user is now set up and ready to
purchase leads without manual assistance (self-service).
Core PHP Files used: PA-ipn.php (async), PA-subauth-cancel.php, PA-subauth-success.php
SQL Routines used: UpdateSubscription, LogResponse
Tables & Fields modified: RegisteredUsers, PayPalTransactions

## Use Case 2 (UC2): Purchasing Leads with Sufficient Credits
![roles](images/nick2.png)

After completing a Power Almanac search and previewing the results, the user wants to download the detailed
leads. The user is presented with a ‘checkouts’ screen showing how the purchase amount is calculated (based on
subscription levels), how much credits they have in the DL bank, and all the details on the Power Almanac leads
‘terms of use’ (what is free, what is charged, etc.). If there are enough credits to cover the download, the user
can proceed to download and the credits are subtracted from the DL bank. If there are not enough credits for
the download, see UC3 below.
The download ZIP (CSV and text) is created from the shell script (/var/www/sh) that invokes Sagi’s stored
procedures. All download files are stored in the server (/var/www/html/dl directory).
Core PHP Files used: PA-Download.php, PA-ValidateAsIs.php, PA-ValidateFullSet.php
SQL Routines used: UpdateDLCount, CheckPaymentForUpdatesDownload, CheckPaymentForFullDownload
Shell scripts: SaveCurrentDownload
Tables & Fields modified: RegisteredUsers , UserPay (sub-users only)

## Use Case 3 (UC3): Purchasing Leads without Sufficient Credits
![roles](images/nick3.png)

Continuing from UC2, from the ‘checkout’ screen, the outstanding amount the user needed to ‘top up’ is
calculated. This is the amount passed to the secured PayPal server. Once the user agreed to the amount needed
to complete the download, they are transferred to the PayPal hosted checkout page. Upon completion from
PayPal, there are again two possible outcomes: SUCCESS or FAILURE (this is processed asynchronously via the
PayPal API and initiated by PayPal).
FAILURE – User is bounced back to Power Almanac’s checkout page and asked to try and complete payment
with PayPal server again.
SUCCESS – Same handshaking with the PayPal API as in UC1, the certified XML text document from the
transaction is sent to Power Almanac, validated with the information received from PayPal versus what’s sent,
and processed accordingly. A copy of the XML certificate is also stored in the PayPal log table. After processing
completes, the amount of credits is calculated (should match what was originally in the ‘checkout’ screen) and
added to the outstanding DL count. The user is now ready to re-run the search (stored in server system
variables) and be able to purchase the leads (they now have the exact amount of credits required). As in UC2,
since there are enough credits to cover the download, the user can proceed to download the leads and the
credits are subtracted from the DL bank. The rest is just like UC2.
Core PHP Files used: PA-ipn.php (async), PA-Download.php, PA-dlauth-cancel.php, PA-dlauth-success.php
SQL Routines used: GetUserInfo, GetSubscriptionInfo, UpdateDLCount, LogResponse
Shell scripts: SaveCurrentDownload
Tables & Fields modified: RegisteredUsers, PayPalTransactions
**NOTE:**
Secondary users (sub-users) cannot purchase extra credits – the Primary account holder have to initiate that
action as all the account details for payment is tied to their account.  
## Use Case 4 (UC4): Managing Downloads – Previous, Updates Only or Latest  
There is a situation here where there may be insufficient DL credits to download updates or new leads from the
same search when the initial download was created more than 12 months ago. The initial download ZIP is
always free to re-download but is subject to the Power Almanac terms of use.
If there are insufficient DL credits based on the download choice selected by the user, they are again transferred
to the PayPal server to ‘top up’ like in UC3.
Core PHP Files used: PA-ipn.php, PA-Download.php, PA-dlauth-cancel.php, PA-dlauth-success.php, PADownloadNew.php,
PA-DownloadFull.php, PA-DownloadUpdatesOnly.php, PA-ValidateAsIs.php, PAValidateFullSet.php,
PA-ValidateUpdatesOnly.php
SQL Routines used: UpdateDLCount, SavePayDataForFullDownload
Shell scripts: DownloadAll, DownloadUpdates
Tables & Fields modified: RegisteredUsers , UserPay (sub-users only), PayPalTransactions  

**Database fields manipulated, processing and order documentation**  
The database fields manipulated by the PHP scripts are self-explanatory the files show the exact fields and tables
modified or added or removed.
Database fields modified by the stored procedures are a lot more complicated but can be deduced from reading
the SQL code. Sagi would be best consulted.

