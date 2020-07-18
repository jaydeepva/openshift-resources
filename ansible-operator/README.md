# Introduction
This folder contains useful ansible tasks for building your Ansible operator

## random-password
How to generate a random password for use within your services

## retrieve-password
How to retrieve password from openshift/kubernetes secret. This assumes that operator or service account has privilages to read secrets data

## multiple-files-in-secret
How to store multiple files in a single secret and inject them into a container

## network-policy
How to configure network policies to control ingress, egress and communication between pods

## openshift-ca
This explains how to use OpenShift CA and service serving certificates capabilities to enable mTLS between your services