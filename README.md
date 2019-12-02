# Incentivized Testnet Stake Pool Registry

##  Background
This repository provides a means to register public stake pools for the Incentivized Testnet. Successful registrations will result in the listing of a stake pool as a delegation option in all of the supported testnet wallets (Daedalus & Yoroi)

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

:warning: The `owner` public key mentioned here **must match** your owner public key used for registering your stake pool on chain! :warning:

Precise entry validity rules are described in the following section.

## Submission well-formedness rules

1. Submissions shall consist of a single commit, directly off the master branch of the **incentivized-testnet-stakepool-registry** repository.

2. Submissions are identified by the subject's Bech32-encoded Ed25519 public key (all lowercase).

3. Submissions shall either add a new entry or modify one.

4. Submissions shall involve (addition or modification) of exactly two files, under the **registry** subdirectory of the repository:
   - the JSON submission entry file, with the `.json` extension (lowercase),
   - the external signature of the file, with the `.sig` extension (lowercase).

5. The file name part of both files must match the aforementioned encoded owner public key, up to character case (all lowercase).

6. Contents of the JSON file:
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

7. The `ticker` and `owner` must be unique in the registry.

8. The external signature file must be a ed25519 signature of the content of the metadata JSON file.

## Step-by-Step Example (Linux / Mac OS)

> :information_source: Notice
>
> In this example, we'll be using a command-line tool called `jcli`. Installations instructions are available on the official Jörmungandr repository: https://github.com/input-output-hk/jormungandr.

1. Clone and fork the repository

```
$ git clone git@github.com:cardano-foundation/incentivized-testnet-stakepool-registry
$ cd incentivized-testnet-stakepool-registry
$ git remote add submission git@github.com:<your-github-username>/incentivized-testnet-stakepool-registry
```

2. Create a public/private key pair (`owner.prv` & `owner.pub`)

```
$ jcli key generate --type ed25519 | tee owner.prv | jcli key to-public > owner.pub
$ cat owner.{prv,pub}
ed25519_sk1----------------------------------------------------------
ed25519_pk1qppzz38el9zxtgaw0ttmf6d6zytllfu3fnwcl5tlc3pp044artxqru55mx
```

3. Create a minimal JSON file (`ed25519_pk1qpp...ru55mx.json`)
```json
{
  "owner": "ed25519_pk1qppzz38el9zxtgaw0ttmf6d6zytllfu3fnwcl5tlc3pp044artxqru55mx",
  "name": "My Stake Pool",
  "ticker": "ADA1",
  "homepage": "https://cardanofoundation.org",
  "pledge_address": "addr1s0nyt67uwcg7dahrxug698h5xfasnyd5qhnsd0h0peqlqvtfqf48ymz680l"
}
```

> :bulb: You can check that your JSON is valid by using the JSON-schema above and your metadata file in a tool like https://www.jsonschemavalidator.net/

4. Sign it using your owner private key (`ed25519_pk1qpp...ru55mx.sig`)

```
$ jcli key sign \
    --secret-key owner.prv \
    --output ed25519_pk1qppzz38el9zxtgaw0ttmf6d6zytllfu3fnwcl5tlc3pp044artxqru55mx.sig \
    ed25519_pk1qppzz38el9zxtgaw0ttmf6d6zytllfu3fnwcl5tlc3pp044artxqru55mx.json
```

5. Create a commit for the submission

```
$ git add ed25519_pk1qppzz38el9zxtgaw0ttmf6d6zytllfu3fnwcl5tlc3pp044artxqru55mx.{json,sig}
$ git commit -m "ADA1"
$ git push submission HEAD
```

6. Make a pull request :tada:!
