> As a data scientist, build a model for anomaly detection in a data stream with a single dimension (metric).

## Preconditions 
* Intel® Analytics Toolkit for Apache Hadoop* software already includes libraries for use in this model.
* Data is cleansed and formatted correctly.  
* The application maintains the state of the most recent result.  
* An engine (rules, report, visualization, etc.) applies business logic to the result.

## Model training
* Acquire a training data set. 
* Calculate mean and standard deviation on the training set. 
* Update mean and SD for each incoming data point (online learning). 

## Model deployment
* Model requires input: value.
* Model returns output: mean, SD.