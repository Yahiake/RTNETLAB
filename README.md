# RTNETLAB
Secure Mailing Application for Intranet — RTNETLAB
# Secure Mailing Application for Intranet — RTNETLAB

A complete **secure internal email infrastructure** designed and deployed for the RTNETLAB laboratory environment.

The project focuses on building a self-hosted mailing system for an intranet, integrating **DNS, SMTP, IMAP, directory services, webmail, authentication, encryption, and email security mechanisms** into a single infrastructure.

Rather than relying on external email providers, the objective was to understand how an enterprise-style mail system works internally, from DNS resolution and user authentication to message delivery, storage, filtering, and secure communication.

---

## Overview

The **RTNETLAB Secure Mailing Application** provides an internal communication platform where users can send and receive email through a centralized infrastructure.

The system combines several network and server technologies:

* **Postfix** — SMTP mail transfer and message delivery
* **Dovecot** — IMAP mail access and mailbox management
* **OpenLDAP** — centralized user and account directory
* **BIND9** — internal DNS infrastructure
* **Apache** — web server
* **Roundcube** — browser-based webmail interface
* **TLS/SSL** — encrypted communications
* **Rspamd** — spam filtering and mail security
* **SPF / DKIM / DMARC** — email authentication and anti-spoofing mechanisms
* **SSH** — secure remote server administration
* **Ubuntu Server** — infrastructure operating system

The different components were configured to communicate with each other and form a functional mail infrastructure.

---

# Architecture

The infrastructure is built around a centralized mail server and supporting services.

```text
                         ┌─────────────────────┐
                         │       Clients       │
                         │                     │
                         │  Thunderbird        │
                         │  Web Browser        │
                         │  Mail Clients       │
                         └──────────┬──────────┘
                                    │
                         IMAP / SMTP / HTTPS
                                    │
                                    ▼
                    ┌───────────────────────────┐
                    │       RTNETLAB Server     │
                    │                           │
                    │       Ubuntu Server       │
                    │                           │
                    │ ┌───────────────────────┐ │
                    │ │       Postfix         │ │
                    │ │       SMTP            │ │
                    │ └───────────┬───────────┘ │
                    │             │             │
                    │ ┌───────────▼───────────┐ │
                    │ │       Dovecot         │ │
                    │ │       IMAP            │ │
                    │ └───────────┬───────────┘ │
                    │             │             │
                    │ ┌───────────▼───────────┐ │
                    │ │       OpenLDAP        │ │
                    │ │  User Authentication  │ │
                    │ └───────────────────────┘ │
                    │                           │
                    │ ┌───────────────────────┐ │
                    │ │        BIND9          │ │
                    │ │         DNS           │ │
                    │ └───────────────────────┘ │
                    │                           │
                    │ ┌───────────────────────┐ │
                    │ │   Apache + Roundcube  │ │
                    │ │       Webmail         │ │
                    │ └───────────────────────┘ │
                    │                           │
                    │ ┌───────────────────────┐ │
                    │ │        Rspamd         │ │
                    │ │ Spam / Mail Filtering  │ │
                    │ └───────────────────────┘ │
                    └───────────────────────────┘
```

---

# Main Objectives

The project was developed with several objectives:

### 1. Build a functional internal mail system

Create an email platform capable of handling communication between users inside an isolated intranet environment.

### 2. Centralize user management

Use **OpenLDAP** as a centralized directory for managing users and authentication rather than maintaining independent accounts across every service.

### 3. Implement secure communication

Protect authentication and email traffic using **TLS/SSL**, reducing the exposure of credentials and sensitive information during transmission.

### 4. Understand email infrastructure

Study the complete lifecycle of an email:

```text
User
  │
  ▼
Mail Client / Webmail
  │
  ▼
SMTP Submission
  │
  ▼
Postfix
  │
  ▼
Mail Processing / Filtering
  │
  ▼
Mailbox
  │
  ▼
Dovecot
  │
  ▼
IMAP Client
```

### 5. Improve email security

