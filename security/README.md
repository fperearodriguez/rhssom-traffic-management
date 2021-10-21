# rhossm-traffic-management
Some examples of security objects using the bookinfo application.

## Authorization Policies
For this case, the mTLS feature must be enabled across the Service Mesh.

### Ingress traffic
This Authorization Policy is applied to the **Ingress** gateway.

For this use case, the _bookinfo_ application must be deployed in the _bookinfo_ namespace and it is already exposed over HTTP and HTTPS with mTLS. For more information about how to expose it, see this [repository](https://github.com/fperearodriguez/rhssom-traffic-management#exposing-the-bookinfo-application).

Replace the $YOUR_EXTERNAL_IP and $YOUR_CN_CERTIFICATE variables according to your environment.

Create an Authorization Policy to restrict the ingress traffic:
```
oc apply -n istio-system -f ingress/ap-ingress.yaml
```


### Intra-mesh traffic
This Authorization Policy is applied to all the workloads deployed in the _bookinfo_ project.

The scope of this Authorization Policy is:
1. Deny all traffic by default.
2. Since the _bookinfo_ application is exposed, allow only the ingress gateway to reach the Kubernetes service of the application. No other application will be able to access to the _bookinfo_ application.
3. Since all the traffic inside the _bookinfo_ project is denied, the _bookinfo_ application is not working properly. The traffic between the different microservices should be allowed.

Create an Authorization Policy to deny all the traffic in the _bookinfo_ project and visit Kiali:
```
oc apply -n bookinfo -f intra-mesh/1-ap-deny-all.yaml
```

Create an Authorization Policy to allow the traffic from the **ingress** gateway and visit Kiali:
```
oc apply -n bookinfo -f intra-mesh/2-ap-allow-ingress.yaml
```

Create an Authorization Policy to allow the traffic between the bookinfo components and visit Kiali:
```
oc apply -n bookinfo -f intra-mesh/3-ap-details.yaml
oc apply -n bookinfo -f intra-mesh/3-ap-ratings.yaml
oc apply -n bookinfo -f intra-mesh/3-ap-reviews.yaml
```


### Egress traffic
TODO