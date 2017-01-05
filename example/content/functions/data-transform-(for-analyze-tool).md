The Analyze tool is like an Excel Pivot table. You want to be able to view the data in various
configurations. But doing it in real-time is agonizingly slow. So we decided to pre-calculate all the possible
combinations during the weekly updates process into basic building blocks (analyze meta-tables). These analyze
meta-tables are quickly presentable without too much post-processing for the TALLY THE RECORDS view - the
ANALYZE PHP live code assemble these building blocks (SQL reads) based on the user selections from the dropdown
menu. But it still requires some processing (should be done in less than 5 minutes real-time) for the final
presentable data for the COMPARE SPENDING view. The compare spending final processing is started in the
background when the ANALYZE view is chosen initially (showing the TALLY spreadsheet). The PHP code checks
for a completion flag in the database before trying to display the COMPARE view. Once completed, the tables
are quickly read and displayed to the user.