# Incentivized Testnet Stake Pool Meta Data Registry

##  Background
This repository provides a means to register public stake pool meta data for the Incentivized Testnet. Successful registrations will result in the listing of a stake pool as a delegation option in all of the supported testnet wallets (Daedalus & Yoroi).

Adding your stake pool to this registry is only one of several steps required to get your stake pool up and running. Please refer to the [testnet site](https://testnet.iohkdev.io/en/cardano/shelley/about/testnet-introduction/) for a description of the entire process.

Use of this registry is subject to the following [usage policy](USAGE_POLICY.md)

## Process

#### New stake pool registration

Stake pool registrants will provide signed submissions in the form of Github pull requests into this testnet pool registry. Here they will be subject to automated checking for well-formedness and human vetting before being merged to master.

#### Stake pool de-registration

This action does not require any changes to the registry -- the inactive pools are simply ignored.

#### Updating existing entries

Stake pool registrants will provide signed submissions in the form of Github pull requests into this testnet pool registry. Here they will be subject to automated checking for well-formedness and human vetting before being merged to master.

## Semantic content of registry entries

Each entry contains the following information:

name             | necessity | description
---              | ---       | ---
`owner`          | required  | subject identifier public key 
`name`           | required  | stake pool name
`description`    | optional  | stake pool description
`ticker`         | required  | stake pool ticker
`homepage`       | required  | URL of the stake pool's web page
`pledge_address` | required  | a pledge address
\-               | required  | signature (verifiable by the public key)

:warning: The `owner` public key mentioned here **must match** your owner public key used for registering your stake pool on chain!

Precise entry validity rules are described in the following section.

## Submission well-formedness rules

1. Submissions shall consist of a single commit, directly off the master branch of the **incentivized-testnet-stakepool-registry** repository.

2. Submissions are identified by the subject's Bech32-encoded Ed25519 public key (all lowercase).

3. Submissions shall either add a new entry or modify one.

4. Submissions shall involve (addition or modification) of exactly two files, under the **registry** subdirectory of the repository:
   - the JSON submission entry file, with the `.json` extension (lowercase),
   - the external signature of the file, with the `.sig` extension (lowercase).

5. The file name part of both files must match the aforementioned encoded owner public key, up to character case (all lowercase).

6. The external signature file must be a ed25519 signature of the content of the metadata JSON file.

7. The `ticker` and `owner` must be unique in the registry.

8. Contents of the JSON file:
   1. Must be a JSON object, reasonably (non-offensively) formatted,
   2. Must satisfy the following [JSON-schema](https://json-schema.org/):

   ```json
   {
     "title": "Stake-pool Metadata",
   
     "type": "object",
   
     "additionalProperties": false,
   
     "required": [
       "owner",
       "name",
       "ticker",
       "homepage",
       "pledge_address"
     ],
    
     "properties": {
       "owner": {
         "type": "string",
         "format": "bech32",
         "pattern": "^ed25519_pk1[qpzry9x8gf2tvdw0s3jn54khce6mua7l]+$",
         "maxLength": 70
       },

       "name": {
         "type": "string",
         "minLength": 1,
         "maxLength": 50
       },

       "description": {
         "type": "string",
         "minLength": 1,
         "maxLength": 255
       },
    
       "ticker": {
         "type": "string",
         "minLength": 3,
         "maxLength": 5,
         "pattern": "^[A-Z0-9]{3,5}$"
       },
    
       "homepage": {
         "type": "string",
         "format": "uri",
         "pattern": "^https://"
       },
    
       "pledge_address": {
         "type": "string",
         "format": "bech32",
         "pattern": "^addr1[qpzry9x8gf2tvdw0s3jn54khce6mua7l]+$",
         "maxLength": 64
       }
     }
   }
   ```

## Step-by-Step Guides

See [Wiki](https://github.com/cardano-foundation/incentivized-testnet-stakepool-registry/wiki).
