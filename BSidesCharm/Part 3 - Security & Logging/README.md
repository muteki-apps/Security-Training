# Holistic AWS Cloud Security Design for Newcomers @ BSides Charm 2022

## Activity Three: Security & Logging

NOTE: In the interests of time, we're using the `member-test-acct` for this activity to avoid some of the complexities of configuring them for the whole Organization.

## Part A: Enable security services

1. Enable Macie
* Log into the `member-test-acct` account's root user
* Navigate to Macie -> "Get started"
* Select the "Analyze buckets" option
* Hit next, and leave Step 2 as is
* In Step 3, select "one time job"
* Leave the rest of the steps as is, give the job a name
* Save and run, we'll come back to it later

2. Enable Security Hub
* Navigate to Security Hub and click "Go to Security Hub"
* Check off all the options under "security standards"
* Save, we'll come back to it later

3. Enable and set rules in AWS Config
* Navigate to Config and enable it 
* Leave all default options
* Under AWS managed rules, search for and enable anything for "s3-"
* NOTE: While Config is not technically free, it charges a miniscule amount by rule, and I've never been charged for enabling a small selection of rules.


## Part B: Examining and understanding findings

4. View noncompliant rules in AWS Config
* In AWS Config, navigate to Rules
* Filter by status "noncompliant"
* Specifically, look at S3, and for the following rules:
    * [s3-bucket-default-lock-enabled](https://docs.aws.amazon.com/config/latest/developerguide/s3-bucket-default-lock-enabled.html) - This requirement would normally be used for compliance purposes. Enabling Object Lock on S3 buckets prevents the objects within from being deleted or overwritten.
    * [s3-bucket-level-public-access-prohibited](https://docs.aws.amazon.com/config/latest/developerguide/s3-bucket-level-public-access-prohibited.html) - Public access to bucket is private by default. Implementing S3 Block Public Access on the AWS account or bucket level enforces this configuration on all S3 resources within, regardless of how the resources are created.

5. View Amazon Macie findings
* Navigate to Macie -> Findings -> by bucket
* Clicking on your S3 bucket, you should see one finding
* Take a look at the files in the bucket, you should be able to quickly find which one was flagged!

6. View Security Hub findings
* Navigate to Security Hub -> Findings
* NOTE: These findings only apply to this account, not the organization
* Sort by Severity to see Critical/High findings
* Looking through the first page or two of results, you'll notice there's quite a bit of crossover with the AWS Config results for S3!


## Part C: Organizational CloudTrail
7. Create an organization trail - [see documentation here](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/creating-an-organizational-trail-in-the-console.html):
* Log in as the Organization management account root user
* Navigate to CloudTrail and expand the menu on the lefthand side (the three bars)
* Click on "trail" and select "Create trail" with the following settings:
    * Name: `organization-trail`
    * Select the checkbox to "enable for all accounts in my organization"
    * Under storage location, make sure the option "Create new S3 bucket" is selected. You can leave the location as default.
    * Deselect "Log file SSE-KMS encryption" -- in the interests of time, we'll skip this
    * Hit next, and on the next page, make sure the only type logged is "Management Events"
    * Review and create the trail
    * Open the new S3 bucket in another tab

8. View exported logs in S3
* Wait ~5 minutes and data will start showing up
* Drill down and take a look at the logs
* ...and then remember that they're intended to go into a SIEM where they can be parsed and searched efficiently!

[Creating a trail for your organization in the console](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/creating-an-organizational-trail-in-the-console.html)

