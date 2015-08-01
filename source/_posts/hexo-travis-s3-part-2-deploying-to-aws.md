title: 'Hexo, Travis, S3 - Part 2: Deploying to AWS'
tags:
  - Hexo
  - AWS
  - Amazon S3
date: 2015-08-01 20:19:50
---


Now that our Hexo site is up and running locally, let's see how to move it into AWS using Hexo's build-in `deploy` command.

<!-- more -->

## Amazon S3

Amazon S3 is the static file hosting part of AWS - it just serves the files we store there wihtout the need of running a server (by ourselves). Thus it reduces the overall cost of running a static website a lot, since it does not need any expensive computing power compared to e.g. Amazon EC2.

### Create S3 Bucket

First of all, we head over to [Amazon S3](https://console.aws.amazon.com/s3/home) to create the S3 bucket for our website. In this case we name the bucket just as the domain itself.

<img
  src="/{{slug}}/s3_create-bucket.min.png"
  width="741"
  alt="Amazon S3: Create bucket"
  title="Amazon S3: Create bucket">

The name doesn't really matter, it just has to be an unique name for the whole region (e.g. **us-west-2**), since all buckets will receive a S3 endpoint which looks like this:

{% blockquote %}
**inject.coffee**.s3-website-**us-west-2**.amazonaws.com
{% endblockquote %}

In this case, **inject.coffee** is our bucket's name and **us-west-2** is the buckets AWS region.

After the bucket is created, we enter it, select the **Properties** tab on the top right and configure it to be able to serve your static website.

#### Permissions

<img
  src="/{{slug}}/s3_permissions.min.png"
  width="624.5"
  alt="Amazon S3: Permissions"
  title="Amazon S3: Permissions">

In the Permissions section, click on **Add bucket policy** and insert the following snippet to make the content available public. Make sure to replace `inject.coffee` with the name you assigned to your bucket previously.

{% codeblock lang:json Bucket Policy %}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadForGetBucketObjects",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::inject.coffee/*"
        }
    ]
}
{% endcodeblock %}

#### Static Web Hosting

Since we're using a static site generator, this is exactly what we're looking for. Therefor we **enable website hosting** and set the **index document** to `index.html`:

<img
  src="/{{slug}}/s3_static-website-hosting.min.png"
  width="549"
  alt="Amazon S3: Static Website Hosting"
  title="Amazon S3: Static Website Hosting">

The **endpoint** mentioned should lead you to an AWS 404 error page for now, since there's no content yet inside our new bucket if we get a 403, we should check our bucket policy once more.

### Manually Deploying to S3

Before we'll set up Travis CI to deploy our website automatically, we'll first take a look at how to do this manually - in case we do not want to host our sources at GitHub.

The Hexo installation comes with some deployment plugins by default, unfortunatly S3 isn't one of them, so we have to install it first:

{% codeblock lang:bash Install hexo-deployer-s3 %}
npm install --save hexo-deployer-s3
{% endcodeblock %}

Now we should add the following `deploy` section to our `_config.yml`:

{% codeblock lang:yaml _config.yml deploy section %}
...
deploy:
  type: s3
  bucket: <AWS bucket name>
  aws_key: <AWS id key>
  aws_secret: <AWS secret key>
  region: <AWS bucket region>
...
{% endcodeblock %}

The bucket name and region seem to be quite easy to get, but what about the **AWS key** and **AWS secret**? They are used as credentials to access our bucket.

Let's head over to the [IAM](https://console.aws.amazon.com/iam/home), the Amazon **I**dentity and **A**ccess **M**anagement to create them.

#### Creating a User

We want to use a dedicated user for your Hexo website, so a click on **New User** takes us to the user creation page, where we give it some meaningfull name. We will then be prompted with the new users initially generated **Accesss Key ID** and **Secret Access Key**. Those are the values we need to enter in the `_config.yml` for beeing able to deploy to Amazon S3.

We can also create a new key pair if we forgot to save the one created initially. Just head over into the users settings and hit **Create Access Key**.

<img
  src="/{{slug}}/iam_create-access-key-1.min.png"
  width="525"
  alt="Amazon IAM: Create Access Key"
  title="Amazon IAM: Create Access Key">

We'll then be prompted with the newly created key pair.  
**Important:** Make sure to delete the old ones you're not using anymore.

<img
  src="/{{slug}}/iam_create-access-key-2.min.png"
  width="612"
  alt="Amazon IAM: Create Access Key"
  title="Amazon IAM: Create Access Key">

**Important:** If you plan on hosting your repository on a public space like [GitHub](http://github.com/), don't store or commit your credentials in the `_config.yml` file itself, but in local environment variables called `AWS_KEY` and `AWS_SECRET` and remove the corresponding lines from your `_config.yml`.

#### Creating a Policy

The user we just created needs the permission to write files to our S3 bucket. AWS already has a default policy called **AmazonS3FullAccess** which - as its name implies - gives the user full access to all of our S3 buckets. However, we just want to allow our new user to access the bucket we created.

In the [IAM](https://console.aws.amazon.com/iam/home) head over to the **Policies** section and hit **Create Policy** using the **Policy Generator**. The service we're looking for is `Amazon S3` and it should allow all actions.

The **ARN** for your bucket should look like this - again, remember to replace `inject.coffee` with your own bucket's name:

{% codeblock Amazon Resource Name (ARN) %}
arn:aws:s3:::inject.coffee/*
{% endcodeblock %}

Finally, the new statement should look like this:

<img
  src="/{{slug}}/iam_create_policy.min.png"
  width="525"
  alt="Amazon IAM: Create Policy"
  title="Amazon IAM: Create Policy">

Hit **Add Statement**, go to the **Next Step** and give our policy a meaningfull name before creating it.

Now inside of the created policy, we attach an entity: our previoulsy created user - in this case _hexo-s3_.

<img
  src="/{{slug}}/iam_attach-policy-user.min.png"
  width="765"
  alt="Amazon IAM: Attach Users to Policy"
  title="Amazon IAM: Attach Users to Policy">

This was the last piece of the users and permissions puzzle. We should now be able to deploy our Hexo website by by running the following command:

{% codeblock lang:bash Deploy Hexo website%}
hexo deploy
{% endcodeblock %}

It should now be available opening the **S3 endpoint** mentioned above.

#### Conclusion

We got our site up and running on Amazon S3 and are able to deploy it using the `hexo deploy` command. This is perfeclty fine if you want to manually deploy our site - but might interfere with collaboration and we never know which version is live, since it deploys from the latest local source set.

What we might want to consider is implementing continuous integration, so our website gets tested and deployed as soon as we push new changes to a target branch. This way we don't have care about deploying ourselves but only work inside our git repository. The _master_ branch (or whichever we chose) will represent the source set for the version that's delivered by our S3 bucket.

In the next part of this series, we'll set up the continuous integration using Travis CI and GitHub.

Also check out {% post_link connect-route-53-domain-to-amazon-s3-bucket %} to see, how to connect a registered domain to our S3 bucket.