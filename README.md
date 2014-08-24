###run_analysis.R 
1. run_analysis.R is run against a provided data set and creates another data set that is clean and tidy.

2. run_analysis.R requires the installation of the "reshape2" library

3. run_analysis.R assumes either 1) that the original data set is moved into the working directory or 2) that the working directory is set to the folder that contains the original data set.

4. The codebook entitled "Movement_Data_Dictionary.pdf" provides information about the original data set that is used by run_analysis.R and how the data is transformed.

5. Here is a line-by-line commentary on how run_analysis.R transforms the original data set:

    Test data is imported into a table test_data
    ``` 
    test_data = read.table("test/x_test.txt")
    ```
    Train data is imported into a table train_data
    ```
    train_data = read.table("train/x_train.txt")
    ```
    test_data and train_data tables are combined into total_data
    ```
    total_data <- rbind(test_data,train_data)
    ```
    Column headings for the data are imported and converted to text
    ```
    labels = read.table("features.txt")
    labels_data <- as.character(labels[,2])
    ```
    Column headings are applied to the data table total_data
    ```
    colnames(total_data) <- labels_data
    ```
    A vector is created that consists of the column numbers of the column headings that contain text of "mean", "std", or "Mean".
    ```
    e <- sort(c(grep("mean",labels_data),grep("std",labels_data),grep("Mean",labels_data)))
    ```
    total_data is reduced to columns identified in previous step and assigned to total_data2
    ```
    total_data2 <- total_data[,e]
    ```
    Activity codes for test data are imported
    ```
    test_labels = read.table("test/y_test.txt")
    ```
    Activity codes for train data are imported
    ```
    train_labels = read.table("train/y_train.txt")
    ```
    Activity codes for test and train data are combined
    ```
    total_labels <- rbind(test_labels,train_labels)
    ```
    Subject codes for test data are imported
    ```
    test_subjects = read.table("test/subject_test.txt")
    ```
    Subject codes for train data are imported
    ```
    train_subjects = read.table("train/subject_train.txt")
    ```
    Subject codes for test and train are combined
    ```
    total_subjects <- rbind(test_subjects,train_subjects)
    ```
    Activity and Subject codes are combined with data
    ```
    total_data3 <- cbind(total_subjects,total_labels,total_data2)
    ```
    Column names are applied to the data table
    ```
    colnames(total_data3) <- c("Subject_ID","Activity",labels_data[e])
    ```
    Activity labels that correlate to the Activity codes are imported and converted to text
    ```
    activities = read.table("activity_labels.txt")
    activities_col2 <- as.character(activities[,2])
    ```
    Activity codes are converted to Activity labels
    ```
    for (i in 1:nrow(total_data3)) {
      total_data3[i,2] <- (if (total_data3[i,2] == "1") activities_col2[1] else if (total_data3[i,2] == "2") activities_col2[2] else if (total_data3[i,2] == "3") activities_col2[3] else if (total_data3[i,2] == "4") activities_col2[4] else if (total_data3[i,2] == "5") activities_col2[5] else if (total_data3[i,2] == "6") activities_col2[6])
    }
    ```
    Data table is "melted" into id and variable categories for use in the next step
    ```
    total_data4 <- melt(total_data3,id.vars=c("Subject_ID","Activity"))
    ```
    A table is created showing the mean of each variable, broken down by Subject and Activity
    ```
    total_data5 <- dcast(total_data4,Subject_ID + Activity ~ variable,mean)
    ```
