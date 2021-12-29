# Hello World

This is a simple application that validates responses produced by `httpbin.org`, a service that can be used to test generic API calls.

## Context

The first request to be tested is the following:

```
curl -X POST "https://httpbin.org/anything" -H  "Content-Type: application/json" -d '{"my_var":"my_val"}'
```

Which produces the following response:
```
{
  "args": {},
  "data": "{\"my_var\":\"my_val\"}",
  "files": {},
  "form": {},
  "headers": {
    "Accept": "*/*",
    "Content-Length": "17",
    "Content-Type": "application/json",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.54.0",
    "X-Amzn-Trace-Id": "Root=1-61cd0fe5-5c76144560fb89e750693502"
  },
  "json": {
    "my_var": "my_val"
  },
  "method": "POST",
  "origin": "XXXX",
  "url": "https://httpbin.org/anything"
}
```

Notice how the json data we passed is returned by the service in the `json` field.


We will extract the value of `my_var` from the json response, store is as `extracted_var`, and then pass it as a parameter in a second request to httpbin:

```
curl -X GET "https://httpbin.org/get?my_param=<extracted_var>"
```

This should produce a response like the following:
```
  "args": {
    "my_param": <extracted_var>
  },
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.54.0",
    "X-Amzn-Trace-Id": "Root=1-61cd1524-44e4fd1e4053a023014dc291"
  },
  "origin": "XXXX",
  "url": "https://httpbin.org/get?my_param=<extracted_var>"
}
```

Finally we will verify that the value of `extraced_var` equals "my_val".

## Setup

### Structure definition

Launch the JMeter GUI and rename the root `Test Plan` to "Hello World"

Add the following elements to the test plan's "Hello World" node; each element can be added by following the path described after its name under the right-click menu of its parent node:

- Thread Group -> Add > Threads (Users) > Thread Group
    - HTTP Request -> Add > Sampler > HTTP Request
      - HTTP Header Manager -> Add > Config Element > HTTP Header Manager
      - JSON Extractor -> Add > Post Processors > JSON Extractor
    - HTTP Request -> Add > Sampler > HTTP Request 
      - JSON Assertion -> Add > Assertions > JSON Assertion
    - View Results tree -> Add > Listener > View Results Tree

### Configuration

Each of the elements added in the previous step can be configured to specify its desired behaviour.

Thread Group:
- Leave the default configuration, which is set to one user and one request only.

Thread Group > HTTP Request 1:
- set `protocol` to "https"
- set `server name` to "httpbin.org"
- set `HTTP Request verb` to "POST"
- set `HTTP Request Path` to "/anything"
- set `Body Data` to
  ```
  {
    "my_var":"my_val"
  }
  ```

Thread Group > HTTP Request 1 > HTTP Header Manager:
- Add the header name "Content-type" with value "application/json"

Thread Group > HTTP Request 1 > JSON Extractor:
- Set "Name of created variables" to `extracted_var`
- Set "JSON Path expressions" to `$['json']['my_var']`, note how this matches the path to the data we want to extract from the first request's response.

Thread Group > HTTP Request 2:
- set `protocol` to "https"
- set `server name` to "httpbin.org"
- set `HTTP Request verb` to "GET"
- set `HTTP Request Path` to "/get"
- add to `Parameters` "my_param" with value "${extracted_var}", this fetches the value of `extracted_var` that was defined in the last step through the JSON Extractor.

Thread Group > HTTP Request 2 > JSON Assertion:
- set `Assert JSON path exists` to `$['args']['my_param']`, note how this matches the path to the data whose presence we want to vlidate from the second request's response.
- check the `Additionally assert value` box and set `Expected Value` to "my_val"

## Test execution

Click the `View Results Tree` node and then press the `Start` button in the toolbar.

If the setup was correct, the execution stages should be appear as entries with green icons on a list, indicating their successful execution. They can be inspected for further analysis.


## Reference
<https://octoperf.com/blog/2018/06/05/jmeter-vs-soapui/#jmeter-scripting>
