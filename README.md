# epam-aws-s3-deploy-static-website
This is a simple practice on how to use github actions ci_cd to deploy a simple static website in aws s3

## Steps
1. Create bucket using AWS S3 service.
2. Make bucket publicly readable and edit bucket resource based policy to allow any principal to read the objects in the bucket
~~~
    {
        "Version": "2012-10-17",
        "Statement": [
          {
              "Sid": "Statement1",
              "Effect": "Allow",
              "Principal": "*",
              "Action": "s3:GetObject",
              "Resource": "arn:aws:s3:::danny-herrera-4637/*"
          }
        ]
    }
~~~
3. Acording to security best practices, configure IAM role to permit your github workflow to upload files into your newly created bucket without the need to store long term user credentials as secrets. For doing so, first need to configure OpenID Connect https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services
5. configure github workflow to upload static website files to s3 bucket on git push event (See code in .gitub/workflows/deploy-static-website-github-actions.yml)
5. Configure acc
