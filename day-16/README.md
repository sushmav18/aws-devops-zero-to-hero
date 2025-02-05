# AWS CLOUD WATCH 

Welcome back to our "30 Days AWS Zero to Hero" series. Today, on Day 16, we will deep dive into AWS CloudWatch.

What is AWS CloudWatch?
Gate keeper for monitoring AWS account tracking activities:
1.Monitoring
2.Logging
3.Reporting
4. Logging

AWS CloudWatch is a powerful monitoring and observability service provided by Amazon Web Services. It enables you to gain insights into the performance, health, and operational aspects of your AWS resources and applications. CloudWatch collects and tracks metrics, collects and monitors log files, and sets alarms to alert you on certain conditions.

Advantages of AWS CloudWatch:

    Comprehensive Monitoring: CloudWatch allows you to monitor various AWS resources such as EC2 instances, RDS databases, Lambda functions, and more. You get a unified view of your entire AWS infrastructure.

    Real-Time Metrics: It provides real-time monitoring of metrics, allowing you to respond quickly to any issues or anomalies that might arise.

    Automated Actions: With CloudWatch Alarms, you can set up automated actions like triggering an Auto Scaling group to scale in or out based on certain conditions.

    Log Insights: CloudWatch Insights lets you analyze and search log data from various AWS services, making it easier to troubleshoot problems and identify trends.

    Dashboards and Visualization: Create custom dashboards to visualize your application and infrastructure metrics in one place, making it easier to understand the overall health of your system.

Problem Solving with AWS CloudWatch:

CloudWatch helps address several critical challenges, including:

    Resource Utilization: Tracking resource utilization and performance metrics to optimize your AWS infrastructure efficiently.
    Proactive Monitoring: Identifying and resolving issues before they impact your applications or users.
    Troubleshooting: Analyzing logs and metrics to troubleshoot problems and reduce downtime.
    Scalability: Automatically scaling resources based on demand to ensure optimal performance and cost efficiency.

Practical Use Cases of AWS CloudWatch:

    Auto Scaling: CloudWatch can trigger Auto Scaling actions based on defined thresholds. For example, you can automatically scale in or out based on CPU utilization or request counts.

    Resource Monitoring: Monitor EC2 instances, RDS databases, DynamoDB tables, and other AWS resources to gain insights into their performance and health.

    Application Insights: Track application-specific metrics to monitor the performance of your applications and identify potential bottlenecks.

    Log Analysis: Use CloudWatch Logs Insights to analyze log data, identify patterns, and troubleshoot issues in real-time.

    Billing and Cost Monitoring: CloudWatch can help you monitor your AWS billing and usage patterns, enabling you to optimize costs.


EXTRA 
AWS CloudWatch is a crucial tool for DevOps engineers, providing monitoring, logging, and observability for AWS resources and applications. Here’s how you can leverage it effectively:

1. Monitoring and Metrics
CloudWatch Metrics: Collects and visualizes performance data from AWS services like EC2, RDS, Lambda, and S3.
Custom Metrics: Publish your own application-level metrics using the AWS SDK or API.
Alarms: Set thresholds for metrics (e.g., CPU usage > 80%) and trigger notifications via SNS.
2. Logging and Troubleshooting
CloudWatch Logs: Aggregates logs from EC2, Lambda, ECS, and on-premises servers.
Log Insights: Query and analyze logs using SQL-like queries for faster troubleshooting.
Log Streams & Groups: Organize logs for better observability.
3. Application and Infrastructure Monitoring
CloudWatch Dashboards: Customizable graphs to monitor system health.
AWS X-Ray Integration: Helps trace application requests to diagnose bottlenecks.
Container Monitoring: Supports ECS, EKS, and Kubernetes via CloudWatch Container Insights.
4. Event-Driven Automation
CloudWatch Events (EventBridge): Automate responses to changes in AWS resources (e.g., auto-restart an EC2 instance on failure).
Lambda Triggers: Invoke AWS Lambda functions based on log patterns or alarms.
5. Cost Optimization and Security
Billing Alarms: Track AWS costs and prevent unexpected charges.
Security Monitoring: Use CloudTrail logs with CloudWatch to detect unauthorized activities.

 EC2 Auto-Recovery & Cost Optimization
📌 Use Case: Automatically recover crashed EC2 instances and detect underutilized ones.

