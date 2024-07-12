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
DATABASE_NAME="jnapolitano"
BUILDS_SQL_FILE="builds.sql" # Name of your builds SQL file
FEED_SQL_FILE="feed.sql"   # Name of your feeds SQL file

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

# Create a database
gcloud sql databases create $DATABASE_NAME --instance=$INSTANCE_NAME

# Execute the SQL files to create the 'builds' and 'feeds' tables
gcloud sql connect $INSTANCE_NAME --user=cobra --database=$DATABASE_NAME --quiet < $BUILDS_SQL_FILE
gcloud sql connect $INSTANCE_NAME --user=cobra --database=$DATABASE_NAME --quiet < $FEED_SQL_FILE


echo "MySQL instance $INSTANCE_NAME created successfully in project $PROJECT_ID with superuser 'cobra' and executed SQL files '$BUILDS_SQL_FILE' and '$FEEDS_SQL_FILE'."


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

chmod +x your-script

```

### Write the sql files used to create the feed and builds tables

I added a submodule that contains my scripts [gh link](https://github.com/justin-napolitano/mysql-config)

#### Builds

```sql
CREATE TABLE builds (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    link VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    generator VARCHAR(255),
    language VARCHAR(10),
    copyright VARCHAR(255),
    lastBuildDate TIMESTAMP,
    atom_link_href VARCHAR(255),
    atom_link_rel VARCHAR(50),
    atom_link_type VARCHAR(50)
);
```

#### Feed

```sql 

CREATE TABLE feed (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    link VARCHAR(255) NOT NULL,
    pubDate TIMESTAMP,
    guid VARCHAR(255),
    description TEXT
);


```

### Run the script

```./yourscript```

## Create the database and the tables

## Connect to The DB

I'm not sure where I will deploy this code... I'm thinking that I will deploy on a virtual machine... or possibly cloud run as a web app. A web app makes sense so I can manage it from a distance.  So run it as a flask app.. Call run. Then scan

Or I just drop it as a cloud run job that deploys every hour..

Or i leave it up as a web app that can be called to update the db. Yeah i like this best..

### Flask App

Flask app that takes very few args

1. Post endpoint to pull the relvent post information
2. Normalize endpoint that calls  a workflow to normalize the entire db.  

## The workflow

### The scraper

The rss scraper pulls the meta information and then forwards it to the post endpoint

### The post endpoint

The post endpoint just adds a row to the posts table. 

