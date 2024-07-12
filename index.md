+++
title =  "Automate Posting Hugo Blog to Social Sites (with a db) Part 3"
date = "2024-07-12"
description = "Creating a CMS DB.. Because why not"
author = "Justin Napolitano"
tags = ['python', "hugo","programming","fail"]
images = ["images/feature-image.png"]
categories = ['Projects']
+++


# Automate Blog with GCP MYSQL Server

So I am just going to create my own CMS.  I know there are other solutions.. but I am nearly there.. I also want to manage content like i manage a linux system.. So I am going to do this with a db.. bashscripts.. and linux servers.  



## Create the MYSQL Instance

I could just use a db wihtin a container.. but i want to scale this out..I have a few ideas.. So I will be initializing the server on gcp

### Write an initialization script


```bash
#!/bin/bash

# Source the .env file to load environment variables
set -o allexport
source .env
set -o allexport

# Set other variables
PROJECT_ID="smart-axis-421517"
INSTANCE_NAME="jnapolitano-db"
REGION="us-west2" # e.g., us-central1

# Authenticate with GCP (make sure you have gcloud SDK installed and authenticated)
gcloud auth login

# Set the project
gcloud config set project $PROJECT_ID

# Enable the Cloud SQL Admin API
gcloud services enable sqladmin.googleapis.com

# Create a Cloud SQL instance
gcloud sql instances create $INSTANCE_NAME \
    --database-version=MYSQL_8_0 \
    --cpu=2 \
    --memory=7680MB \
    --region=$REGION

# Set the root password using environment variable
gcloud sql users set-password root \
    --host=% \
    --instance=$INSTANCE_NAME \
    --password=$ROOT_PASSWORD

# Create a user called 'cobra' using environment variable
gcloud sql users create cobra \
    --host=% \
    --instance=$INSTANCE_NAME \
    --password=$COBRA_PASSWORD

# Grant superuser privileges to 'cobra'
gcloud sql connect $INSTANCE_NAME --user=root --quiet << EOF
ALTER USER 'cobra'@'%' WITH GRANT OPTION;
GRANT ALL PRIVILEGES ON *.* TO 'cobra'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
EOF

# Optional: Create a database (uncomment if needed)
# DATABASE_NAME="your-database-name"
# gcloud sql databases create $DATABASE_NAME --instance=$INSTANCE_NAME

echo "MySQL instance $INSTANCE_NAME created successfully in project $PROJECT_ID with superuser 'cobra'."

```

### Create a .env file to save your passwords

There are a number of ways to keep passwords out of github. In this case I am just going to add the passes to a .env file and source it 

the file looks like 

```bash
# .env
ROOT_PASSWORD="your-pass"
COBRA_PASSWORD="your-pass"
```

### Run

### Chmod

```bash

chmod +x your-script && ./your-script

```


