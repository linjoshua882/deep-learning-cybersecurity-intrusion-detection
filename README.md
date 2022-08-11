# deep-learning-cybersecurity-intrusion-detection

## Problem Statement

Companies, especially with small teams, have large gaps in their cybersecurity procedures. In most cases, even large companies are fairly easy targets to get hacked. Microsoft, NVidia, and Okta all got hacked by the same teenager in England in 2022 (https://www.bloomberg.com/news/articles/2022-03-23/teen-suspected-by-cyber-researchers-of-being-lapsus-mastermind), resulting in credit card information, customer addresses, customer emails, logins, and other proprietary documents being accessed by unauthorized attackers. This is an increasingly growing problem that, when unchecked, can potentially result in entire companies collapsing. This project hopes to use digital packet and payload behavior to create an effective network intrusion detection system (IDS) to identify when an unauthorized attacker is probing a server.

## Definitions

In this project, there are going to be a lot of terms that may not be familiar to most data experts, as they refer to cybersecurity or networking concepts. Under this category is a list of these definitions.

- TCP Flags: In networking, a TCP flag is recorded to indicate a specific state or connection status between multiple parties that provides useful information for troubleshooting or other purposes. Examples of TCP flags that we will see in this project include SYN, FIN, or RST. Each flag refers to 1 bit of information or data.
    - SYN: The first step of connection establishment phase or 3-way handshake process between the two hosts. Only the first packet from sender as well as receiver should have this flag set. This is used for synchronizing sequence number i.e. to tell the other end which sequence number they should accept.
    - ACK: Used to acknowledge packets which are successful received by the host.
    - FIN: When there is no more data from the sender, it requests for connection termination. This is the last packet sent by sender. It frees the reserved resources and gracefully terminate the connection. 
    - RST: Used to terminate the connection if the RST sender feels something is wrong with the TCP connection or that the conversation should not exist.
    - URG: Data inside a segment with URG = 1 flag is forwarded to application layer immediately even if there are more data to be given to application layer. It is used to notify the receiver to process the urgent packets before processing all other packets.
    - PSH: Tells the receiver to process these packets as they are received instead of buffering them.
- Payload: A collection of data sent from one party to another.
- Header: Contains protocol information as well as party addresses. This link may address any confusions about payloads and headers. (https://www.tutorialspoint.com/what-is-payload-in-computer-network)
- Probe/Probing: A preliminary scan that an attacker uses to gauge a better understanding of his/her target. This can result in an attacker gaining information such as any open ports he/she can enter on, holes in firewall security systems, as well as the operating system.
- Window size: Refers to an "advertisement" of how much data a given system is willing to receive at any given time. More information here: (https://www.networkcomputing.com/data-centers/network-analysis-tcp-window-size)

Each row in the dataset used is a payload sent.

## Data Dictionary, Cleaned Dataset

|Feature|Type|Description|
|---|---|---|
|**down_up_ratio**|*float*|Ratio of download/uploaded data|
|**fwd_header_size_min**|*int*|Minimum header size from user to server (in bytes)|
|**fwd_header_size_max**|*int*|Maximum header size from user to server (in bytes)|
|**bwd_header_size_min**|*int*|Minimum header size from server to user (in bytes)|
|**bwd_header_size_max**|*int*|Maximum header size from server to user (in bytes)|
|**flow_FIN_flag_count**|*int*|Amount of FIN flags sent and recorded in any given communication|
|**flow_SYN_flag_count**|*int*|Amount of SYN flags sent and recorded in any given communication|
|**flow_RST_flag_count**|*int*|Amount of RST flags sent and recorded in any given communication|
|**fwd_pkts_payload.min**|*int*|Minimum amount of packets being sent in any given payload, from user to server|
|**fwd_pkts_payload.min**|*int*|Maximum amount of packets being sent in any given payload, from user to server|
|**fwd_pkts_payload.avg**|*float*|Average of packets being sent in any given payload, from user to server|
|**fwd_pkts_payload.std**|*float*|Standard deviation of packets being sent in any given payload, from user to server|
|**bwd_pkts_payload.min**|*int*|Minimum amount of packets being sent in any given payload, from server to user|
|**bwd_pkts_payload.std**|*float*|Standard deviation of packets being sent in any given payload, from server to user|
|**flow_pkts_payload.min**|*int*|Minimum amount of packets being sent in any given payload, both from user to server and server to user|
|**flow_pkts_payload.avg**|*float*|Average of packets being sent in any given payload, both from user to server and server to user|
|**flow_pkts_payload.std**|*float*|Standard deviation of packets being sent in any given payload, both from user to server and server to user|
|**bwd_bulk_rate**|*float*|Rate of packets being sent in any given payload, classified as a bulk payload|
|**fwd_init_window_size**|*int*|An advertisement of how much data a given system is willing to receive at any given time. Fwd in this case, refers to the server.|
|**bwd_init_window_size**|*int*|An advertisement of how much data a given system is willing to receive at any given time. Bwd in this case, refers to the user.|
|**label**|*int*|Dependent classifier of attacker vs. unauthorized user. 0 for authorized user, 1 for attacker or probe.|

## Tech Stack

- Jupyter
- Google Colab
- Python
- sklearn
- Tensorflow/Keras

## Methods

- Data cleaning
- Feature selection / engineering (SelectKBest)
- Principal component analysis (PCA)
- Exploratory data analysis (EDA)
- Scaling (MinMaxScaler)
- Oversampling (SMOTE)
- Machine learning model testing (Random Forests, AdaBoost, LogisticRegression, XGBoost)
- Neural network deployment

## Methodology Summary

I wanted to focus on probing problems for my IDS to simplify my problem statement, but also to account for the amount of data available. 6% of the data available was probing data, while other types of attacks (aside from brute force) accounted for much less. From there, I went into an entire modeling workflow to test and see if, computationally, based on the data I have, if an accurate classification of these two situations (authorized users vs. probes) is possible. So, I ran a trial workflow that included a train test split, SMOTE for oversampling to account for a 94/6 class imbalance, minmaxscaler to account for sparse data with different scales and heavily right-skewed distributions, as well as a basic logistic regression, which resulted in an approximate 83% accuracy rate.

I moved forward in creating a neural network instead of a machine learning model to classify between probes and authorized users. This was to challenge myself in the realm of deep learning, and I figured that the extra computational power wouldn't hurt either. I constructed a neural network inspired by research I found related to neural networks that have worked well in the past with other anomaly detection projects. (https://arxiv.org/pdf/1807.07282.pdf)

![Neural Network Architecture](https://github.com/linjoshua882/deep-learning-cybersecurity-intrusion-detection/blob/main/assets/nn-architecture.png)

### Feature Selection

My focus on feature selection was to create an automated quantitative methodology to select my features. There were a few reasons for this, the strongest being a lack of industry understanding as well as wanting to have a scientific method to select features outside of anecdotal evidence. I could use an sklearn package to select the best features, but how would I know how many features was best? I used SelectKBest to optimize for f-score within a for loop in order to test a basic LogisticRegression's model performance at different amounts of features selected (in steps of 10). After 20 features, model accuracy didn't change much, so that was the amount of features that I selected.

![SelectKBest Selected Features at Multiple Levels](https://github.com/linjoshua882/deep-learning-cybersecurity-intrusion-detection/blob/main/assets/k_features.png)

### Preprocessing

- Missing Data / Nulls
    - Not applicable, as there were no nulls in the data.
- Outlier Handling
    - There are a lot of considered outliers as the data is very sparse. Also, although there are outliers, every point is a valid point, since these are computer generated data points. Have not yet handled outliers.
- Scaling (MinMaxScaler)
    - Scaled with MinMaxScaler due to the sparse data creating extremely right-skewed distributions in the dataset.
- Oversampling (SMOTE)
    - Oversampled with SMOTE as there is a 94/6 class imbalance. Did not pursue undersampling, as after testing, resulted in a model with poor performance.
- Principal Component Analysis (PCA)
    - Preprocessed with PCA as to address potential multi-collinearity. It does this by creating a co-variance matrix, as well as creating eigenvector/eigenvalue weights for each feature to address multi-collinearity. After n_components=8, explained variance from PCA makes little change.

![PCA n_components](https://github.com/linjoshua882/deep-learning-cybersecurity-intrusion-detection/blob/main/assets/pca-explained-variance.png)

### Modeling Decisions

I tested multiple machine learning classifiers to see what would work best with the data. None of the ML models performed up to standard by any means, and they were completely unable to classify between authorized users and attackers.

![logreg ROC Curve Sample](https://github.com/linjoshua882/deep-learning-cybersecurity-intrusion-detection/blob/main/assets/logreg-roc-curve.png)

I chose a neural network because, while the accuracy rate isn't much different from a logistic regression model or other tested machine learning models (RandomForests, AdaBoost, etc.), the neural network's true positive rate was much higher, and I wanted to optimize for true positive rate (positive = attacker, negative = authorized user). I wanted to optimize for true positive rate because I would rather my model falsely identify authorized users as attackers than falsely identifying attackers as authorized users. However, as the model currently stands, it is overcompensating too much in that direction.

I selected my neural network architecture based on anomaly detection research that incorporated a similar architecture. Also, after some sensitivity testing, I found that the architecture outlined in the external research was the most successful.

I found that I had to implement regularization to 1. bridge the 6-7% gap in train and test accuracy, as well as earlystopping to conserve computational resources as well as to take time complexity into consideration.

![Model Accuracy, No Regularization](https://github.com/linjoshua882/deep-learning-cybersecurity-intrusion-detection/blob/main/assets/model-acc-no-reg.png)

## Results Summary

The neural network resulted in an approximate 83% test accuracy. The model also resulted in a near perfect true positive rate, but an approximate 18% false positive rate, labeling authorized users as unauthorized attackers. Ultimately, this was an unsuccessful attempt at creating a network IDS. While this neural network does a great job at understanding what network behavior attackers tend to take on, it is unable to effectively classify the difference between authorized and unauthorized users. Imagine being an employee at a company, trying to access a server that you need to use for your daily tasks, just to be labeled as an attacker 18% of the time.

![Model Accuracy](https://github.com/linjoshua882/deep-learning-cybersecurity-intrusion-detection/blob/main/assets/model-acc-earlystop.png)

![ROC Curve](https://github.com/linjoshua882/deep-learning-cybersecurity-intrusion-detection/blob/main/assets/nn-roc-curve.png)
