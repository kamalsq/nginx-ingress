# Basic-authentication-on-nginx-ingress
Install NGINX ingress controller using :
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.5/deploy/static/provider/cloud/deploy.yaml

Create a ingress-service.yaml file (adding up in repository ) :
kubectl create -f ingress-service.yml

Create a deployment and service for your application (I created nginx service) :
Two files : deploy.yml and svc.yml 
kubectl apply -f deploy.yml 
kubectl apply -f svc.yml 

Create a ingress.yml file for your ingress : ingress.yml
kubectl apply -f ingress.yml 
Access your application using your sub-domain.

Now, let's set up a basic authentication using htpasswd 
before doing that install apache2-utils using these commands:
sudo apt-get update -y
sudo apt-get install -y apache2-utils

Let's create an auth file with username and password:

$ htpasswd -c auth <username>
New password: <password>
Re-type new password:
Adding password for user <username>

Create k8s secret :
$ kubectl -n ingress-nginx create secret generic basic-auth --from-file=auth

secret "basic-auth" created

Verify the secret :
$ kubectl get secret basic-auth -o yaml

Output :
apiVersion: v1
data:
  auth: Zm9vOiRhcHIxJE9GRzNYeWJwJGNrTDBGSERBa29YWUlsSDkuY3lzVDAK
kind: Secret
metadata:
  name: basic-auth
  namespace: ingress-nginx
type: Opaque
  
Use following annotations in our ingress manifest by updating the ingress manifest :
 
sudo vim ingress.yml 
add these annotations=
  
annotations:
      nginx.ingress.kubernetes.io/auth-type: basic
      nginx.ingress.kubernetes.io/auth-secret: basic-auth
      nginx.ingress.kubernetes.io/auth-realm: "squareops Authentication Required - squareops"

Now that ingress is updated, hit the URL again and as shown in the image below we are asked for authentication

