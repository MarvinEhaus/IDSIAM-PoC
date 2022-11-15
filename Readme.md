# Readme - Prototyp SSI based IAM

---

## Setup:

Setup a Virtual Machine with the latest Ubuntu version.
If possible the IDE should have at least the following settings
- up to 120 GB Disk space
- Minimum of 8 GB RAM
- Minimum of 4 CPU Cores
- Enable Guest Addition

Install git, node, Docker and an IDE of your choice e.g. VSCode or Webstorm. To do this please follow these instructions:

### Install git:

```shell
sudo apt-get install git-all

git config --global user.name "FIRST_NAME LAST_NAME"
git config --global user.email "MY_EMAIL@example.com"
```

### Install node

It is highly recommended to use Node version manager to setup npm

```shell
sudo apt-get update
apt-get install build-essential libssl-dev -y
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
nvm --version
```

Close and reopen your terminal

```shell
nvm install node
```

### Install Docker:

```shell
sudo apt-get install docker.io
sudo apt-get install docker-compose
```
Run this command to check if the installation was successfull. It should return the following output: "docker-compose version 1.29.2, build 5becea4c"

```shell
docker-compose --version
```


If there are permissioning issues with docker, try

```shell
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

Now log out and log in and the permission issues with docker should be fixed

### Download the gitlab Repo:
```shell
git clone --recurse-submodules https....
```

---

### Initial Setup:

Run the following commands to build the von-network initially:

```shell
cd von-network
./manage build
```
Wait until the build process is completed. Should you have any issues please see [here](https://github.com/bcgov/von-network).

To start the von-network run this command in the von-network directory:

```shell
./manage up
```

You can see the current network status at [localhost:9000](localhost:9000).
If you want to seee the pooled transactions in the current network visit [localhost:9000/browse](localhost:9000/browse).

To start the three Agennts representing a holder, issuer and verifier please use the following commands:

```shell
cd ..
docker-compose up -d
```

You can see the logs of a container with this command:
```shell
docker logs CONTAINER_NAME
```
You can add *-f* if you want a continous log output

---


# Basic SSI - Workflow
## Prerequisites:

Make sure that your von-network and the agents are running properly.
The issuer should have a public DID. All agents need a wallet.

Go to the Administration API at localhost:ADMIN_PORT/api/doc.
In the  early development stage teh aadmin insecure mode should be running and you don't need an API-Key to access the API.

## Post Schema to the Indy Ledger :

Run this POST command within the Swagger API or with any other Http Request Tool, e.g. Postman:

Command:
```HTTP
http://localhost:ADMIN_PORT/schemas
```

Body:

```JSON
{
  "attributes": [
    "score" #List an array of attribute names for your schema
  ],
  "schema_name": "prefs", #Name your schema as follows
  "schema_version": "1.0" #Your schema version
}
```

You should get a response body as follows:

``` JSON
{
  "schema_id": "4cU41vWW82ArfxJxHkzXPG:2:prefs:1.0",  #Your Schema ID
  "schema": {
    "ver": "1.0",
    "id": "4cU41vWW82ArfxJxHkzXPG:2:prefs:1.0",
    "name": "prefs",
    "version": "1.0",
    "attrNames": [
      "score"
    ],
    "seqNo": 9 #The Block number where this schema definition is posted on the Indy ledger
  }
}
```


## Post a credential definition to the Indy ledger :

Run this POST command within the Swagger API or with any other Http Request Tool, e.g. Postman:

Command:
```HTTP
http://localhost:ADMIN_PORT/credential-definitions
```

Body:

```JSON
{
  "schema_id": "4cU41vWW82ArfxJxHkzXPG:2:prefs:1.0",
  "support_revocation": false,
  "tag": "prefs1.0"
}
```

You should get a response body as follows:

``` JSON
{
  "sent": {
    "credential_definition_id": "4cU41vWW82ArfxJxHkzXPG:3:CL:9:prefs1.0"
  },
  "credential_definition_id": "4cU41vWW82ArfxJxHkzXPG:3:CL:9:prefs1.0"
}
```

Now you can visit the von-network [here](http://localhost:9000/browse/domain) and see the transactions you posted to the ledger.


## Create and Receive a Connection Invitation:

Run this POST command within the Swagger API or with any other Http Request Tool, e.g. Postman to create a connection invitation:

Command:
```HTTP
http://localhost:ADMIN_PORT/connections/create-invitation
```

for the simple example you need to parse an empty Body:

```JSON
{}
```

You should get a response body that looks as follows:

``` JSON
{
  "connection_id": "688b3dc2-d7e3-4310-8221-15c8ac62a94b",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "9f1f49f6-4ef0-497f-ba1d-28ce71c41171",
    "recipientKeys": [
      "4QoArGY53DNuPzpRJh2CaKYby7eSWtmg2qPudC7YuiKE"
    ],
    "label": "ISSUER-Agent",
    "serviceEndpoint": "http://172.17.0.1:8000"
  },
  "invitation_url": "http://172.17.0.1:8000?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiOWYxZjQ5ZjYtNGVmMC00OTdmLWJhMWQtMjhjZTcxYzQxMTcxIiwgInJlY2lwaWVudEtleXMiOiBbIjRRb0FyR1k1M0ROdVB6cFJKaDJDYUtZYnk3ZVNXdG1nMnFQdWRDN1l1aUtFIl0sICJsYWJlbCI6ICJJU1NVRVItQWdlbnQiLCAic2VydmljZUVuZHBvaW50IjogImh0dHA6Ly8xNzIuMTcuMC4xOjgwMDAifQ=="
}
```

The value "invitation_url" can be used to create a QR-Code to connect with your mobile phone or for automation purposes. 
For this example here, we connect with another aries-cloudagent instance, e.g. the holder-agent.
Therefore you need to run this command:


Command:
```HTTP
http://localhost:ADMIN_PORT/connections/receive-invitation
```

for the simple example you need to parse the JSON at the key "invitation" as Body:

```JSON
{
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "9f1f49f6-4ef0-497f-ba1d-28ce71c41171",
    "recipientKeys": [
      "4QoArGY53DNuPzpRJh2CaKYby7eSWtmg2qPudC7YuiKE"
    ],
    "label": "ISSUER-Agent",
    "serviceEndpoint": "http://172.17.0.1:8000"
  }
