# Deliverable ⓹ Content Delivery Network Deployment: JWT Pizza

![course overview](../courseOverview.png)

Now that we know how to deploy static content using CloudFront and S3 using GitHub Actions, it is time to move our CDN hosting from GitHub Pages to AWS CloudFront.

## CDN distribution

Set up your AWS S3 and CloudFront configuration as described in the [AWS CloudFront](../awsCloudFront/awsCloudFront.md) instruction. Your AWS configuration should consist of a private S3 bucket that contains your static frontend code. A CloudFront distribution then exposes the frontend to the world using a secure HTTP connection defined by your DNS hostname.

### Demonstrating completion

Once completed, your DNS entry should point to your CloudFront endpoint. You can verify this with the `dig` or `nslookup` utility.

```sh
dig +short CNAME pizza.byucsstudent.click

d3pl23dqq9jlpy.cloudfront.net.
```

## Secure deployment

Alter your GitHub Actions deployment process using the [AWS S3 Deployment](../awsS3Deployment/awsS3Deployment.md) instruction such that it updates S3 when files are pushed to your fork of `jwt-pizza`. Your GitHub CI pipeline deploys your frontend code through a secure connection that is authenticated using OIDC that follows the principle of least privilege, by exposing only the necessary access.

### 404 page

You need to remove the creation of the `404.hml` that GitHub Pages used to handle when a user refreshes the browser. You can do that by deleting the creation of the file from your workflow.

```yml
cp dist/index.html dist/404.html
```

Instead you need to configure CloudFront to return the `index.html` file whenever a 404 or 403 error is encountered.

![Handle 404](handle404.png)

This will cause the React DOM Router to get loaded and properly route back to the correct React component for the path.

### Demonstrating completion

Once completed, your repository's GitHub Actions workflow history should demonstrate a successful deployment to S3.

![Workflow output](workflowOutput.png)

## ☑ Assignment

Demonstrate your mastery of the concepts for this deliverable, complete the following.

1. Create a secure S3 bucket to host the frontend static files.
1. Create a CloudFront distribution.
1. Alter your DNS record in Route 53 to point to the CloudFront distribution.
1. Create the IAM policies, roles, and identity provider definitions necessary to secure access for deployment.
1. Alter your GitHub Actions workflow to update S3 and CloudFront instead of deploying to GitHub Pages.

Once this is all working, submit JWT Pizza URL of your fork of the `jwt-pizza` repository to the Canvas assignment. This should look something like this:

```txt
https://pizza.cs329.click
```

### Rubric

| Percent | Item                                                                              |
| ------- | --------------------------------------------------------------------------------- |
| 10%     | Strong GitHub commit history that documents your work in your fork of `jwt-pizza` |
| 48%     | Secure CloudFront deployment based on S3 bucket                                   |
| 2%      | Properly handles browser refresh React DOM Routing                                |
| 40%     | Updated GitHub Action workflow deploying to S3 bucket                             |
