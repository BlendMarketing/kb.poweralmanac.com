# Documentation Questions from Ron to Sagi (8.1.16)   
#### 1. How do we change the NAME of a local government?  
you should update the correct field of the govenment table + same field name @ all_data table (instead of re-bulding the all_data table from 0). I'm not sure which field you reffer to at the government table, but probably - Government_Place_Name.  
record for example from the government table:  

```Government_ID: 35302102800000       
Government_Place_Name: Steiner        
Government_Type_Name: TOWNSHIP      
Government_Web_Address:      
Government_PhoneNumber:  
Dept_Name_for_phone_number:         
Government_Type: township          
Form_of_Government: 7         
Governing_Body_Name: 0             
County_Name: HETTINGER        
Fiscal_Year_End_Date: 0331
Original_Incorporation_Date: 0     
Type_Code: 3                 
County_Code: 021                   
Unit_Code: 028                  
FIPS_State: 38                 
FIPS_County: 41                  
FIPS_Place: 75860                  
Population: 25        
Population_Category: P1            
Population_2007: 31    
Population_2007_Category: P1      
Taken_from:             
Updated_website:                     
state: ND          
Address_Street_Box:                 
City:                    
Zip_Code:                      
Gov_ID: 25151              
Electron_Month: 0
```

#### 2.How do we DELETE a local government (and all the records associated with it)?
1) delete from government_officials according to the government_id.     
2) delete 1 records from government.
3) re-build all_data and everything.

#### 3.How do we update the population data? (NOTE – We haven’t done this in a few years, and I’m hoping to give you new population data for all 39,000 local governments sometime this month.)  
update table - new_population_2010 and re-build all data.
you also have table - Government_Demographic_STG that have demographic related data (the quartiles are built according to this table.

#### 4.How do we update the SPENDING (budget) data?   (NOTE – I’m not sure, but I think that the census bureau has an updated set of this data available now.   Will you be able to tell me the format you need the updated data, and will you then be able to incorporate the new data into  the system?)
update table - government_budget_data and rebuild all data. basically, I can adjust the loading to any format you have.. just make sure you have fields - Government_ID,Item_Code,Amount,Year_of_data,Imputation_type

#### 5.CONTEXT:  There are about 39,000 local governments in the Power Almanac database, of which about 21,000 are ͞live͟ for users (i.e.  we have records for them).   The other 18,000 are, for the most part, ones that have a population of less than 1,000 people.   After we upload the fresh population data, some of those 18,000 governments might have more than 1,000 people, and therefore we may want to have our team in the Philippines start calling them and creating records for them.  QUESTION:   How do we move a local government from the ͞in-active͟  list to the ͞live͟ list (and therefore included in the ͞data maintenance͟ system and showing up on our live website)?
you have currently  39,022 records @ government table. 289,835 records @ giverment_officials table from them 21,012 unique governments used. if I examine the data more @ government_officials table , I have -  mysql> select role,count(distinct government_id) total_govs from government_officials group by role;

which means that in order to make government "alive" we will have to -  
1) insert 8 dummy records of role_exists='No' of each role besides role "Top Elected Official".
2) insert 1 dummy record of role_exists='Yes' of role "Top Elected Official".
3) NOT NULL fields must have values. the columns are -  
Government_ID  
First_Name  
Last_Name  
Mailing_Street_Box  
Mailing_City  
Mailing_State   
Phone_Number   
Email_Address

#### 6.If we decided to add another 1-2 roles for each government, would you know how to make that happen (in the databases and in the maintenance system).  For example, what if I wanted to add the ͞head of HR͟ as a new role for our team in the Philippines to start covering next year?
unfortunately it is not as simple as previous items. I made a summary of what have to be done -  
1) changing enum field of role in ALL relevant tables like all_data.SP - role_column should be used to update the column type in order to change the role column type in all of the DB.
2) build_all_data SP has to be updated
3) new column r11_exists have to be added as well.  
4) main search should be updated -  SearchDetails2 and SearchSummary2 and SearchSummarySpecial2 SPs, will have get a new search parameters for the new role chosen/sent from the PHP layer.
5) add dummy data to government_officials with role_exists='No' ---> for each government that have at least 1 record @ government officials table add 1 dummy record with the new role.
6) update the analyze SP to include the new role only on the role related tables.  (column OR row will have to be added.. and probably changes @ PHP layer will be needed as well to get this new row/column).anyway, after performing such change, we should first test it on TEST environment. Can we use the old environment and connect it to the web ??  (I see the DB machine is up and running).

#### 7.Right now we track the ͞election month͟ of each local government, but we don’t make it visible on our website.  If we want to make it visible, do you know where to find that ͞election month͟ data in the databases?

it's found at the government table -->  mysql> select electron_month,count(*) total from government group by electron_month order by 2 desc;
I have have already written version of SearchSummary2 procedure (but it's not online now, probably you didn't want it in the past) that will support both - search condition for it and show this column. (PHP will have to support the output of course)

#### 8.How does the ͞Analyze͟ functionality on the website work, and the coding and database tables behind it?
the SP involved is - Bulid_Research_Tables2 . it's taking the search results and building many aggregates based on different horizontal/vertical dimensions. about 68 tables are built for each aggregate --> {search_table_name}_per_capita_no_data_vector  
{search_table_name}_exp_no_data_vector   
{search_table_name}_state_roles_1   
{search_table_name}_state_roles_govs   
{search_table_name}_total_exp_vector   
{search_table_name}_total_per_capita_vector   
{search_table_name}_state_roles_2   
{search_table_name}_state_roles_3   
{search_table_name}_state_roles_4   
{search_table_name}_GovTypes_roles_1   
{search_table_name}_GovTypes_roles_govs   
{search_table_name}_GovTypes_roles_2   
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

The cleanup is done with - Delete_Research_Tables procedure, it will also clean the search results.

#### 9. Could you give a summary of the code that keeps track of which records a subscriber has already downloaded so that they’re not “charged” when they download the same record within 1 year?  
If we wanted to change “within 1 year” to “within 6 months”, how would we do that? for every subscriber pay_{reg_id} table is built.  this table have the official id and the expiration date. in case the record exists at the pay table it means that the subscriber have the credit for it. the daily event - delete_daily_old_paid_officials will make sure you remove all 1+ year officials according to the expiration column found for every official at the each pay_* table. it appears you migrated the DB WITHOUT the DB events.. is there any particular reason for that ??  in this situation  no official will ever expire. please give me a green light to copy those important events.

#### 10. What is The code that prevents a subscriber’s download credits from ever having a “negative balance”?
there is no such thing on my side that prevents negative results. I'm talking about field - User_DL_Reserves in table - RegisteredUsers. the procedure -  UpdateSubscription , get "reserves" as input. and it does - (besides updating other fields at table RegisteredUsers table) - User_DL_Reserves = User_DL_Reserves + reserves. so if the input is negative and abs(input)>User_DL_Reserves , the output for User_DL_Reserves will be negative. we should understand from the PHP layer when and why - UpdateSubscription SP is being invoked. of course I can add a code to prevent such thing but we should understand why it's happening first.  also , SP - UpdateUserDLCredits is updating this field (just updating the field as the input). so if the input < 0 it will also cause a negative resault.
