# Nginx Ingress Controller Minikube

This is a small tutorial to deploy Nginx pods with a service and Ingress Controller to your local environment.
We are going to use dnsmask which is a easy to configure DNS forwarder, DHCP server software and router advertisement subsystem for small local networks.

![Nginx running](https://raw.githubusercontent.com/christi4n/k8s-dnsmask-ingress/master/assets/nginx-local.png)

We use Minikube for our local cluster.

It is a good practice to use a specific namespace for a particular project but for our tutorial, we are going to use the default namespace.

## Create Minikube Cluster under MacOS

```bash
$ minikube start --cpus 6 --memory 8192 --vm=true --driver=hyperkit
```

### Enable the NGINX Ingress controller
```bash
$ minikube addons enable ingress
```

### Verify that the NGINX Ingress controller is running
```bash
$ kubectl get pods --all-namespaces
```

### Create nginx deployment
```bash
$ kubectl apply -f ./deployment.yaml
```

### Create nginx service
```bash
$ kubectl apply -f ./service.yaml
```

### Create nginx ingress
```bash
$ kubectl apply -f ./ingress.yaml
```

### Install dnsmasq
```bash
$ brew install dnsmasq
```

### Start dnsmasq
```bash
$ sudo brew services start dnsmasq
```

### Get minikube IP for service
```bash
$ minikube service nginx-service --url
```
Even better, you could use :

```bash
$ kubectl get ep nginx-service
```
I have the following IP: 192.168.64.2
The result of this command on your local machine could be different.

### Configure dnsmasq

Add your fake domain name. I'm using "kube". Therefore, I can add everything I want for the subdomain.

```bash
$ sudo vi /usr/local/etc/dnsmasq.conf
```

Add the following line at the bottom of the file:
address=/.kube/192.168.64.2

### Restart dnsmasq
```bash
$ sudo brew services restart dnsmasq
```

### Only send .kube queries to dnsmasq

Beware: use the ip of your localhost.

```bash
sudo mkdir /etc/resolver
sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/kube'
```

### Check resolvers
```bash
scutil --dns
```

### Test DNS name
```bash
$ dig nginx.kube @127.0.0.1
```
