---
slug: github-setup-mysql-gcp
id: github-setup-mysql-gcp
title: Automate MySQL Setup on Google Cloud Platform
repo: justin-napolitano/setup-mysql-gcp
githubUrl: https://github.com/justin-napolitano/setup-mysql-gcp
generatedAt: '2025-11-24T21:36:17.672Z'
source: github-auto
summary: >-
  This project provides scripts to automate MySQL server setup and configuration
  on GCP, including user management and SQL schema files.
tags:
  - gcp
  - mysql
  - bash
  - cloud sql
  - google cloud sdk
  - automation
seoPrimaryKeyword: setup mysql gcp automation
seoSecondaryKeywords:
  - mysql cloud sql setup
  - gcp mysql automation
  - bash scripts for gcp
  - google cloud mysql configuration
seoOptimized: true
topicFamily: null
topicFamilyConfidence: null
kind: project
entryLayout: project
showInProjects: true
showInNotes: false
showInWriting: false
showInLogs: false
---

A shell-based project to automate the setup and configuration of a MySQL server on Google Cloud Platform (GCP). This repository includes scripts and SQL schema files to initialize a scalable MySQL instance and configure databases and users.

---

## Features

- Automates creation of a MySQL 8.0 Cloud SQL instance on GCP
- Sets up database users with appropriate permissions
- Provides SQL schema files for blog-related tables (posts, authors, mastodon_posts, builds, feed)
- Includes configuration and initialization scripts

---

## Tech Stack

- Shell scripting (Bash) for automation
- Google Cloud SDK (`gcloud`) for managing GCP resources
- MySQL 8.0 as the database engine

---

## Getting Started

### Prerequisites

- Google Cloud SDK installed and authenticated (`gcloud auth login`)
- A GCP project with billing enabled
- Environment variables set in a `.env` file (e.g., `ROOT_PASSWORD`, `COBRA_PASSWORD`)

### Installation & Usage

1. Clone the repository:

```bash
git clone https://github.com/justin-napolitano/setup-mysql-gcp.git
cd setup-mysql-gcp
```

2. Set up your `.env` file with necessary credentials.

3. Run the initialization script to create and configure the Cloud SQL instance:

```bash
bash gcp_initialize_mysql_server.sh
```

4. Use the SQL schema files in `mysql-config/` to create tables and databases as needed.

---

## Project Structure

```
setup-mysql-gcp/
├── gcp_initialize_mysql_server.sh  # Bash script to initialize MySQL instance on GCP
├── index.md                        # Blog post describing the automation process
└── mysql-config/                   # Folder containing SQL schema and configuration files
    ├── authors.sql                # Authors table schema
    ├── builds.sql                 # Builds table schema
    ├── feed.sql                   # Feed table schema
    ├── mastodon.sql               # Mastodon posts table schema
    ├── posts.sql                  # Posts table schema
    └── index.md                   # MySQL server configuration notes
```

---

## Future Work / Roadmap

- Add error handling and validation in the initialization script
- Automate deployment of SQL schema files after instance creation
- Expand user management with role-based access control
- Integrate CI/CD pipelines for automated testing and deployment
- Document backup and restore procedures for the Cloud SQL instance

---

*Note: This project assumes familiarity with GCP, MySQL, and shell scripting.*
