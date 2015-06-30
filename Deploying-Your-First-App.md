If you are new to deploying apps on the Trusted Analytics platform (TAP) or an instance of Cloud Foundry, these steps will guide you through the process.

## Tools

Aside from having an IDE to code your application, the primary tool to deploy apps is the CLI (Command Line Interface) for Cloud Foundry. You can prepare your environment and install this tool by visit [this page](Development-environment-setup) or going to TAP Console >> Tools >> App Developer.

## Sample Application

A good general Cloud Foundry app is "Spring Music", which you can clone from guthub here: https://github.com/cloudfoundry-samples/spring-music. This app requires little knowledge of the platform and also features hooks for MongoDB, a good service to be familiar with.

The TAP team has also built a sample app for working with this platform and pulling data from hdfs. While more advanced, this is also a good app example to clone and explore: https://github.com/trustedanalytics/dataset-reader-sample. We've created a full workflow around trying this app as it explains in more detail how to build apps around data ingested into TAP. You can view that here: https://github.com/trustedanalytics/dataset-reader-sample/wiki/HDFS-Dataset-Reader-App-Exercise-for-Trusted-Analytics

## Assembly

In order to deploy Java apps to the Trusted Analytics platform, you must assemble them using a tool like Maven. You can download Maven here: https://maven.apache.org/download.cgi. If you are entirely new to Maven, here are some good steps to get familiar with the app building/assembly process: https://maven.apache.org/guides/getting-started/.

## Pushing the App

You will use the CLI to push the app to your TAP instance.

```
$ cf login -a <Your TAP instance Endpoint>
  API endpoint: <Your TAP instance Endpoint>
  Username> your_username
  Password> your_password
  Org> your_org
  your_space
$ cf push your_app
```

## Viewing the App and its instance in TAP

With the app successfully pushed, you can now view it on your instance of TAP. Go the TAP Console >> Applications to view the app's instance. Clicking the URL will load the app. Clicking "See details" will reveal the profile of the app with controls to start|stop|restage it.

![](https://github.com/intel-data/platform-wiki/blob/master/wikiImages/TAP_console_spring_music_app_instance.png)

If you are new to deploying apps on the Trusted Analytics platform (TAP) or an instance of Cloud Foundry, these steps will guide you through the process.

## Binding a Service

The service brokerage of TAP, found in Console >> Marketplace, makes it easy to add popular services like MongoDB to your application. This highlight extensible aspect of TAP and Cloud Foundry allows you to easily integrate instances of data stores as well as 3rd party services to your app. You can also programmatically add services, including making your own server logs available as a service.

Continuing with the "Spring Music" app example above, you can easily create and bind an instance of the MongoDB service to your app. This is good to do since, up until now, the app has only been storing entries in its own memory. Should the app need to be restaged, it will lose all its data. 

Go to Console >> Marketplace and browse to the "MongoDB" service. Click on it and in the service's profile, add a new instance with name "myMongo". Once available, the instance will list below showing you which space it is available in.

![](https://github.com/intel-data/platform-wiki/blob/master/wikiImages/TAP_console_marketplace.png)

![](https://github.com/intel-data/platform-wiki/blob/master/wikiImages/TAP_console_add_mongodb_instance.png)

Going back to Console >> Applications, view your "spring-music" profile by clicking its "See details" link. Click on the "Bindings" tab. You should see the "myMongo" instance listed in the "Available service instances" to the right. Click "Bind". You will now be promoted to click "Restage" so the app will be restaged now bound to the MongoDB instance you created. Once restaged, all data entered will be stored in the MongoDB instance, thus creating a persistent data store for the app.

![](https://github.com/intel-data/platform-wiki/blob/master/wikiImages/TAP_console_bind_service.png)