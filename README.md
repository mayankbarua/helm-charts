# CSYE 7374 - Spring 2020

## Team Information

| Name | NEU ID | Email Address |
| --- | --- | --- |
|Jagmandeep Kaur | 001426439|kaur.j@husky.neu.edu |  | | |
|Mayank Barua| 001475187| barua.m@husky.neu.edu|
|Yogita Patil| 001435442|patil.yo@husky.neu.edu |
|Prajesh Jain| 001409343| Jain.pra@husky.neu.edu|

## Run Helm Chart

To create Frontend and Backend charts

```bash
$ ansible-playbook -vvv create_helm_charts.yml --extra-vars "backendReleaseName=value imageBackend=value rdsConnection=value bucketName=value awsKey=value secretKey=value imageFrontend=value frontendReleaseName=value dockerString=value redis_sentinel_enabled=true/false redis_cluster_enabled=true/false redis_password=Redis_Password"
```

