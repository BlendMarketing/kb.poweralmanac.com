/* Title: Search */

The main search functionality is accessible at [http://www.poweralmanac.com/search-government](http://www.poweralmanac.com/search-government). This page provides a GUI to accessing the different search parameters available to the user. The page updates asynchrosly allowing easy selecting/deselecting of the different options. The php script behind this page is found at `search-government.php`. 

After making selections, the user is shown the total number of records that match his current search criteria, and then shown the price that he/she will have to pay (if any) to download the record set. The price is set up so that if the cost to download the current records is greater than it would be on one of the PA plans, then the user is shown the plan's pricing instead of the per record price. 

Once the user is comfortable with the selected criteria he can select either "Save Search" to save the criteria to his account for future reference or he can hit the download button and begin the [ordering process](/functions/order-process).

*Note:* Due to a glitch that can occur when a user deselects multiple options, then quickly hits the download button before the ajax results have processed properly resulting in a different data set being passed to the next step, once a user hits the download button, a final search with the most up to date settings is sent once more before proceeding to the next page. This ensures that the proper data is based from the search page to the checkout page.

