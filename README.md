# mock-services

This project provide Mock Servers for a bunch of services that cannot be accessed out of the production environment.

It's compouded by:
    Wiremocks - used to create SOAP e REST APIs server mocks [https://github.com/wiremock/wiremock]
    FakeSMTP - used to Mock SMTP Server [Copyright (c) 2011-2015, Gautier <Nilhcem> MECHLING http://nilhcem.com/FakeSMTP/]


## Docker Build and Run Wiremocks (localhost)
```shell
cd ./wiremocks
export WIREMOCK_OPTIONS=--port=8089,--https-port=8088,--max-request-journal=1000,--local-response-templating,--root-dir=/home/wiremock/storage
docker build -t santana-repos/wiremocks .
docker container run --name wiremocks --hostname --wiremocks --network default -p 8089:8089 -e WIREMOCK_OPTIONS santana-repos/wiremocks
```

Refs.: https://github.com/wiremock/wiremock

## Testing Jira mock REST API endpoints:
You need to have *curl* and *jq* installed into your environment
```shell
curl -X GET -vv http://localhost:8089/jira/rest/api/3/user/groups?accountId=5b10ac8d82e05b22cc7d4ef5 | jq
curl -X GET -vv http://localhost:8089/jira/rest/api/3/user/groups?accountId=00-test-account-001 | jq
```

Refs.: https://developer.atlassian.com/cloud/jira/platform/rest/v3/api-group-users/#api-rest-api-3-user-groups-get


## Testing Github mock REST API endpoints:
You need to have *curl* and *jq* installed into your environment
```shell
curl -X GET -vv http://localhost:8089/github/orgs/001/members/mock-001 | jq
curl -X GET -vv http://localhost:8089/github/orgs/001/members/00-santana-account-002 | jq
```

Refs.: https://docs.github.com/en/rest/orgs

## Wiremocks REST api Admin (GET) endpoints:
You need to have *curl* and *jq* installed into your environment
```shell
curl -X GET -vv http://localhost:8089/__admin/ | jq
curl -X GET -vv http://localhost:8089/__admin/mappings | jq
```



### Sample how to create a Jira search mock endpoint via Wiremocks's Admins API
You need to have *curl* installed into your environment
curl --location --request POST 'https://localhost:8089/__admin/mappings/new' \
--header 'Content-Type: application/json' \
--data-raw '{ 
    "request": { 
        "method": "GET", 
        "urlPathPattern": "/jira/rest/api/3/user/search", 
				"queryParameters": { 
           "query": { 
              "matches": ".*" 
           } 
        } 
    }, 
    "response": { 
        "status": 200, 
        "jsonBody": {
            "self": "",
            "accountId": "",
            "username": "{{request.query.query}}",
            "accountType": "",
            "emailAddress": "",
            "avatarUrls": {
                    "_48x48": "",
                    "_24x24": "",
                    "_16x16": "",
                    "_32x32": ""
            },
            "displayName": "",
            "active": "",
            "timeZone": "",
            "locale": "",
            "groups": {
                    "size": "",
                    "items": [
                            {
                                    "name": "",
                                    "self": ""
                            }
                    ]
            },
            "applicationRoles": {
                    "size": "",
                    "items": [
                            {
                                    "name": "",
                                    "self": ""
                            }
                    ]
            },
            "expand": ""
        }, 
        "headers": { 
            "Content-Type": "application/json" 
        }, 
        "transformers": [ 
            "response-template" 
        ] 
    } 
}'


--------------------------------------------------------------------------------------
*Note:* at wiremock/mappings folder you will find the config files for the already created mock server (Jira and Github) and you will be able create your own SOAP or REST mappings


## Docker Build and Run FakeSMTP (localhost)
```shell
cd ./fake-smtp
docker build -t santana-repos/fake-smtp .
docker run --name fake-smtp --hostname fake-smtp --network default -p 25:25 santana-repos/fake-smtp
```

Refs.:  http://nilhcem.com/FakeSMTP/

## Testing SMTP mock server:
You need to have *ssmtp* installed into your environment.
Edit your */etc/ssmtp/ssmtp.conf* file and put the value *localhost:25* at the key *mailhub*
```shell
echo "Test message from my host to Fake SMTP using ssmtp :)" | ssmtp -vvv user@santana-repos.com.br
```

### AWS {ECR + Codebuild + EKS} build, deploy and run
buildspec.yml