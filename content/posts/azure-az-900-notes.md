---
title: "Azure AZ-900 Notes"
date: 2021-09-13T19:10:49Z
draft: true
---

Some notes on the Azure AZ-900 test:

## Cloud Concepts

* Definition - Delivery of computing services over the internet.
* Infrastructure in the cloud, runs with pay-as-you-use model which generally results in less overall costs.
* Dynamically scalable as business needs change.
* Provides self-service on-demand via the Azure Portal.

* Compute - VMs, AKS, Azure Functions
* Networking - Virtual Network, Load Balancer, Application Gateway, CDN
* Storage - Azure Blob/File/Queue/Table Storage
* Mobile - Mobile backend services
* Databases - SQL, Cosmos, MySQL, PostgreSQL, MariaDB, Redis
* Web - App Service, API Management
* IOT
* Big data
* AI - Machine Learning Service
* DevOps - Azure DevOps

Azure Account -> Subscriptions -> Resource Groups -> Resources

## Core Services

### Compute

In most->least concern for infrastructure for hosting applications:

* VMs - virtual machines in the cloud (includes scale sets for auto-scaling, Azure Batch for large parallel jobs in cloud)
* Containers/AKS - deploy and manage containers
* App Service - runs web, mobile, and API apps without infrastructure concern
* Function - serverless, works well with REST, timer, and messages

Azure Virtual Desktop - allows for remote development.

## Core Solutions & Management Tools

### IOT

Ordered in build -> buy solution:

* IoT Hub - managed service to handle bi-direction communication between IoT apps and devices.
* IoT Central - provide dashboard for managing IoT devices.
* Azure Sphere - end-to-end IoT solution with 3 parts:
  * Azure Sphere micro-controller unit
  * Custom Linux OS
  * Azure Sphere Security Service (AS3)

## General Security and Network Security Features

* Security Center - provides overview of security (and score) across all resources, provide list of allowed applications
* Sentinel - provides security via machine learning.
* Key Vault - manages secrets, keys, certificates, and access monitoring.
* Dedicated Host - allows for deploying workloads onto isolated servers.

## Identity, Governance, Privacy, and Compliance Features

* Azure Active Directory
  * Provides a list of users for a tenant (organization).
  * Provides authentication, SSO, application management, and device management.
  * Allows for integration with on-premises using Azure AD Connect.
  * Supports MFA (including biometric).
  * Conditional Access provides more granular MFA based on signals (location, device, etc., requires Azure AD P1 or higher).

## Cost Management and Service Level Agreements

* TCO (Total Cost of Ownership) Calculator - estimates costs over time between on-premises and cloud.
  * Servers
  * Databases
  * Storage
  * Networking
* Azure Advisor - monitors usage, provides suggestions.
* Other considerations:
  * Spending limits to prevent overcharging.
  * Azure Reservations to pre-pay for discounts.
  * Use low-cost regions.
  * Deallocate VMs during off-hours.
