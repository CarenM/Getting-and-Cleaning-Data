# This is the README.md as part of the course project for the Coursera course: Getting and Cleaning Data offered by Jeff Leek, PhD, Roger D. Peng, PhD, Brian Caffo, PhD

The script run_analysis.R merges two datasets (test.txt and train.txt) into one.
Adds a list of subjects and activities to the new dataset.
Reads the file with column headers and returns the column number of columns whose name contains either mean() or std() in exact that format.
The new dataset is subsetted according to the results from the column names containing mean() and std(), subjects and activities are also included.
The activities get descriptive names instead of a number-code and the columns are renamed for better understanding of what they contain.
The dataframe is then rearranged to show the average value for each measurement for each activity for each subject.
This rearranged and smaller dataframe is then written out into the text file: tidy_dataset.txt


data <- read.table("test/X_test.txt")
data <- rbind(data,  read.table("train/X_train.txt"))
activity <- read.table("test/y_test.txt")
activity <- rbind(activity, read.table("train/y_train.txt"))
activity$V1 <- as.factor(activity$V1)
subject <-  read.table("test/subject_test.txt")
subject <- rbind(subject, read.table("train/subject_train.txt"))
data <- cbind(data, subject, activity)
data <- data[, c(which(grepl("mean()", read.table("features.txt")$V2, fixed=TRUE)),which(grepl("std()", read.table("features.txt")$V2, fixed=TRUE)), 562, 563)] 
activity <- read.table("activity_labels.txt")
levels(data$V1.2) <- list(WALKING = 1, WALKING_UPSTAIRS = 2, WALKING_DOWNSTAIRS = 3, SITTING = 4, STANDING  = 5, LAYING = 6)
names(data) <- c(as.character(read.table("features.txt")[c(grep("mean()", read.table("features.txt")$V2, fixed=TRUE)),2]), as.character(read.table("features.txt")[c(grep("std()", read.table("features.txt")$V2, fixed=TRUE)),2]), "subject", "activity")
data$subject <- as.factor(data$subject)
library("reshape2")
tidy <- dcast(melt(data, id.vars= c("subject", "activity")), subject + activity ~ variable , fun=mean)
write.table(tidy, file = "tidy_dataset.txt", row.names=FALSE)
