See Documents\Sda\Info\OneBlink\OneBlinkApiNotes.md

## Setup differences

* OneBlink Console > Developer Tools > Web/CND Hosting > [+]
  - Create something like nswfoodauthority-parthenium-carrier.cdn.oneblink.io
  - CORS enabled ?

* Windows create a project folder
  - E.g. Documents\Sda\Code\OneBlink\Apps\PartheniumWeedCarriersAssets

* In Project folder
  - Create/Copy from another CND .blinkmrc.json

        {
          "server": {
            "project": "nswfoodauthority-parthenium-carrier.cdn.oneblink.io",
            "timeout": 58,
            "cors": true,
            "network": {
              "dev": {
                "vpcSubnets": [
                  "subnet-xxxxxxx",
                  "subnet-zzzzzz"
                ],
                "vpcSecurityGroups": [
                  "sg-yyyyyyyyy"
                ]
              },
              "test": {
                "vpcSubnets": [
                  "subnet-xxxxxxx",
                  "subnet-zzzzzz"
                ],
                "vpcSecurityGroups": [
                  "sg-yyyyyyyyy"
                ]
              },
              "train": {
                "vpcSubnets": [
                  "subnet-xxxxxxx",
                  "subnet-zzzzzz"
                ],
                "vpcSecurityGroups": [
                  "sg-yyyyyyyyy"
                ]
              },
              "prod": {
                "vpcSubnets": [
                  "subnet-xxxxxxx",
                  "subnet-zzzzzz"
                ],
                "vpcSecurityGroups": [
                  "sg-yyyyyyyyy"
                ]
              }
            },
            "routes": [],
            "variables": {
              "ONEBLINK_ACCESS_KEY": "redacted",
              "ONEBLINK_SECRET_KEY": "redacted",
              "ONEBLINK_ENVIRONMENT": {
                "local": "local",
                "dev": "dev",
                "test": "test",
                "train": "train",
                "prod": "prod"
              },
              "ENV_PREFIX": {
                "local": "LOCAL - ",
                "dev": "DEV - ",
                "test": "TEST - ",
                "train": "TRAIN - ",
                "prod": ""
              }
            }
          },
          "cdn": {
            "scope": "nswfoodauthority-parthenium-carrier.cdn.oneblink.io"
          }
        }

    - Replace network vpcSubnet values with real thing.
    
    - Create/Copy .gitgnore

          /.vscode/
          /node_modules/
          /out/
          .blinkmrc.json
          *.code-workspace
          package-lock.json

    - npm init

* npm install oneblink/cli -D

* Set access keys in session

  - $ENV:ONEBLINK_ACCESS_KEY="
  - $ENV:ONEBLINK_SECRET_KEY="

* Set scope

  - npx oneblink cdn scope nswfoodauthority-parthenium-carrier.cdn.oneblink.io

* Create a folder www under which all other assets go

* Create an index.html that explains the contents.

## Deploy


* Deploy a folder without uploading the specified folder in the resulting url)

  npx oneblink cdn deploy www --env dev;Get-Date

  e.g. can surf to https://nswfoodauthority-parthenium-carrier-dev.cdn.oneblink.io/images/logos/PRIMARY-nsw-government-logo.svg

* (Deploy a folder but include the folder in the resulting url)

  npx oneblink cdn deploy ./www --env dev;Get-Date


## Prune

npx oneblink cdn deploy . --env dev --prune

## Help

npx oneblink cdn --help

https://github.com/oneblink/cli/blob/master/docs/cdn/overview.md