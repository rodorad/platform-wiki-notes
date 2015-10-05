
[Download PDF](releaseNotes/release_notes_september_2015.pdf)

## New Features

### Encryption of HDFS folders used by Data Catalog component

HDFS-broker now contains new plan “encrypted”, which creates encrypted directory on hdfs. In other words: all user data uploaded to Trusted Analytics Platform is encrypted.

### JIRA project for Trusted Analytics Platform

For better and easier development there has been created JIRA project devoted to Trusted Analytics Platform - https://trustedanalytics.atlassian.net/secure/Dashboard.jspa. This is the place where you can do the following:

* enter defects for platform components
* see all open & resolved defects for the project
* see the list of all open change/feature requests
* create change/feature requests

### Dashboard chart using D3 component

Charts displaying platform statistics on dashboard are now using well known, open source D3 component (Data Driven Documents - http://d3js.org).

### RStudio and iPython exposed through go-router
Service-exposer was developed to overcome difficulties related to exposing service instances with HTTP interface to end-user. In order to access newly created service instances, service-exposer registers instances routes in Cloud Foundry.

### Support for folder binding on HDFS to dataset-reader component

Dataset-reader component (https://github.com/trustedanalytics/dataset-reader-sample.git) is currently capable of reading whole folder on HDFS.

### Integration of Uploader component with platform deployment scripts

Uploader component (https://github.com/trustedanalytics/uploader.git) is now part of core platform and is also included in platform deployment scripts.

###Updates to Trusted Analytics project’s wiki page

Definitions of "Priority" and "Severity" of bugs, new wiki page mentioning JIRA project devoted to Trusted Analytics Platform.

## Enhancements

### New methods in cf-client component (https://github.com/trustedanalytics/cf-client.git)

New methods implementation in cf-client component for getting environment variables for given app GUID and listing all services instances for given service plan.

### Method create service instance change in cf-client component (https://github.com/trustedanalytics/cf- client.git)

Method “createServiceInstance” was refactored and now returns metadata object with metadata and entity properties.

### IPython password exposure in Console

It’s available to retrieve IPython password without searching for it in application environment using command line.

### RStudio login & password exposure in Console

Huge facilitation for obtaining RStudio credentials from Trusted Analytics Platform console.

### Zookeeper-broker security enhancements

Zookeeper broker stored sensitive data in application.yml file, which is now delivered via Cloudfoundry manifest.

### CDH integration scripts for user group mapping

Implementation of integration scripts for CDH and parcels.


## Fixes

### Adding new user with incorrect e-mail address fix

Fixed application error when trying to add new user with inappropriate data.

### Problem with kafka2hdfs fixed

Fixed component error when pushing component to cloud foundry.

### Latest events box empty on Openstack environment fix

Fixed error of empty latest events box instead of message “No latest events”.

### Ghost pagination control fix

Fixed GUI pagination control displaying users list.

### Onboarding fix

Fixed issue with onboarding new user to the platform.

### Dataset results view fix

Fixed issue with datasets view control not displaying search results.

### HDFS instance creation fix

Fixed error when creating instance of HDFS service in marketplace.

### Organization delete fix

Platform allowed to delete organizations with added users. Validation rule has been implemented, currently it is impossible to delete organization with users.
