#Script explanation
==================
1.-  library(plyr)

     loads the plyr library in order to later use ddply function

2.-  f_widths <- matrix(as.integer(16), 561, 1)

     creates a matrix with 561 rows, all of them with one column with value 16

3.-  features <- read.table (file = "UCI HAR Dataset/features.txt", header = FALSE, sep = " ", col.names = c("f_num", "f_name"), as.is = TRUE)

     reads features names from "UCI HAR Dataset/features.txt" and stores the data into a data frame called "features" with columns "f_num" and "f_name"

4.-  x_train<- read.fwf(file = "UCI HAR Dataset/train/X_train.txt", widths = f_widths[,1], header = FALSE, col.names = features$f_name, buffersize = 100)

     reads train dataset variable values from "UCI HAR Dataset/train/X_train.txt", using fixed width (f_widths created in step 2) and no header,
     stores the data into a data frame called "x_train" with columns readed in step 3
     buffering the read/write operation to avoid memory exhaustion

5.-  y_train<- read.table(file = "UCI HAR Dataset/train/y_train.txt", header = FALSE, col.names = "act_num")

     reads train dataset activity code values from "UCI HAR Dataset/train/y_train.txt" and stores the data into a data frame called "y_train" with one column "act_num"

6.-  s_train<- read.table(file = "UCI HAR Dataset/train/subject_train.txt", col.names = "subject")

     reads train dataset subject values from "UCI HAR Dataset/train/subject_train.txt" and stores the data into a data frame called "s_train" with one column "subject"

7.-  train<- cbind(x_train, y_train, s_train)

     creates "train" data frame bindind columns from "x_train", "y_train", "s_train"

8.-  x_test<- read.fwf(file = "UCI HAR Dataset/test/X_test.txt", widths = f_widths[,1], header = FALSE, col.names = features$f_name, buffersize = 100)
9.-  y_test<- read.table(file = "UCI HAR Dataset/test/y_test.txt", header = FALSE, col.names = "act_num")
10.- s_test<- read.table(file = "UCI HAR Dataset/test/subject_test.txt", col.names = "subject")
11.- test<- cbind(x_test, y_test, s_test)

     Steps 8 to 11 creates "test" data frame in a similar way that "train" (steps 4 to 7) was created
     
12.- all_data<-rbind(train, test)

     creates a unified "all_data" data frame binding rows from "train", "test"
     
13.- filtered_data <- all_data[,grepl("mean()|std()|act_num|subject", x = names(all_data)) & !grepl("meanFreq", x = names(all_data))]

     filters only mean and standard deviation columns ( not considering meanFreq into selection)
     
14.- act_labels <- read.table (file = "UCI HAR Dataset/activity_labels.txt", header = FALSE, sep = " ", col.names = c("act_num", "act_label"), as.is = TRUE)

     reads activity labels from "UCI HAR Dataset/activity_labels.txt" and stores the data into a data frame called "act_labels" with columns "act_num", "act_label"
     
15.- filtered_labeled_data<- merge(x = filtered_data, y = act_labels, by = "act_num")

     adds "act_label" column by merging "filtered_data" with "act_labels"
     
16.- act_sub_means<- ddply(filtered_labeled_data, c('act_label', 'subject'), function(x) c(colMeans(x[,2:67])))

     groups the data by activity ("act_label"), and subject ("subject") and calculate column means only for variable values (final dataset)
     
17.- write.table(x = act_sub_means, file = "act_sub_means.txt", row.name=FALSE)

     writes the final dataset to a file "act_sub_means.txt" in the working directory

# Final Dataset Features
======================
The features selected for this dataset represents the average of the Mean values (mean..) and the average of Standard deviations (std..) for each of the following variable groupped by activity and subject.

tBodyAcc-XYZ
tGravityAcc-XYZ
tBodyAccJerk-XYZ
tBodyGyro-XYZ
tBodyGyroJerk-XYZ
tBodyAccMag
tGravityAccMag
tBodyAccJerkMag
tBodyGyroMag
tBodyGyroJerkMag
fBodyAcc-XYZ
fBodyAccJerk-XYZ
fBodyGyro-XYZ
fBodyAccMag
fBodyAccJerkMag
fBodyGyroMag
fBodyGyroJerkMag

Note: each variable that contains '-XYZ' is represented by 3-axial signals in the X, Y and Z directions:
e.g. tBodyAcc-XYZ is  represented in the dataset by:

"tBodyAcc.mean...X"
"tBodyAcc.mean...Y"
"tBodyAcc.mean...Z"
"tBodyAcc.std...X"
"tBodyAcc.std...Y"
"tBodyAcc.std...Z"