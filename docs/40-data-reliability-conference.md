## Date May 25, 2022

### Value of data reliability 

* Leads to bad decision
* Trust
* Waste of time
* Biases leading to reputation loss
* Loss of revenue 
* Increased cost

### Steps to solve

* Automate as much as possible 
* Monitor as much as possible 
* Control releases - version control, code review and environments
* Design simplicity

### Pipeline promotion without the commotion

* Great expectations python library helps to set up tests in data pipeline
* dbt helps create pipeline and put documentaton alongside
* dbt allows for tests in pipelines, defined as yaml
* dbt has library `dbt_expectation` for tests
* Connects with github for version control

### What do Data scientists look for in data

* discoverable
* relevant
* usable
* accurate
* timely
* fit for purpose

### Workflow orchestration to reduce negative engineering

* If the data team spends 80% of time in negative engineering, reducing my 25% with double productivity.
* Prefect is open source, python based workflow orchestration
* List of negative engineering tasks

### Controlled release of data pipeline 

* Create notifications with Slack and GitHub actions 
* Implementation will be automatic 
* After pull request, docker images are created automatically and pussed to image registry
* Kubernetes will take the latest container

### Objective of data observability

* Constantly monitor data, notify data teams so that data teams knows before the consumers of data
* Demo platform: https://drecon-workshop.bigeye.com/#0
* Observability can be set up using SQL queries at a basic level

### Tools to check

* dbt - SQL workflows
* bigeye - data observability solution
* great expectation - data testing library in python
* MLFlow - hosting ML models
* ArgoCD - CI/CD for kubernetes
* Prefect - workflow orchestration tool, gcp has Workflow
* GitFlow

### Data outage 

It is very different from software outage, because the spectrum is large -
1. data is old
1. data is missing
1. its wrong
1. the database is missing

Easy to estimte cost of ourage in e-Commerce. For data, the cost can be low if no one is looking at the data, or very high cost when the CEO makes a wrog decision.

We should be truth seeking, but risk taking. While we should be making investments to improve data reliability, we should not wait to make bold decisions until we have the best data.

### Data reliability

What it is? 

### Contacts 

* [Christianna Clark](https://www.linkedin.com/in/christianna-clark-391aa0136/)
* [Shailvi Wakhlu](https://www.linkedin.com/in/shailviw/)
* [Scott Shi](https://www.linkedin.com/in/scottrshi/) 
Handled 5B tickets when working for Uber.
* [Randy Pitcher](https://www.linkedin.com/in/randypitcherii/)
dbt developer advocate. Very good overview of dbt.
* [Egor Gryaznov](https://www.linkedin.com/in/egorgryaznov/)
BigEye founder. 
* [Pavani Rangavajhula](https://www.linkedin.com/in/pavani-rangavajhula-189a4aa9/)
Good overview of CI/CD of data pipelines.
* [Glen-Erik Cortes](https://www.linkedin.com/in/glenerik/)
Started with MBA and now working as ML Engineering Manager. Talks about ML Ops.
* [Jerry Shen](https://www.linkedin.com/in/yizhou-jerry-shen-8a653a25/)
Very articulate data scientist. Works at OpenSea.
* [Miriah Peterson](https://www.linkedin.com/in/miriah-peterson-35649b5b/)

