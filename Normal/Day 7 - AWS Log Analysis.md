******************************************************************************************************************************
                    Day 7: Oh,no. I'm SPEAKING IN CLOUDTRAIL!   
******************************************************************************************************************************

These instances are (in AWS) called EC2 instances (Amazon Elastic Compute Cloud)

CloudWatch

 AWS CloudWatch is a monitoring and observability platform that gives us greater insight into our AWS environment by monitoring applications at multiple levels. CloudWatch provides functionalities such as the monitoring of system and application metrics and the configuration of alarms on those metrics for the purposes of today's investigation, though we want to focus specifically on CloudWatch logs. Running an application in a cloud environment can mean leveraging lots of different services (e.g. a service running the application, a service running functions triggered by that application, a service running the application backend, etc.); this translates to logs being generated from lots of different sources. CloudWatch logs make it easy for users to access, monitor and store the logs from all these various sources. A CloudWatch agent must be installed on the appropriate instance for application and system metrics to be captured.

A key feature of CloudWatch logs that will help the Warevile SOC squad and us make sense of what happened in their environment is the ability to query application logs using filter patterns. Here are some CloudWatch terms you should know before going further:

Log Events: A log event is a single log entry recording an application "event"; these will be timestamped and packaged with log messages and metadata.
Log Streams: Log streams are a collection of log events from a single source.
Log Groups: Log groups are a collection of log streams. Log streams are collected into a log group when logically it makes sense, for example, if the same service is running across multiple hosts.
CloudTrail

CloudWatch can track infrastructure and application performance, but what if you wanted to monitor actions in your AWS environment? These would be tracked using another service called AWS CloudTrail. Actions can be those taken by a user, a role (granted to a user giving them certain permissions) or an AWS service and are recorded as events in AWS CloudTrail. Essentially, any action the user takes (via the management console or AWS CLI) or service will be captured and stored. Some features of CloudTrail include:

Always On: CloudTrail is enabled by default for all users
JSON-formatted: All event types captured by CloudTrail will be in the CloudTrail JSON format
Event History: When users access CloudTrail, they will see an option "Event History", event history is a record of the actions that have taken place in the last 90 days. These records are queryable and can be filtered on attributes such as "resource" type.
Trails: The above-mentioned event history can be thought of as the default "trail," included out of the box. However, users can define custom trails to capture specific actions, which is useful if you have bespoke monitoring scenarios you want to capture and store beyond the 90-day event history retention period.
Deliverable:  As mentioned, CloudWatch can be used as a single access point for logs generated from various sources; CloudTrail is no different and has an optional feature enabling CloudTrail logs to be delivered to CloudWatch.

