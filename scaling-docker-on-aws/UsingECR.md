# Using ECR 

A managed private registry from AWS. Note that image repositories on AWS assume the form ``<aws_account_id>.dkr.ecr.<region>.amazonaws.com/<REPO-NAME>``.

## Login

Open a terminal and type:
```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ $(aws ecr get-login --no-include-email)
```
This will output something like the following:

```
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /home/mario/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

From now on, you can run your docker push/pull commands while being authenticated with the ECR repository.


Note that, depending your AWS region of choice, your private ECR's URL will be something like:
```
https://015589054601.dkr.ecr.eu-central-1.amazonaws.com
```

## Create a new repository

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecr create-repository --repository-name deepdive/nginx
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:eu-central-1:015589054601:repository/deepdive/nginx",
        "registryId": "015589054601",
        "repositoryName": "deepdive/nginx",
        "repositoryUri": "015589054601.dkr.ecr.eu-central-1.amazonaws.com/deepdive/nginx",
        "createdAt": 1579880777.0,
        "imageTagMutability": "MUTABLE",
        "imageScanningConfiguration": {
            "scanOnPush": false
        }
    }
}
```

## Describe a repository

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecr describe-repositories --repository-name deepdive/nginx
{
    "repositories": [
        {
            "repositoryArn": "arn:aws:ecr:eu-central-1:015589054601:repository/deepdive/nginx",
            "registryId": "015589054601",
            "repositoryName": "deepdive/nginx",
            "repositoryUri": "015589054601.dkr.ecr.eu-central-1.amazonaws.com/deepdive/nginx",
            "createdAt": 1579880777.0,
            "imageTagMutability": "MUTABLE",
            "imageScanningConfiguration": {
                "scanOnPush": false
            }
        }
    ]
}
```

## List images within a given repository

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecr list-images --repository-name deepdive/nginx
{
    "imageIds": []
}
```

## Pushing images to remote registry

Just pull an image of your choice, tag it with your repository name (repository URI) and then push it!

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ docker pull nginx:1.17.8
1.17.8: Pulling from library/nginx
...
sha256:70821e443be75ea38bdf52a974fd2271babd5875b2b1964f05025981c75a6717
Status: Downloaded newer image for nginx:1.17.8
docker.io/library/nginx:1.17.8
docker pull nginx:1.17.8
```

Now tag it:
```
nginx                                                                            mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ docker tag nginx:1.17.8 015589054601.dkr.ecr.eu-central-1.amazonaws.com/deepdive/nginx:1.17.8
```
```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ docker image ls
REPOSITORY                                                                       TAG                 IMAGE ID            CREATED             SIZE
015589054601.dkr.ecr.eu-central-1.amazonaws.com/deepdive/nginx                   1.17.8              5ad3bd0e67a9        2 days ago          127MB
nginx                                                                            1.17.8              5ad3bd0e67a9        2 days ago          127MB
...
```
Now you can push the tagged image to our remote repository (this may take a while:
```
docker push 015589054601.dkr.ecr.eu-central-1.amazonaws.com/deepdive/nginx
```

Now, look at the repository for confirmation:
```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecr list-images --repository-name deepdive/nginx
{
    "imageIds": [
        {
            "imageDigest": "sha256:401ff5d136d690b2eaf61055aabdb7b4bc2ed8114fb5e423963249db7ac0188e",
            "imageTag": "1.17.8"
        }
    ]
}
```

# Update the task definition image 
Now we can change our web-task-definition.json in order to use the image stored on our repository:
```
{
  "containerDefinitions": [
    {
      "name": "nginx",
      "image": "015589054601.dkr.ecr.eu-central-1.amazonaws.com/deepdive/nginx:1.17.8",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80
        }
      ],
      "memory": 50,
      "cpu": 102
    }
  ],
  "family": "web"
}
```

Now we can update the task definition
```
aws ecs register-task-definition --cli-input-json file://web-task-definition-ecr.json
```

And run a new task on the cluster:
```
aws ecs run-task --cluster deepdive --task-definition web --count 1

```

