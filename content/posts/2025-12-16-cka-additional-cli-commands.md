---
title: CKA Additional CLI Commands
description: My notes while prepping
date: 2025-12-16T09:17:38.822Z
preview: ""
draft: true
tags:
    - technology
categories:
    - HowTo
authors: Ivan Lawrence
externalLink: ""
series: {}
author: ""
---

## Authentication
If I have to make a cert I'm gonna forget all this so here we go
1. Add a user named bob
```
sudo useradd -s /bin/bash bob

sudo passwd bob
```
1. Make a CSR (cert signing request)
```
openssl genrsa -out bob.key 2048

openssl req -new -key bob.key -out bob.csr -subj "/CN=bob/O=development" # the Org of developer is to match the context
```
1. Make a x509 cert (self signed)
```
sudo openssl x509 -req -in bob.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out bob.crt -days 45
```
1. set the credentials in `kubectl` using what we just created
```
kubectl config set-credentials bob --client-certificate=/home/student/bob.crt --client-key=/home/student/bob.key
```
1. create a context
```
kubectl config set-context bob-context --cluster=kubernetes --namespace=development --user=bob
```



