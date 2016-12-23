/* Title: Weekly Backup */

Every Sunday morning at 11:40, a backup cron job is run that moves the records that have been processed by the team in the Philippines into the main production database. Any records that have been marked "Done Updating" will be moved into the new database. In addition, a number of procedures will be done on existing searches to indicate wether there are any searches that have received updated records, in order to let people know they can download them.

Here is a break down of the specific steps involved.

1. Manipulation on fields from the ready table 

    * Government_Web_Address
    * Last_name 
    * Government_Title
    * Email_Address
    * Phone_Number (removing '-')
    * Government_PhoneNumber (removing '-') 

2. Updating `government` table according to fields from the `ready` table 

    * Government_ID
    * Role
    * Role_exists
    * First_Name
    * Last_Name
    * Government_Title
    * Email_Address
    * Part_of_Governing_Board
    * Mailing_Street_Box
    * Mailing_Suite_Number
    * Mailing_City
    * Mailing_State
    * Mailng_Zip_Code
    * Phone_Number
    * Phone_Number_Ext
    * Date_Confirmed
     
3. Updating `government_officials` table according to fields from the `ready` table

    * Government_ID
    * Role
    * Role_exists
    * First_Name
    * Last_Name
    * Government_Title
    * Email_Address
    * Part_of_Governing_Board
    * Mailing_Street_Box
    * Mailing_Suite_Number
    * Mailing_City
    * Mailing_State
    * Mailng_Zip_Code
    * Phone_Number
    * Phone_Number_Ext
    * Date_Confirmed

4. Inserting new rows to table "government_officials" from the "ready" table.
5. Updating fields to remove tab and  (char 9+34)  at the `governmanet_officials` table. 

    * First_Name
    * Last_Name 
    * Government_Title 
    * Mailing_Street_Box 
    * Mailing_Suite_Number
    * Mailing_City
    * Mailing_State
    * Mailng_Zip_Code
    * Phone_Number
    * Phone_Number_Ext
    * Email_Address
    * Email_Type
    * Special\_Notes 

6. Updating Fields to remove tab and " (char 9+34)  at the `governmanet` table. 

    * Government_Web_Address
    * Government_PhoneNumber
    * Address_Street_Box
    * City

7. Running `pop_Government_demographic` Stored Procedures which will re-create table `Government_Demographic` table from tables `Government_Demographic_STG` (which were loaded from LTBL_community-profile\_2011.txt) and the lookup table - `Government_Demographic_Lookup` and table `new_population_2010` (only the population is taken from this table).

8. Insert more 4 hardcoded governments and their population to table `Government_Demographic`

9. Updating government table column `Population_2007` if needed from table `new_population_2007`

10. Updating government table column `Population_Category` if population column is different. from table `government_demographic`

11. Re-build table `all_demographic` according to county/township/municipality from table `government_demographic` 

12. Rebuild table `all_budget` from table `Government_Budget_Data` (it was loaded from file).

13. Remove tab and " (char 9+34)  at the `government_officials` table.

14. Create Table `all_data` from tables 

    * government_officials
    * government
    * all_budget
    * all_demographic
    * zip_code2
    * Note: That will include populations fields, Fiscal\_Month according to Fiscal\_Year\_End\_Date, GovAge according to Original\_Incorporation\_Date 

15. Build `budget_research` table.

16. Checking all downloads. Recreate `i/u_{SearchName}_{RegID}` tables for each row in table `saveddownloads`. Update table `saveddownloads` as needed.

17. Checking all searches. Update table `savedsearches` according to table `ss_{SavedSer_ID}`

18. Creating `full_data` table from `government` and `government_officials` and `zip_code2` tables.

19. Try to delete `[va|qc|fa]_{job_id}` tables in `done` status according to `jobs` table.

20. Update `r_state`='allocated' in `full_date` table for `government` found in the `delete` column of `[va|qc|fa]_{job_id}` tables.

21. Running general search for table name `NickPA` with no search params so all DB will be taken.

22. Running details on that search with 25 records.

23. Update table `database_summary`

24. Run general search for table named `createpa` with search params `top_elected=0`

25. Build all research tables (for the analyze tool) for the `createpa` search. 





