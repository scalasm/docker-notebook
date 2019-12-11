# AWS

See the AWS ECR doc for reference but 

* The ECR default repository name template is ``<aws_account_id>.dkr.ecr.<region>.amazonaws.com/<REPO-NAME>``


# Create an image repo
```
aws ecr create-repository --repository-name <REPO-NAME> --region <REGION>
aws ecr create-repository --repository-name awscourse
```
will create a repository called ``321723152483.dkr.ecr.eu-central-1.amazonaws.com/awscourse``

# Login 
```
mario@scalasm-xps:~/src/AWSCourse/awscourse-docker-sample$ $(aws ecr get-login --no-include-email)
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /home/mario/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

# Pushing images to repository

```
docker push 321723152483.dkr.ecr.eu-central-1.amazonaws.com/awscourse:1.0
```
