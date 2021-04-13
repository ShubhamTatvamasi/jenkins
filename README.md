# jenkins

Add help repo:
```bash
helm repo add jenkins https://charts.jenkins.io
helm repo update
```

Install Jenkins:
```bash
helm install jenkins jenkins/jenkins \
  --create-namespace \
  --namespace jenkins \
  --set controller.adminPassword=password
```
ID: `admin` Pass: `password`

Ingress value for jenkins:
```bash
kubectl apply -f - << EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: jenkins
  namespace: jenkins
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
      - jenkins.k8s.shubhamtatvamasi.com
    secretName: jenkins-tls
  rules:
  - host: jenkins.k8s.shubhamtatvamasi.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: jenkins
            port:
              number: 8080
EOF
```

---

### Deployment

create a deployment and service:
```bash
kubectl create deployment jenkins --image=jenkins/jenkins:lts
kubectl expose deployment jenkins --port=8080 --name=jenkins
```

get admin password:
```bash
kubectl exec -it deploy/jenkins -- cat /var/jenkins_home/secrets/initialAdminPassword
```

delete everything:
```bash
kubectl delete deploy/jenkins svc/jenkins ing/jenkins
```
