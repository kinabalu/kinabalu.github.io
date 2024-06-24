---
layout: post
title:  "Build a Cheap Docker Swarm Cluster using Terraform and AWS"
date:   2017-12-25 12:00:00
author:
  display_name: Ryan Sadler
  login: sadler
  email: sadler@mysticcoders.com
  url: https://www.mysticcoders.com
comments: true
---
![screenshot](/images/2017-12-27-docker-horizontal-large.png)

I set out to build  a super cheap Swarm Cluster for a personal project.  With a little help from terraform you can be up and running with a multi-node swarm cluster in a matter of minutes. We aren’t going to get into what you can do with your cluster yet, that will be a future article.  We’re just going to look at prices and see how cheap we can do this.

<!--more-->

## Overview

Why use Swarm, when you could just spin up some cheap servers?  The nice thing about swarm is you get a lot of cluster functionality out of the box, not to mention that it will run your docker containers. Things like Service Discovery and Rolling updates are easy with Swarm across the entire cluster.  If you’re building an app that needs to be updated periodically, this is a huge help.  [See more about Swarm](https://docs.docker.com/engine/swarm/)

In our comparison we are going to look at two scenarios:

- Self hosted commodity cluster
- AWS Hosted cloud cluster

When self-hosting a cluster, it’s not just about the cost of hardware, but also about the electricity used, which as we will find out can be significant.

You could host your cluster using any of major cloud players out there, such as [Microsoft Azure](https://azure.microsoft.com/), [Google Cloud](https://cloud.google.com/compute/), or [IBM BlueMix](https://www.ibm.com/cloud/).  Each has different advantages, with comparable pricing.   But for this article I’m just going to focus on [Amazon Web Services](https://aws.amazon.com/).  Leave a comment below if you'd like us to compare cloud providers in a future article.

## Self Hosting with Commodity Hardware

So let’s start with our baseline.  What would it cost to get up and running with a 3 node cluster using generally available?  Just using parts from NewEgg, you can build a cheap commodity system for about $500.  Circa 2017, this would get you 4GB of RAM, and a 2.9GHz Dual Core Intel processor.  Most of that cost is overhead, such as power supply, case, motherboard, storage space, networking and cooling.  To make a good comparison with the cloud, let’s amortize the cost of the hardware over a 2 year period, which gives us an amortized rate of $20.83/month.

But we also need to consider the cost of electricity.  Let’s assume a $0.10/kWh rate, which is quite inexpensive, but also common across large parts of North America.  Assuming a 300W constant power draw, we are looking at $21.60/month, just for electricity.  Our cost of electricity is about the same amount as our amortized hardware costs!  Not really what I had in mind for cheap, but this is just our baseline.

Ignoring a large number of other considerable factors, this gives us an estimated monthly cost of operation of $42.43.  This is the amortized cost of the hardware, plus the cost of electricity.

There are a number of alternatives we could explore on the self hosted side that could reduce our costs.  For example, we could explore the use of low power ARM processors, which are both inexpensive as well as efficient.  Depending on the intended purpose, this may be a valid option.  But for the purpose of comparison, we will stick with an Intel processor.

To summarize:

| Description | Cost |
| ----------- | ---- |
| Monthly Amortized Hardware Costs | $20.83 |
| Monthly Electricity Usage        | $21.60 |
| Total                            | $42.43 |

## Cloud Hosting

With [AWS](https://aws.amazon.com/), there are a large number of server types to choose from.  A comparable EC2 type to our self-hosted solution would be the t2.medium instance.  [Here’s a great site that compares the various options and prices.](https://www.ec2instances.info/)  If we use on-demand pricing, we will get higher availability, but we are paying full price.  We have the option to go with spot pricing and we are going to sacrifice availability for some savings.  Here’s the comparison for the t2.medium, with current 2017 pricing.


| Lifecycle | Total Hourly Cost | Total Monthly Cost |
| --------- | ----------------- | ------------ |
| On-Demand | $0.0464 | $33.41 |
| Spot | $0.0185 | $13.32 |

Even with full availability, we are still getting cheaper prices than our self-build, but a significant savings when we look at spot pricing.

## Setting it all up

Another major factor we need to discuss is how long it takes to set this stuff up.  We’ve done a simple comparison which makes a compelling case for the cloud.  But where the cloud really shines is in the time savings setting the cluster up.

It look me about 2 hours to build each of my self-hosted boxes, that’s after sourcing all of the parts.  Let’s say a total of 6 hours, assuming everything goes well.

How long does it take on the cloud?  With a little help from [Terraform](https://www.terraform.io/), we can have that same setup in about 5 minutes.

So let’s factor that in as well.  How you value your time is certainly up to you, but to try to keep costs low, let’s assume a $10 minimum hourly wage for the setup time.  That’s an extra $60 of upfront setup time.  Over the course of the lifetime of the servers however, that’s not really that big of a deal, but it does add up.  We could also talk about the cost of maintenance if something goes wrong.  If you’re self hosting, you’re on the hook to fix stuff, unless you need an expensive paper weight.   In the Cloud, in a matter of minutes, you can swap out a bad instance with a fresh new one.

## Show me the code

If you would like to spin something up quickly using spot instances, go ahead and checkout the code.

[https://github.com/rrx/aws-docker-swarm](https://github.com/rrx/aws-docker-swarm)

The code is a fork of some well written terraform by another developer.  It was so well done, that I didn’t think it was worth it to reinvent the wheel.  I just modified it to use spot instances.

Here are some quick instructions to get you up and running.

- Create a keypair in AWS (if you haven’t already). [You can find instructions on how to do this here.](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)
- Setup your AWS credentials file
- [Install Terraform.](https://www.terraform.io/intro/getting-started/install.html) Download the appropriate files for your OS and make that file available in your path.
- ```git clone https://github.com/rrx/aws-docker-swarm```
- ```cd aws-docker-swarm```
- Update terraform.tfvars with appropriate values
- ```make plan```
- ```make apply```
- View your instances: ```make swarm-instances```
- SSH into the swarm master
- Run benchmarks
- Take down your cluster. ```make destroy```

## Conclusion

So the bottom line is the cloud is cheap and easy.  And you only pay for what you use.  It’s a new way of thinking about things and it takes some getting used to.  Years ago we didn’t have a choice, we had to buy and provision our servers.  It was expensive to build and maintain.  This new era of cloud computing is cutting those costs down by an order of magnitude and fueling a huge portion of today’s technology innovations.

Until next time.