✅ How?

Set up a CloudWatch Alarm to detect an unhealthy EC2 instance.
Use an AWS Lambda function to restart the instance.
Create another alarm to detect low CPU utilization (e.g., < 10%) to shut down idle instances.
🔹 Example Command (EC2 Auto-Recovery Alarm)

bash
Copy
Edit
aws cloudwatch put-metric-alarm --alarm-name "EC2_Recover" \
  --metric-name "StatusCheckFailed_System" --namespace "AWS/EC2" \
  --statistic Maximum --threshold 1 --comparison-operator GreaterThanThreshold \
  --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
  --evaluation-periods 2 --period 60 \
  --alarm-actions arn:aws:automate:region:ec2:recover
2️⃣ Detecting & Restarting Crashed Containers (ECS/EKS/Fargate)
📌 Use Case: Detect failed ECS tasks and restart them automatically.

✅ How?

Set up a CloudWatch Alarm to monitor ECS task status.
Use Amazon EventBridge to trigger a Lambda function to restart the task.
🔹 EventBridge Rule for Task Failure

json
Copy
Edit
{
  "source": ["aws.ecs"],
  "detail-type": ["ECS Task State Change"],
  "detail": {
    "desiredStatus": ["STOPPED"]
  }
}
3️⃣ Monitor & Alert on High CPU/Memory Usage
📌 Use Case: Detect performance bottlenecks in EC2, ECS, or Lambda functions.

✅ How?

Create CloudWatch Alarms for CPU and Memory thresholds.
Trigger an SNS notification to alert the team via Slack or Email.
🔹 Example: Alarm for High CPU Usage on ECS

bash
Copy
Edit
aws cloudwatch put-metric-alarm --alarm-name "HighCPUUsage" \
  --metric-name "CPUUtilization" --namespace "AWS/ECS" \
  --statistic Average --threshold 80 --comparison-operator GreaterThanThreshold \
  --dimensions Name=ClusterName,Value=my-cluster Name=ServiceName,Value=my-service \
  --evaluation-periods 2 --period 60 --alarm-actions arn:aws:sns:us-east-1:123456789012:DevOpsAlerts
4️⃣ Log Monitoring & Automated Security Alerts
📌 Use Case: Detect security threats like failed SSH logins or API abuse.

✅ How?

Use CloudWatch Logs Insights to analyze system logs.
Set up alarms for multiple failed login attempts to trigger an automated response (e.g., block the IP).
🔹 Example Query for Failed SSH Logins

sql
Copy
Edit
fields @timestamp, @message
| filter @message like "Failed password"
| stats count(*) as failed_attempts by sourceIP
| sort failed_attempts desc
🔹 Automate Blocking IP using AWS WAF

Use AWS Lambda + EventBridge to block IPs based on log insights.
5️⃣ Detect S3 Bucket Changes & Trigger Workflows
📌 Use Case: Trigger CI/CD pipeline when a new object is uploaded to S3.

✅ How?

Use CloudWatch Events to detect S3 PUT requests.
Trigger a CodePipeline or Lambda function for processing.
🔹 Example EventBridge Rule for S3 Upload

json
Copy
Edit
{
  "source": ["aws.s3"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventName": ["PutObject"],
    "requestParameters": {
      "bucketName": ["my-bucket"]
    }
  }
}
6️⃣ Monitor and Optimize AWS Billing
📌 Use Case: Get notified when AWS usage crosses a specific budget threshold.

✅ How?

Create a CloudWatch Billing Alarm that triggers an SNS alert.
🔹 Example: Alarm for $100 Monthly Billing

bash
Copy
Edit
aws cloudwatch put-metric-alarm --alarm-name "BillingAlert" \
  --namespace "AWS/Billing" --metric-name "EstimatedCharges" \
  --statistic Maximum --threshold 100 --comparison-operator GreaterThanThreshold \
  --evaluation-periods 1 --period 21600 --region us-east-1 \
  --alarm-actions arn:aws:sns:us-east-1:123456789012:BillingAlerts
7️⃣ Detecting Latency Issues in API Gateway & Lambda
📌 Use Case: Monitor and alert on API latency spikes.

✅ How?

Create a CloudWatch Alarm on API Gateway or Lambda duration metrics.
🔹 Example: Alert if API Gateway Latency Exceeds 2 Seconds
