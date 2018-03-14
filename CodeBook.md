
**_Data Set URL:_**<br />
https://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones#

**_Data Set Information (from Data Set URL):_**<br />
The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data.

The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). The sensor acceleration signal, which has gravitational and body motion components, was separated using a Butterworth low-pass filter into body acceleration and gravity. The gravitational force is assumed to have only low frequency components, therefore a filter with 0.3 Hz cutoff frequency was used. From each window, a vector of features was obtained by calculating variables from the time and frequency domain.

**_Data Set Files_**<br />
- 'features_info.txt': Shows information about the variables used on the feature vector.<br />
- 'features.txt': List of all features.<br />
- 'activity_labels.txt': Links the class labels with their activity name.<br />
- 'train/X_train.txt': Training set.<br />
- 'train/y_train.txt': Training labels.<br />
- 'test/X_test.txt': Test set.<br />
- 'test/y_test.txt': Test labels.<br />

The following files are available for the train and test data. Their descriptions are equivalent. 

- 'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30.<br /> 
- 'train/Inertial Signals/total_acc_x_train.txt': The acceleration signal from the smartphone accelerometer X axis in standard gravity units 'g'. Every row shows a 128 element vector. The same description applies for the 'total_acc_x_train.txt' and 'total_acc_z_train.txt' files for the Y and Z axis. <br />
- 'train/Inertial Signals/body_acc_x_train.txt': The body acceleration signal obtained by subtracting the gravity from the total acceleration.<br />
- 'train/Inertial Signals/body_gyro_x_train.txt': The angular velocity vector measured by the gyroscope for each window sample. The units are radians/second.<br />

**_The above data is then downloaded, transformed, and analyzed by the run_analysis.R script, below:_**<br />
*Overview of run_analysis.R script*<br />
This script pulls training and test data from accelerometers from Samsung Galaxy S smartphones

*Enable the data.table library*<br />
library(data.table)

*Navigate to, then download, source data*<br />
fileurl = 'https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip'
if (!file.exists('./UCI HAR Dataset.zip')){<br />
  download.file(fileurl,'./UCI HAR Dataset.zip', mode = 'wb')<br />
  unzip("UCI HAR Dataset.zip", exdir = getwd())<br />
}<br />

*Read UCI HAR data*<br />
features <- read.csv('./UCI HAR Dataset/features.txt', header = FALSE, sep = ' ')
features <- as.character(features[,2])

*Read x, activity, and subject information from train data and define each as separate variables*<br />
data.train.x <- read.table('./UCI HAR Dataset/train/X_train.txt')<br />
data.train.activity <- read.csv('./UCI HAR Dataset/train/y_train.txt', header = FALSE, sep = ' ')<br />
data.train.subject <- read.csv('./UCI HAR Dataset/train/subject_train.txt',header = FALSE, sep = ' ')<br />

*Create data frame from train data with subject, activity, and x variables combined*<br />
data.train <-  data.frame(data.train.subject, data.train.activity, data.train.x)<br />
names(data.train) <- c(c('subject', 'activity'), features)<br />

*Read x, activity, and subject information from test data and define each as separate variables*<br />
data.test.x <- read.table('./UCI HAR Dataset/test/X_test.txt')<br />
data.test.activity <- read.csv('./UCI HAR Dataset/test/y_test.txt', header = FALSE, sep = ' ')<br />
data.test.subject <- read.csv('./UCI HAR Dataset/test/subject_test.txt', header = FALSE, sep = ' ')<br />

*Create data frame from test data with subject, activity, and x variables combined*<br />
data.test <-  data.frame(data.test.subject, data.test.activity, data.test.x)<br />
names(data.test) <- c(c('subject', 'activity'), features)<br />

*Combine train and test data into data.all with rbind function*<br />
data.all <- rbind(data.train, data.test)<br />

*Select mean and standard deviation from data.all with grep function*<br />
*Create data.sub from data.all with concatenation*<br />
mean_std.select <- grep('mean|std', features)<br />
data.sub <- data.all[,c(1,2,mean_std.select + 2)]<br />

*Create activity labels through read function*<br />
activity.labels <- read.table('./UCI HAR Dataset/activity_labels.txt', header = FALSE)<br />
activity.labels <- as.character(activity.labels[,2])<br />
data.sub$activity <- activity.labels[data.sub$activity]<br />

*Rename activity labels with gsub function*<br />
name.new <- names(data.sub)<br />
name.new <- gsub("[(][)]", "", name.new)<br />
name.new <- gsub("^t", "Time_", name.new)<br />
name.new <- gsub("^f", "Frequency_", name.new)<br />
name.new <- gsub("Acc", "Accelerometer", name.new)<br />
name.new <- gsub("Gyro", "Gyroscope", name.new)<br />
name.new <- gsub("Mag", "Magnitude", name.new)<br />
name.new <- gsub("-mean-", "_Mean_", name.new)<br />
name.new <- gsub("-std-", "_StandardDeviation_", name.new)<br />
name.new <- gsub("-", "_", name.new)<br />
names(data.sub) <- name.new<br />

*Aggregate combined data by activity and subject<br />
*Create new tidy data table with mean observations across variables*<br />
tidy_data <- aggregate(data.sub[,3:81], by = list(activity = data.sub$activity, subject = data.sub$subject),FUN = mean)<br />
write.table(x = tidy_data, file = "tidy_data.txt", row.names = FALSE)<br />
