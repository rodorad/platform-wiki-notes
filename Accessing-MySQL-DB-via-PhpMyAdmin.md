Do the following:

9. Get the PhpMyAdmin tool:

      `git clone https://github.com/cloudfoundry-community/phpmyadmin-cf`

9. Log on to a platform with your cf client. (Choose an appropriate org and space.)

9. Go to the phpmyadmin folder and push the downloaded application to the target org and space. 

9. Log on to the platform using your user/password credentials and choose the target org and space.

9. Go to *Marketplace* and spin up a new instance of MySQL database. 

9. Go to *Applications* and find your instance of PhpMyAdmin app.

9. Bind your instance of MySQL DB to your instance of PhpMyAdmin. 

9. Go to the cf command line and run the following command: 

    `cf env <the name of your instance of PhpMyAdmin>`

 ![](https://github.com/trustedanalytics/platform-wiki/blob/master/wikiImages/platform-getting-credentials-for-mysql.png)

9. Get the credentials for your MySQL DB.

9. Return to your browser and follow the URL that is exposed by your instance of PhpMyAdmin tool.

9. You should be presented with the PhpMyAdmin login page. Once you enter credentials to your instance of MySQL DB, you will get access to your MySQL DB. 



