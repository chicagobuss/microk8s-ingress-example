# microk8s-ingress-example

Microk8s ingress example - taken from [here](https://kndrck.co/posts/microk8s_ingress_example) ([github](https://github.com/kendricktan/microk8s-ingress-example)) and updated to work with k8s as of 2024/03

## Steps
1. Install MicroK8s

Microk8s quickstart [link](https://microk8s.io/#quick-start)
```bash
sudo snap install microk8s --classic
microk8s.start
```
2. Install Ingress Dependent Resources
```bash
microk8s.kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/mandatory.yaml
```
3. Enable Ingress Addon
```bash
microk8s.enable ingress
```
4. Run a local Docker Registry

_You can skip this step if you wanna host your image on a public/private repository._
```bash
docker run -p 5000:5000 registry
```
5. Clone Example Repository And Build Docker Image
```bash
git clone https://github.com/chicagobuss/microk8s-ingress-example
cd microk8s-ingress-example

docker build . -t my-microk8s-app
docker tag my-microk8s-app localhost:5000/my-microk8s-app
docker push localhost:5000/my-microk8s-app
```
6. Run Applications And Ingress
```bash
microk8s.kubectl apply -f bar-deployment.yml
microk8s.kubectl apply -f foo-deployment.yml
microk8s.kubectl apply -f ingress.yml
```
7. Expose Deployments to Ingress

_If you skip this step you'll get a 503 service unavailable_
```bash
microk8s.kubectl expose deployment foo-app --type=LoadBalancer --port=8080
microk8s.kubectl expose deployment bar-app --type=LoadBalancer --port=8080
```
8. Testing Endpoint Out
```bash
curl -kL https://127.0.0.1/bar
curl -kL https://127.0.0.1/foo
```
