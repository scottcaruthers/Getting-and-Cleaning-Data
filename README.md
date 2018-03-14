# Getting-and-Cleaning-Data
Repository for collecting &amp; analyzing wearable computing data

*Purpose*\
Demonstrate ability to collect, work with, and clean a data set.

*Goal*\
Prepare a tidy data set that can be used for later analysis.

*Background*\
One of the most exciting areas in all of data science right now is wearable computing - see for example this article (http://www.insideactivitytracking.com/data-science-activity-tracking-and-the-battle-for-the-worlds-top-sports-brand/) 
Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

Data for the exercise can be found here: https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

*Process*\
Create an R script, run_analysis.R, that performs the following:
1. Merges the training and the test sets to create one data set
2. Extracts only the measurements on the mean and standard deviation for each measurement
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive variable names
5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and    each subject

*run_analysis.R script review:*
1. Load the data.table library
2. Download the data from the above URL
3. Read the X, Y, and subject data from the downloaded data
4. Merge the training and test data with data.frame
5. Extract the mean and standard deviation data with grep
6. Rename activity data with gsub
7. Label data set with descriptive variable names with gsub
8. Create tidy data set with average variable data/activity/subject as data_tidy.txt
