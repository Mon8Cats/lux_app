# Cloud Run Overview

- Design, implement, deploy, secure, manage, scale applications on Google Cloud using Cloud Run.
- Deploy highly available applications with low-end user latency.
- How to migrate code using languages including Python, Node.js
- How to implement secure service-to-service communication based on service identities and grant applications only they permissions they need.
- How to connect and persist data in managed database offerings on Google Cloud.
- How removing the need for all infrastructure management can create a simpler experience.

## Introducing to Cloud Run

- A scalable, on-demand web application platform
- Workflow: write code -> build and package into a container image -> deploy to cloud run
- My application is exposed on a unique subdomain of the global run.app domain (or use own domain)
- User cases
  - Client -> REST API -> PostgreSQL
  - Client -> Cloud CDN -> Cloud Armor -> Cloud Run Service -> PostgreSQL, Redis, Third party API
  - Client -> Cloud IAP, Identity Provider -> Cloud Run Service
  - Client -> Frontend Cloud Run services (-> Redis) -> Microservices
  - File upload event -> Cloud Storage -> Cloud Run -> Pub/Sub -> Other GCP services
  - Cloud Scheduler -> Cloud Run Services
- High availability: automatic scaling, incremental application updates, load balancing
- Challenges: auto-scaling costs, scaling mismatch, vendor reliance, portability.
- Serverless: Cloud Run, Cloud Functions, App Engine

## Understanding Cloud Run

- Container images and containers
- Autoscaling and on-demand containers

## Building Container Images

## Diving Deeper into Cloud Run

## Service Identity and Authentication

## Serving Request

## Controlling Inbound and Outbound

## Persisting Data

## Service-to-Service Communication

## Building Serverless Workflows