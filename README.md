# DevOps Women Network

Plataforma diseñada con las siguientes herramientas:
- Django REST Framework
- Docker
- Kubernetes
- AWS
- Terraform
- CI/CD con GitHub Actions

## 📘 Proyecto: DevOps Women Network — Kubernetes Learning Journey

Este repositorio documenta mi camino de aprendizaje en DevOps, creando paso a paso un sistema desplegado en Kubernetes. Cada día incorpora un nuevo concepto y una mejora al proyecto.

---

## 🟣 Progreso actual (Día 1–5)

Hasta este punto he logrado:

### ✔ 1. Deployment funcional

* Usa imagen `nginx:latest`
* Incluye variables de entorno desde Secrets
* Monta un ConfigMap como volumen en `/usr/share/nginx/html`

### ✔ 2. ConfigMap operativo

Se usa para servir contenido HTML personalizado dentro de Nginx.

### ✔ 3. Secret cifrado en base64

Almacena una variable sensible (`DB_PASSWORD`) que se inyecta en el Pod.

### ✔ 4. Service tipo ClusterIP

Provee networking interno entre Pods.

### ✔ 5. DNS interno funcionando

Validado mediante un Pod temporal (`test-pod`) con `curl` instalado.

### ✔ 6. Troubleshooting real

Tuve que:

* reinstalar curl dentro del test-pod
* verificar endpoints del Service
* corregir un `ImagePullBackOff`
* validar coredns en running state

Todo funcionando correctamente.

---

## 📂 Estructura del proyecto

```
infra/
  k8s/
    configmap-html.yaml
    deployment.yaml
    secret.yaml
    service.yaml
    service-clusterip.yaml
    ingress.yaml (próximo)
```

---

##  Cómo desplegar todo hasta ahora

```
kubectl apply -f infra/k8s/configmap-html.yaml
kubectl apply -f infra/k8s/secret.yaml
kubectl apply -f infra/k8s/deployment.yaml
kubectl apply -f infra/k8s/service-clusterip.yaml
```

Ver Pods:

```
kubectl get pods
```

Ver Service:

```
kubectl get svc
```

Probar DNS interno con test-pod:

```
kubectl run test-pod --image=alpine -- sleep 999999
kubectl exec -it test-pod -- sh
apk add curl
curl hello-service
```

---

## 🔮 Siguiente paso: Día 6

Implementar **Ingress** para exponer la aplicación vía Traefik en:

> [http://localhost](http://localhost)

---

## 🖋️ Firma
**🍀— Miriam Zamora · Backend & DevOps in Progress 🍀**  
*Code. Ship. Repeat.*  
*Building systems that don’t break.*