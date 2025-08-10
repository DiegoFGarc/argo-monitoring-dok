# Observability stack (KPS + Loki) con Argo CD

Despliegue GitOps de:

- **kube-prometheus-stack** (Prometheus + Alertmanager + Grafana)
- **loki-stack** (Loki + Promtail)

Todo versionado en este repo con **Argo CD**.

---

## 📋 Prerrequisitos

- Cluster Kubernetes funcional.
- **Argo CD** instalado y corriendo (`argocd` namespace).
- Ingress controller (si vas a exponer Grafana por dominio).
- `kubectl` configurado contra tu cluster.
- (Opcional) CLI de Argo CD: `brew install argocd` o equivalente.

> Si no tienes Argo CD aún, sigue la guía oficial:  
> https://argo-cd.readthedocs.io/en/stable/getting_started/

---

## 🗂️ Estructura del repo

- argo/
- ---project-monitoring.yaml # AppProject de Argo (seguridad/scope)
- ---app-kps.yaml # Application: kube-prometheus-stack
- ---app-loki-stack.yaml # Application: loki-stack
- kps/
- ---values.yaml # Valores helm para kube-prometheus-stack (Grafana/Ingress/Datasources)
- loki-stack/
- ---values.yaml # Valores helm para Loki + Promtail
- ingress/
- ---grafana-ingress.yaml # (Opcional) Ingress crudo si no lo habilitas en values.yaml


---

## 🚀 Despliegue “desde cero”

> ⚠️ Cambia los dominios/secret names en `kps/values.yaml` si vas a exponer Grafana.

1. **Crear namespace de monitoreo** (Argo lo puede crear solo, pero lo dejo explícito):

```
kubectl create namespace monitoring --dry-run=client -o yaml | kubectl apply -f -
```

2. **Crear el proyecto de Argo** (scoping/seguridad):

```
bash kubectl apply -f argo/project-monitoring.yaml
```

3. **Desplegar Loki + Promtail con Argo:**

```
kubectl apply -f argo/app-loki-stack.yaml
```

4. **Desplegar kube-prometheus-stack con Argo:**

```
bash kubectl apply -f argo/app-kps.yaml
```

## 🧹 Desinstalación

```
kubectl delete -f argo/app-kps.yaml
kubectl delete -f argo/app-loki-stack.yaml
kubectl delete -f argo/project-monitoring.yaml
kubectl delete namespace monitoring
```