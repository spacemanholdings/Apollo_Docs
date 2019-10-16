# Apollo Documentation v2

Apollo v2 has the following endpoints:

/Credential
  - Used to create schemas & credential definitions & get a list of them 

/Challenge
  - Used to generate new connectionless challenges 

/Wallet
 - Used to register a new wallet, connect with other users, issue and claim credentials, fulfil challenges and otherwise interact with your custody wallet

All requets to Apollo v2 follow this format:

    {
      request: {
        wallet_name:string,
        type?: string,
        body?: object
      },
      signature: string
    }

The signature payload must be signed by the key associated with the wallet_name passed in the request. 

wallet_name is the name of the wallet you're interacting with
type is dependant on the endpoint & body is dependant on type. Read on for the breakdown of valid types and bodies. 


## /Wallet

For /wallet, the following types may be used:
    
###    "new_wallet" 
New wallet is used to register a new cloud wallet. A wallet may be created for any number of users and only requires that the wallet_name is passed in. The body maybe an empty object. 

The return response will either be a {success:true} or an {errorMessage:string} 
errorMessages:
 - User already exists

TODO: In the future, new_wallet will also take in a public key that you would like to sign requests with. All future wallet requests would need to be signed by this key.

###    "create_connection_invite" 
Create connection invite generates a base64 encoded invite URL. You can pass in any name for the connection_name parameter, think of this like setting the name in your contacts list.

    {
      "request": {
        "wallet_name": string,
        "type": "create_connection_invite",
        "body": {
          "connection_name": string
        }
      },
      "signature": string
    }

set connection_name to anything you want to name them

###    "send_invite" 
 TODO: will send an  invite to them via email/phone number

###    "send_credential_offer" 
Send cred offer endpoint is only valid for wallets with issuing capabilities

    {
      "request": {
        "wallet_name": string,
        "type": "send_credential_offer",
        "body":{
          "schema_name": string,
          "connection_name": string,
          "credential_values": {attribute_name:attribute_value, attribute_name:attribute_value}
        }
      },
      "signature": string
    }

###    "complete_invite"
    {
      "request": {
        "wallet_name":string,
        "type": "complete_invite",
        "body": {
          "connection_name": string,
          "invite_url": string
        }
      },
      "signature": string
    }


###    "complete_offer" 

    {
      "request": {
        "wallet_name": string,
        "type":"complete_offer",
        "body": {
          "offerName": string
        }
      },
      "signature": string
    }

###    "complete_challenge"
    {
      "request": {
        "wallet_name": string,
        "type": "complete_challenge",
        "body": {
          "challenge_url": string
        }
      },
      "signature": string
    }
###    "get_user"
  TODO: waiting to be written
  will return the User object associated with the user account


## /Credential

### create_schema
    {
      "request": {
        "wallet_name":string,
        "type": "create_schema",
        "body":{
          "schema_name": string,
          "attributes": [attribute,attribute]
        }
      },
      "signature": string
    }

### create_credential_definition
    {
      "request": {
        "wallet_name": string,
        "type": "create_credential_definition",
        "body":{
          "schema_name": string
        }
      },
      "signature": string
    }

## /Challenge

### new_challenge
    {
      "request": {
        "wallet_name":string,
        "type": "new_challenge",
        "body": {
          "callback_url":string,
          "requested_attributes": [{"name":"attribute_name", restrictions?:[]}
        }
      },
      "signature": string
    }