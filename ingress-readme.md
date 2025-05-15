Sure! Here's your README.md content formatted for your project:

````markdown
# Minikube Ingress with SSL using Helm and Self-Signed Certificates (Dev Environment)

## Overview

This guide explains how to enable ingress in Minikube, deploy your app using Helm with custom ingress resources, and set up SSL using self-signed certificates for local development environments.

---

## Steps

### 1. Enable Minikube Ingress Addon

```bash
minikube addons enable ingress
````

### 2. Get Minikube Load Balancer IP and Update `/etc/hosts`

Get Minikube IP (the LoadBalancer IP):

```bash
minikube ip
# Example output: 192.168.49.2
```

Edit your `/etc/hosts` file to point your local domain to the Minikube IP:

```
192.168.49.2   dev.example.com
```

---

### 3. Deploy Your App with Helm Including Ingress

* Use Helm charts for your app in different namespaces (`dev`, `prod`).
* Include your custom `ingress.yaml` in the Helm chart.
* Set `ingress.hosts` in your Helm values to `dev.example.com`.

Example snippet in `dev-values.yaml`:

```yaml
ingress:
  enabled: true
  hosts:
    - host: dev.example.com
      paths:
        - /
  tls:
    - hosts:
        - dev.example.com
      secretName: dev-example-tls
certManager:
  issuerName: selfsigned
```

---

### 4. Set Up Cert-Manager Self-Signed Issuer

Create a self-signed cluster issuer (run once):

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: selfsigned
spec:
  selfSigned: {}
```

Apply it:

```bash
kubectl apply -f selfsigned-clusterissuer.yaml
```

---

### 5. Deploy Helm Chart with Values Including Self-Signed Cert

This will create a Certificate resource using the `selfsigned` issuer, which generates a self-signed certificate and stores it in the TLS secret `dev-example-tls`.

---

### 6. Verify

* Check your Ingress resource:

```bash
kubectl describe ingress dev.example.com -n dev
```

* Check the Certificate and TLS Secret:

```bash
kubectl get certificates -n dev
kubectl get secret dev-example-tls -n dev
```

* Test with curl or your browser:

```bash
curl -vk https://dev.example.com
```

---

## Notes

* Browsers will warn about self-signed certificates. For local development, this is expected unless you add the CA certificate to your trusted store.
* For production environments, use a real certificate issuer such as Let's Encrypt with your valid domain.
* Run `minikube tunnel` to expose LoadBalancer services on your local machine.

```bash
sudo minikube tunnel
```

Keep this terminal open while accessing your services.

---

Feel free to customize the Helm values and ingress rules for your environment!

---

# Enjoy your local Minikube HTTPS setup!

```

Would you like me to save this as a file for you?
```
