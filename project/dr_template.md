# Infrastructure

## AWS Regions
**Primary**: us-east-2\
**Secondary**: us-west-1

## Servers and Clusters

### Table 1.1 Summary
| Asset      | Purpose           | Size                                                                   | Qty                                                             | DR                                                                                                           |
|------------|-------------------|------------------------------------------------------------------------|-----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| EC2 Instances | Application virtual machines hosting e-commerce site | t3.micro | 3 | Deployed in Primary and Secondary AWS regions |
| EKS Cluster | Kubernetes cluster hosting monitoring stack | t3.medium | 2 | Deployed in Primary and Secondary AWS regions |
| VPC | Virtual network | /16 | 1  | Deployed in Primary and Secondary AWS regions, across multiple availability zones |
| ALB | Application load balancer |  | 1 | Deployed in Primary and Secondary AWS regions |
| RDS Cluster | Managed relational database | db.t2.small | 2 | Deployed in Primary and Secondary AWS regions, across multiple availability zones |

### Descriptions

**EC2:** EC2 instances host 'Ubuntu-Web' which is an HTTP web server for the e-commerce site. There are 3 instances in each AWS region for HA purposes.    \
**EKS:** EKS Kubernetes cluster which hosts the kube-prometheus-stack for monitoring. Blackbox exporter is used to probe endpoints. There are 2 nodes per cluster in each AWS region for HA purposes.     \
**VPC:** Class B VPC virtual network with a /16 CIDR. One virtual network is available in each AWS region, with additional redundancy being provided by multiple availability zones. \
**ALB:** Application load balancer listening on port 80 (HTTP) for the 3 backend EC2 instances. This allows traffic to be distributed across the instances for HA purposes and better performance. A single ALB is available in each AWS region. \
**RDS:** Managed relational database cluster deployed across both AWS regions, with additional redundancy being provided by availability zones. Backups are set to 5 days and replication is enabled between the primary and secondary region clusters.

## DR Plan
### Pre-Steps:
- Check IaC to confirm that the terraform code is available.
- Confirm that aws_ami is available in the secondary region.
- Prepare a backend DR S3 bucket in the secondary region.
- Check that aws_ami and S3 bucket have been defined.
- From your secondary region directory `./zone2` run `terraform apply` and follow the prompts to deploy your secondary region resources.
- Check health of RDS cluster and resources in AWS portal.


## Steps:
- ### Manual failover of RDS cluster
    + Login to AWS portal
    + Open RDS Management Console (RDS)
    + Click 'Databases'
    + Choose correct region
    + Select 'Writer Instance'
    + Select 'Actions/Failover' 
    + Click 'Failover'
    + Refresh databases page until reader/writer instances change
- ### Failover of application to secondary region.
    + Configure the AWS Route 53 DNS service to provide a DNS name which can point to the load balancers within both regions.
    + Point your DNS to the secondary region.
    + Manually failover the RDS cluster as above.
    + Automatically failover the RDS cluster by health checks.