```

You should get a response body that looks as follows:

``` JSON
{
  "their_label": "ISSUER-Agent",
  "rfc23_state": "request-sent",
  "created_at": "2022-11-15T19:07:14.909621Z",
  "invitation_mode": "once",
  "invitation_key": "4QoArGY53DNuPzpRJh2CaKYby7eSWtmg2qPudC7YuiKE",
  "accept": "auto",
  "connection_protocol": "connections/1.0",
  "routing_state": "none",
  "state": "request",
  "updated_at": "2022-11-15T19:07:14.961441Z",
  "their_role": "inviter",
  "invitation_msg_id": "9f1f49f6-4ef0-497f-ba1d-28ce71c41171",
  "request_id": "0fcc1f7f-f97c-466b-90d0-3c60e7f5fcde",
  "my_did": "LuxMKtAREks1tbFYqSGZaU",
  "connection_id": "985413db-a524-43de-bfe5-50ed4c8ca605"
}
```

## Issue a credential

Run this POST command within the Swagger API or with any other Http Request Tool, e.g. Postman to issue a credential and send it to the connected agent / wallet.

Command:
```HTTP
http://localhost:ADMIN_PORT/issue-credential/create
```

for the simple example you need to parse an empty Body:

```JSON
{
  "cred_def_id": "4cU41vWW82ArfxJxHkzXPG:3:CL:9:prefs1.0",
  "credential_proposal": {
    "@type": "issue-credential/1.0/credential-preview",
    "attributes": [
      {
        "name": "score",
        "value": "martini"
      }
    ]
  },
  "issuer_did": "4cU41vWW82ArfxJxHkzXPG",
  "schema_id": "4cU41vWW82ArfxJxHkzXPG:2:prefs:1.0",
  "schema_issuer_did": "4cU41vWW82ArfxJxHkzXPG",
  "schema_name": "prefs",
  "schema_version": "1.0"
}```

You should get a response body that looks as follows:

``` JSON
{
  "connection_id": "688b3dc2-d7e3-4310-8221-15c8ac62a94b",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "9f1f49f6-4ef0-497f-ba1d-28ce71c41171",
    "recipientKeys": [
      "4QoArGY53DNuPzpRJh2CaKYby7eSWtmg2qPudC7YuiKE"
    ],
    "label": "ISSUER-Agent",
    "serviceEndpoint": "http://172.17.0.1:8000"
  },
  "invitation_url": "http://172.17.0.1:8000?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiOWYxZjQ5ZjYtNGVmMC00OTdmLWJhMWQtMjhjZTcxYzQxMTcxIiwgInJlY2lwaWVudEtleXMiOiBbIjRRb0FyR1k1M0ROdVB6cFJKaDJDYUtZYnk3ZVNXdG1nMnFQdWRDN1l1aUtFIl0sICJsYWJlbCI6ICJJU1NVRVItQWdlbnQiLCAic2VydmljZUVuZHBvaW50IjogImh0dHA6Ly8xNzIuMTcuMC4xOjgwMDAifQ=="
}
```
