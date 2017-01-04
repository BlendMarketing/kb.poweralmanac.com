## Database Questions



###### MySQL Database Access  
Hosted on AWS and accessible via http://poweralmanac.com/phpmyadminwith rootuser account.This is how I access the database. Sagi does it differently andprobably use SSH. I usePuTTY on Windows with the PPK key file (pagenm1.ppk) for shell accesswhen necessary.

##### 2.**Where's that database stored?**  
On AWSRDS instance and EC2. EC2 and RDS instances but created and configured by a third party AWS expert in Israel.Will need to tap Hanan and Etzion for details.

##### 3.**What happens on the weekly updates?**  
The data verification team in the Philippines finishes all updates before the weekly Sunday updates. All committed changes(all government ID in the work queue in FAstatus)are push from the iCube Government databases to the production Government databases. And the rest of the scripts to create the Analyze and Search meta-tables get run after that.More details on all scripts and schedule from Sagi...

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

