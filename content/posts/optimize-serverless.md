---
title: "Serverless optimization"
date: 2023-11-21T11:30:03+00:00
tags: ["serverless", "aws", "performance", "lambda"]
author: "Kevin L"
showToc: true
TocOpen: false
draft: false

comments: false
description: "Optimizing your AWS Lambda function memory settings can improve performance and reduce costs. Use the appropriate amount of memory for your function based on its requirements, and consider using a smaller amount for functions that don't need it. Additionally, use AWS Lambda power tuning to optimize your function's performance, and eliminate unnecessary resources and costs by removing unused functions and optimizing your stack."
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
--- 

## Optimize code

Use packers such as esbuild or webpack, in several tests performed there was evidence of improvement using esbuild for compilation time and size here is a reference. Both have offline support.


* [serverless-esbuild](https://www.npmjs.com/package/serverless-esbuild)

* [serverless-webpack](https://www.serverless.com/plugins/serverless-webpack)

## Use of Graviton2

### Workloads

* Multithreading or performing many I/O operations

* Machine learning inference based on the CPUs

* Video encoding

* Gaming


### Cost
* 20% cheaper, including provisioned concurrency

* Supported on Compute Savings Plans

### Configuration in Serverless Framework
You must add the following line `architecture: 'arm64'`.
```
# serverless.yml
provider:
    architecture: 'arm64'
```
### Using AWS Lambda Power Tunning
Measuring cost efficient memory size is one of the very easy and useful optimization practices.

By default using frameworks like serverless framework or SAM you can granularly define the amount of memory to each lambda but it is good how much memory and vCPU (Virtual CPUs) provide better response times and with it better.
We have a lambda that performs a read operation on a DB and brings 50 records for which we have a comparative table with the different memory configurations, response times and cost.

![1_step](/1_step_power_tuning.png "Step 1")

A very important fact is that the 128 MB configuration has a similar cost to the 1536 MB option but the time difference is significant 10 to 1.

More memory does not always mean higher costs

![2_step](/2_step_power_tuning.png "Step 2")

### Usage guide

Choose the option that involves the least effort option #1. Open the following [link](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-1:451282441545:applications~aws-lambda-power-tuning) while logged into the AWS account.

In the following template you can configure the range of RAM memory allowed for the evaluation
![3_step](/3_step_power_tuning.png "Step 3")
Una vez termine el proceso de creación se debe dar clic en el link powerTuningStateMachine

![4_step](/4_step_power_tuning.png "Step 4")
![5_step](/5_step_power_tuning.png "Step 5")

Enter the json with the information of the lambda to be tested

```
{
        "lambdaARN": "your-lambda-function-arn",
        "powerValues": [128, 256, 512, 1024, 2048, 3008],
        "num": 10,
        "payload": "{}",
        "parallelInvocation": true,
        "strategy": "cost"
}
```

Execution begins:

![5_step](/5_step_power_tuning.png "Step 5")

![6_step](/6_step_power_tuning.png "Step 6")

Tenemos el siguiente resultado el cual nos brinda información muy útil para realizar ajustes en nuestra lambda [link](https://lambda-power-tuning.show/#gAAAAQACAAQACMAL;AACJQ6uqGUMAAIhBAAA4QVVVPUEAAEBB;ERP6NIWNDDXGP/gzEzwvNBM8rzQesAA1)

![result_power_tuning](/result_power_tuning.png "result_power_tuning")

## Serverless optimizations 

Once finished, the stack must be deleted. To do so, go to CloudFormation > Stacks, select the power Tunings stack and delete it, thus deleting all the resources created, to avoid incurring additional costs.

## References:

* https://docs.aws.amazon.com/lambda/latest/operatorguide/perf-optimize.html

* https://docs.aws.amazon.com/lambda/latest/operatorguide/execution-environments.html

* https://docs.aws.amazon.com/lambda/latest/operatorguide/computing-power.html

* https://docs.aws.amazon.com/lambda/latest/operatorguide/static-initialization.html

* https://docs.aws.amazon.com/lambda/latest/operatorguide/architecture-best-practice.html

* https://docs.aws.amazon.com/lambda/latest/operatorguide/profile-functions.html

* https://github.com/alexcasalboni/aws-lambda-power-tuning

 

