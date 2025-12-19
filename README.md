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

Tuve que:

* reinstalar curl dentro del test-pod
* verificar endpoints del Service
* corregir un `ImagePullBackOff`
* validar coredns en running state

### ✔ 6. Ingress configurado y funcionando

* Se creó un archivo ingress.yaml con las reglas de enrutamiento.
* Traefik detectó y aplicó la regla para el host localhost.
* El tráfico HTTP externo entra al clúster y llega al Service hello-service.

Depuración avanzada

* Durante este día se resolvieron problemas relacionados con:
* ImagePullBackOff por fallas de red.
* Reinicio de CoreDNS.
* Re-creación de Pods para tomar imágenes locales.
* Validación del funcionament del Ingress con Traefik.


¡Ingress funcionando y tráfico HTTP fluyendo correctamente! 🎉

### ✔ 7. Reconstrucción completa del clúster

* Durante este día el clúster presentó fallas críticas:
* ImagePullBackOff incluso para imágenes pequeñas
* Pod de pruebas (dns-check) también fallaba
* Fallas de DNS interno
* Traefik no podía enrutar al Service porque no existían endpoints saludables


La causa: k3d perdió conectividad externa y DNS interno se corrompió, por lo cual el cluster no podía descargar imágenes ni resolver dominios.

#### Solución implementada

Se recreó el clúster desde cero:

- k3d cluster delete dev
- k3d cluster create dev --servers 1 --agents 0 --port "80:80@loadbalancer"


Después se verificó conectividad con:

- kubectl run dns-check --image=alpine --restart=Never -- sh -c "apk add bind-tools >/dev/null && nslookup google.com"

Una vez confirmado el acceso a Internet, se aplicó nuevamente toda la infraestructura


####  Cómo desplegar todo hasta ahora
```
kubectl apply -f infra/k8s/configmap-html.yaml
kubectl apply -f infra/k8s/secret.yaml
kubectl apply -f infra/k8s/deployment.yaml
kubectl apply -f infra/k8s/service.yaml
kubectl apply -f infra/k8s/service-clusterip.yaml
kubectl apply -f infra/k8s/ingress.yaml
```
---
Ver Pods:

```
kubectl get pods
```
---
Ver Service:

```
kubectl get svc
```
---
Probar DNS interno con test-pod:

```
kubectl run test-pod --image=alpine -- sleep 999999
kubectl exec -it test-pod -- sh
apk add curl
curl hello-service
```
---
Resultado

* Pod del Deployment en estado Running
* DNS interno de Kubernetes funcionando
* CoreDNS operativo
* Ingress de Traefik nuevamente accesible en http://localhost
* Endpoints del Service correctos y en estado Ready

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
    ingress.yaml
```
---

### ✔ 8. Lectura de Logs para saber si hay errores (Del ruido a lo real)

###### En caso de tener problemas con el cluster, pods, y manifiestos, elimanos y volvemos a crear el cluster y aplicamos los manifiestos que hasta aquí llevamos 🤓

Desde mi deployment actual:

```
kubectl logs deployment/hello-deployment
```
---

Vayamos a lo importante, leer accesos:

```
kubectl logs deployment/hello-deployment --tail=50
```
---

Escalemos el deployment, listamos los pods y vemos los logs de alguno de ellos:

```
kubectl scale deployment hello-deployment --replicas=2
kubectl get pods
kubectl logs hello-deployment-c58964-46283
```
---

Verifiquemos con Traefik los logs de ingress:

```
kubectl logs -n kube-system deployment/traefik
```
---

Veamos las metricas del cluster:

```
kubectl top nodes
kubectl top pods
```
---

Borremos uno o los pods que tengamos, esto dependerá de la necesidad y para el aprendizaje:

```
kubectl delete pod -l app=hello
```

## 🔮 Siguiente paso: Día 9

Gestión correcta de configuraciones por entorno:

- ConfigMap vs Secret
- Override por entorno
- Errores comunes en producción


---

<p align="center">
  <strong>🍀 Miriam Zamora · Backend & DevOps in Progress 🍀</strong><br/>
  <em>Code. Ship. Repeat.</em><br/>
  <em>Building systems that don’t break.</em>
</p>