Terraform Script Documentation for NGINX Deployment
Objective
Create a Terraform script to deploy an NGINX web server on a Kubernetes cluster.

Requirements
Provider: Use the Kubernetes provider for Terraform.

Namespace: Deploy NGINX in a specific namespace (e.g., web).

Deployment:

Name: nginx-deployment

Replicas: 2

Container:

Image: nginx:latest

Port: 80

Service:

Name: nginx-service

Type: ClusterIP

Port: 80

Target Port: 80

Labels:

App: nginx

Environment: production

Terraform Configuration Structure
Provider Block:

Configure the Kubernetes provider with the required credentials and context.

Namespace Resource:

Create a Kubernetes namespace named web.

Deployment Resource:

Define a Kubernetes deployment for NGINX with the specified replicas, container image, and port.

Service Resource:

Define a Kubernetes service to expose the NGINX deployment internally within the cluster.

Example Input for AI
hcl
Copy
provider "kubernetes" {
  config_path = "~/.kube/config"  # Path to kubeconfig file
}

resource "kubernetes_namespace" "web" {
  metadata {
    name = "web"
  }
}

resource "kubernetes_deployment" "nginx" {
  metadata {
    name      = "nginx-deployment"
    namespace = kubernetes_namespace.web.metadata[0].name
    labels = {
      App         = "nginx"
      Environment = "production"
    }
  }

  spec {
    replicas = 2

    selector {
      match_labels = {
        App = "nginx"
      }
    }

    template {
      metadata {
        labels = {
          App = "nginx"
        }
      }

      spec {
        container {
          name  = "nginx"
          image = "nginx:latest"

          port {
            container_port = 80
          }
        }
      }
    }
  }
}

resource "kubernetes_service" "nginx" {
  metadata {
    name      = "nginx-service"
    namespace = kubernetes_namespace.web.metadata[0].name
    labels = {
      App         = "nginx"
      Environment = "production"
    }
  }

  spec {
    selector = {
      App = kubernetes_deployment.nginx.metadata[0].labels.App
    }

    port {
      port        = 80
      target_port = 80
    }

    type = "ClusterIP"
  }
}
Instructions for the AI
Use the provided structure to generate a Terraform script.

Ensure the script is syntactically correct and adheres to Terraform best practices.

Include comments in the script to explain each block and resource.

Validate the script to ensure it can be applied without errors.
