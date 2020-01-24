# Terminate your cluster and clean up things

```
export EC2_INSTANCE_ID="i-0091d1d5dba03472a"
aws ec2 terminate-instances --instance-ids $EC2_INSTANCE_ID
```

```
aws s3 rm s3://links-deepdive --recursive
aws s3api delete-bucket --bucket links-deepdive
```

```
mario@SCALASM-NB:~/src/courses/scaling-docker-on-aws/deepdive$ aws ecr delete-repository --repository-name deepdive/nginx --force
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:eu-central-1:015589054601:repository/deepdive/nginx",
        "registryId": "015589054601",
        "repositoryName": "deepdive/nginx",
        "repositoryUri": "015589054601.dkr.ecr.eu-central-1.amazonaws.com/deepdive/nginx",
        "createdAt": 1579880777.0
    }
}
```

```
aws ecs delete-cluster --cluster deepdive
```