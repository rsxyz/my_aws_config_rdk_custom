## custom config rule

https://rdk.readthedocs.io/en

https://github.com/awslabs/aws-config-rdk

https://github.com/awslabs/aws-config-rules

```
$ python3 -m venv .venv
$ source .venv/bin/activate
$ pip install rdk
$ rdk -h
$ mkdir config
$ cd config/
$ rdk init
Running init!
Found Config Recorder: default
Found Config Role: arn:aws:iam::xxxxxxxxxxxx:role/aws-service-role/config.amazonaws.com/AWSServiceRoleForConfig
Found Bucket:
Config Service is ON
Config setup complete.
Creating Code bucket config-rule-code-bucket-xxxxxxxxxxxx-us-east-1
$

```

# Rule to t2-micro desired instance type

```
$ rdk sample-ci AWS::EC2::Instance
$ rdk create ec2_desired_instance_type --runtime python3.7 --resource-types AWS::EC2::Instance --input-parameters '{"desiredInstanceType":"t2.micro"}'

edit: ec2_desired_instance_type.py file and add code

    ###############################
    # Add your custom logic here. #
    ###############################
    if configuration_item['resourceType'] != 'AWS::EC2::Instance'
        return 'NOT_APPLICABLE'

    if configuration_item['configuration']['instanceType'] == valid_rule_parameters['desiredInstanceType']:
        return 'COMPLIANT'

    return 'NON_COMPLIANT'
```

# run unit test

```
$ rdk test-local ec2_desired_instance_type
Running local test!
Testing ec2_desired_instance_type
Looking for tests in /Users/amznravs/Desktop/AOD-Security/demos/config/ec2_desired_instance_type
ec2_desired_instance_type_test.py
Debug!
<unittest.suite.TestSuite tests=[<unittest.suite.TestSuite tests=[<ec2_desired_instance_type_test.ComplianceTest testMethod=test_sample>]>, <unittest.suite.TestSuite tests=[<ec2_desired_instance_type_test.TestStsErrors testMethod=test_sts_access_denied>, <ec2_desired_instance_type_test.TestStsErrors testMethod=test_sts_unknown_error>]>]>
test_sample (ec2_desired_instance_type_test.ComplianceTest) ... ok
test_sts_access_denied (ec2_desired_instance_type_test.TestStsErrors) ... ok
test_sts_unknown_error (ec2_desired_instance_type_test.TestStsErrors) ... ok

---

Ran 3 tests in 0.002s

OK
<unittest.runner.TextTestResult run=3 errors=0 failures=0>
```

# change compliant rule eval frequency

```
$ rdk modify ec2_desired_instance_type --maximum-frequency One_Hour
Running modify!
Modified Rule 'ec2_desired_instance_type'. Use the `deploy` command to push your changes to AWS.
```

# deploy rule

```
$ rdk deploy ec2_desired_instance_type
Running deploy!
Found Custom Rule.
Zipping ec2_desired_instance_type
Uploading ec2_desired_instance_type
Upload complete.
Creating CloudFormation Stack for ec2_desired_instance_type
Waiting for CloudFormation stack operation to complete...
...
CloudFormation stack operation complete.
Config deploy complete.
```