Implement mechanisms designed to reduce spam, spoofing, and unauthorized use of the mail infrastructure.

---

# Technologies

| Component             | Technology    | Purpose                              |
| --------------------- | ------------- | ------------------------------------ |
| Operating System      | Ubuntu Server | Server infrastructure                |
| SMTP                  | Postfix       | Sending and receiving mail           |
| IMAP                  | Dovecot       | Mailbox access                       |
| Directory             | OpenLDAP      | Centralized user management          |
| DNS                   | BIND9         | Name resolution and mail-related DNS |
| Web Server            | Apache        | Web application hosting              |
| Webmail               | Roundcube     | Browser-based email client           |
| Security              | TLS/SSL       | Encrypted communication              |
| Filtering             | Rspamd        | Spam and mail filtering              |
| Email Authentication  | SPF           | Sender authorization                 |
| Email Authentication  | DKIM          | Cryptographic email signing          |
| Email Authentication  | DMARC         | Email authentication policy          |
| Remote Administration | SSH           | Secure server administration         |

---

# Core Components

## Postfix

**Postfix** acts as the SMTP server and is responsible for handling email transport.

It manages:

* SMTP connections
* Message reception
* Message submission
* Mail routing
* Authentication
* Integration with the filtering layer
* Secure SMTP communication

Postfix forms the central transport layer of the infrastructure.

---

## Dovecot

**Dovecot** provides mailbox access through IMAP.

Its role includes:

* IMAP service
* Mailbox access
* Authentication integration
* Secure client connections
* Message retrieval and management

This allows users to access their mail from clients such as Thunderbird or through the webmail interface.

---

## OpenLDAP

Instead of creating independent users for every service, the project uses **OpenLDAP** to provide centralized directory management.

The directory contains information used for:

* User identification
* Authentication
* Account management
* Mail-related user information

This approach demonstrates how directory services can be integrated into a larger network infrastructure.

---

## BIND9

**BIND9** provides the DNS infrastructure required by the environment.

DNS is essential to a mail system because mail services depend heavily on hostname and domain resolution.

The DNS configuration provides the necessary records for the internal environment and supports mail-related services.

---

## Roundcube

**Roundcube** provides a browser-based interface for accessing the mail system.

Users can:

* Log in through a web browser
* Read messages
* Send emails
* Reply and forward messages
* Manage their mailbox

Roundcube communicates with the underlying mail infrastructure instead of acting as an independent mail server.

---

## Apache

**Apache HTTP Server** hosts the webmail interface and provides the web layer required by Roundcube.

HTTPS can be used to protect the communication between the user's browser and the webmail service.

---

# Security

Security was an important part of the project rather than an afterthought.

## TLS / SSL

Encrypted connections were configured for the relevant services to protect credentials and email traffic while in transit.

This is particularly important for authentication because transmitting credentials over unencrypted protocols would expose them to network interception.

---

## SPF

**Sender Policy Framework (SPF)** allows a domain to specify which servers are authorized to send email on its behalf.

This helps reduce sender-address spoofing.

---

## DKIM

**DomainKeys Identified Mail (DKIM)** adds a cryptographic signature to outgoing messages.

The receiving side can verify the signature using the corresponding public key published through DNS.

---

## DMARC

**Domain-based Message Authentication, Reporting and Conformance (DMARC)** builds on SPF and DKIM to define how receiving systems should handle messages that fail authentication checks.

Together:

```text
             ┌─────────┐
             │   SPF   │
             └────┬────┘
                  │
                  │
             ┌────▼────┐
             │  DKIM   │
             └────┬────┘
                  │
                  ▼
             ┌─────────┐
             │  DMARC  │
             └─────────┘
```

These mechanisms provide multiple layers of protection against email impersonation and spoofing.

---

## Rspamd

**Rspamd** provides mail filtering and security analysis.

It can analyze incoming messages and assign scores based on characteristics associated with spam or suspicious messages.

The filtering layer works alongside the mail transport infrastructure.

---

# Network Services

The project involved configuring and testing multiple network services and protocols, including:

