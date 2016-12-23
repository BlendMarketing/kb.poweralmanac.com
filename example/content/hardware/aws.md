/* Title: AWS Infrastructure */

Power Almanac makes extensive use of Amazon Web Services (AWS) for hosting the Power Almanac application. Here is a list of the different services currently employed in the applications.

* EC2 for Web Servers
* RDS for Database Hosting and backups
* S3 for hosting downloads (coming soon)

## EC2

Currently there is one server hosted on EC2 that hosts the php application. This IP address serves as the end point for the domain name `www.poweralmanac.com` and handles the bulk of the server load.

## RDS

Poweralmanc uses 1 RDS server to manage the database needs of the application. In addition to hosting the application, the RDS service also provides rolling backups up to 5 minutes of previous activity.

## S3 (coming soon)

PA stores the generated download files on the S3 servicve for long term keeping. This minimizes the need for storing them on the server and minimizes the depenecy of having files stored on the server.
