# One2OneTool

This repo includes a simple Node.js web application. 

The application makes use of a built-in JSON data file, whose filename can be specified in the “DATA_FILE” environment variable. If the environment variable is not found, then it will default to using “Questions.json”.

In order to test it, I had created a CI/CD pipeline in Jenkins to monitors the “release” and “staging” branches of the repository. The pipeline should be triggered on new commits and perform at least the following:

- The pipeline should build and run tests on the application
- Containerise and deploy the application on a public cloud instance
- The “staging” branch should use “Questions-test.json” as its input data file
- The “release” branch should use “Questions.json” as its input data file
- Versioning to differentiate the builds
- Pipeline should be stoped and send an email alert with relevant information if any tasks failed

# Github

## Clone the repository
Git clone using SSH if HTTPS didn't work.
```
git clone https://github.com/cheahhowong/one2onetool.git
git clone git@github.com:cheahhowong/one2onetool.git (for SSH)
```

# Jenkins

## How to use the Jenkins pipeline
1. Visit [Jenkins](http://ec2-18-136-100-249.ap-southeast-1.compute.amazonaws.com:8080/) hosted in AWS EC2 instances. 
2. Login with credential **Username = admin** & **Password = admin**
3. Click the Jenkins job with name `one2onetool`
4. Click either **staging** branch or **release** branch to view the pipeline

## To test **staging** branch use **Questions-test.json** as its input data file
``` 
git checkout staging 
```

Make some changes in any file (eg. Add a new empty line at the end of JenkinsFile)

```
git add .
git commit -m 'test'
git push origin staging
```
1. View the Jenkins [staging branch page](http://ec2-18-136-100-249.ap-southeast-1.compute.amazonaws.com:8080/job/one2onetool/job/staging/) to check if the deployment is success
2. If success, visit the [Web app](http://ec2co-ecsel-1l9l9yvh54yxb-1768850757.ap-southeast-1.elb.amazonaws.com:3000/) to view the web app.
3. It will show the questions and category as shown in the diagram below
![Questions-test](https://raw.githubusercontent.com/cheahhowong/one2onetool/master/screenshots/image2.png)

## To test **release** branch use **Questions.json** as its input data file
``` 
git checkout release 
```

Make some changes in any file (eg. Add a new empty line at the end of JenkinsFile)

```
git add .
git commit -m 'test'
git push origin release
```
1. View the Jenkins [release branch page](http://ec2-18-136-100-249.ap-southeast-1.compute.amazonaws.com:8080/job/one2onetool/job/release/) to check if the deployment is success
2. If success, visit the [Web app](http://ec2co-ecsel-1l9l9yvh54yxb-1768850757.ap-southeast-1.elb.amazonaws.com:3000/) to view the web app.
3. It will show the questions and category as shown in the diagram
![Questions](https://raw.githubusercontent.com/cheahhowong/one2onetool/master/screenshots/image1.png)

## To test the pipeline should send stopped and send an email alert
1. Visit Jenkins [setting](http://ec2-18-136-100-249.ap-southeast-1.compute.amazonaws.com:8080/manage/configure)
2. Scroll to **Extended E-mail Notification** section
3. Click **Advanced** to expand
4. Configure **SMTP server** & **SMTP Port** if you are not using gmail
5. Add a new **Crendentials** (**Kind: Username with password**) & use select it
6. Tick SSL
7. Scroll to next section E-mail Notification
8. Change **SMTP server** & **SMTP Port** if you are not using gmail
9. Configure **Use SMTP Authentication** part with the same email & password at **Extended E-mail Notification** section
10. Save the changes

``` 
git checkout release 
```

Make changes below in JenkinsFile 
1. Example: (Add a symbol * at any stages in JenkinsFile to break the code)
2. Change the **to** to your email address in line 84 in JenkinsFile

```
git add .
git commit -m 'test'
git push origin release
```

- The latest build in Jenkins [release branch page](http://ec2-18-136-100-249.ap-southeast-1.compute.amazonaws.com:8080/job/one2onetool/job/release/) should show build error
- You should received an email with Subject **[Jenkins Pipeline] Build Error**. The console output log is attached together with the email