## Database Questions

##### 1. **Where's the admin system hosted?**  
Not sure if you are referring to the database access or the data entry admin system.  
###### Voice Agent System 
This is hosted on Amazon Web Service (AWS) at: http://www.poweralmanac.com/jxarh/  
Also referred to as **iCube**.  

Roles are: Process Initiator (PI), Voice Agents (VA), Quality Checker (QC), and FinalApprover(FA). Workflowis from VA to QC (and maybe back to VA if errors found) to FA (and maybe back to VA/QC for errors). Once a government is in the FAstage, it gets processed and moved into production(Data Uploader).  

Implemented with a combination of PHP, YUI,SQL, and stored procedure calls like the main Power Almanac application.  

*Let me know if you want to access or more details as there are many stored procedures and PHP code involved...* 

###### MySQL Database Access  
Hosted on AWS and accessible via http://poweralmanac.com/phpmyadminwith rootuser account.This is how I access the database. Sagi does it differently andprobably use SSH. I usePuTTY on Windows with the PPK key file (pagenm1.ppk) for shell accesswhen necessary.

##### 2.**Where's that database stored?**  
On AWSRDS instance and EC2. EC2 and RDS instances but created and configured by a third party AWS expert in Israel.Will need to tap Hanan and Etzion for details.

##### 3.**What happens on the weekly updates?**  
The data verification team in the Philippines finishes all updates before the weekly Sunday updates. All committed changes(all government ID in the work queue in FAstatus)are push from the iCube Government databases to the production Government databases. And the rest of the scripts to create the Analyze and Search meta-tables get run after that.More details on all scripts and schedule from Sagi...

##### 4.**What's the process to change the name of a city?**  
Need to ask Sagi...  

##### 5. **How do we update the demographic data by zip code?**
Need to ask Sagi...  
##### 6. **How does data transform work for the analyze tool**
This is a BIG one. The Analyze tool is like an Excel Pivot table. You want to be able to view the data in various
configurations. But doing it in real-time is agonizingly slow. So we decided to pre-calculate all the possible
combinations during the weekly updates process into basic building blocks (analyze meta-tables). These analyze
meta-tables are quickly presentable without too much post-processing for the TALLY THE RECORDS view - the
ANALYZE PHP live code assemble these building blocks (SQL reads) based on the user selections from the dropdown
menu. But it still requires some processing (should be done in less than 5 minutes real-time) for the final
presentable data for the COMPARE SPENDING view. The compare spending final processing is started in the
background when the ANALYZE view is chosen initially (showing the TALLY spreadsheet). The PHP code checks
for a completion flag in the database before trying to display the COMPARE view. Once completed, the tables
are quickly read and displayed to the user.
More details from Sagi on the SQL code...  

##### 7. **Descriptions of any recurring database maintenance script**  
Need to ask Sagi...  

##### 8. **Table naming nomenclature for all the rest of the tables and how they’re created**
Need to ask Sagi...

##### 9. **Could you update the ERD you sent us previously to include any application tables that aren’t on it?**
Need to ask Sagi...  

## Programming Questions
* **A process flow documentation of an order (what are all the routines called, steps people go through, part of
its branching, one set versus another based on amount of credits, when you get kicked out to PayPal to buy
more credits etc.)**
The documentation that follows is based on how I implemented this a few years back - Jon might have changed
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

