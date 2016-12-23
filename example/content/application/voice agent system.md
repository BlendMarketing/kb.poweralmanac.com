###### Voice Agent System 
This is hosted on Amazon Web Service (AWS) at: http://www.poweralmanac.com/jxarh/  
Also referred to as **iCube**.  

Roles are: Process Initiator (PI), Voice Agents (VA), Quality Checker (QC), and FinalApprover(FA). Workflowis from VA to QC (and maybe back to VA if errors found) to FA (and maybe back to VA/QC for errors). Once a government is in the FAstage, it gets processed and moved into production(Data Uploader).  

Implemented with a combination of PHP, YUI,SQL, and stored procedure calls like the main Power Almanac application.  