As mentioned, Cloudtrail helps capture and record actions taken. These actions could be interactions with any number of AWS services. For example, services like S3 (Amazon Simple Storage Service used for object storage) and IAM (AWS's Identity and Access Management service can be used to secure access to your AWS environment with the creation of identities and the assigning of access permissions to those identities) will have actions taken within their service recorded. These recorded events can be very helpful when performing an investigation.

JQ is a lightweight and flexible command line processor that can be used on JSON

[

{ "book_title": "Wares Wally", "genre": "children", "page_count": 20 },

{ "book_title": "Charlottes Web Crawler", "genre": "young_ware", "page_count": 120 },

{ "book_title": "Charlie and the 8 Bit Factory", "genre": "young_ware", "page_count": 108 },

{ "book_title": "The Princess and the Pcap", "genre": "children", "page_count": 48 },

{ "book_title": "The Lion, the Glitch and the Wardrobe", "genre": "young_ware", "page_count": 218 }

]

user@tryhackme$ jq '.[]' book_list.json

{
  "book_title": "Wares Wally",
  "genre": "children",
  "page_count": 20
}
{
  "book_title": "Charlottes Web Crawler",
  "genre": "young_ware",
  "page_count": 120
}
{
  "book_title": "Charlie and the 8 Bit Factory",
  "genre": "young_ware",
  "page_count": 108
}
{
  "book_title": "The Princess and the Pcap",
  "genre": "children",
  "page_count": 48
}
{
  "book_title": "The Lion, the Glitch and the Wardrobe",
  "genre": "young_ware",
  "page_count": 218
}

user@tryhackme$ jq  '.[] | .book_title' book_list.json

"Wares Wally"
"Charlottes Web Crawler"
"Charlie and the 8 Bit Factory"
"The Princess and the Pcap"
"The Lion, the Glitch and the Wardrobe"

******************************************
                Investigation
******************************************        

We sent out a link on the 28th of November to everyone in our network that points to a flyer with the details of our charity. The details include the account number to receive donations. We received many donations the first day after sending out the link, but there were none from the second day on. I talked to multiple people who claimed to have donated a respectable sum. One showed his transaction, and I noticed the account number was wrong. I checked the link, and it was still the same. I opened the link, and the digital flyer was the same except for the account number.

28th November - Everyone - Flyer
Account Number originally correctly sent however Account Number is wrong

wareville-bank-account-qr.png - Amazon AWS S3 bucket named wareville-care4wares

The day after the link was sent out, several donations were received.
Since the second day after sending the link, no more donations have been received.
A donator has shown proof of his transaction. It was made 3 days after he received the link. The account number in the transaction was not correct.
McSkidy put the digital flyer in the AWS S3 object named wareville-bank-account-qr.png under the bucket wareville-care4wares.
The link has not been altered.

{
  "eventVersion": "1.10",
  "userIdentity": {
    "type": "IAMUser",
    "principalId": "AIDAXRMKYT5O5Y2GLD4ZG",
    "arn": "arn:aws:iam::518371450717:user/wareville_collector",
    "accountId": "518371450717",
    "accessKeyId": "AKIAXRMKYT5OZCZPGNZ7",
    "userName": "wareville_collector"
  },
  "eventTime": "2024-10-21T22:13:24Z",
  "eventSource": "s3.amazonaws.com",
  "eventName": "ListObjects",
  "awsRegion": "ap-southeast-1",
  "sourceIPAddress": "34.247.218.56",
  "userAgent": "[aws-sdk-go/0.24.0 (go1.22.6; linux; amd64)]",
  "requestParameters": {
    "bucketName": "aoc-cloudtrail-wareville",
    "Host": "aoc-cloudtrail-wareville.s3.ap-southeast-1.amazonaws.com",
    "prefix": ""
  },
  "responseElements": null,
  "additionalEventData": {
    "SignatureVersion": "SigV4",
    "CipherSuite": "TLS_AES_128_GCM_SHA256",
    "bytesTransferredIn": 0,
    "AuthenticationMethod": "AuthHeader",
    "x-amz-id-2": "yqniVtqBrL0jNyGlvnYeR3BvJJPlXdgxvjAwwWhTt9dLMbhgZugkhlH8H21Oo5kNLiq8vg5vLoj3BNl9LPEAqN5iHpKpZ1hVynQi7qrIDk0=",
    "bytesTransferredOut": 236375
  },
  "requestID": "YKEKJP7QX32B4NZB",
  "eventID": "fd80529f-d0af-4f44-8034-743d8d92bdcf",
  "readOnly": true,
  "resources": [
    {
      "type": "AWS::S3::Object",
      "ARNPrefix": "arn:aws:s3:::aoc-cloudtrail-wareville/"
    },
    {
      "accountId": "518371450717",
      "type": "AWS::S3::Bucket",
      "ARN": "arn:aws:s3:::aoc-cloudtrail-wareville"
    }
  ],
  "eventType": "AwsApiCall",
  "managementEvent": false,
  "recipientAccountId": "518371450717",
  "eventCategory": "Data",
  "tlsDetails": {
    "tlsVersion": "TLSv1.3",
    "cipherSuite": "TLS_AES_128_GCM_SHA256",
    "clientProvidedHostHeader": "aoc-cloudtrail-wareville.s3.ap-southeast-1.amazonaws.com"
  }
}

| Field              | Description                                                      |
|--------------------|------------------------------------------------------------------|
| userIdentity       | Details of the user account that acted on an object.            |
| eventTime          | When did the action occur?                                      |
| eventType          | What type of event occurred? (e.g., AwsApiCall or AwsConsoleSignIn, AwsServiceEvent) |
| eventSource        | From what service was the event logged?                         |
| eventName          | What specific action occurred? (e.g., ListObjects, GetBucketObject) |
| sourceIPAddress    | From what IP did the action happen?                             |
| userAgent          | What user agent was used to perform the action? (e.g., Firefox, AWS CLI) |
| requestParameters  | What parameters were involved in the action? (e.g., BucketName) |

By using the guide above, we can read the example log entry as follows: 

The IAM user, wareville_collector, listed all objects (ListObjects event) of the S3 bucket named aoc-cloudtrail-wareville.
The IP address from which this request originated is 34.247.218.56.
The user agent indicates that the request was made using the AWS SDK tool for Go.

Logs in = ~/wareville_logs

jq -r '.Records[] | select(.eventSource == "s3.amazonaws.com" and .requestParameters.bucketName=="wareville-care4wares")' cloudtrail_log.json
| Command                                             | Description                                                                                                                                               |
|-----------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `jq -r 'FILTER' cloudtrail_log.json`                | Executes a `jq` command with the `-r` flag to output results in RAW format instead of JSON. The FILTER defines specific conditions to parse the input file. |
| `-r`                                                | Tells `jq` to output results in RAW format instead of JSON.                                                                                               |
| `'FILTER'`                                          | Specifies the filter conditions enclosed in single quotes.                                                                                               |
| `cloudtrail_log.json`                               | The input file being processed, in this case, `cloudtrail_log.json`.                                                                                      |
| `.Records[]`                                        | Instructs `jq` to parse the events within the `Records` container element, which is the top-level field in the JSON-formatted CloudTrail log.             |
| `| select(.eventSource == "s3.amazonaws.com" and .requestParameters.bucketName=="wareville-care4wares")` | Filters the parsed output to include only events where `eventSource` equals `s3.amazonaws.com` and `requestParameters.bucketName` matches `wareville-care4wares`. |
| `s3.amazonaws.com`                                  | Filters events related to the Amazon AWS S3 service.                                                                                                     |
| `wareville-care4wares`                              | Targets events related specifically to the S3 bucket named `wareville-care4wares`.                                                                       |


jq -r '.Records[] | select(.eventSource == "s3.amazonaws.com" and .requestParameters.bucketName=="wareville-care4wares") | [.eventTime, .eventName, .userIdentity.userName // "N/A",.requestParameters.bucketName // "N/A", .requestParameters.key // "N/A", .sourceIPAddress // "N/A"]' cloudtrail_log.json

| Command                                                                                                   | Description                                                                                                                                                                                                                          |
|-----------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `[.eventTime, .eventName, .userIdentity.userName // "N/A", .requestParameters.bucketName // "N/A", .requestParameters.key // "N/A", .sourceIPAddress // "N/A"]` | Formats the filtered output to include only specified keys such as `.eventTime`, `.eventName`, and `.userIdentity.userName`. The resulting output is structured as an array.                                                         |
| `[]`                                                                                                      | Encloses the selected keys to create an array containing the specified fields from each record.                                                                                                                                      |
| `.eventTime`                                                                                              | Includes the event timestamp in the output.                                                                                                                                                                                          |
| `.eventName`                                                                                              | Includes the event name in the output.                                                                                                                                                                                               |
| `.userIdentity.userName // "N/A"`                                                                         | Extracts the user name from the `userIdentity` field. If the field is absent, `N/A` is displayed instead.                                                                                                                             |
| `.requestParameters.bucketName // "N/A"`                                                                  | Extracts the bucket name from `requestParameters`. If the field is absent, `N/A` is displayed.                                                                                                                                       |
| `.requestParameters.key // "N/A"`                                                                         | Extracts the object key from `requestParameters`. If the field is absent, `N/A` is displayed.                                                                                                                                        |
| `.sourceIPAddress // "N/A"`                                                                               | Extracts the source IP address from the log. If the field is absent, `N/A` is displayed.                                                                                                                                             |
| `// "N/A"`                                                                                                | Ensures that any field without a value is replaced with the string `N/A` for consistent formatting in the output.                                                                                                                    |


jq -r '["Event_Time", "Event_Name", "User_Name", "Bucket_Name", "Key", "Source_IP"],(.Records[] | select(.eventSource == "s3.amazonaws.com" and .requestParameters.bucketName=="wareville-care4wares") | [.eventTime, .eventName, .userIdentity.userName // "N/A",.requestParameters.bucketName // "N/A", .requestParameters.key // "N/A", .sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t

| Command                                                                                  | Description                                                                                                                                                               |
|------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `jq -r '["Event_Time", "Event_Name", "User_Name", "Bucket_Name", "Key", "Source_IP"], SELECT_FILTER | SPECIFIC FIELDS'` | Prepends a column header row to the output and defines it as an array that matches the selected fields from the log. Combines the header and selected results using a comma. |
| `["Event_Time", "Event_Name", "User_Name", "Bucket_Name", "Key", "Source_IP"]`           | Defines the column headers corresponding to the selected fields for the output.                                                                                           |
| `SELECT_FILTER | SPECIFIC FIELDS`                                                       | Processes the filtered data to extract only the specific fields defined in the column headers.                                                                            |
| `@tsv`                                                                                  | Converts each array element into a tab-separated value (TSV) format for cleaner processing and readability.                                                               |
| `column -t -s $'\t'`                                                                    | Beautifies the TSV output by processing tabs and aligning the columns for a structured, visually appealing result.                                                        |

How many log entries are related to the wareville-care4wares bucket?
Which user initiated most of these log entries?
Which actions did the user perform based on the eventName field?
Were there any specific files edited?
What is the timestamp of the log entries?
What is the source IP related to these log entries?
What event types are included in these log entries?
What is the timestamp of these log entries?
Which IPs are included in these log entries?
What tool/OS was used in these log entries?

jq -r '["Event_Time", "Event_Source", "Event_Name", "User_Name", "Source_IP"],(.Records[] | select(.userIdentity.userName == "glitch") | [.eventTime, .eventSource, .eventName, .userIdentity.userName // "N/A", .sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t -s $'\t'

| Command                                                                                              | Description                                                                                                     |
|------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| `S3Console/0.4, aws-internal/3 aws-sdk-java/1.12.750 Linux/5.10.226-192.879.amzn2int.x86_64 OpenJDK_64-Bit_Server_VM/25.412-b09 java/1.8.0_412 vendor/Oracle_Corporation cfg/retry-mode/standard` | The `userAgent` string for the internal AWS console. It provides minimal information about the source or usage. |
| `Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/129.0.0.0 Safari/537.36` | The `userAgent` string identifies a Google Chrome browser running on a Mac OS system, providing context about the anomalous account's platform. |

.eventSource == "iam.amazonaws.com"

jq -r '["Event_Time", "Event_Source", "Event_Name", "User_Name", "Source_IP"], (.Records[] | select(.eventSource == "iam.amazonaws.com") | [.eventTime, .eventSource, .eventName, .userIdentity.userName // "N/A", .sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t -s $'\t'

What Event Names are included in the log entries?
What user executed these events?
What is this user’s IP?

jq '.Records[] |select(.eventSource=="iam.amazonaws.com" and .eventName== "CreateUser")' cloudtrail_log.json

{
  "eventVersion": "1.10",
  "userIdentity": {
    "type": "IAMUser",
    "principalId": "AIDAXRMKYT5O6Z6AZBXU6",
    "arn": "arn:aws:iam::518371450717:user/mcskidy",
    "accountId": "518371450717",
    "accessKeyId": "ASIAXRMKYT5OVOMUJU3P",
    "userName": "mcskidy",
    "sessionContext": {
      "attributes": {
        "creationDate": "2024-11-28T15:20:54Z",
        "mfaAuthenticated": "false"
      }
    }
  },
  "eventTime": "2024-11-28T15:21:35Z",
  "eventSource": "iam.amazonaws.com",
  "eventName": "CreateUser",
  "awsRegion": "ap-southeast-1",
  "sourceIPAddress": "53.94.201.69",
  "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/129.0.0.0 Safari/537.36",
  "requestParameters": {
    "userName": "glitch"
  },
  "responseElements": {
    "user": {
      "path": "/",
      "userName": "glitch",
      "userId": "AIDAXRMKYT5O7SKYSEJBQ",
      "arn": "arn:aws:iam::518371450717:user/glitch",
      "createDate": "Oct 22, 2024 3:21:35 PM"
    }
  },
  "requestID": "415e0a96-f1b6-429a-9cac-1c921c0b85f5",
  "eventID": "64dd59fc-c1b1-4f2d-b15c-b005911f1de4",
  "readOnly": false,
  "eventType": "AwsApiCall",
  "managementEvent": true,
  "recipientAccountId": "518371450717",
  "eventCategory": "Management",
  "tlsDetails": {
    "tlsVersion": "TLSv1.3",
    "cipherSuite": "TLS_AES_128_GCM_SHA256",
    "clientProvidedHostHeader": "iam.amazonaws.com"
  },
  "sessionCredentialFromConsole": "true"
}

jq '.Records[] | select(.eventSource=="iam.amazonaws.com" and .eventName== "AttachUserPolicy")' cloudtrail_log.json
{
  "eventVersion": "1.10",
  "userIdentity": {
    "type": "IAMUser",
    "principalId": "AIDAXRMKYT5O6Z6AZBXU6",
    "arn": "arn:aws:iam::518371450717:user/mcskidy",
    "accountId": "518371450717",
    "accessKeyId": "ASIAXRMKYT5OVOMUJU3P",
    "userName": "mcskidy",
    "sessionContext": {
      "attributes": {
        "creationDate": "2024-11-28T15:20:54Z",
        "mfaAuthenticated": "false"
      }
    }
  },
  "eventTime": "2024-11-28T15:21:36Z",
  "eventSource": "iam.amazonaws.com",
  "eventName": "AttachUserPolicy",
  "awsRegion": "ap-southeast-1",
  "sourceIPAddress": "53.94.201.69",
  "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/129.0.0.0 Safari/537.36",
  "requestParameters": {
    "userName": "glitch",
    "policyArn": "arn:aws:iam::aws:policy/AdministratorAccess"
  },
  "responseElements": null,
  "requestID": "975d9d18-27d5-48a8-a882-a8b58b3a7173",
  "eventID": "08799cc5-535f-4d58-8373-d0b2cbb73a52",
  "readOnly": false,
  "eventType": "AwsApiCall",
  "managementEvent": true,
  "recipientAccountId": "518371450717",
  "eventCategory": "Management",
  "tlsDetails": {
    "tlsVersion": "TLSv1.3",
    "cipherSuite": "TLS_AES_128_GCM_SHA256",
    "clientProvidedHostHeader": "iam.amazonaws.com"
  },
  "sessionCredentialFromConsole": "true"
}


The incident starts with an anomalous login with the user account mcskidy from IP 53.94.201.69.
Shortly after the login, an anomalous user account glitch was created.
Then, the glitch user account was assigned administrator permissions.
The glitch user account then accessed the S3 bucket named wareville-care4wares and replaced the wareville-bank-account-qr.png file with a new one. The IP address and User-Agent used to log into the glitch, mcskidy, and mayor_malware accounts were the same.
the User-Agent string and Source IP of recurrent logins by the user account mcskidy are different

~/wareville_logs/rds.log
grep INSERT rds.log

2024-11-28T15:22:17.728Z 2024-11-28T15:22:17.728648Z	  263 Query	INSERT INTO wareville_bank_transactions (account_number, account_owner, amount) VALUES ('8839 2219 1329 6917', 'Care4wares Fund', 342.80)
2024-11-28T15:22:18.569Z 2024-11-28T15:22:18.569279Z	  263 Query	INSERT INTO wareville_bank_transactions (account_number, account_owner, amount) VALUES ('8839 2219 1329 6917', 'Care4wares Fund', 929.57)
2024-11-28T15:23:02.605Z 2024-11-28T15:23:02.605700Z	  263 Query	INSERT INTO wareville_bank_transactions (account_number, account_owner, amount) VALUES ('----- REDACTED ----', 'Mayor Malware', 193.45)
2024-11-28T15:23:02.792Z 2024-11-28T15:23:02.792161Z	  263 Query	INSERT INTO wareville_bank_transactions (account_number, account_owner, amount) VALUES ('----- REDACTED ----', 'Mayor Malware', 998.13)

jq -r '["Event_Time","Event_Source","Event_Name", "User_Name","User_Agent","Source_IP"],(.Records[] | select(.userIdentity.userName=="mcskidy") | [.eventTime, .eventSource, .eventName, .userIdentity.userName // "N/A",.userAgent // "N/A",.sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t -s $'\t'
jq -r '["Event_Time","Event_Source","Event_Name", "User_Name","User_Agent","Source_IP"],(.Records[] | select(.userIdentity.userName=="glitch") | [.eventTime, .eventSource, .eventName, .userIdentity.userName // "N/A",.userAgent // "N/A",.sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t -s $'\t'
jq -r '["Event_Time","Event_Source","Event_Name", "User_Name","User_Agent","Source_IP"],(.Records[] | select(.userIdentity.userName=="mayor_malware") | [.eventTime, .eventSource, .eventName, .userIdentity.userName // "N/A",.userAgent // "N/A",.sourceIPAddress // "N/A"]) | @tsv' cloudtrail_log.json | column -t -s $'\t'

Which IP does each user typically use to log into AWS?
Which OS and browser does each user usually use?
Are there any similarities or explicit differences between the IP addresses and operating systems used?

mcskidy - 31.210.15.79
mayor_malware - 53.94.201.69

*****************************************************
                Commandline Arguments
*****************************************************











*****************************************************
                Section 1
*****************************************************

*****************************************************
                Section 2
*****************************************************

*****************************************************
                Section 3
*****************************************************