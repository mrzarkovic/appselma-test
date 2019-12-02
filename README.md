# Deploy Docker App to AWS EB

## Project setup

### Folder structure

```
 root
 ├── Dockerrun.aws.json
 ├── .ebextensions
 │ └── environmentvariables.config <- Environment variables
 └── .elasticbeanstalk
    ├── config.yml
    ├── Dockerfile.local
    └── logs
```

#### Dockerrun.aws.json

```
{
  "AWSEBDockerrunVersion": "1",
  "Volumes": [
    {
      "HostDirectory": "/var/app/appselma-app",
      "ContainerDirectory": "/var/www/html/storage"
    }
  ],
  "Image": {
    "Name": "430864128097.dkr.ecr.eu-west-1.amazonaws.com/appselma:staging",
    "Update": "true"
  },
  "Ports": [
    {
      "ContainerPort": 80,
      "HostPort": 80
    }
  ]
}

```

#### .ebextensions/environmentvariables.config

```
option_settings:
  - option_name: APP_NAME
    value: AppSelMa
  - option_name: APP_ENV
    value: local
  - option_name: APP_KEY
    value: base64:iVI3KT2o+0W1rzRBTuJ0l0a8PqB4y132RBssbrqhKd9=
  - option_name: APP_DEBUG
    value: true
  - option_name: APP_URL
    value: http://appselma.i2vf2dxd2u.eu-central-1.elasticbeanstalk.com <- UPDATE THIS!
  - option_name: LOG_CHANNEL
    value: stack
  - option_name: DB_CONNECTION
    value: mysql
  - option_name: DB_HOST
    value: appselma-stag.cxyt1mnizojq.eu-west-1.rds.amazonaws.com
  - option_name: DB_PORT
    value: 3306
  - option_name: DB_DATABASE
    value: appselma
  - option_name: DB_USERNAME
    value: appselma_user
  - option_name: DB_PASSWORD
    value: appselma_password
  - option_name: BROADCAST_DRIVER
    value: log
  - option_name: CACHE_DRIVER
    value: file
  - option_name: QUEUE_CONNECTION
    value: database
  - option_name: SESSION_DRIVER
    value: file
  - option_name: SESSION_LIFETIME
    value: 120
  - option_name: MAIL_DRIVER
    value: smtp
  - option_name: MAIL_HOST
    value: smtp.mailtrap.io
  - option_name: MAIL_PORT
    value: 2525
  - option_name: MAIL_USERNAME
    value: username
  - option_name: MAIL_PASSWORD
    value: password
  - option_name: MAIL_ENCRYPTION
    value: tls
  - option_name: MAIL_FROM_NAME
    value: name
  - option_name: MAIL_FROM_ADDRESS
    value: address
  - option_name: CLIENT_ID
    value: 10
  - option_name: CLIENT_SECRET
    value: HnwFyzGfmj7YU884EJH2dkYxzumLdAqOJlkZTd9U
  - option_name: TDL_API_URL
    value: https://staging-tdl.com
  - option_name: TDL_API_ACCESS_TOKEN
    value: ck0wr01e200040qpg97l4flir
  - option_name: TDL_API_HOST
    value: staging-tdl.com
  - option_name: WEPOW_API_URL
    value: https://api.wopow.co
  - option_name: WEPOW_API_USERNAME
    value: username
  - option_name: WEPOW_API_PASSWORD
    value: password

```

## Project initialization

- Initialize App
  `$ eb init -i` [Example](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb3-init.html#eb3-initexample)
- Create EB App on AWS
  `$ eb create appselma`
- (Optional) Update App (uses latest git commit)
  `$ eb deploy`
- (Don't do this if not necessary) Delete all resources
  `$ eb terminate appselma`

## CI Workflow

Run this in your CI/CD build script:

- Install dependency tools

  `$ apk add --no-cache --virtual .build-deps g++ python3-dev libffi-dev openssl-dev && apk add --no-cache --update python3 && pip3 install --upgrade pip setuptools`

- Install AWS CLI

  `$ pip3 install awscli --upgrade --user`

- Install AWS EB CLI

  `$ pip install awsebcli --upgrade --user`

- Configure CLIs

  `$ export PATH=$PATH:/root/.local/bin`
  `$ export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID`
  `$ export AWS_SECRET_ACCESS_KEY=$AWS_ACCESS_KEY_SECRET`
  `$ aws configure set default.region $AWS_REGION`

- Update App
  `$ eb deploy`
