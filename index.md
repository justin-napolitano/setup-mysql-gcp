---
slug: github-setup-mysql-gcp
title: Automating MySQL 8.0 Setup on Google Cloud Platform for CMS
repo: justin-napolitano/setup-mysql-gcp
githubUrl: https://github.com/justin-napolitano/setup-mysql-gcp
generatedAt: '2025-11-23T09:35:40.461936Z'
source: github-auto
summary: >-
  Automation script and schema for deploying a MySQL 8.0 instance on GCP, configuring users and
  database schema for a custom CMS backend.
tags:
  - mysql
  - google-cloud-platform
  - cms
  - automation
  - bash
  - cloud-sql
seoPrimaryKeyword: mysql setup gcp
seoSecondaryKeywords:
  - cloud sql automation
  - cms database
  - gcp bash script
seoOptimized: true
topicFamily: automation
topicFamilyConfidence: 0.95
topicFamilyNotes: >-
  The post centers on automating the setup and configuration of a MySQL server on GCP, using shell
  scripting and cloud CLI tools, which aligns closely with the Automation family focused on
  deployment and scripting automation.
---

# Automate Blog with GCP MySQL Server

## Motivation

Managing content for a CMS requires reliable and scalable data storage. While containerized databases are an option, scaling and managing persistent storage can be challenging. Using Google Cloud Platform's managed MySQL service provides scalability, reliability, and ease of management.

This project automates the setup of a MySQL 8.0 instance on GCP and configures it to serve as the backend for a custom CMS, enabling control over data with standard Linux and shell tooling.

## Problem Statement

Manual setup of cloud database instances and schema configuration is error-prone and time-consuming. There is a need for a repeatable, automated process to initialize a MySQL server on GCP, configure users, and establish the required database schema.

## Implementation Details

### Initialization Script

The core automation is a Bash script (`gcp_initialize_mysql_server.sh`) that:

- Sources environment variables from a `.env` file to keep credentials and configuration separate from code.
- Authenticates the user with GCP using the `gcloud` CLI.
- Sets the active GCP project.
- Enables the Cloud SQL Admin API to allow instance management.
- Creates a Cloud SQL instance with specified CPU, memory, and region parameters.
- Sets the root password and creates an additional user (`cobra`) with superuser privileges.

This script assumes the user has the Google Cloud SDK installed and authenticated.

### Database Schema

The `mysql-config` directory contains SQL files defining the schema for multiple tables:

- `authors.sql`: Stores author information with UUID primary keys.
- `posts.sql`: Stores blog posts with references to authors.
- `mastodon.sql`: Stores Mastodon posts, likely for social media integration.
- `builds.sql` and `feed.sql`: Define tables for RSS feed data and build metadata.

Each table uses binary UUIDs for unique identifiers, favoring a lightweight and consistent ID generation approach.

### User and Permissions Management

The configuration notes describe creating multiple MySQL users with different privileges:

- `cobra`: A superuser with full privileges.
- `admin`: A user with limited administrative privileges.
- `dummy`: A user with no access, used for testing connections.

This separation supports security best practices by limiting access based on roles.

## Practical Considerations

- Passwords are managed via environment variables to avoid hardcoding sensitive data.
- The script requires manual login to GCP (`gcloud auth login`), which could be automated further with service accounts.
- The schema files are designed for a small to medium scale CMS, with UUIDs used for primary keys despite known performance trade-offs.
- The project structure separates automation scripts from SQL schema and documentation, improving maintainability.

## Summary

This project provides a pragmatic approach to automating the deployment and configuration of a MySQL server on GCP for CMS use. It balances automation with manual control, enabling repeatable deployments while allowing customization of database schema and user permissions. The use of shell scripting and GCP CLI tools aligns with common DevOps practices, making it accessible to engineers familiar with cloud and Linux environments.

Returning to this project, one should focus on extending automation, improving security (e.g., secret management), and integrating schema deployment into the initialization workflow.

