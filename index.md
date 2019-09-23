# Apollo Documentation v1
Apollo has a set of major endpoints with the given functionality

/Agent
  - Used by wallets to communicate ssi/agent-wire messages

/Challenge
  - "getnewchallenge" : Returns a URL with the connection invite base64 encoded
 
/Connection
  - "getnewinvite" : Returns a new connection invite in base64 URL
  - "getconnectioninfo": Returns basic information about connection (name, email)
  - "issuecred" : Issues a credential to the user

/Credential
  - "createnewcred" : Creates a new credential to be issued
  - "getcredbycreddefid" : Returns the schema for a given Cred ID
  - "getallcredentials" : Returns a list of all Spaceman Credentials

All requests to Apollo follow this format: 

All POST requests to apollo MUST have a body formatted in this manner of content-type: "application/json". body.body should be formatted based on the requested TYPE

    {
      client_id:string,
      type: string,
      body: { ? }
    }

with x-api-key in the header related to that client ID. 
The body object depends on the type submitted

While each Reply from Apollo will take this format

  {
    sucess: true | false,
    errorMessage?: string,
    body?: { ? }
  }

errorMessage will only be included if success is false and something went wrong

# Connections
Types: "getnewinvite", "getconnectioninfo", "issuecred"

## getnewinvite
Get new invite payload requires only an email to be able to 

    {
      client_id: "xxx"
      type: "getnewinvite"
      body: {
        name: string,
        email: string
      }
    }
    

Name: the name of the user you want to connect with
Email: the email for the person you want to connect with  

Return Object

    {
      connection_id: string,
      invitation_url: string
    }

Connection_ID is a string that refers to the unique connection between the client and the user. 
Invitation_URL is a base64 encoded invite for the user's wallet 

Example

    {
      "success": "true",
      "body": {
        "connection_id": "2c86c960-fd91-441c-9669-7c47e21d1c13",
        "invitation_url": "https://o9iux56e78.execute-api.us-east-1.amazonaws.com/dev/v1/agent?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiZGU0YzE4YWItMGM3Zi00MGVkLWE2ZTctODg4ZWIyMWJlNDhmIiwgInJlY2lwaWVudEtleXMiOiBbIjJ4OXNXWEU1ZUthUnBrdFpqcnd1bkpBRXBiUXJ0eTlIN1E3Zms4NjUydndGIiwgIkRzRDJ4eDhxUG53YVNrTWFoZDdxYnZ6MnFHOUo3MzN2ekVpMUZEWFVUQTEyIl0sICJzZXJ2aWNlRW5kcG9pbnQiOiAiaHR0cHM6Ly9vOWl1eDU2ZTc4LmV4ZWN1dGUtYXBpLnVzLWVhc3QtMS5hbWF6b25hd3MuY29tL2Rldi92MS9hZ2VudD9jbGllbnRfaWQ9c3BjaWR3ZWJhcHAxMDEwJmNvbm5lY3Rpb25faWQ9MmM4NmM5NjAtZmQ5MS00NDFjLTk2NjktN2M0N2UyMWQxYzEzIiwgImxhYmVsIjogIlNwYWNlbWFuLUlEIn0="
      }
    }

## getconnectioninfo
The input for getconnectioninfo: 

    {
      client_id: string,
      type: "getconnectioninfo"
      body: {
        email: string
      }
    }
        
Email refers to the email of the connection you want to get
The lookup will only be for users connectioned to the specified wallet.

Return Object: 

    {
      email: string, 
      connection_id: string, 
      name: string,
      conn_status: "sent" | "active"
      credentials_issued: string[]
      credentials_revoked: string[]
    }

conn_status refers to if the user has accepted the invite or not
credentials_issed and revoked is a list of credential_defintion_ids which point to a given schema and issuer of the credential 

Example 

## issuecred

Issue cred is used to issue a credential to a user. 

The input model: 

    {
      client_id: string,
      type: "issuecred",
      body: {
        email: string,
        cred_def_id: string,
        vales: {}
      }
    }
The cred_def_id is the id for the credential you want to issue
The values object takes the form of: 

  {
    "attribute_name":"value",
    "attribute_name":"value",
    ...
  }
For a given issuing cred_def_id

Return Object
 TODO 

# Credentials
The credentials endpoint is used to create new schemas, and fetch schemas for the credentials

## createnewcred
The input for createnewcred: 

    {
      credential_name: string,
      attributes: [] 
    }

This will create a new schema, then build a credential definition based off your client id. Schema is the blueprint of the credential, the credential_definition is the specific signed version issued by a given organization. 

For example, there might be a single drivers license schema, but 50 credential definitions as each state would issue it uniquely signed by their own keys. 

The Return Object

    {
      cred_def_id:string
    }

The ID of the credential definition

## getcredbycreddefid

## getallcredentials
This will fetch all the credentials in the Spaceman Database by network 

 TODO

# Challenges
The challenges endpoint takes in a callback url and returns a connectionless challenge. Right now you can you only request challenges for a given credential, but in the future this will be signficantly added to in the terms of functionality

## getnewchallenge
Input Object

    {
      client_id: string,
      type: "issuecred",
      body: {
        cred_def_id: string,
        callbackUrl:string,  
      }
    }

Output Object:
    {
      challenge_id: string,
      url: string
    }

Challenge_ID refers to an internal designation for the challenge that you've generated

URL is the challenge in base64 url form to be shown to wallet via QR code or similar

# Agent
This endpoint is not for use by services. This endpoint is for wallet apps to make ssi/agent-wire message requests to our wallets