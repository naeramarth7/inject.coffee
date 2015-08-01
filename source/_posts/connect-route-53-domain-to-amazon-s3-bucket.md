title: Connect Route 53 Domain to Amazon S3 Bucket
tags:
  - AWS
  - Amazon S3
  - Amazon Route 53
date: 2015-08-01 20:20:03
---


In {% post_link hexo-travis-s3-part-2-deploying-to-aws %} we got our site deployed to S3, so it's available to everyone - still **inject.coffee.s3-website-us-west-2.amazonaws.com** seems to be an odd domain name and we got our own domain **inject.coffee**, we want to connect to the bucket.

<!-- more -->

## Route 53

With a domain registered at Amazon Route 53, we have to set up a hosted zone for this Domain. Head over to [Route 53](https://console.aws.amazon.com/route53/home) » **Hosted Zones** and create a new one.

<img
  src="/{{slug}}/route-53_create-hosted-zone.png"
  width="469" height="268"
  alt="Route 53: Create Hosted Zone"
  title="Route 53: Create Hosted Zone">

The *NS* and *SOA* records should already be set up for you. What we need now, is at least 1 domain pointing to our S3 bucket.

Hit **Create Record Set**, set the type to `A`, make it an `ALIAS` and select our S3 bucket as the alias target:

<img
  src="/{{slug}}/route-53_add-s3-alias-1.png"
  width="413" height="355"
  alt="Route 53: Create S3 Alias"
  title="Route 53: Create S3 Alias">

This makes our S3 bucket available at **inject.coffee**, but we also want it to be available on the **www** subdomain, so we set up another **A** record pointing to the first one:

<img
  src="/{{slug}}/route-53_add-s3-alias-2.png"
  width="420" height="352"
  alt="Route 53: Create another S3 Alias"
  title="Route 53: Create another S3 Alias">

### Connecting an non-Route 53 registered Domain

If we have our domain registered with a different registrar than Amazon, we have to change the at our registrar **nameservers** to match the ones given by our Hosted Zones **NS** record.

<img
  src="/{{slug}}/route-53_hosted-zone-nameservers.png"
  width="611" height="183"
  alt="Route 53: Hosted Zone Nameservers"
  title="Route 53: Hosted Zone Nameservers">
