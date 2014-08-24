##run_analysis.R 
1. run_analysis.R is run against a provided data set and creates another data set that is clean and tidy.

2. run_analysis.R requires the installation of the "reshape2" library"

3. run_analysis.R assumes either 1) that the original data set is moved into the working directory or 2) that the working directory is set to the folder that contains the original data set.

4. The codebook entitled "Movement_Data_Dictionary.pdf" provides information about the original data set that is used by run_analysis.R.

5. Here is a line-by-line commentary on how run_analysis.R transforms the original data set:

    test_data = read.table("test/x_test.txt")
    train_data = read.table("train/x_train.txt")
    total_data <- rbind(test_data,train_data)
    labels = read.table("features.txt")
    labels_data <- as.character(labels[,2])
    colnames(total_data) <- labels_data
    e <- sort(c(grep("mean",labels_data),grep("std",labels_data),grep("Mean",labels_data)))
    total_data2 <- total_data[,e]
    
    test_labels = read.table("test/y_test.txt")
    train_labels = read.table("train/y_train.txt")
    total_labels <- rbind(test_labels,train_labels)
    test_subjects = read.table("test/subject_test.txt")
    train_subjects = read.table("train/subject_train.txt")
    total_subjects <- rbind(test_subjects,train_subjects)
    total_data3 <- cbind(total_subjects,total_labels,total_data2)
    
    colnames(total_data3) <- c("Subject_ID","Activity",labels_data[e])
    activities = read.table("activity_labels.txt")
    activities_col2 <- as.character(activities[,2])
    for (i in 1:nrow(total_data3)) {
      total_data3[i,2] <- (if (total_data3[i,2] == "1") activities_col2[1] else if (total_data3[i,2] == "2") activities_col2[2] else if (total_data3[i,2] == "3") activities_col2[3] else if (total_data3[i,2] == "4") activities_col2[4] else if (total_data3[i,2] == "5") activities_col2[5] else if (total_data3[i,2] == "6") activities_col2[6])
    }
    
    total_data4 <- melt(total_data3,id.vars=c("Subject_ID","Activity"))
    total_data5 <- dcast(total_data4,Subject_ID + Activity ~ variable,mean)
