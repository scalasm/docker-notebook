
# Task definitions

## Register a task definition

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs register-task-definition --cli-input-json file://web-task-definition.json
{
    "taskDefinition": {
        "taskDefinitionArn": "arn:aws:ecs:eu-central-1:015589054601:task-definition/web:1",
        "containerDefinitions": [
            {
                "name": "nginx",
                "image": "nginx",
                "cpu": 1024,
                "memory": 50,
                "portMappings": [
                    {
                        "containerPort": 80,
                        "hostPort": 80,
                        "protocol": "tcp"
                    }
                ],
                "essential": true,
                "environment": [],
                "mountPoints": [],
                "volumesFrom": []
            }
        ],
        "family": "web",
        "revision": 1,
        "volumes": [],
        "status": "ACTIVE",
        "placementConstraints": [],
        "compatibilities": [
            "EC2"
        ]
    }
}
```

## List task definition families

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs list-task-definition-families
{
    "families": [
        "web"
    ]
}
```

## List task definitions

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs list-task-definitions
{
    "taskDefinitionArns": [
        "arn:aws:ecs:eu-central-1:015589054601:task-definition/web:1"
    ]
}
```
Note that *1* is the task definition revision number and may change over time (e.g., updates to task definition)

## Describe task definition

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs describe-task-definition --task-definition web
{
    "taskDefinition": {
        "taskDefinitionArn": "arn:aws:ecs:eu-central-1:015589054601:task-definition/web:1",
        "containerDefinitions": [
            {
                "name": "nginx",
                "image": "nginx",
                "cpu": 1024,
                "memory": 50,
                "portMappings": [
                    {
                        "containerPort": 80,
                        "hostPort": 80,
                        "protocol": "tcp"
                    }
                ],
                "essential": true,
                "environment": [],
                "mountPoints": [],
                "volumesFrom": []
            }
        ],
        "family": "web",
        "revision": 1,
        "volumes": [],
        "status": "ACTIVE",
        "placementConstraints": [],
        "compatibilities": [
            "EC2"
        ]
    }
}
```

## Deregister a task definition

```
aws ecs deregister-task-definition --task-definition web:1
```


## Generate a skeleton JSON file for the task definition

This is useful when you want to write your own container definition from scratch and you need a skeleton template to start from.
```
aws ecs register-task-definition --generate-cli-skeleton
```
