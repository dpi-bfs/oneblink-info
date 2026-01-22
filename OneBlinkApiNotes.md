
## Reference

* https://github.com/oneblink/cli/blob/master/docs/api/README.md

## Setup for the first time

### Quick Start

- OB console > Create API e.g. my-place.api.oneblink.io

- Setup and configuration of local code files.
  + Copied files
  + npm update
  + npx npm-check-updates --upgrade
  + npm install (twice)

- Local files upload to OB API my-place.api.oneblink.io
  + .blinkmrc.json my-place.api.oneblink.io
  + Upload some code to the OB console (the code isn't expected to work, we are just checking we have access to update the code in the console)
    * Terminal process 01: npm run watch
    * Terminal process 02: Set Access and Secret keys
      - $ENV:ONEBLINK_ACCESS_KEY="[put key from .blinkmrc.json in here]"
      - $ENV:ONEBLINK_SECRET_KEY="[put key from .blinkmrc.json in here]"
    * Terminal process 02: npx oneblink api deploy --env dev;Get-Date
  + Deployment to Dev completed Friday, 7 February 2025 19:29:10
  + Sighted in "API Hosting"

- OB Console > Developer Tools > Dynamic Lookups. Created New Dynamic Lookup
  + Data Lookup & Hosted API
  + Label: FSS Certificate Validator
  + Endpoint: my-place.api.oneblink.io
  + Dev: Permit status

- Form "Validate a Food Safety Supervisor certificate"
  + Fetch permit status. Data Lookup changed from "Movement Permit Validation" to "FSS Certificate Validator"
  + Version updated v20250207-1935
  + Tried https://nswfoodauthority-forms-dev.cdn.oneblink.io/forms/24785?preFillData={%22PermitSubmissionId%22:%224acd559a-6940-43f9-b427-1c8c6df95306%22} Approved.

- Add your local files to a new local git repo
  - Initialize Git
  - Project Settings. Create fake Git email
  - Workflowy reference
    + Create a local repo from existing files
    + Create a remote github repository (repo) from a local repo. Created https://github.com/dpi-bfs/fss-certificate-validator
    + Created as Private for now
    + Added remote to local repo https://github.com/dpi-bfs/fss-certificate-validator.git
    + Pushed local files to remote
    + Prompted for SmartGit Password
    + Files uploaded to remote.

### Code Basics

* Latest Node.js version supported be OneBlink Check documentation at

  > https://github.com/oneblink/cli/blob/master/docs/README.md
  > https://github.com/oneblink/sdk-node-js/blob/master/package.json
  > https://github.com/oneblink/cli/blob/master/package.json

* OneBlink doesn't necessarily use the latest Node.js LTS version. It uses the version given in the Api logs. Check the logs to get the supported version ...

  > INIT_START Runtime Version: nodejs:18.v57	Runtime Version ARN: arn:aws:lambda:ap-southeast-2::runtime:8865cfc6a1d3f2dfabf5c509eaa9fbd70aa12fa4bbe614047030158c21978bcc

  ... then up/down grade your node to match

* Deprecated
  - Nodejs version support in the console #80596 https://support.oneblink.io/support/tickets/80596
  - See that at https://github.com/oneblink/cli/blob/master/docs/api/environment.md we have a link to https://nodejs.org/dist/latest-v18.x/docs/api/
  - https://nodejs.org/en/download to check what the latest Node LTS version is.
  - Check your current version

* Check node version.

      ps> node -v

      winget list node
      Name        Id                Version Source
      ---------------------------------------------
      Node.js LTS OpenJS.NodeJS.LTS 16.14.2 winget

      From (The Cypress Team 2022, "Cypress Docs"), Getting Started, Installing Cypress, https://docs.cypress.io/guides/getting-started/installing-cypress ...

* Check Node versions available: https://nodejs.org/download/release/

* (As required) Downgrade node.

      ps> winget uninstall Node.js
      Check the versions supported by Node.js https://nodejs.org/download/release/
      ps> winget install OpenJS.NodeJS.LTS --version 18.20.7

* (As required) Upgrade node npm to the latest.

      winget upgrade Node.js (this will upgrade Id OpenJS.NodeJS.LTS)

* Check npm version

      npm -v

* (In some contexts, e.g. Cypress testing) Downgrade to npm v6 to avoid hanging.

       npm install -g npm@6

    https://stackoverflow.com/a/71013772/872154


* Console > Advanced Tools > Developer Tools >  API Hosting > + > [Create a project url e.g. nswfoodauthority-fmd.api.oneblink.io]

* Local Project
  - Download https://github.com/oneblink/cli/tree/master/examples/api/lookup
  - .blinkmrc.json
    + "project": "nswfoodauthority-fmd.api.oneblink.io",
  - Create a .gitignore.

        /.vscode/
        /node_modules
        /out/
        .blinkmrc.json
        *.code-workspace
        package-lock.json

  - Double check especially that .blinkmrc.json won't be committed to your git repo, because it contains secrets.
  - Double check that /node_modules won't be committed to your git repo, because it takes up a lot of space and can be recreated from package.json.
  - npm init (package.json)

* See [.blinkmrc.json](.blinkmrc-example.json) example. Remove `-example` from file name.
      

* Verify your .blinkmrc.json doesn't have a superfluous "https://" in the .blinkmrc.json server.project field. That is,

  // Do this
  "server": {
    "project": "nswfoodauthority-link-mapper.api.oneblink.io",

  // Not this
  "server": {
    "project": "https://nswfoodauthority-link-mapper.api.oneblink.io",

  ... otherwise you'll get authentication failures when trying to deploy.


* Verify your .blinkmrc.json has the Web Application Firewall disabled. Otherwise, it has the following effect:
  + You can hit your code fine from Postman but not from a form.
  + When hitting from a form:
    * Your API logging console.log commands won't be visible
    * In the Browser Developer Console you'll see a CORS error
      > Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at https://nswfoodauthority-get-holdings-from-pic-dev.api.oneblink.io/get-holdings. (Reason: CORS header ‘Access-Control-Allow-Origin’ missing). Status code: 403.

                "waf": {
                  "local": false,
                  "dev": false,
                  "test": false,
                  "train": false,
                  "prod": false
                },

* Local project package.json

      "main": "out/index.js",
      "type": "module",

* Local Project. Use js file extensions in ts imports. e.g.

      import { sendRequest } from './jitterbit.js'

* Local Project. tsconfig.json

      {
        "compilerOptions": {
          "target": "ES2015",
          "module": "ES2022",
          "moduleResolution": "Node",
          "esModuleInterop": true,
          "strict": true,
          "skipLibCheck": true,
          "forceConsistentCasingInFileNames": true,
          "resolveJsonModule": true,
          "outDir": "out",
          "rootDir": "src",
          "baseUrl": ".",
          "sourceMap": true,
          "useUnknownInCatchVariables": false,
        },
        "include": [
          "src/**/*"
        ],
        "exclude": [
          "src/embedding/*"
        ]
      }

* Local Project. Compile your typescript:

      npx tsc --watch // In it's own terminal

      // Or

      npx tsc

      // Or

      package.json
        "scripts": {
          "print": "echo %npm_package_config_externalLibraryPath%",
          "watch-typescript": "npx tsc --watch",
          "watch": "npm-run-all watch-typescript",
          "start": "node ./out/index.js"
        },
      npm run build

* Install OneBlink CLI (be sure to run to update). Install it locally rather than globally.

      npm install @oneblink/cli

### (Deprecated) Working with Typescript/library (now "Typescript/BfsLibrary")

* In MovementPermitValidator we have package.json

      "config": {
        "externalLibraryPath": "../../../Typescript/library/",
        "externalLibraryFileList": " dateTime.* httpWrapper.* http.*"
      },
      "scripts": {
        "print": "echo %npm_package_config_externalLibraryPath%",
        "watch-typescript": "npx tsc --watch",
        "mirror-library-files": "robocopy %npm_package_config_externalLibraryPath% src/lib %npm_package_config_externalLibraryFileList% /MIR /lev:1",
        "watch-library-files": "chokidar %npm_package_config_externalLibraryPath%*.{ts,mts} -c \"npm run mirror-library-files\" --initial",
        "clean": "rimraf out src/lib",
        "watch": "npm-run-all clean --parallel watch-library-files watch-typescript",
        "start": "node ./out/index.js"
      },

* Open a terminal instance in MovementPermitValidation root; then
    npm run watch

* Open a terminal instance in Library root; then
    npm run watch

* Update library code in the /Typescript/library/, not MovementPermitValidator

### Working with Typescript/BfsLibrary as Git submodule

* See John\Documents\Sda\Info\Git\KB\Reference\GitAndGithubReference.md for working with Git submodules generally. That's the master copy, copied below as "Git Submodules Setup" and "Git Submodules Workflow"

### Git Submodules Setup

SmartGit:

* Create/add your local library as a Git Repo.
* From your project git add the local library as a Git submodule
  - SmartGit Repositories Pane > [My Project] (Already open) > /src [click to select]
  - SmartGit Menu > Remote > Submodule > Add ...
    + E.g. Local Repository C:\Users\bentlj02\OneDrive - DPIE\Documents\Sda\Code\Typescript\BfsLibrary
* You should now see in SmartGit Repositories Pane:
  - MyProject > src > BfsLibrary (the git submodule)
* Commit the addition of the git submodule. Observe:
  - "src" folder changes from red to yellow.
  - In Visual Studio Code BfsLibrary has been imported at src/BfsLibrary with child files.

### Git Submodules Workflow

* Ensure you now have two instances of SmartGit open. For your project and the local library.
* Local library:
  - Work on code.
  - Commit locally
* Project. Pull local library changes into Project.
  - Verify you are checkout on a branch:
    + SmartGit > Branches pane. Observe triangle against branch is fully black, and doesn't have a white core. Otherwise;
    + SmartGit > Branches pane > On a branch double click.
  - > Repositories > [My Project] > src > [My Git Submodule] (E.g. BfsLibrary) > Right click > [Pull ...]
  - > Repositories > [My Project] Click
    + Observe your Submodule will be marked as modified; and your project library will have been updated.
    + Commit the change
  - Ensure your project references the library as a subfolder in your project. E.g.

        import * as HttpWrapper from './BfsLibrary/httpWrapper.js'

    ... rather than, say, as a symbolic link  `npm link @dpibfs/httpWrapper` (using, from your library, `npm link`)

        import * as HttpWrapper from '@dpibfs/httpWrapper'

### Git Submodules Troubleshooting

* If you find form a parent repo you can't pull in submodule changes.
  - At Submodule repo: Check the submodule has committed it's changes.
  - At Parent repo: verify you are checkout on a branch:
    + SmartGit > Branches pane. Observe triangle against branch is fully black, and doesn't have a white core. Otherwise;
    + SmartGit > Branches pane > On a branch double click.
* Don't have both parent repo and submodule checkout at the same time to make the parent repo be able to push submodule changes.

### Refactor copied code

* If you've copied code from a another project refactor it, pruning as necessary, for the current project.

## Update code

* Check for updates

      ps> npx npm-check-updates --upgrade

* Update packages

      ps> npm install

      // or

      ps> npm update

* Verify updates

  - For specific packages

      npx oneblink --version

      -or-

  - All installed packages

      npm list

* To build the Typescript files, including assets, to /output use the following

  - A package.json custom command

      ps> npm run watch

    After execution this will automatically update changed files in /src to /output.

    `npm-run-all` is needed to execute multiple commands properly on Windows (and is a cross platform solution https://github.com/mysticatea/npm-run-all)

    Observe package.json for the `npm run watch` dependencies:

        "scripts": {
          "copy-assets": "copyfiles -u 1 src/templates/*.mustache output",
          "watch-assets": "chokidar \"src/templates/**/*\" -c \"npm run copy-assets\" --initial",
          "watch-typescript": "npx tsc --watch",
          "watch": "npm-run-all --parallel watch-assets watch-typescript"
        },
        "devDependencies": {
          "chokidar-cli": "^3.0.0",
          "copyfiles": "^2.4.1",
          "npm-run-all": "^4.1.5",
          "typescript": "^5.1.6"
        },

    -or-

  - (If the project doesn't need a more sophisticated watch as above) Typescript general watch command

      ps>npx tsc --watch

* Work on any code and commit it to the local git.

## OneBlink Authenticate

* Get access to the OneBlink API hosting location and/or usage of the OneBlink CLI. Either

  - Login

      npx oneblink login

    -or-

  - Use deployment keys https://github.com/oneblink/cli/blob/master/docs/api/deployment-keys.md

    * Create or reuse developer keys from the console.
      - OneBlink Console > Developer Tools > Developer Keys.
      - See a card with an "APIs" permission.
      - Copy the Key Id and Key Secret respectively as an OS environment variable. E.g. In Windows powershell

            $ENV:ONEBLINK_ACCESS_KEY="TheKeyId"
            $ENV:ONEBLINK_SECRET_KEY="TheKeySecret"

  - Display the currently set environment variables for ONEBLINK

      Get-ChildItem Env: | Where-Object { $_.Name -like 'ONEBLINK*' }

* Then testing locally or deploying remotely is enabled ...

## Test locally

* Locally test by running a local server.

      npx oneblink api serve --env dev;Get-Date

* You should see

      HTTP service for local development is available from:
        http://localhost:3000

      Hit CTRL-C to stop the service

* If you change code you'll have to restart the local server:
  - Hit CTRL-C to stop the service
  - npx oneblink api serve --env dev;Get-Date

* Use Postman to test:

  - Download the app: https://www.postman.com/downloads/
  - Create new collection "My Project"
    + Create "web" and "local" subfolders
  - Under each subfolder create New HTTP Request
    + Web
      - Post: https://nswfoodauthority-fmd-dev.api.oneblink.io/company-details
        A particular **environment link** given a project line. E.g. Observe the "dev" in ...
        + https://nswfoodauthority-fmd.api.oneblink.io/ (Project Link)
        + https://nswfoodauthority-fmd-dev.api.oneblink.io/ (Environment specific Link)

      - Body > Raw: JSON >

            {
                "submission": {
                    "abn": "2"
                }
            }

      - [Send]

      - Should get returned:

            {
              "Company_name": "Roberts burgers"
            }
    + Local
      - Post: http://localhost:3000/approval-form-personalisation
      - Body and Result format is project/api dependent e.g.
      - Body > Raw: JSON >

          {
              "formsAppId": 1716,
              "formId": 19289,
              "externalIdUrlSearchParam": "F4AZDG",
              "jobId": null,
              "previousFormSubmissionApprovalId": "3b2fc9a3-b6d2-4d1c-85f9-0a453bab319a"
          }

      - [Send]

      - Should get returned:

            {
              "Company_name": "Roberts burgers"
            }

* Or Use Invoke-WebRequest

      Invoke-WebRequest -Method POST -Uri https://nswfoodauthority-fmd-dev.api.oneblink.io/company-details -Body '{
        >>     "submission": {
        >>         "abn": "2"
        >>     }
        >> }'

      Invoke-WebRequest -Method POST -Uri http://localhost:3000/pic-validate/property_identification_code -Body '{
        >>     "submission": {
        >>         "abn": "2"
        >>     }
        >> }'

* To discover the request format to send to postman. Use your Browser's Dev Tools network tab; or OneBlink API logs to examine the request object.

  - For a form submission

    + Open your Browser (I'll use Firefox) > F12 Dev tools > Network tab ..

      - Submit a form (use a prefill url to make this easier) and look for HTTP that calls (Generally POSTs) your API routes. E.g. Parthenium Weed Project ..

        + Submit a form (which doesn't call the API for this project, that comes later) https://nswfoodauthority-dpi-forms-dev.cdn.oneblink.io/forms/19049
        +  https://nswfoodauthority-dev.approvals.oneblink.io/ > F12 Dev tools
        + [Click on a record awaiting approval]. Dev tools > https://nswfoodauthority-parthenium-carrier-dev.api.oneblink.io/approval-form-personalisation > Right Click > [Copy Post Data]. E.g.

            {"formsAppId":1716,"formId":19289,"externalIdUrlSearchParam":"05E9L1","jobId":null,"previousFormSubmissionApprovalId":"094e30d7-a246-45ac-b565-46212cbce847"}

        > [Review] > Complete Approval Form > [Continue]

      - https://nswfoodauthority-parthenium-carrier-dev.api.oneblink.io/approval-form-receipt-id

    + OneBlink API Log e.g.

      - https://console.oneblink.io/accounts/57ede17e01d7f85383b56c81/apis/nswfoodauthority-parthenium-carrier.api.oneblink.io/environments/dev/logs
      - Look for POST entries just above an "END" line. E.g. "INFO POST /base-form-approval-event 200"
      - Look for a req (request) object log line since the prior "START". e.g.

            2023-10-04T01:34:28.544Z	c971e453-c579-4e6f-9dff-022709cc9069	INFO	req {
              body: {
                formsAppId: 1538,
                formId: 19049,
                submissionId: '0f3cf343-e28b-4634-837e-6e5235ee5631',
                isDraft: false,
                submissionTimestamp: '2023-10-04T01:00:59.930Z',
                externalId: 'HC6K6I',
                secret: 'jjjjjjjjjjjjjjj'
              },
              headers: {
                'content-type': 'application/json',
                'x-forwarded-proto': 'https',
                'user-agent': 'OneBlink - Form Submission Events',
                host: 'nswfoodauthority-parthenium-carrier-dev.api.oneblink.io'
              },
              method: 'post',
              route: '/base-form-approval-event',
              url: {
                host: 'nswfoodauthority-parthenium-carrier-dev.api.oneblink.io',
                hostname: 'nswfoodauthority-parthenium-carrier-dev.api.oneblink.io',
                params: {},
                pathname: '/base-form-approval-event',
                protocol: 'https:',
                query: {},
                querystring: ''
              }
            }

      - Take the body only and reformat it with quotes.
        + VSC > copy to *.json file > Ctrl + Click on each of the Keys > Shift + " to surround each key with quotes.
        + VSC > Select a single quote > Ctrl+Alt+F, repeatedly press F > Shift + "

      - Paste the result into PostMan body ...

            {
              "formsAppId": 1538,
              "formId": 19049,
              "submissionId": "0f3cf343-e28b-4634-837e-6e5235ee5631",
              "isDraft": false,
              "submissionTimestamp": "2023-10-04T01:00:59.930Z",
              "externalId": "HC6K6I",
              "secret": "jjjjjjjjjjjjjj"
            }


      - Send
      - Verify the response:

            {
                "message": "/base-form-approval-event completed."
            }
    - For an element data lookup

      + Upload some code (even if not working) to the DEV Api, to at least create the relevant OB route
      + Create an OB Dynamic Lookup
      + Set the element to use a Data Lookup, referring to the Dynamic Lookup.
      + With your browser's console open and the form loaded, trigger the element lookup.
      + Copy the request e.g.

          {
            "element": {
              "name": "PaperCertificateNumber",
              "label": "Paper certificate number",
              "type": "text",
              "required": true,
              "id": "41d1d371-db22-440c-ae6b-8e6ee9d91f10",
              "conditionallyShow": false,
              "readOnly": false,
              "isDataLookup": true,
              "isElementLookup": false,
              "hintPosition": "BELOW_LABEL",
              "hint": "This is the number on the paper biosecurity certificate given to you. This is also reproduced on the PDF digital biosecurity certificate (available if you supplied an email on the record of movement), up the top as \"Paper certificate number\". It may have been filled for you if you followed a link we provided.",
              "regexPattern": "[P|Q]\\d{4}",
              "regexMessage": "Must be \"P\" (for 2023) or \"Q\" (for 2024) followed by four digits. For example, \"P1234\", \"Q5678\".",
              "dataLookupId": 708
            },
            "definition": {
              "id": 23378,
              "name": "Parthenium Weed Carriers - NSW Destinations Notification",
              "description": "",
              "organisationId": "57ede17e01d7f85383b56c81",
              "elements": [
                {
                  "name": "trackingCode",
                  "label": "Destinations notification reference code",
                  "type": "text",
                  "required": false,
                  "id": "329a2da7-6d0d-4cac-86e2-363d0f93a02d",
                  "conditionallyShow": false,
                  "readOnly": false,
                  "isDataLookup": false,
                  "isElementLookup": false
                },

              ],
              "isAuthenticated": false,
            ...
              "googleMapsIntegrationEnvironmentId": null,
              "isArchived": false,
              "formsAppIds": [
                1538
              ]
            },
            "submission": {
              "PaperCertificateNumber": "P1111",
              "Destinations": [
                {
                  "DestinationCannotFindAddressBySearch": false
                }
              ],
              "env": "ENV",
              "__linkToRestart": true
            }
          }

      + Paste the request into Postman to hit the *local* route
      + Serve the app locally:

          npx oneblink api serve --env dev;Get-Date

      + Send a request from postman
      + To retest changes:
        * Kill the serve process with Ctrl+C
        * Adjust code.
        * Restart local server:  npx oneblink api serve --env dev;Get-Date
        * Send request from Postman again.



* OneBlink API Log order. E.g. https://console.oneblink.io/accounts/57ede17e01d7f85383b56c81/apis/nswfoodauthority-parthenium-carrier.api.oneblink.io/environments/dev/logs. When testing via Postman it's useful to get the order of events right by reviewing the OneBlink API log order.

* When testing locally review the Terminal's "TERMINAL" tab for console logging output.


## Deploy remotely

* Deploy command

    npx oneblink api deploy --env dev;Get-Date

* Deploy to other environments

    npx oneblink api deploy --env test;Get-Date

## Tear down and delete Api E.g. nswfoodauthority-movement-permit-validation.api.oneblink.io

Successfully tore down environment after learning developer keys take precedence over login credentials; and teardown does not permit the use of developer keys. Solution: use `npx oneblink login` in session where developer keys have not been set as OS environment variables.

  Check .blinkmrc.json has project set to the api you want to teardown. E.g.

      "project": "nswfoodauthority-movement-permit-validation.api.oneblink.io",

  Open session where developer keys not set as OS environment variables.

  npx oneblink login

  npx oneblink api teardown --env dev

  Optionally Console > Developer Tools > Api Hosting > [API project e.g. "nswfoodauthority-movement-permit-validation.api.oneblink.io"] > Delete.

## Form Definition

* To get the form definition:
  - Load the form in the browser
  - Open the Developer Panel (F12) (in FF) > Network > Filter: XHR > Look for Domain (auth-api.blinkm.io) File (20913?injectForms=true)
  - |Response| Tab.
  - Observe the response JSON. Among other things it should have an "elements" array.

For a convenient list of elements use "OneBlink Elements of Interest" https://github.com/dpi-bfs/oneblink-elements-of-interest


## .blinkmrc.json API timeout 

* Typescript API code timeout if it exceeds 119 (or maybe 120) no timeout email notification error is received even if the power automate gateway times out and is logged.
* The timeout is with respect to the time between API code posting data and receiving a response from Power Automate. e.g. 2025-09-10 
  - (when 119 timeout set)
    * 11:17:41:393 INIT_START Runtime Version: nodejs:18.v81 Runtime Version ARN: arn:aws:lambda:ap-southeast-2::runtime:383248ddd42b9c40948071c40a8f726625554b9d343cee049792b82a21330b2c
    * **11:18:45:301** INFO Posting data to power automate
    * **11:20:37:066** 7113088a-70ab-41f6-a4ab-60ce4c3487dd END
    * 11:20:37:066 REPORT Duration: 119000.00 ms Billed Duration: 119000 ms Memory Size: 1024 MB Max Memory Used: 174 MB Status: timeout
  - (when 600 timeout set)
    + 11:30:10:571 INIT_START Runtime Version: nodejs:18.v81 Runtime Version ARN: arn:aws:lambda:ap-southeast-2::runtime:383248ddd42b9c40948071c40a8f726625554b9d343cee049792b82a21330b2c
    + **11:31:47:572** 5983efcd-f7c7-4079-ac44-ac6f0f5c8f84 INFO Posting data to power automate
    + **11:33:47:638** 5983efcd-f7c7-4079-ac44-ac6f0f5c8f84	ERROR	Error: Error: HTTP error! status: 504; statusMessage: Gateway Timeout; json: {"code":"UnexpectedError","message":"An unexpected error occurred."}  


## Troubleshooting

### Authentication problems when deploying code

Example:

> Authentication failed... FetchError: request to https://auth-api.blinkm.io/v3/apis/nswfoodauthority-parthenium-carrier.api.oneblink.io/environments/dev/credentials/deploy failed, reason: self-signed certificate in certificate chain

Try:

- Suspending Checkpoint harmony; if that doesn't work ...
- Connecting via Wifi NSWGovMDM rather than Corporate_Wifi worked

### SELF_SIGNED_CERT_IN_CHAIN

Problem: npm error code SELF_SIGNED_CERT_IN_CHAIN. 
Solution: switch from Corporate_Wifi to NSWGovMDM wifi

## Other deployment problems

* Problem
✔ Are you sure you want to deploy to environment "dev": [Y] Yes
✔ Authentication complete!
✔ Validation complete!
✔ Removed developer dependencies!
✔ Compression complete!
✖ Transfer failed: 0%
There was a problem executing oneblink api deploy:

TypeError: config.requestChecksumCalculation is not a function

* Solution:
 - delete node_modules 
 - npm install
 - npm run watch
 - redeploy

## Coding tips and tricks

### Boom messages that OneBlink will display

See \Documents\Sda\Code\OneBlink\Apps\PartheniumWeedCarriers\src\certificate-validator.ts

OB only recognizes Boom.badRequest() messages.At least it doesn't recognize Boom.notFound

Paragraph handling:

    As this gets inserted into a <p>, use:
    <br /><br /> if you want paragraphs; and
    <br /> if you want an end of line
