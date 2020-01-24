# Running containers on AWS ECS

## Notes and References 

We use the standard AWS CLI to run ECS commands but you may want to use the specific [ECS CLI](https://github.com/aws/amazon-ecs-cli) instead, which should make things a bit easier when managing your cluster.

In example, the ECS CLI can use the **Docker Compost** configurations straight away: look at the [AWS CLI Tutorial](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI.html).

* [Amazon ECS-optimized AMIs](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html)
* [Amazon ECS Container Agent Configuration](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html)

## Setting up a security group

Create a new Security Group
```
mario@SCALASM-NB:~$ aws ec2 create-security-group --group-name  mario_SG_eucentral1 --description "Security group for VM instances"
{
    "GroupId": "sg-0c340f909161831a2"
}

mario@SCALASM-NB:~$ export SGID="sg-0c340f909161831a2"
mario@SCALASM-NB:~$ aws ec2 describe-security-groups --group-id $SGID
```

Authorize SSH and HTTP access for everyone
```
aws ec2 authorize-security-group-ingress --group-id $SGID --protocol tcp --port 22 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-id $SGID --protocol tcp --port 80 --cidr 0.0.0.0/0
```

Authorize future EC2 instances to connect to RDS and ElastiCache
```
aws ec2 authorize-security-group-ingress --group-id $SGID --protocol tcp --port 5432 --source-group sg-5f63c627
aws ec2 authorize-security-group-ingress --group-id $SGID --protocol tcp --port 6379 --source-group sg-5f63c627
```

## Creating IAM roles for ECS

Using the Web Console, we created:
* LinksECSInstanceRole with the policies to allows EC2 to connect to an ECS cluster
* LinksECSServiceRole with the policies to allow ECS to create resources on our behalf


## Create a cluster

```
aws ecs create-cluster --cluster-name deepdive
aws ecs list-clusters
```

```
mario@SCALASM-NB:~$ aws ecs describe-clusters --clusters deepdive
{
    "clusters": [
        {
            "clusterArn": "arn:aws:ecs:eu-central-1:015589054601:cluster/deepdive",
            "clusterName": "deepdive",
            "status": "INACTIVE",
            "registeredContainerInstancesCount": 0,
            "runningTasksCount": 0,
            "pendingTasksCount": 0,
            "activeServicesCount": 0,
            "statistics": [],
            "tags": [],
            "settings": [
                {
                    "name": "containerInsights",
                    "value": "disabled"
                }
            ],
            "capacityProviders": [],
            "defaultCapacityProviderStrategy": []
        }
    ],
    "failures": []
}
```

## Create a VM

See [ECS documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html) for AMI ids across regions:

```
aws ec2 run-instances --image-id ami-0b97931838a22e7c1 --count 1 --instance-type t2.micro --iam-instance-profile Name=LinksECSInstanceRole --key-name LinksDevOpsKeyPair \
 --security-group-ids $SGID --user-data file://copy-ecs-config-to-s3

export EC2_INSTANCE_ID="i-0091d1d5dba03472a"
```

```
aws ec2 describe-instance-status --instance-id $EC2_INSTANCE_ID
aws ec2 describe-instances --instance-ids $EC2_INSTANCE_ID
```

Now you can check that the EC2 instance is part of the cluster:

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs list-container-instances --cluster deepdive
{
    "containerInstanceArns": [
        "arn:aws:ecs:eu-central-1:015589054601:container-instance/f904d331-83c7-48f9-9ef6-37a83acd3012"
    ]
}
```

More details about the container instance:
```
aws ecs describe-container-instances --cluster deepdive --container-instances "arn:aws:ecs:eu-central-1:015589054601:container-instance/f904d331-83c7-48f9-9ef6-37a83acd3012"
```

## Terminating EC2 instances

```
aws ec2 terminate-instances --instance-ids $EC2_INSTANCE_ID
```
