# Services

## Starting a service 
```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs create-service --cluster deepdive --service-name web --task-definition web --desired-count 1
{
    "service": {
        "serviceArn": "arn:aws:ecs:eu-central-1:015589054601:service/web",
        "serviceName": "web",
        "clusterArn": "arn:aws:ecs:eu-central-1:015589054601:cluster/deepdive",
        "loadBalancers": [],
        "serviceRegistries": [],
        "status": "ACTIVE",
        "desiredCount": 1,
        "runningCount": 0,
        "pendingCount": 0,
        "launchType": "EC2",
        "taskDefinition": "arn:aws:ecs:eu-central-1:015589054601:task-definition/web:2",
        "deploymentConfiguration": {
            "maximumPercent": 200,
            "minimumHealthyPercent": 100
        },
        "deployments": [
            {
                "id": "ecs-svc/7238516577444642416",
                "status": "PRIMARY",
                "taskDefinition": "arn:aws:ecs:eu-central-1:015589054601:task-definition/web:2",
                "desiredCount": 1,
                "pendingCount": 0,
                "runningCount": 0,
                "createdAt": 1579875950.897,
                "updatedAt": 1579875950.897,
                "launchType": "EC2"
            }
        ],
        "events": [],
        "createdAt": 1579875950.897,
        "placementConstraints": [],
        "placementStrategy": [],
        "schedulingStrategy": "REPLICA",
        "enableECSManagedTags": false,
        "propagateTags": "NONE"
    }
}
```

## List running service

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs list-services --cluster deepdive
{
    "serviceArns": [
        "arn:aws:ecs:eu-central-1:015589054601:service/web"
    ]
}
```

## Get detailed info about the running service

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs describe-services --cluster deepdive --services web
{
    "services": [
        {
            "serviceArn": "arn:aws:ecs:eu-central-1:015589054601:service/web",
            "serviceName": "web",
            "clusterArn": "arn:aws:ecs:eu-central-1:015589054601:cluster/deepdive",
            "loadBalancers": [],
            "serviceRegistries": [],
            "status": "ACTIVE",
            "desiredCount": 1,
            "runningCount": 1,
            "pendingCount": 0,
            "launchType": "EC2",
            "taskDefinition": "arn:aws:ecs:eu-central-1:015589054601:task-definition/web:2",
            "deploymentConfiguration": {
                "maximumPercent": 200,
                "minimumHealthyPercent": 100
            },
            "deployments": [
                {
                    "id": "ecs-svc/7238516577444642416",
                    "status": "PRIMARY",
                    "taskDefinition": "arn:aws:ecs:eu-central-1:015589054601:task-definition/web:2",
                    "desiredCount": 1,
                    "pendingCount": 0,
                    "runningCount": 1,
                    "createdAt": 1579875950.897,
                    "updatedAt": 1579875966.0,
                    "launchType": "EC2"
                }
            ],
            "events": [
                {
                    "id": "543f98f6-1c49-4b61-9dca-17254a8cc070",
                    "createdAt": 1579875966.015,
                    "message": "(service web) has reached a steady state."
                },
                {
                    "id": "c1ebec9e-1950-4d97-9bdf-c607d270b9e3",
                    "createdAt": 1579875956.03,
                    "message": "(service web) has started 1 tasks: (task 0393892b-1f7a-4df1-85e0-db1a2e1f0575)."
                }
            ],
            "createdAt": 1579875950.897,
            "placementConstraints": [],
            "placementStrategy": [],
            "schedulingStrategy": "REPLICA",
            "enableECSManagedTags": false,
            "propagateTags": "NONE"
        }
    ],
    "failures": []
}
```

## Update a service configuration

We want to have more instances, so we update the service configuration:
```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs update-service --cluster deepdive --service web --task-definition web --desired-count 2
{
    "service": {
        "serviceArn": "arn:aws:ecs:eu-central-1:015589054601:service/web",
        "serviceName": "web",
        "clusterArn": "arn:aws:ecs:eu-central-1:015589054601:cluster/deepdive",
        "loadBalancers": [],
        "serviceRegistries": [],
        "status": "ACTIVE",
        "desiredCount": 2,
        "runningCount": 1,
        "pendingCount": 0,
        "launchType": "EC2",
        "taskDefinition": "arn:aws:ecs:eu-central-1:015589054601:task-definition/web:2",
...
    }
}
```

## Delete a service

First of all set the desired count to 0 (otherwise you won't be able to delete it):
```
aws ecs update-service --cluster deepdive --service web --task-definition web --desired-count 0
```

Then you can delete it:
```
```

## Create a JSON service skeleton for CLI

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs create-service --generate-cli-skeleton
{
    "cluster": "",
    "serviceName": "",
    "taskDefinition": "",
    "loadBalancers": [
        {
            "targetGroupArn": "",
            "loadBalancerName": "",
            "containerName": "",
            "containerPort": 0
        }
    ],
    "serviceRegistries": [
        {
            "registryArn": "",
            "port": 0,
            "containerName": "",
            "containerPort": 0
        }
    ],
    "desiredCount": 0,
    "clientToken": "",
...
}

```

Now you can delete it:
```
aws ecs delete-service --cluster deepdive --service web
```
