# Running your containers on AWS ECS

## References 

* [Amazon ECS-optimized AMIs](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html)
* [Amazon ECS Container Agent Configuration](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-agent-config.html)

## Managing your cluster

### Create cluster
```
aws ecs create-cluster  --cluster-name deepdive
```

This will output:
```
{
    "cluster": {
        "clusterArn": "arn:aws:ecs:eu-central-1:321723152483:cluster/deepdive",
        "clusterName": "deepdive",
        "status": "ACTIVE",
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
        ]
    }
}
```

### Deleting a cluster

```
aws ecs delete-cluster --cluster <CLUSTER-NAME>
aws ecs delete-cluster --cluster deepdive
```

### Obtaining info about the clusters

```
aws ecs list-clusters
```

```
aws ecs describe-clusters --clusters <CLUSTER-NAME>
aws ecs describe-clusters --clusters deepdive
```

### Check the container instances for a given cluster

```
aws ecs list-container-instances --cluster <CLUSTER-NAME>
aws ecs list-container-instances --cluster deepdive
```


## Creating a container instance with CLI

** Note #1 ** look at the AWS documentation for the ECS-optimized AMI to use
** NOte #2 ** using a custom security group ``ecs-cluster-default-group (sg-0ad454da0eef085e0)`` with SSH and HTTP traffic enabled 

aws ec2 run-instances --image-id ami-074dc9dd588b6ea52 --count 1 --instance-type t2.micro \
    --iam-instance-profile Name=ECSCourse-InstanceRole --key-name MyEC2KeyPair --security-group-ids sg-0ad454da0eef085e0 \
    --user-data copy-ecs-config-to-s3
 
 Look for the instance ID (``i-062a5eda20c1bd554`` in my case):

```
aws ec2 describe-instance-status --instance-id <INSTANCE-ID>
aws ec2 describe-instance-status --instance-id i-062a5eda20c1bd554
```

```
aws ec2 terminate-instances --instance-ids <INSTANCE-ID>
aws ec2 terminate-instances --instance-ids i-062a5eda20c1bd554
```

