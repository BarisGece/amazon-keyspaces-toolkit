# Using the Amazon Keyspaces Toolkit  from AWS CloudShell


AWS CloudShell is a convenient pre-authenticated browser based shell that gives you a secure and easy way to manage and interact with your AWS resources. In addition, AWS CloudShell offers persistent storage of 1 GB for each AWS region at no additional cost. The persistent storage is located in your home directory ($HOME) and is private to you. Unlike ephemeral environment resources that are recycled after each shell session ends, data in your home directory persists between sessions. CloudShell is outside of the VPC and needs to communicate with the Amazon Keyspaces public endpoint.  The Amazon Keyspaces Toolkit contains common Cassandra tooling and helpers that come preconfigured for Amazon Keyspaces, it's lightweight and supports the Sigv4 Authentication plugin, and you can execute cqlsh without having to download the full distribution. This makes the toolkit lightweight. Now you can access the Amazon Keyspaces tool kit through the AWS Cloud Shell. In this readme file are the steps to install the Amazon Keyspaces toolkit in your cloud shell environment.


## Prerequisites to installing cqlsh-expansion in AWS CloudShell


In this section we will be prepare the AWS CloudShell for installation. The preferred method of installation is through pip. pip is the [package installer ](https://packaging.python.org/guides/tool-recommendations/) for Python. You can use pip to install packages from the [Python Package Index.](https://pypi.org/) The cqlsh-expansion requires python 2 so you have to verify the what version python the Cloudshell is running  before installing cqlsh-expansion.

`
python --version
`


The following curl command uses the get-pip.py script to install pip. As a result pip will be install in your Cloudshell home directory. The home directory can currently store 1GB of storage that will be persisted between CloudShell sessions.

`
curl -L https://bootstrap.pypa.io/pip/2.7/get-pip.py -o/tmp/get-pip.py
`

`
python2 /tmp/get-pip.py
`


## Installing cqlsh-expansion on CloudShell


Now that you have pip installed, you can install the cqlsh-expansion into your home directory.
Use the following command to install the cqlsh-expansion into the CloudShell. Installing the cqlsh-expansion into the home directory will enable it to be persisted between sessions.

`
pip install cqlsh-expansion --user
`



## Setting up cqlsh-expansion to connect to Amazon Keyspaces


When using the cqlsh-expansion with Amazon Keyspaces you can use the following post install script or follow the instructions found in the official [Amazon Keyspaces documentation.](https://docs.aws.amazon.com/keyspaces/latest/devguide/programmatic.cqlsh.html)
By default the cqlsh-expansion is not configured with ssl enabled, but the package includes a post [install script](https://github.com/aws-samples/amazon-keyspaces-toolkit/blob/master/cqlsh-expansion/config/post_install.py) helper to quickly set up your environment after installation. The script will place the necessary configuration and SSL certificate in the user’s .cassandra directory. Amazon Keyspaces only accepts secure connections using Transportation Layer Security or TLS. Encryption in transit provides an additional layer of data protection by encrypting your data as it travels to and from Amazon Keyspaces. The post install script first will create the .cassandra directory if it does not exist already. Then it will copy a preconfigured [cqlshrc file](https://github.com/aws-samples/amazon-keyspaces-toolkit/blob/master/cqlsh-expansion/config/cqlshrc_template) and the Starfield digital certificate into the .cassandra directory. The .cassandra directory will be created in the user home directory, as it is the default location. As best practice, please review the post [install script ](https://github.com/aws-samples/amazon-keyspaces-toolkit/blob/master/cqlsh-expansion/config/post_install.py) before executing. Modifications made by this post install script will not be undone if uninstalling the cqlsh-expansion with pip.



This command is configing the Toolkit in CloudShell.

`
cqlsh-expansion.init
`




## Connecting to Amazon Keyspaces

Now that weve installed the cqlsh-expansion and have set up the configuration for SSL communication with Amazon Keyspaces, you can connect to the Amazon Keyspaces services using your IAM access keys or Service Specific Credentials.

### Choosing a region and endpoint

For us to connect to Amazon Keyspaces you will need to choose one of the [service endpoints](https://docs.aws.amazon.com/keyspaces/latest/devguide/programmatic.endpoints.html). You can also connect to Amazon Keyspaces using [Interface VPC endpoints](https://docs.aws.amazon.com/keyspaces/latest/devguide/vpc-endpoints.html) to enable private communication between your Virtual Private Cloud (VPC) running in Amazon VPC and Amazon Keyspaces. For example, to connect to the Keyspaces service in US East (N. Virginia) (us-east-1) [you will want to use the cassandra.us-east-1.amazonaws.com](http://cassandra.us-east-1.amazonaws.com/) service endpoint. All communication with Amazon Keyspaces will be over port 9142.


## Choose an authentication method when connecting

To provide users and applications with credentials for programmatic access to Amazon Keyspaces resources, you can do either of the following:

### Connecting with IAM access keys (users,roles, and federated identities)

For enhanced security, we recommend creating IAM access keys for IAM users and roles that are used across all AWS services. To use IAM access keys to connect to Amazon Keyspaces, customers can use the Signature Version 4 Process (SigV4) authentication plugin for Cassandra client drivers. To learn more about how the Amazon Keyspaces SigV4 plugin enables IAM users, roles, and [federated identities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) to authenticate Amazon Keyspaces API requests, see [AWS Signature Version 4 process (SigV4)](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html). You can use the Sigv4 plugin with the cqlsh-expansion script by providing the following flag: --auth-provider "SigV4AuthProvider" . The Sigv4 plugin depends on the AWS SDK for Python (Boto3) which is included in the requirements file. You will also need to set the the proper credentials to make service calls. You can use the following tutorial to set up credentials using the [AWS CLI.](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/credentials.html)
After you have the credentials set up with [privileges](https://docs.aws.amazon.com/keyspaces/latest/devguide/security_iam_service-with-iam.html) to access Amazon Keyspaces system tables, you can execute the following command to connect to Amazon Keyspaces with CQLSH using the Sigv4 process.


`
cqlsh-expansion cassandra.us-east-1.amazonaws.com 9142 --ssl --auth-provider "SigV4AuthProvider"
`


### Connecting with service-specific credentials

When creating service-specific credentials that are similar to the traditional username and password that Cassandra uses for authentication and access management. AWS service-specific credentials are associated with a specific AWS Identity and Access Management (IAM) user and can only be used for the service they were created for. For more information, see Using IAM with [Amazon Keyspaces (for Apache Cassandra)](https://docs.aws.amazon.com/keyspaces/latest/devguide/security-iam.html) in the IAM User Guide. To connect to Amazon Keyspaces using the cqlsh-expansion and IAM service-specific credentials you can use the command below. In this command we are connecting to us-east-1 region with service specific user ‘mike-user-99’* and service specific user password ‘user-pass-01’. *You will need to replace these credentials with your own user name and password that were given to you when creating the service specific credentials.



`
cqlsh-expansion cassandra.us-east-1.amazonaws.com 9142 --ssl -u Autumn-user-99 -p user-pass-01
`

Alternatively, if you want to use the cqlsh without the additional functionality included in the cqlsh-expansion package you can execute the following.

`
cqlsh cassandra.us-east-1.amazonaws.com 9142 --ssl -u mike-user-99 -p user-pass-01
`


### Cleaning up

When removing the cqlsh-expansion package you can use the pip uninstall api. Additionally, if you executed the post install script cqlsh-expansion.init, you may want to delete the .cassandra directory which contains the cqlshrc file and the ssl certificate. Using pip uninstall will not remove changes made by the post install script.

Clean up pip cache & remove unnecessary files

`
pip cache purge
rm -f ~/.cassandra/get-pip.py
`


`
pip uninstall cqlsh-expansion
`




### Additional info

[AWS CloudShell](https://docs.aws.amazon.com/cloudshell/latest/userguide/welcome.html)


[Cqlsh-expansion package](https://pypi.org/project/cqlsh-expansion/)


# License

This library is licensed under the MIT-0 License. See the LICENSE file.
