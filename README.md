That mini-framework generate dynamics scenarios based upon on a config file in addition to test API calls and their responses.  
Initially I intended make a mini-framework where I could assert any data of results from API calls, but it ran away from my first intention of simplicity. So now,  the major purpose of that project is, briefly, mixing scenarios set to all resources of an API and ensuring that the HTTP status code is equal to what was set in config file.

It is written in javascript and it uses Vows (to API tests, whereas it allows to create dynamic contexts - test cases) and Mocha (to helpers tests).

If you plan to test other data and business logic of your API, so I suggest you to create tests (unit and integration) inside your API project so that you can get more detailed information and not rely on infrastructure, network and external services (you can mock and stub them).  
In the meantime, if you want to ensure the the proper operation of all involved with your API, in the simple way, and the basic behavior (send some data and expect specific HTTP status code), that project can help you.

## Prerequisites
**Step 1:** Install node and npm. 

**Step 2:** Install all dependencies for the project.

```
npm install
```
**Step 3:** Install mocha for run tests of helpers.

```
npm install -g mocha
```
**Step 4:** Install vows for run dynamic tests of API.

```
npm install -g vows
```

## Steps to run all tests of the helpers

Run the following command: 
```
make test-others
```

## Steps to run the API tests

Run the following command: 
```
make test-api
```

## How to configure 

Open the file **/config/configApiExample.json** in order to follow the information below.

Modify that file (rename it for better readability) with the config of your api.

### API config
Data related to api and debug.

### AUTH config
Data required to OAuth or Basic authentication. Wheter Basic so you can leave unnecessary fields blank.

### RESOURCES config

Data for resources of your API.

#### Details

**"name"**: some alias to resource.  
**"url"**: relative url to specific resource. Example: "/search". In url is possible has placeholder to some dynamic part. Example: "/places/{placeId}/reviews/new". Note that "placeId" is embraced by brackets {}. It tell to system generate a dynamic value based on "placeId" param.  
**"auth"**: type of authentication. Can be "oauth" or "basic".  
**"methodHttp"**: the name say all. Can be "put", "post", "get" or "delete".  

##### params config

**"params"**: all data relative to params of that resource (or some dynamic value in URL).  
**"name"**: param name.  
**"scenario"**: an array of mappings containing "value" and "statusHttp". All those values will be tested on.  
In a nutshell, the system will get a "value" from a scenario and will mix with all others needed params for that resource with POSITIVE (status http = 200) values. It ensures that wheter ypou are generating a negative scenario (e.g. status http = 400) so only current value is negative and others positive to test one negative thing by time. So, "statusHttp" make sense now, since expected result is relative to that specific value of the param, when it is sent with all others valid params.  

Example: 
```
    "params": [
        "name": "rating",
        "scenario": [
                {"value": "1", "statusHttp": "200"},
                {"value": "2", "statusHttp": "200"},
                {"value": "3", "statusHttp": "200"},
                {"value": "4", "statusHttp": "200"},
                {"value": "5", "statusHttp": "200"},
                {"value": "0", "statusHttp": "400"},
                {"value": "6", "statusHttp": "400"},
                {"value": "a", "statusHttp": "400"},
                {"value": "é", "statusHttp": "400"}
         ]
    ]
```
Params required by multiple resources can just referenced in params without "scenario".  
It will tell to system to use this param and get scenarios from "paramsDefault" (see below).  

Example:
```
    "params": [
        "name": "type"
    ]
```
##### paramsDefault config

Put here all params (or dynamic value to urls) that you want to use with multiple resources so you don't need to repeat yourself.  

```
"paramsDefault":
    [
        {
            "name": "type",
            "scenario": [
                    {"value": "json", "statusHttp": "200"},
                    {"value": "xml", "statusHttp": "200"},
                    {"value": "a","statusHttp": "400"}
            ]
        }
    ]
```

### Dynamic values for scenario

Imagine that you need to test a resource that have a constraint to unique values. You can do it in the following way:
Inside "value" of a scenario, type the char ***$*** followed by a valid function name.

Example: 
```
 {
    "name": "content",
    "scenario": [
        { "value": "$randomText(20)", "statusHttp": "200" }
    ]
}
```

Note the value ***$randomText(20)***. Now, you can ask: ***WHERE is that function?***
The system will search that function in "/lib/helper/config.js" file. That function is there just for the sake of example.
That file should contain all your helper functions that you wants to generate dynamic values in config file.