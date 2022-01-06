# sending-metrics-to-thanos-from-remote-cluster-

# create private s3 bucket in aws and attach following policy
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Statement",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": [
                "s3:ListBucket",
                "s3:GetObject",
                "s3:DeleteObject",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::Mention your bucket name*",
                "arn:aws:s3:::Mention your bucket name"
            ]
        }
    ]
}
```
```
# create one values.yaml file place below content
objstoreConfig: |-
  type: s3
  config:
    bucket: Mention your s3 bucket name
    endpoint: s3.us-east-1.amazonaws.com
    access_key: Mention your aws account key
    secret_key: "Mention your aws account key"
    insecure: true 
minio:
  enabled: false
receive:
  enabled: true
  ingress:
    enabled: true
    hostname: thanos-receive.fvwcfhmltaiiqzzhmbns.apis.klusternetes.com 
```

```
# Install Thanos on server
helm repo add bitnami https://charts.bitnami.com/bitnami
helm search repo bitnami
helm install thanos bitnami/thanos -f values.yaml
```
```
# create ingress for thanos query 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
  name: annotated
spec:
  ingressClassName: nginx
  rules:
  - host: thanos-query.your domain name
    http:
      paths:
      - backend:
          service:
            name: Mention thanos-query service name
            port:
              number: 9090
        path: /
        pathType: Prefix
```
```
# Access thanos query using host name in the ingress file
thanos-query.your domain name
```
```

# Install Prometheus on another server
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/prometheus -f values.yaml
```


