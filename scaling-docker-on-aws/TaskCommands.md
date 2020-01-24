# Running Tasks

## RunTask

We can run tasks on the cluster (the scheduler will find where to run it):
```
aws ecs run-task --cluster deepdive --task-definition web --count 1
```

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs list-tasks --cluster deepdive
{
    "taskArns": [
        "arn:aws:ecs:eu-central-1:015589054601:task/7eb78742-78d1-4b25-a353-02278f253563"
    ]
}
```

Tasks can then be stopped by specifying their Arns:

```
aws ecs stop-task --cluster deepdive --task "arn:aws:ecs:eu-central-1:015589054601:task/7eb78742-78d1-4b25-a353-02278f253563"
```
}

## StartTask

With *StartTask* we can choose which container instance to use.

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs list-container-instances --cluster deepdive
{
    "containerInstanceArns": [
        "arn:aws:ecs:eu-central-1:015589054601:container-instance/f904d331-83c7-48f9-9ef6-37a83acd3012"
    ]
}
```

And then:
```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs start-task --cluster deepdive --task-definition web --container-instances "arn:aws:ecs:eu-central-1:015589054601:container-instance/f904d331-83c7-48f9-9ef6-37a83acd3012"
{
    "tasks": [
        {
            "attachments": [],
            "availabilityZone": "eu-central-1b",
            "clusterArn": "arn:aws:ecs:eu-central-1:015589054601:cluster/deepdive",
            "containerInstanceArn": "arn:aws:ecs:eu-central-1:015589054601:container-instance/f904d331-83c7-48f9-9ef6-37a83acd3012",
            "containers": [
                {
                    "containerArn": "arn:aws:ecs:eu-central-1:015589054601:container/26c50982-6867-424a-8d75-e1782c68a5ed",
                    "taskArn": "arn:aws:ecs:eu-central-1:015589054601:task/131fc3cb-1bcf-426d-8a75-13658a9ad700",
                    "name": "nginx",
                    "image": "nginx",
                    "lastStatus": "PENDING",
                    "networkInterfaces": [],
                    "cpu": "102",
                    "memory": "50"
                }
...
            "taskArn": "arn:aws:ecs:eu-central-1:015589054601:task/131fc3cb-1bcf-426d-8a75-13658a9ad700",
            "taskDefinitionArn": "arn:aws:ecs:eu-central-1:015589054601:task-definition/web:5",
            "version": 1
}
```

Then we can use the *taskArn* to stop the started task:
```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecs stop-task --cluster deepdive --task "arn:aws:ecs:eu-central-1:015589054601:task/131fc3cb-1bcf-426d-8a75-13658a9ad700"
{
...
}
```

