# FINAL REPORT
### by Erendiz Tarakci

## Introduction:
For the ETL project, I was interested in using electroencephalography (EEG) data since that is one of the primary data I collect, preprocess, and analyse at work. An EEG usually consists of 32-64 electrodes placed on the scalp in order to read a participant’s brain waves as electrical signals. This data is saved in the BioSemi Data Format (BDF), named after the equipment and software used to collect the data. Depending on the one analyzing the data, it is then often converted into a MAT file (Matlab). 

However, in a recent effort to make brain imaging data more organized, the Brain Imaging Data Structure (BIDS) format has emerged: https://bids.neuroimaging.io/. Rather than a new file type, this format saves the data as Tab Separated Files (TSV) with JSON files for additional descriptive information. 

## Extract:
The website OpenNeuro was used to search for a suitable dataset, leading to an EEG meditation study conducted with 24 subjects with two sessions each. The data can be found here: https://openneuro.org/datasets/ds001787/versions/1.0.2. This data is organized in the BIDS (Brain Imaging Data Structure) format which consists of the original BDF EEG files, TSV files of the data (created from the BDFs), and JSON files describing different aspects of the data and the study. 

The data was extracted from the TSV files by looping through each subject and session number and stored in pandas dataframes. The event and channel data from each subject’s sessions were concatenated into their respective data frames and the participants TSV was placed into a dataframe. The participants TSV file was included, despite its short length, as a reference for each participant’s demographics.

Data was not extracted from the JSON files as it was brief and descriptive. However, if a website was to be created surrounding this data, the JSON files would make it easy to describe the elements of the study (such as the type of equipment used, what the response codes correspond to, and the study staff). 

## Transform:
Transforming the data primarily involved reordering columns, setting the indices, and setting types. Because data of all 24 subjects was concatenated into a single dataframe, there were several rows from the same subject and session. Due to this, another column was needed in order to create a unique index. For the events dataframe, the subject_id, session, and onset was used (as the onset is the time in seconds when the sample and response were recorded). In the channels dataframe, the subject_id, session, and channel name were used to create the index while the participants dataframe was indexed by only the participant_ids. The orders were kept the same with the exception of the indices always beginning the the subject_id and session. 

The columns in the events dataframe that had a majority NaN values were checked and, since they did not have entirely null values, were not removed in order to keep the integrity of the data. No non-null or zero values were removed since they are considered relevant data. Finally, the appropriate data types were assigned to each column, with the three data frames only consisting of int, float, and string (object in pandas) data. 

## Load:
Three databases were loaded into SQL using pgAdmin: events_master_df, channels_master_df, and ppts_df (the concatenated events, channels and participants data respectively). This was done using sqlalchemy to connect to the engine and pandas’ “to_sql” to load the data into tables in the database. The final tables can be found on the pgAdmin “eeg_db” database as eeg_events, eeg_channels, and participants.
