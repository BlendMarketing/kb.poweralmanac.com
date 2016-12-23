/* Title: Order Process */

Once a user clicks download the order is processed according to a number of different paths.

## 0. checkout.php
The main script that receives the data from the search page is checkout.php. This script relies on data based in the session variable to retrieve the needed information. It then determines what the next step for processing the records is.

## 1. Login/Register User
As everything on the site relies on user accounts, a user must be logged in before proceeding. If they are not, they are sent to register.php to sign in/up.

## 2. Check Account Plan/Credits
The users account is checked to see how many credits they have, and to see what plan they are on.

1. If the user is a Unlimited Power subscriber, the [download is generated](/functions/generate-download) and they are directed to their dasbhoard page (/dashboard) and shown the download in their download list, with the option to download the file.

2. If the user is not an Unlimited Power subscriber, the user is directed to a checkout page showing the order and what the order will cost the user. If they have enough credits, it will show the user the total records they will have remaining after the download, and a download button to download the order. 

3. If they do not have enough credits, the checkout page will show the user the total monetary cost for the order and a checkout button for PayPal. Once they complete their Paypal order they will be sent back to the dashboard to view their download. In the background, Paypal will [process the transaction](/functions/process-paypal) 
