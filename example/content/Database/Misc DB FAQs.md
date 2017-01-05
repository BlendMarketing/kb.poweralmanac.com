# Database Questions
## **Where is the admin system hosted?**
not sure I understand your question. you have the icube_govenment database to update government+officials and insert new officials.

## MySQL Database Access  
Hosted on AWS and accessible via http://poweralmanac.com/phpmyadmin with rootuser account.This is how I access the database. Sagi does it differently and probably use SSH. I (Nick) use PuTTY on Windows with the PPK key file (pagenm1.ppk) for shell access when necessary.


## What's the process to change the name of a city?
update all_Data table +government_officials table.

## How do we update the demographic data by zipcode?
from table - government - you take the government_id according to the zip_code. and then you update table - new_population_2010. (running build_all_data SP is needed after).  

## How does data transform work for the analyze toolit's?  
counting data (government or officials) from search filter and doing aggregates according to the dimensions involved. about 60 tables are being created to include all the different dimensions. Descriptions of any recurring database maintenance scriptyou have everything in the crontab.  
--> crontab -l
1. weekly - backup of both DBs (using mysqldump).   
2. weekly - invoke the launch SP. DB events.  

--> mysql> show events;
1. daily - delete_daily_gov + delete_daily_official - deletes old reseach tables.   
2. daily delete_daily_old_paid_officials  - delete the 1+ year officials from the pay_* tables

## Table naming nomenclature for all the rest of the tables and how they’re created

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
