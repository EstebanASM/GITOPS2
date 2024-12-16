#  GITOPS2 con ArgoCD y Kubernetes


Este repositorio implementa un flujo **GitOps** utilizando **ArgoCD**, **Minikube** y **Kubernetes**. La aplicación consiste en una página simple **HTML** servida mediante **Nginx**, diseñada para mostrar un flujo completo de integración y despliegue continuo utilizando prácticas modernas de GitOps.

El objetivo de este proyecto es ayudarte a comprender cómo implementar y gestionar aplicaciones en Kubernetes usando **ArgoCD** y un clúster local gestionado con **Minikube**.

## 📋 Descripción

GitOps permite gestionar configuraciones y aplicaciones de Kubernetes declarativamente a través de Git como fuente de verdad. Usando **ArgoCD**, los cambios realizados en el repositorio se sincronizan automáticamente con el clúster de Kubernetes, asegurando que el estado deseado siempre se mantenga.

En este proyecto, desplegamos una aplicación web que muestra una página HTML personalizada, gestionada por **Nginx**, y demostramos cómo cada actualización en el repositorio se refleja automáticamente en el clúster gracias a **ArgoCD**.


## 📂 Estructura del repositorio

```plaintext
GITOPS/
│
├── index.html            # Pagina web
├── README.md             # Pasos del desarrollo
└── manifests/           # Archivos YAML de Kubernetes
    ├── deployment.yaml
    ├── service.yaml
    └── configmap.yaml


```

## 🎯 Requisitos previos

Antes de comenzar, asegúrate de tener instaladas las siguientes herramientas:

- Docker
- Kubectl
- Minikube
- ArgoCD CLI
- Git

## ⚙️ Configuración del entorno

### 1. Iniciar el clúster Kubernetes con Minikube

Crea e inicia un clúster local con Minikube:

```bash
minikube start --driver=virtualbox

```

Para exponer servicios tipo LoadBalancer, habilita el complemento de Minikube:

```bash
minikube addons enable ingress
minikube addons enable metrics-server

```

Verifica que el clúster esté en funcionamiento:

```bash
kubectl get nodes

```

### 2. Instalar ArgoCD

Instala ArgoCD en el clúster:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f argocd-install.yaml
```

Exponlo utilizando port-forward para acceder a la interfaz de ArgoCD:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

- URL de acceso: https://localhost:8080
- **Usuario:** `admin`
- **Contraseña:** Recupera la contraseña con:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### 3. Crear una aplicación en  ArgoCD

1. En la interfaz de ArgoCD, selecciona "Applications" y luego "New App".
2. Completa los detalles:
3. Nombre de la aplicación: nginx-app
4. Proyecto: default
5. Repositorio Git: https://github.com/EstebanASM/GITOPS2.git
7. Ruta: manifests/ (el directorio donde se encuentran los manifiestos).
8. Cluster: Selecciona tu clúster de Kubernetes.
9. Namespace: default (o el namespace que desees).

## 🌐 Acceder a la aplicación

### Opción 1: Minikube service

Usa el siguiente comando para exponer el servicio en Minikube:

```bash
minikube service gitops-web-app-service

```

Abre tu navegador en: `[http://localhost:8080](http://localhost:8080)`.

### Opción 2: Port-forward

Si prefieres acceder localmente sin usar Minikube service:

```bash
kubectl port-forward svc/gitops-web-app-service 8080:80

```

Localiza el puerto y accede con `[http://localhost:8080](http://localhost:8080)`.

## 🧪 Prueba el flujo GitOps

1. Realiza un cambio en el archivo **index.html** (por ejemplo, actualiza el texto).
2. Sube los cambios al repositorio Git:

```bash
git add .
git commit -m "Actualizar página GitOps Web App"
git push

```

3. ArgoCD detectará el cambio y sincronizará el clúster automáticamente.
4. Verifica la actualización en **http://localhost:8080**
