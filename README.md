## Summary

My team like visual query analyze tools like 
* https://explain.depesz.com
* http://tatiyants.com/pev

However I would prefer a tool we can run locally and at pgconf.eu last year there was a cool presentation regarding pev2 which you can deploy locally.

While pev2 is great the deployment was to a docker image and I wanted an s3 based deployment as the hosting costs are very low and this is easy to distribute around the world i.e. via cloudfront.

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

