In this tutorial you will learn how to use RStudio and H2O on TAP to perform simple data analysis. In few steps you will get access to RStudio and H2O. Then you will upload sample data set to TAP platform and generate scoring engine using RStudio and H2O.

### Getting access to RStudio on TAP:

1. Go to TAP console Webpage.
1. Click "Data Science" and then "RStudio" in the left-side menu.
1. Create new RStudio instance by typing a name and clicking "Create new rstudio instance". Your instance should be ready within few seconds.
1. Refresh the Webpage. Credential to your RStudio should be visible in the list.
<TODO image>
1. Open "App Url" in a new card/window and try to authenticate with credentials given in the TAP console Webpage.
<TODO image>
1. After this step you should see something like this:
<TODO image>

### Installing H2O client in RStudio:

1. Find H2O url, username and password. It is on the "DP2 Environments" page on secure wiki.
1. Go to H2O Webpage and authenticate with user&password.
1. Find in top-menu buttons "Help" and then "About". You should see such popup:
<TODO image>
1. We need to find out "H2O Build project version". It is important to install client in version similar to the version of server.
**When you see version 3.0.1.99999 it means that you are connecting to a server spawned from our modified version of H2O. Actually is a version 3.0.1.7 with Basic Authentication implemented by our team.**
1. You need to find H2O documentation in the right version. For instance here you can find documentation for 3.0.1.7 version: http://h2o-release.s3.amazonaws.com/h2o/rel-simons/7/index.html
1. Go to "INSTALL IN R" card, and copy all lines up to "library(h2o)"
1. Go back to RStudio page.
1. Run installation script:
  1. Create new file: go to "File", then "New File" and then "R Script"
  1. Paste the content of your clipboard.
  1. Run script by highlighting whole file and clicking "Run"
<image>

### Run sample analysis:

1. Upload data set to TAP.
  1. Go to TAP console Webpage.
  1. Click "Data Catalog" in the left-side menu.
  1. Go to "Submit Transfer" card.
  1. Schedule uploading by setting "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data" in the "Link" field, setting "h2o_test_Iris" (or whatever you like) in the "Title" field and clicking "Upload" button.
  1. You can track download progress in the "Transfers" card. When it has status "DOWNLOADED" go to "Data sets" card.
  1. Find your data set (by "Title" specified in 1.4. step ) and click on its name to see details.
  1. On details page find "targetUri" field and copy its value to the clipboard.
2. Connect to H2P instance. 
  1. You will need "internal address", "username" and "password". You can find this information on the "DP2 Environments" page on secure wiki.
  1. Connect to H2O by executing such line (remember to change ip address, username and password to these acquired in the previous point):
    ```
    tapH2O = h2o.init("10.10.4.80",  strict_version_check = FALSE, username = "username", password = "p4$sw0rD")
    ```

3. Import folder from a hdfs by executing such line:
```
irisFrame <- h2o.importFolder(<here you need to paste "targetUri" acquired in 1.7. step>)
```
4 Train a classification tree:
```
irisModel <- h2o.gbm(x=1:4, y=5, training_frame = irisFrame)
```
5 Score the same dataset used for training:
```
h2o.predict(irisModel, irisFrame)
```
6 Generate scoring engine as a java code:
```
h2o.download_pojo(irisModel)
```

