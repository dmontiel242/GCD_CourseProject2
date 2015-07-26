Assumptions: The run_analysis script assumes UCI data has been downloaded manually and run_analysis.r is placed in top level directory. test and training data set files are located in appropiate subfolders.

Scripts:

run_analysis.r : script reads in measurement training and test datasets, combines them, extracts measurements corresponding to mean and std, calculates the mean of each of these mean and std measurements for every subject and activity

Required Datasets:
X_train.txt
Y_train.txt
Y_test.txt
X_test.txt
subject_test.txt
activity_labels.txt


library(reshape2)
#read in and combine training and test datasets
xtrain <- read.table("C:\\Users\\Dan\\Desktop\\Coursera\\getdata-projectfiles-UCI HAR Dataset\\UCI HAR Dataset\\train\\X_train.txt")#load in traing set
xtest <- read.table("C:\\Users\\Dan\\Desktop\\Coursera\\getdata-projectfiles-UCI HAR Dataset\\UCI HAR Dataset\\test\\X_test.txt")#load in traing set
xcomb <-rbind(xtrain,xtest)
ytrain <-read.table('C:\\Users\\Dan\\Desktop\\Coursera\\getdata-projectfiles-UCI HAR Dataset\\UCI HAR Dataset\\train\\Y_train.txt',col.names = 'ActivityLabel')
ytest <-read.table('C:\\Users\\Dan\\Desktop\\Coursera\\getdata-projectfiles-UCI HAR Dataset\\UCI HAR Dataset\\test\\Y_test.txt',col.names = 'ActivityLabel')
ycomb <- rbind(ytrain,ytest)
subjecttrain <-read.table('C:\\Users\\Dan\\Desktop\\Coursera\\getdata-projectfiles-UCI HAR Dataset\\UCI HAR Dataset\\train\\subject_train.txt',col.names = 'Subject')
subjecttest <-read.table('C:\\Users\\Dan\\Desktop\\Coursera\\getdata-projectfiles-UCI HAR Dataset\\UCI HAR Dataset\\test\\subject_test.txt',col.names = 'Subject')
subjcomb <-rbind(subjecttrain,subjecttest)

#read in feature  and activity labelstable
feat = read.table('C:\\Users\\Dan\\Desktop\\Coursera\\getdata-projectfiles-UCI HAR Dataset\\UCI HAR Dataset\\features.txt')
activity_labels <-read.table('C:\\Users\\Dan\\Desktop\\Coursera\\getdata-projectfiles-UCI HAR Dataset\\UCI HAR Dataset\\activity_labels.txt')
f2 <-f2 <-function(x) activity_labels[x,2]

#determine which features are measurements of mean or std
extract <- grepl('mean',feat[,2],ignore.case=TRUE)|grepl('std',feat[,2],ignore.case=TRUE)

#reduce data set to measurements only of mean and std
reducexcomb = xcomb[,extract]
colnames(reducexcomb)<-feat[extract,2]
ad <-data.frame(activity_description=lapply(ycomb,f2))
subj <-data.frame(subject=subjcomb)
smallcomb <-cbind(subj,ad,reducexcomb)

#melt dataset based on Subject and Activity label
smallmelt <- melt(smallcomb, id = c('Subject','ActivityLabel'),measure.vars = colnames(reducexcomb))
#use dcast to calculate mean of each measurement based on each subject and activity label
meansmallmelt <- dcast(smallmelt, Subject+ActivityLabel~variable,mean)
#write output to textfile
write.table(meansmallmelt,file = 'step5tidydataset.txt',row.names=FALSE)
