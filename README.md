# deep-learning-cybersecurity-intrusion-detection

This project is a current work in progress. Comprehensive in-notebook notes, README, data cleaning, EDA, modeling, and gridsearch optimization still have not been implemented yet.

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

## Methods

- Data cleaning
- Exploratory data analysis (EDA)
- Oversampling (SMOTE)
- Neural network deployment

## Methodology Summary

I wanted to focus on probing problems for my IDS to simplify my problem statement, but also to account for the amount of data available. 6% of the data available was probing data, while other types of attacks (aside from brute force) accounted for much less. From there, I went into an entire modeling workflow to test and see if, computationally, based on the data I have, if an accurate classification of these two situations (authorized users vs. probes) is possible. So, I ran a trial workflow that included a train test split, SMOTE for oversampling of a 94/6 class imbalance, minmaxscaler to account for sparse data with different scales and heavily right-skewed distributions, as well as a basic logistic regression, which resulted in an approximate 79% accuracy rate.

## Results Summary

I moved forward in creating a neural network instead of a machine learning model to classify between probes and authorized users. This was to challenge myself in the realm of deep learning, and I figured that the extra computational power wouldn't hurt either. I constructed a neural network based on research I found related to neural networks that have worked well in the past with other anomaly detection projects.