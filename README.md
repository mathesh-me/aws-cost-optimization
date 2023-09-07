# Efficient AWS Cost Management through Stale Resource Detection

## Problem :
Sometimes, developers create EC2 instances with volumes attached to them by default. For backup purposes, these developers also create snapshots. However, when they no longer need the EC2 instance and decide to terminate it, they sometimes forget to delete the snapshots created for backup. As a result, they continue to incur costs for these unused snapshots, even though they are not actively using them.

## Solution :
We're using AWS to save money on storage costs. We made a Smart Lambda function that looks at our snapshots and our EC2 instances. If Lambda finds a snapshot that isn't connected to any active EC2 instances, it deletes it to save us money. This helps us keep our AWS costs down.

## Note :
There are many similar problems like this. For instance, we might attach an Elastic IP to our EC2 instance but forget to delete the Elastic IP after terminating the EC2 instance. In such a case, the Elastic IP continues to incur costs for us.

## Steps :
### Step 1:
1. Log into your AWS Console.<br>
2. Navigate to the EC2 Console.<br>
3. In the Instances section, select 'Instances,' and then click on 'Launch Instance'.<br>

![EC2](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/00d34fa9-8a86-4095-9253-3d30b5b2f43b)<br>

4. Next, navigate to the 'Elastic Block Store' section and select 'Volumes'.<br>

![Volume](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/6e98098c-06b8-467f-a605-d9662af36bed)<br>

5. You will notice that a default volume has already been created for us.<br>
6. Next, click on 'Snapshots,' and then click the 'Create Snapshot' button. It will prompt you with a page that looks like this.<br>

![Snapshot](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/ac7ce301-7166-434a-883a-75d30ba823e9)<br>

7. In Volume ID section choose your default Volume ID created when we create instance.<br>
8. Next, click 'Next,' provide a name for your Snapshot, and then scroll down and click 'Create Snapshot'.<br>

![Snapshot1](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/e32bfc4d-6eaf-48f0-ad18-064aa63b5f1f)<br>

### Step 2 :
1. After creating a Snapshot, navigate to the Lambda Console..<br>
2. You will see some options in the user interface, such as 'Create Function'.<br>
3. Click on 'Functions'.<br>

![Lambda](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/0e0b8165-2894-4159-9d4b-d8f966126e53)

4. Select 'Author from Scratch,' then enter the Function name, and choose the latest Python version.<br>
5. Scroll down and click 'Create Function'.<br>
6.After creating the function, scroll down, and you will see something like the image below..<br>

![Lambda1](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/b910ebd4-d4aa-4b40-90e0-75381376f682)

7. Click on the 'Code' section.<br>
8. Next, clear the existing code and replace it with the 'identify_stale_snapshots.py' code.<br>

![lambda2](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/412779d2-9547-496a-aa9c-2143265d4c73)

9. Click 'Deploy' to save your changes, and then click 'Test.' It will prompt a page that looks like the one given below.<br>

![lambda3](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/ab918626-a9b0-4c97-915e-d54cdd2df9e8)

10. Please configure the settings as displayed above and then scroll down. Next, click on 'Create Event'.
11. Once you've created the event, proceed to the IAM Console(Identity and Access Management) and then navigate policies section to create a new policy.

![lambda sp](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/bbbc219c-5046-441c-bfe4-307f96e4be5e)

12. Select the service as 'EC2'
![lambda5](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/bfdfc8de-ab59-4ccd-900c-07a26d30bc34)

13. In the 'Actions' section, grant permissions for the following actions: DescribeInstances, DescribeVolumes, DescribeSnapshots, DeleteSnapshots.
14. After Creating the Policies , Navigate to the Lambda Sections.
15. Next, go to the page of the Lambda function you've created. In the "Permissions" section, click on the role name.

![lambda4](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/3a6160ad-9b81-4aba-b052-03c3a1191df8)

16. Click on 'Add Permissions' and then select 'Attach Policy.'

![lambda6](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/f37a6365-5416-427d-be99-4424ff6ead6e)

17. Choose the correct policy you created.

![lambda7](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/473368f0-a3cf-4c3f-b8c0-d1cb62701ddd)

18. Then scroll down and click 'Add Permissions'.
19. After that, you can go to the Lambda function page and run the code; it will display some outputs as shown below.

![lambda11](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/193c1496-b9ef-4455-bb29-718f9497b86d)

### Step 3 :
1. You can terminate the EC2 instance to test our Lambda function.
2. Navigate to the EC2 console and then terminate the EC2 instance.
3. Return to the Lambda console to test the code; go to the Lambda Function page.
4. Under the Code section, click 'Test code', it will display an output like this.

![lambda10](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/d64968b5-951f-4f94-851d-b4841bba8beb)

5. As expected, our Lambda function deleted the snapshot because it was associated with a volume that couldn't be found.

## Additional notes:
We can use CloudWatch to automatically trigger the Lambda function every hour, day, minute, or second. However, this may result in higher costs because our Lambda execution time increases when triggered automatically. Nevertheless, manually triggering this function is a better choice because it allows us to trigger it when needed.

## CloudWatch or EventBridge Implementation :
### Steps :
1. Navigate to CloudWatch Console.

![cw1](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/86e6d0b0-9870-43f5-bdaf-7550fe347ec9)
![cw2](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/7a68754a-6228-4a00-be8a-815e2d70f8ce)
![cw3](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/8124a1b3-efaf-453f-8ed6-d0d228c3edf2)
![cw4](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/5a298d1f-7dc0-43e5-825b-b79a41ac8efc)

2. Next, on the following page, configure the schedule pattern as follows:

![cw5](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/6d7d0a2b-e916-4930-af66-34a322302fe6)

3. Scroll Down and then Click Next.

![cw6](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/dcca1fe9-faaf-4927-b50b-8d230ecfc7a2)
![cw7](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/bbabe976-93d9-4f63-88f3-0937f6e433f7)

4. Scroll Down and then Click Next.
5. On the next page, choose 'None' for the 'Action after Schedule' option.

![cw8](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/297b9ced-d775-4d6f-a583-9a5994343cc9)
![cw9](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/d760257e-fe30-4610-9cd9-97a7ff86783b)
![cw10](https://github.com/itz-mathesh/aws-cost-optimization/assets/144098846/61f60cf5-ff79-4a87-a659-6b1cb1516865)

6. You have successfully created the scheduler, which will trigger the Lambda function every hour.
7. However, please note that this setup will incur some costs since the function is triggered continuously every hour. Alternatively, we can configure it to run on specific days and times as needed.
