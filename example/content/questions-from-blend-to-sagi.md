# Original Documentation Questions for Sagi from Blend (Completed 7.8.16)
### **Where is the admin system hosted?**
not sure I understand your question. you have the icube_govenment database to update government+officials and insert new officials.

### **What happenes on the weekly updates?**
1.  manipulation on fields - Government_Web_Address, Last_name, Government_Title, Email_Address, Phone_Number (removing '-') , Government_PhoneNumber (removing '-') at the "ready" table.  
2.  updating "govenment" table according to fields -  Government_ID Role Role_exists First_Name Last_Name Government_Title Email_Address Part_of_Governing_Board Mailing_Street_Box Mailing_Suite_Number Mailing_City Mailing_State Mailng_Zip_Code Phone_Number Phone_Number_Ext Date_Confirmed from the "ready" table.  
3.  updating "government_officials" table according to fields -  Government_ID Role Role_exists First_Name Last_Name Government_Title Email_Address Part_of_Governing_Board Mailing_Street_Box Mailing_Suite_Number Mailing_City Mailing_State Mailng_Zip_Code Phone_Number Phone_Number_Ext Date_Confirmed   
from the "ready" table.
4. inserting new rows to table "government_officials" from the "ready" table.  
5. updating fields - First_Name ,Last_Name ,Government_Title ,Mailing_Street_Box ,Mailing_Suite_Number ,Mailing_City ,Mailing_State ,Mailng_Zip_Code ,Phone_Number ,Phone_Number_Ext ,Email_Address ,Email_Type ,Special_Notes to remove tab and " (char 9+34)  at the "governmane_officials" table.  
6. updating fields - Government_Web_Address, Government_PhoneNumber, Address_Street_Box, City to remove tab and " (char 9+34)  at the "governmanet" table.  
7. running - pop_Government_demographic SP which will re-create table - Government_Demographic table from tables - Government_Demographic_STG (which were loaded from LTBL_community-profile_2011.txt) and the lookup table - Government_Demographic_Lookup and table new_population_2010 (only the population is taken from this table).  
8. insert more 4 hardcoded govnments and their population to table Government_Demographic.  
9. updating govenment table, column - Population_2007 if needed . from table - new_population_2007.  
10. updating govenment table, column - Population_Category if population column is different. from table - government_demographic.  
11. e-build table all_demographic according to - county/township/municipality from table - government_demographic.  
12. re-build table all_budget from table - Government_Budget_Data (it was loaded from file).  
13.  remove tab and " (char 9+34)  at the "government_officials" table.  
14. creating table - all_data from tables - government_officials, government, all_budget, all_demographic, zip_code2 only where the role exists. (that will include population fields - Fiscal_Month according to Fiscal_Year_End_Date GovAge according to Original_Incorporation_Date) )  
15. building budget_research table.  
16. checking all downloads. - recreate - i/u_{SearchName}_{RegID} tables for each row in table - saveddownloads, update table saveddownloads - as needed.  
17. checking all searches.  - update table savedsearches according to table - ss_{SavedSer_ID}.   
18. creating full_data table from government and government_officials and zip_code2 tables.   
19. try to delete va/qc/fa_{job_id} tables in 'done' status according to the jobs table  
20. updating r_state='allocated' @ full_date table for govenment found at the- delete va/qc/fa_{job_id} tables.   
21. running general search for table named - "NickPA" with no search params so all DB will be taken.  
22.  running details on that search with 25 records.  
23. updating table - database_summary.  
24. running general search for table named - "createpa" with  search params - top_elected=0.   
25.  build all research tables (for the analyze tool) for the "createpa" search.  

### What's the process to change the name of a city?
update all_Data table +government_officials table.

##How do we update the demographic data by zipcode?
from table - government - you take the government_id according to the zip_code. and then you update table - new_population_2010. (running build_all_data SP is needed after).  

##How does data transform work for the analyze toolit's?  
counting data (government or officials) from search filter and doing aggregates according to the dimensions involved. about 60 tables are being created to include all the different dimensions. Descriptions of any recurring database maintenance scriptyou have everything in the crontab.  
--> crontab -l
1. weekly - backup of both DBs (using mysqldump).   
2. weekly - invoke the launch SP. DB events.  

--> mysql> show events;
1. daily - delete_daily_gov + delete_daily_official - deletes old reseach tables.   
2. daily delete_daily_old_paid_officials  - delete the 1+ year officials from the pay_* tables

