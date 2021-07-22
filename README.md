# Tips to get Burp flexing

## Search saved Burp files

```bash
grep --include=\*.burp -rnw . -e "hotel"


# -r recursive
# -n line number
# -w match whole word
```

## Replay the same requests many times

You can do this with Intruder ( not Repeater, as you might expect ).  

 - Send request to `Intruder`
 - In `Positions` tab, `Clear §`
 - In `Payloads` tab, select:
    - `Payload Type: Null Payment`
    -  Select number of requests to replay

## Enumerate a single user's account email

### Find API

```json
POST /v1/check-email-address HTTP/1.1
Host: foobar.com

{"email":"foo.bar@foobar.com"}
```

### Response

```json
{"registered":false}
```

### Burp Intruder set up

 - Send request to `Intruder`
 - In `Positions` tab, select `Clear §`
 - Then select `Add §` after highlighting `"foo.bar@foobar.com"`
 - In `Payloads` tab, select:
    - `Payload Type: Username Generator`
    - `Payload Options [Username Generator]` add base target email `foo_bar@foobar.com`
    - `Payload Encoding` de-select the `URL encode` box
 - In `Options` tab, select:
    - de-select _"make unmodified baseline request"_
    - In `Attack Results` specify whether to save requests and responses
    - In `grep match` add the line `"registered":true` [ to ensure it is simple to view a successful attack ]

### Optional

You can slow the enumeration by adding a custom `Resource Pool` inside of `Intruder`.  You can delay the time between requests.

## Inject XSS Payload

### Request

```json
POST /v1/final-order HTTP/1.1
Host: foobar.com

{"address":"125 important place"}
```

### Burp Extender

From `Extender` select `BApp Store`. Install `xssValidator`.

### Burp Intruder set up

 - Send request to `Intruder`
 - In `Positions` tab, select `Clear §`
 - Then select `Add §` after highlighting `"125 important place"`
 - In `Payloads` tab, select:
    - `Payload Type: Extension-generated`
    - `Payload Options [Extension-generated]` select `XSS Validator Payloads`
 - In `Options` tab, select:
    - de-select _"make unmodified baseline request"_
    - `Grep – Match section`, and enter the string expected.

## JMeter

### Set a replayed request

`Copy as cURL` from within Firefox Web Developer.

`/Tools/Import from cURL`
Test 1: 5000 requests

Set the `Thread Group`:
   Number of Threads (users): ${__P(threads,10)}
   Ramp-up period (seconds): ${__P(rampup,30)}
   Loop Count: Infinite

Right click on `Thread Group` and select `Add Think Time to Children`.

Select `HTTP Request` and set the `Use KeepAlive`.

Then adjust the `Think Time` as required.

Right click on `Thread Group` and select `Validate`.
