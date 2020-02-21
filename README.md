# CSYE 7374 - Spring 2020

## Team Information

| Name | NEU ID | Email Address |
| --- | --- | --- |
|Jagmandeep Kaur | 001426439|kaur.j@husky.neu.edu |  | | |
|Mayank Barua| 001475187| barua.m@husky.neu.edu|
|Yogita Patil| 001435442|patil.yo@husky.neu.edu |
|Prajesh Jain| 001409343| Jain.pra@husky.neu.edu|

## Run Helm Chart

To create back-end replica set

```bash
$ helm install RELEASENAME --set image.repository=baruamayank92/back-end:68d0d3ae99965283edb53a98e5b7295e8e4ddceb,namespace.createNamespace=true,configmap.RDS_CONNECTION_STRING=RDS_NAME  --debug ./back-end/
```

To create front-end replica set

```bash
$ helm install RELEASENAME --set image.repository=baruamayank92/front-end:68d0d3ae99965283edb53a98e5b7295e8e4ddceb,namespace.createNamespace=true,intiContainer.backendDependencyEndpoint=RELEASENAME-back-end.api.svc.cluster.local  --debug ./front-end/
```
