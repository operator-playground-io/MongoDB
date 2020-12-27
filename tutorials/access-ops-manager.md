### Access Ops Manager

Open your Ops Manager application in a browser to complete configuration. Locate the URL of the LoadBalancer service which was created by the Operator.

```execute
kubectl get svc ops-manager-svc-ext -o jsonpath='{.status.loadBalancer.ingress[0].hostname}' -n mongodb
```

You may need to wait for some time until the DNS gets populated for the Load Balancer address.

Open the link http://<elb-url>:8080 in your browser. Login to Ops Manager using the credentials you specified in the secret created and pass the wizard steps to finish Ops Manager configuration. You’ll reach the admin page.

