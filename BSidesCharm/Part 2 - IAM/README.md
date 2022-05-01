# Holistic AWS Cloud Security Design for Newcomers @ BSides Charm 2022

## Activity Two: IAM User Access and Policies

By the end of this activity, you should have familiarity with the following:

* Creating new IAM user accounts
* Creating and attaching IAM policies
* Understanding identity-based and resource-based IAM policies
* Understanding IAM policy conditions

## TIPS:
- We'll be working within the `member-test-acct` account, switching between two different users within that account. You can exit out of the Organizational management account.
- To switch between accounts, use different browsers or private browsing sessions to leave both up at the same time.
- To check which account and user you're logged into, look at the top righthand side of the screen in the console.
- Check and copy your AWS account ID by going into Account in the top right, under your username. This gives you information about your account, user, and the Organization. Copy down the Account ID, which you'll need later.


## Part A: Grant an IAM user access to upload to an S3 bucket

1. Create new IAM user
* Log/switch into the `member-test-acct` account. 
* Navigate to the IAM services and go to "users"
* Create a new user using the following information:
    * Name: `bsidescharm2022awstraininguser`
    * Set a password matching requirements, and save to use later
* Under AWS credential type, select "password" to allow login access to the AWS Console.
* Uncheck the "reset password" requirement
* Leave everything else as default, ignore warnings (practice only!!)

2. Create and apply an identity-based policy
* Remaining in IAM, go to "policies"
* Create a new policy, and switch to JSON view
* Paste in the contents of the [S3 identity-based policy here](s3-open-policy.json)
* Name the policy `s3-access-policy` and save

4. Grant S3 permissions to user
* In IAM, return to user view and select your new user, `bsidescharm2022awstraininguser`
* To attach `s3-access-policy` policy to the user, click "add permissons"
* Under "grant permissions", switch to "attach existing policies"
* Select `s3-access-policy`, review and save

5. Test: Upload files to S3 as the IAM user
* Open a new browser or private browsing session
* Sign into the AWS Console using the new IAM user, logging in as IAM user:
    * Account ID: copy from Account info in main window (see tips)
    * Username `bsidescharm2022awstraininguser` and password that you set
* Once signed in, navigate to the S3 service
* Note: S3 is a file storage service, where 'buckets' are file containers you can create structure within 
* Upload some files, making sure to include the file [user_data.xlsx](user_data.xlsx) (you'll need to download it)
* Confirm they've uploaded successfully


## Part B: Resource-based policies in S3

6. Create and apply a resource-based policy
* NOTE: An S3 "bucket policy" is a type of resource-based policy
* Back in the root user of `member-test-acct`, navigate to S3
* Go into the `bsidescharm2022awstrainingbucket-XYZ` bucket and select the "Permissions" tab
* In the "policy" section of Permissions, click Edit
* Paste in the contents of [S3 resource-based policy here](s3-bucket-policy.json)
* IMPORTANT: Update the *resource* portion with your unique bucket name/ARN on line 10
* Save/Apply

7. Test the resource-based policy
* Switch back to the `bsidescharm2022awstraininguser` and go to S3
* Try to upload more files to the bucket

***The upload to the bucket should fail!***

Briefly review the contents of [S3 resource-based policy here](s3-bucket-policy.json) to see why the upload failed

### You're done!


### BONUS: More fun with IAM

8. Bucket administration through IAM
* Try to modify the bucket policy while logged in as the IAM user `bsidescharm2022awstraininguser`. 
* Verify why this can’t be done by looking at the identity policy assigned to IAM user
* Modify the policy to allow the IAM user to change the bucket policy

9. Policy conditions
* Modify the existing bucket policy to remove the previous restriction, and only allow access by users with `upload-access` tag
* Reference: [Controlling access to and for IAM users and roles using tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_iam-tags.html)
* Test with existing IAM user -- this should fail!
* Add tag `upload-access` to `bsidescharm2022awstraininguser`
    * Which account do you need to be in for this to work?

10. Restrict IAM at the Organizational level
* At the Organization level, implement an SCP that prevents member accounts from creating new IAM user, and test. [HINT: Use this SCP policy](https://asecure.cloud/a/scp_deny_iam_user_creation/)
* NOTE: Apply SCPs to OUs under root, not to root itself, as a best practice!


## References:

[Policies and permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)
[Controlling access to and for IAM users and roles using tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_iam-tags.html)