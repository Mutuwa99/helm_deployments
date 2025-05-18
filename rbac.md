

---

#### 3. 🔐 Create a token (for Kubernetes v1.24+)

```bash
TOKEN=$(kubectl -n dev create token myapp-dev)
```

---

#### 4. 🌍 Get Minikube’s cluster endpoint

```bash
CLUSTER_ENDPOINT=$(kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}')
```

---

#### 5. 📦 Set credentials and context for your ServiceAccount

```bash
kubectl config set-credentials myapp-dev-user --token=$TOKEN

kubectl config set-context myapp-dev-context \
  --cluster=minikube \
  --user=myapp-dev-user \
  --namespace=dev

kubectl config use-context myapp-dev-context
```

---

#### ✅ 6. Test permissions

```bash
kubectl get pods               # ✅ should work
kubectl get pods -n kube-system # ❌ should be forbidden
kubectl create deployment foo --image=nginx # ❌ should be forbidden
```

---

Let me know if you want to allow more resources (like ConfigMaps or Secrets), or convert this into a script.
