# **What happenes on the weekly updates?**
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