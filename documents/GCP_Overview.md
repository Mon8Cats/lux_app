# GCP Overview

## Region, Zone

- Region: 
  - choose based on geographical proximity for lower latency
  - contains multiple zones
- Zone: 
  - distribute resources across zones for redundancy and fault tolerance.
  - each zone may include multiple data centers

## GCP Products/Services and VPC

- Within a  VPC:
  - Compute services: computer engine, gke, cloud run, app engine
  - Storage services: filestore, cloud SQL, memorystore
  - network services: cloud load balancing, cloud nat, cloud vpn, interconnect and peering
  - data analytics: dataproc
  - others: dataflow, batch
- Outside a  VPC:
  - Serverless and application deployment: app engine, cloud run, cloud function
  - Storage: cloud storage, bigquery
  - database: firestore, bigtable
  - networking: cloud cdn, cloud dns
  - data analytics and AL/ML: bigquery, dataflow, ai platform servcies
  - management and monitoring: cloud logging, cloud monitoring
  - identity and security: IAM, KMS
- Hybrid:
  - cloud function, cloud run, dataflow

## Use secrets from Secret Manager

```bash
Enable APIs: Cloud Build, Secret Manager APIs.
IAM permissions: roles/secretmanager.secretAccessor to SA used for the build.

steps:
- name: 'gcr.io/cloud-builders/docker'
  entrypoint: 'bash'
  args: ['-c', 'docker login --username=$$USERNAME --password=$$PASSWORD']
  secretEnv: ['USERNAME', 'PASSWORD']
availableSecrets:
  secretManager:
  - versionName: projects/PROJECT_ID/secrets/DOCKER_PASSWORD_SECRET_NAME/versions/DOCKER_PASSWORD_SECRET_VERSION
    env: 'PASSWORD'
  - versionName: projects/PROJECT_ID/secrets/DOCKER_USERNAME_SECRET_NAME/versions/DOCKER_USERNAME_SECRET_VERSION
    env: 'USERNAME'

steps:
- name: python:slim
  entrypoint: python
  args: ['main.py']
  secretEnv: ['MYSECRET']
availableSecrets:
  secretManager:
  - versionName: projects/$PROJECT_ID/secrets/mySecret/versions/latest
    env: 'MYSECRET'

import os
print(os.environ.get("MYSECRET", "Not Found")[:5], "...")

steps:
- name: 'launcher.gcr.io/google/ubuntu1604'
  id: Create GitHub pull request
  entrypoint: bash
  args:
  - -c
  - curl -X POST -H "Authorization:Bearer $$GH_TOKEN" -H 'Accept:application/vnd.github.v3+json' https://api.github.com/repos/GITHUB_USERNAME/REPO_NAME/pulls -d '{"head":"HEAD_BRANCH","base":"BASE_BRANCH", "title":"NEW_PR"}'
  secretEnv: ['GH_TOKEN']
availableSecrets:
  secretManager:
  - versionName: projects/PROJECT_ID/secrets/GH_TOKEN_SECRET_NAME/versions/latest
    env: GH_TOKEN


steps:
- name: gcr.io/cloud-builders/gcloud
  entrypoint: 'bash'
  args: [ '-c', "gcloud secrets versions access latest --secret=secret-name --format='get(payload.data)' | tr '_-' '/+' | base64 -d > decrypted-data.txt" ]


steps:
- name: gcr.io/cloud-builders/gcloud
  entrypoint: 'bash'
  args: [ '-c', "gcloud secrets versions access latest --secret=secret-name --format='get(payload.data)' | tr '_-' '/+' | base64 -d > decrypted-data.txt" ]
- name: gcr.io/cloud-builders/docker
  entrypoint: 'bash'
  args: [ '-c', 'docker login --username=my-user --password-stdin < decrypted-data.txt']
```

## Use encrypted credential form Cloud KMS

```bash
Enable APIs: Cloud Build and Cloud KMS APIs
IAM permissions: roles/cloudkms.cryptoKeyDecrypter to the build service account

 steps:
 - name: 'gcr.io/cloud-builders/docker'
   entrypoint: 'bash'
   args: ['-c', 'docker login --username=$$USERNAME --password=$$PASSWORD']
   secretEnv: ['USERNAME', 'PASSWORD']
 - name: 'gcr.io/cloud-builders/docker'
   entrypoint: 'bash'
   args: ['-c', 'docker pull $$USERNAME/IMAGE:TAG']
   secretEnv: ['USERNAME']
 availableSecrets:
   inline:
   - kmsKeyName: projects/PROJECT_ID/locations/global/keyRings/USERNAME_KEYRING_NAME/cryptoKeys/USERNAME_KEY_NAME
     envMap:
       USERNAME: 'ENCRYPTED_USERNAME'
   - kmsKeyName: projects/PROJECT_ID/locations/global/keyRings/PASSWORD_KEYRING_NAME/cryptoKeys/PASSWORD_KEY_NAME
     envMap:
       PASSWORD: 'ENCRYPTED_PASSWORD'


steps:
- name: gcr.io/cloud-builders/gcloud
  args:
  - kms
  - decrypt
  - "--ciphertext-file=ENCRYPTED_PASSWORD_FILE"
  - "--plaintext-file=PLAINTEXT_PASSWORD_FILE"
  - "--location=global"
  - "--keyring=KEYRING_NAME"
  - "--key=KEY_NAME"
- name: gcr.io/cloud-builders/docker
  entrypoint: bash
  args:
  - "-c"
  - docker login --username=DOCKER_USERNAME --password-stdin < PLAINTEXT_PASSWORD_FILE

steps:
- name: 'gcr.io/cloud-builders/docker'
  entrypoint: 'bash'
  args: ['-c', 'docker login --username=user-name --password=$$PASSWORD']
  secretEnv: ['PASSWORD']
- name: 'gcr.io/cloud-builders/docker'
  args: ['push', 'user-name/myubuntu']
secrets:
- kmsKeyName: projects/project-id/locations/global/keyRings/keyring-name/cryptoKeys/key-name
  secretEnv:
    PASSWORD: 'encrypted-password'

```

