## Summary

My team like visual query analyze tools like 
* https://explain.depesz.com
* http://tatiyants.com/pev

However I would prefer a tool we can run locally and at pgconf.eu last year there was a cool presentation regarding pev2 which you can deploy locally.

While pev2 is great this deployment is using docker image and I wanted an s3 based deployment as the hosting costs are very low and this is easy to distribute around the world i.e. via cloudfront.

## Setting up cloudfront distribution 

We've setup a prototype E3EGZABZ7I8T0L cloudfrount distribution-id.

todo: I will make some more notes on the initial cloudfront config.


## package.json

Here is my package.json with my cloudfrount --distribution-id E3EGZABZ7I8T0L hard coded.

```
~/projects/pev2 $ cat package.json
{
  "name": "pev2",
  "version": "0.1.20",
  "description": "A VueJS component to show a graphical vizualization of a PostgreSQL execution plan.",
  "main": "index.html",
  "repository": "git@github.com:dgapitts/pev2.git",
  "author": "Pierre GIRAUD <pierre.giraud@gmail.com>",
  "license": "MIT",
  "private": true,
  "scripts": {
    "deploy": "aws s3 sync ./public s3://pev2.ebabel.eu --acl public-read --exclude '*.DS_Store*'",
    "invalidate": "aws cloudfront create-invalidation --distribution-id E3EGZABZ7I8T0L --paths \"/*\""
  }
}
```

## Key aws s3 (sync) and aws cloudfront (create-invalidation) commands

### aws s3 sync
```
~/projects/pev2 $ aws s3 sync ./public s3://bbdba-pev2-s3 --acl public-read --exclude '*.DS_Store*' --profile pd-playground-dev
upload: public/index.html to s3://bbdba-pev2-s3/index.html
upload: public/favicon.ico to s3://bbdba-pev2-s3/favicon.ico
upload: public/samples/plan_2.sql to s3://bbdba-pev2-s3/samples/plan_2.sql
upload: public/samples/plan_3.json to s3://bbdba-pev2-s3/samples/plan_3.json
upload: public/samples/plan_3.sql to s3://bbdba-pev2-s3/samples/plan_3.sql
upload: public/samples/plan_5.json to s3://bbdba-pev2-s3/samples/plan_5.json
upload: public/samples/plan_1.txt to s3://bbdba-pev2-s3/samples/plan_1.txt
upload: public/js/chunk-vendors.6a27db18.js to s3://bbdba-pev2-s3/js/chunk-vendors.6a27db18.js
upload: public/js/index.1a948696.js to s3://bbdba-pev2-s3/js/index.1a948696.js
upload: public/samples/plan_5.sql to s3://bbdba-pev2-s3/samples/plan_5.sql
upload: public/samples/plan_2.json to s3://bbdba-pev2-s3/samples/plan_2.json
upload: public/samples/plan_6.txt to s3://bbdba-pev2-s3/samples/plan_6.txt
upload: public/samples/plan_parallel.json to s3://bbdba-pev2-s3/samples/plan_parallel.json
upload: public/samples/plan_4.json to s3://bbdba-pev2-s3/samples/plan_4.json
upload: public/samples/plan_7.txt to s3://bbdba-pev2-s3/samples/plan_7.txt
upload: public/samples/plan_parallel2.sql to s3://bbdba-pev2-s3/samples/plan_parallel2.sql
upload: public/samples/plan_parallel2.txt to s3://bbdba-pev2-s3/samples/plan_parallel2.txt
upload: public/samples/plan_trigger.json to s3://bbdba-pev2-s3/samples/plan_trigger.json
upload: public/samples/plan_trigger.sql to s3://bbdba-pev2-s3/samples/plan_trigger.sql
upload: public/samples/plan_trigger.txt to s3://bbdba-pev2-s3/samples/plan_trigger.txt
upload: public/samples/plan_trigger_2.sql to s3://bbdba-pev2-s3/samples/plan_trigger_2.sql
upload: public/samples/plan_1.json to s3://bbdba-pev2-s3/samples/plan_1.json
upload: public/samples/plan_8.json to s3://bbdba-pev2-s3/samples/plan_8.json
upload: public/samples/plan_1.sql to s3://bbdba-pev2-s3/samples/plan_1.sql
upload: public/js/index.1a948696.js.map to s3://bbdba-pev2-s3/js/index.1a948696.js.map
upload: public/logo_pev2.svg to s3://bbdba-pev2-s3/logo_pev2.svg
upload: public/js/chunk-vendors.6a27db18.js.map to s3://bbdba-pev2-s3/js/chunk-vendors.6a27db18.js.map
```
### aws cloudfront create-invalidation
```
~/projects/pev2 $ aws cloudfront create-invalidation --distribution-id E387Q1XT4TGQ05 --paths "/*" --profile pd-playground-dev

SSL validation failed for https://cloudfront.amazonaws.com/2018-11-05/distribution/E387Q1XT4TGQ05/invalidation [SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: self signed certificate in certificate chain (_ssl.c:1056)
```


### Setup 01 - s3 static webhosting - pev2.ebabel.eu - with PublicRead

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicRead",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::pev2.ebabel.eu/*"
        }
    ]
}
```



### Background - local testing

You can test locally by
```
~/projects/pev2 $ cd public/
~/projects/pev2/public $ ls
favicon.ico	index.html	js		logo_pev2.svg	samples
~/projects/pev2/public $ open index.html
```

But you'll need to start a http-server in the current (.) directory
```
~/projects/pev2/public $ which http-server
/usr/local/bin/http-server
~/projects/pev2/public $ http-server .
Starting up http-server, serving .
Available on:
  http://127.0.0.1:8080
  http://192.168.178.16:8080
  http://10.103.126.215:8080
Hit CTRL-C to stop the server
[2020-03-08T10:05:37.652Z] "GET /" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36"
(node:21190) [DEP0066] DeprecationWarning: OutgoingMessage.prototype._headers is deprecated
[2020-03-08T10:05:38.056Z] "GET /js/chunk-vendors.6a27db18.js" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36"
[2020-03-08T10:05:38.058Z] "GET /js/index.1a948696.js" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36"
[2020-03-08T10:05:38.783Z] "GET /favicon.ico" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36"
[2020-03-08T10:05:40.952Z] "GET /samples/plan_3.json" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36"
[2020-03-08T10:05:40.952Z] "GET /samples/plan_3.sql" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36"
^Chttp-server stopped.
```
you don't have to use 8080 i.e. to override the default port  
```
~/projects/pev2/public $ http-server -p 8084 .
Starting up http-server, serving .
Available on:
  http://127.0.0.1:8084
  http://192.168.178.16:8084
  http://10.103.126.215:8084
Hit CTRL-C to stop the server
^Chttp-server stopped.
```


