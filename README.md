# Gitcoin
Blockchain is a public, decentralized, and immutable 'linked list' with hashed pointers used for recording/storing transactions/data. Built using a few of the underlying core concepts of blockchain, Gitcoin is a barebones cryptocurrency using proof of work and consensus algorithms to maintain blockchain integrity. With a Flask backend, you can use cURL, Postman, or a similar tool to view requests and confirm json responses.

This app is not currently live.


## Table of Contents
* [General Info](#general-information)
* [Technologies Used](#technologies-used)
* [Features](#features)
* [Setup](#setup)
* [Making Requests](#making-requests)
* [Improvements](#improvements)


## General Information
Gitcoin was built to learn about and demonstrate the interworking of a blockchain, while incorporating some of the methods used by cryptocurrencies to remain decentralized and immutable. In a typical cryptocurrency, each node in the network would have its own physical copy of the blockchain, used to come to a 'consensus' with the rest of the network on which chain is authoritative. Since this isn't practical at this base level, Gitcoin takes a slightly different approach.

Each node in the network has its own unique address-- think a 'wallet', which is how it can be identified to send or receive currency. Each node 'shares' the blockchain on the Flask server, making requests through the API to interact with it. While other nodes are adding transactions or mining blocks, the blockchain is updated. So in order for each node to keep up with the current chain, they must adopt the agreed upon current 'version' of the chain. It does this by comparing each node's chain to its own, ensuring valid hashes/ proofs, and assuming the longest valid chain is the current chain. 

Whew, blockchain can be a lot to comprehend, especially for those who do not have exposure. I encourage you to go read about blockchain, to get an entry-level understanding of the WHY behind it. I always find it best to understand the WHY behind a concept, and the HOW will follow.


## Technologies Used
- Flask, a micro-framework
- Blockchain design
    - Proof of work algorithm
    - Consensus algorithm


## Features
- Add a new transaction to the ledger
- Register a node to the network
- Mine a block
- Resolve authoritative chain
- Get current chain


## Setup
Ensure python 3.9 is installed.

Install requirements:
```bash
pip install -r requirements.txt
```

Or, access virtual environment:
```bash
pip install pipenv
pipenv shell
```

### Running the Server
```
python run.py
```


## Making Requests
Open up your favorite HTTP client tool for API interaction (cURL, Postman, etc), and follow the below steps to call the endpoints:

### Register Node
Send a POST request to the /nodes/register endpoint with your address in JSON, in the body as follows:
```
{
    "nodes": ["<YOUR_ADDRESS>:<YOUR_PORT>"]
}
```

### New Transaction
Send a POST request to the /transactions/new endpoint with the following format in the body:
```
{
    "sender": "<YOUR_ADDRESS>",
    "recipient": "<RECIPIENT_ADDRESS>",
    "amount": 5
}
```
Note: Addresses do not have to be valid chain members, as there is no way to verify transactions at this time

### Mine Block
Send a GET request to the /mine endpoint, and you should see a response formatted as such:
```
{
    "index": 3,
    "message": "New Block Forged",
    "previous_hash": "94c0a37037c76d4574588dad2efbeb85566aa4f06572b74622c7ff2d9d94ffb0",
    "proof": 58403,
    "transactions": [
        {
            "amount": 1,
            "recipient": "3a06e23aa623400cb5258748f6168ba0",
            "sender": "0"
        }
    ]
}
```
Note: This will be resource intensive! To limit the difficulty, reduce the amount of leading zeros in the valid_proof method in the Blockchain class.

### Get Chain
Send a GET request to the /chain endpoint, and you should get a response formatted like:
```
{
    "chain": [
        {
            "index": 1,
            "previous_hash": "1",
            "proof": 100,
            "timestamp": 1631728870.0435,
            "transactions": []
        },
        {
            "index": 2,
            "previous_hash": "0d1136d04cf41d62b2dfefa6daa25593ce687d632ec8f63f6710e2f594bad0b6",
            "proof": 87779,
            "timestamp": 1631728934.3442519,
            "transactions": [
                {
                    "amount": 1,
                    "recipient": "3a06e23aa623400cb5258748f6168ba0",
                    "sender": "0"
                }
            ]
        }
    ],
    "length": 2
}
```
### Validate Chain
To validate the chain, you must have multiple instances running. You can either use multiple machines, or spin different instances on the same machine using different port numbers. Then, mine some blocks on one instance, then send a GET to /nodes/resolve on the other instance, and your response should look something like this:
```
{
    "message": "Our chain was replaced",
    "new_chain": [
        {
            "index": 1,
            "previous_hash": "1",
            "proof": 100,
            "timestamp": 1631728870.0435,
            "transactions": []
        },
        {
            "index": 2,
            "previous_hash": "0d1136d04cf41d62b2dfefa6daa25593ce687d632ec8f63f6710e2f594bad0b6",
            "proof": 87779,
            "timestamp": 1631728934.3442519,
            "transactions": [
                {
                    "amount": 1,
                    "recipient": "3a06e23aa623400cb5258748f6168ba0",
                    "sender": "0"
                }
            ]
        }
    ]
}
```


## Improvements
Improvements:
- Transaction data should be validated
- Some sort of wallet system

To Do:
- Figure out a secure way to deploy it live
- Data transfer limit
