# Level 200: Cost and Usage Analysis
http://wellarchitectedlabs.com 

## Introduction
 This hands-on lab will guide you through the steps to setup a platform to analyze your cost and usage reports. The skills you learn will help you perform analysis on your cost and usage, in alignment with the AWS Well-Architected Framework.

![Images/AWSCostReadme.png](Images/AWSCostReadme.png)

## Goals
- Setup an analysis platform for your cost and usage data
- Perform basic analysis of your cost and usage


## Prerequisites
- A master AWS Account
- A linked AWS Account (preferred, not mandatory)
- Have your Cost and Usage Report (CUR) enabled [as per 100_1_Account Setup](../100_1_AWS_Account_Setup/README.md)
- [Cost_and_Usage_Governance](../200_2_Cost_and_Usage_Governance/README.md) has been completed
- Have usage that is tagged (preferred, not mandatory)


## Permissions required
- Log in as the Cost Optimization team, created in [Cost_and_Usage_Governance](../200_2_Cost_and_Usage_Governance/README.md)
- NOTE: There may be permission error messages during the lab, as the console may require additional privileges. These errors will not impact the lab, and we follow security best practices by implementing the minimum set of privileges required.


<BR>

## [Start the Lab!](Lab_Guide.md)

<BR>
<BR> 

## Best Practice Checklist 
- [ ] Load your CUR files into Athena for analysis
- [ ] Create a separate table to contain only a specific member accounts usage
- [ ] Analyze your cost and usage by executing queries against your CUR files

*** 

## License
Licensed under the Apache 2.0 and MITnoAttr License.

Copyright 2018 Amazon.com, Inc. or its affiliates. All Rights Reserved.

Licensed under the Apache License, Version 2.0 (the "License"). You may not use this file except in compliance with the License. A copy of the License is located at

http://aws.amazon.com/apache2.0/

or in the "license" file accompanying this file. This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