* DNS
* SMTP
* SMTP Submission
* IMAP
* HTTPS
* LDAP
* LDAPS
* SSH
* TLS

Understanding the relationship between these services was one of the main technical aspects of the project.

---

# Testing

The infrastructure was tested at multiple levels.

### DNS Testing

DNS resolution was tested to verify that the required hostnames and records were correctly configured.

### SMTP Testing

SMTP connectivity and mail delivery were tested to verify that Postfix could correctly accept and process messages.

### IMAP Testing

Dovecot was tested to verify that authenticated users could access their mailboxes.

### TLS Testing

Encrypted service connections were tested to verify certificate configuration and secure communication.

### Authentication Testing

User authentication was tested against the centralized directory infrastructure.

### Webmail Testing

Roundcube was used to verify complete end-to-end communication through a browser.

---

# End-to-End Mail Flow

A typical internal email follows a workflow similar to:

```text
                 USER
                  │
                  ▼
          ┌───────────────┐
          │   Roundcube   │
          │    Webmail    │
          └───────┬───────┘
                  │
                HTTPS
                  │
                  ▼
          ┌───────────────┐
          │    Postfix    │
          │     SMTP      │
          └───────┬───────┘
                  │
                  ▼
          ┌───────────────┐
          │    Rspamd     │
          │    Filtering  │
          └───────┬───────┘
                  │
                  ▼
          ┌───────────────┐
          │    Dovecot    │
          │     IMAP      │
          └───────┬───────┘
                  │
                  ▼
              MAILBOX
```

Authentication and directory lookups are handled through the LDAP infrastructure.

---

# What I Learned

This project provided practical experience with several areas of networking and systems administration:

* Linux server administration
* Network configuration
* DNS infrastructure
* SMTP architecture
* IMAP architecture
* Mail server deployment
* LDAP directory services
* Authentication systems
* TLS certificates
* Email security
* Spam filtering
* Web server configuration
* Webmail deployment
* Service integration
* Network troubleshooting
* Protocol-level testing
* Secure remote administration

One of the most valuable parts of the project was understanding that an email platform is not a single application. It is an ecosystem of interconnected services, each responsible for a specific part of the communication pipeline.

---


---

# Project Context

**Project:** Secure Mailing Application for Intranet
**Environment:** RTNETLAB
**Field:** Telecommunications / Computer Networks / Systems Administration
**Platform:** Linux / Ubuntu Server
**Focus:** Network Services, Email Infrastructure & Security

---

# Skills Demonstrated

### Networking

* TCP/IP
* DNS
* SMTP
* IMAP
* LDAP
* HTTPS
* Network troubleshooting

### Systems

* Linux administration
* Service configuration
* Server deployment
* SSH
* Authentication
* Directory services

### Security

* TLS/SSL
* SPF
* DKIM
* DMARC
* Spam filtering
* Secure authentication

### Infrastructure

* Postfix
* Dovecot
* OpenLDAP
* BIND9
* Apache
* Roundcube
* Rspamd

---

# Disclaimer

This repository is intended for **educational and documentation purposes**.

The configurations should be adapted and reviewed before being deployed in a production environment.

Sensitive information such as passwords, private keys, authentication credentials, real IP addresses, and internal infrastructure details should not be published.

---

## Author

**Yahia Kemari**

Telecommunications Engineer / Network & Systems Enthusiast

Interested in:

* Computer Networks
* Linux & Infrastructure
* Cybersecurity
* Artificial Intelligence
* Automation
* Software Development
* Telecommunications
* Audio Technology

---

## Why This Project Matters

The goal of this project was not simply to install a mail server.

It was to understand how the different layers of a networked communication system interact:

```text
DNS
 ↓
Network Connectivity
 ↓
Authentication
 ↓
SMTP
 ↓
Filtering
 ↓
Mailbox Storage
 ↓
IMAP
 ↓
Webmail
 ↓
User
```

Building the infrastructure from these individual components provided practical experience in designing, configuring, integrating, testing, and troubleshooting a real multi-service network application.
