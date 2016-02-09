# aws-cli-tools
Command Line tools built around AWS CLI

Requires aws-cli

#### /ec2/security-group/whitelist-domain

Add AWS Security Group Outbound rules for a given domain, port and aws security group id. The script translate the domain name to IP address(es) first, then add the rule for each IP after checking the rule is not in place already. 

This is useful when you want to maintain a secured environment with explicit whitelist ACL rules implemented by AWS Security Groups. 