##Table naming nomenclature for all the rest of the tables and how they’re createdCould you update the ERD you sent us previously to include any application tables that aren’t on it?   
There is not much to add. for saved searches you have -  ss_* tables SavedSearches for downloads -  s_* i_* u_*   tables. SavedSearches for icube -  va/qc/fa_{job_id} tables jobs full_data ready jobs employees notes for searches o_*,g_*, {search_tabe_name}

for analyze tool {search_table_name}_per_capita_no_data_vector {search_table_name}_exp_no_data_vector  
{search_table_name}_state_roles_1  
{search_table_name}_state_roles_govs  
{search_table_name}_total_exp_vector  
{search_table_name}_total_per_capita_vector  
{search_table_name}_state_roles_2  
{search_table_name}_state_roles_3  
{search_table_name}_state_roles_4  
{search_table_name}_GovTypes_roles_1  
{search_table_name}_GovTypes_roles_govs  
search_table_name}_GovTypes_roles_2  
{search_table_name}_GovTypes_roles_3  
{search_table_name}_GovTypes_roles_4  
{search_table_name}_Population_roles_1  
{search_table_name}_Population_roles_govs  
{search_table_name}_Population_roles_2  
{search_table_name}_Population_roles_3  
{search_table_name}_Population_roles_4  
{search_table_name}_state_GovTypes_1  
{search_table_name}_state_GovTypes_govs  
{search_table_name}_state_GovTypes_2  
{search_table_name}_state_GovTypes_3  
{search_table_name}_state_GovTypes_4  
{search_table_name}_state_Population_Categorys_1  
{search_table_name}_state_Population_Categorys_govs  
{search_table_name}_state_Population_Categorys_2  
{search_table_name}_state_Population_Categorys_3  
{search_table_name}_state_Population_Categorys_4  
{search_table_name}_Population_Categorys_GovTypes_1  
{search_table_name}_Population_Categorys_GovTypes_govs  
{search_table_name}_Population_Categorys_GovTypes_1P  
{search_table_name}_Population_Categorys_GovTypes_govsP  
{search_table_name}_Population_Categorys_GovTypes_2  
{search_table_name}_Population_Categorys_GovTypes_3  
{search_table_name}_Population_Categorys_GovTypes_4  
{search_table_name}_state_GovTypes_Total_Expenditure  
{search_table_name}_state_GovTypes_Expenditure_govs  
{search_table_name}_state_GovTypes_per_capita  
{search_table_name}_state_Population_Categorys_Total_Expenditure  
{search_table_name}_state_Population_Categorys_Expenditure_govs  
{search_table_name}_state_Population_Categorys_per_capita  
{search_table_name}_Population_Categorys_GovTypes_Total_Expenditure  
{search_table_name}_Population_Categorys_GovTypes_Expenditure_govs  
{search_table_name}_Population_Categorys_GovTypes_Total_ExpenditureP  
{search_table_name}_Population_Categorys_GovTypes_Expenditure_govsP  
{search_table_name}_Population_Categorys_GovTypes_per_capita  
{search_table_name}_Population_Categorys_GovTypes_per_capitaP  
{search_table_name}_budget_GovTypes_median_total  
{search_table_name}_budget_GovTypes_median_govs  
{search_table_name}_budget_GovTypes_median_per_capita  
{search_table_name}_budget_GovTypes_median_per_capita_govs  
{search_table_name}_budget_Population_median_total  
{search_table_name}_budget_Population_median_govs  
{search_table_name}_budget_Population_median_per_capita  
{search_table_name}_budget_Population_median_per_capita_govs  
{search_table_name}_budget_State_median_total  
{search_table_name}_budget_State_median_govs  
{search_table_name}_budget_State_median_per_capita  
{search_table_name}_budget_State_median_per_capita_govs  
{search_table_name}_budget_Total_Quartiles_median  
{search_table_name}_budget_Total_Quartiles_median_govs  
{search_table_name}_budget_Per_Capita_Quartiles_median   
{search_table_name}_budget_Per_Capita_Quartiles_median_govs   
{search_table_name}_state_GovTypes_per_capita_govs   
{search_table_name}_state_Population_Categorys_per_capita_govs  
{search_table_name}_Population_Categorys_GovTypes_per_capita_govs  
{search_table_name}_Population_Categorys_GovTypes_per_capita_govsP   
