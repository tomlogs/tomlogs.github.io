---
layout: post
title: Building a photos application in Python with Azure App Service and Blob Storage
permalink: build-a-photos-application-with-azure-app-service-and-blob-storage
date: 2021-11-09 01:33:00 +0000

---
Photos are precious: they encapsulate memories, and looking at them brings joy. It makes sense to want to back them up on a web application. This would also make it easy to view and share pictures on any device, since the application would be accessible through any web browser.

In this blog post, we are going to build a Python web application from scratch, using the Flask framework and we will use Azure App Service and Azure Blob Storage to host our application and store our pictures. Specifically, the application will let us upload images, view the images, and delete any image. Let's get started!

Table of contents:

1. Create the web application
2. Connect the web application to Blog Storage to store pictures
3. Deploy the web application using App Service

Prerequisites:

* Python 3.8.5 ([https://www.python.org/downloads/](https://www.python.org/downloads/ "https://www.python.org/downloads/"))
* Flask 1.1.2 ([https://flask.palletsprojects.com/en/2.0.x/](https://flask.palletsprojects.com/en/2.0.x/ "Flask"))

## Create the Web Application

### Create a form upload to upload photos

We'll start off by creating a simple Flask application with an upload form. This is where we will be uploading our pictures. The first endpoint will have a "/" route.  This endpoint will return HTML with a form to upload photos. (Eventually, we will display the uploaded pictures on this page). We'll define the second endpoint as having the route "/upload-photos" and expecting a POST request.

    from flask import Flask, request
    
    app = Flask(__name__)
    
    @app.route("/")
    def view_photos():
        return '''
            <h1>Upload new File</h1>
            <form method="post" action="/upload-photos" 
                enctype="multipart/form-data">
                <input type="file" name="photos" multiple >
                <input type="submit">
            </form> 
            '''
    
    #flask endpoint to upload a photo
    @app.route("/upload-photos", methods=["POST"])
    def upload_photos():
        filenames = ""
        for file in request.files.getlist("photos"):
            filenames += file.filename + " "
    
        return "<p>Uploaded: {}</p>".format(filenames)        

![Web Application (left) and source code (right)](/uploads/screenshot-2021-11-08-212154.png "Form Upload for Photos")

For the moment, the second endpoint will display the filenames of the uploaded files when they are submitted. This will indicate that the form is working as expected.

![](/uploads/screenshot-2021-11-08-212424.png)

### Create a Blob Storage resource in Azure

We now have to store the uploaded photos. While we could store the uploaded photo files locally, this would occupy a lot of space on our server. Instead, we will be storing the in Azure Blob Storage.

We first create a resource group, which will contain all the resources that we need for this project. The resource group name will be "photos-app" and we'll select the East US region, although any region will work.

![](/uploads/screenshot-2021-11-08-230918.png)

After reviewing and creating the resource group, we can create the first resource that we'll need, the Storage account. To create a new storage account, search for "Storage accounts" and create a new resource. In the storage account creation form, we'll make sure to select the same subscription and we'll select the resource group we just created. This is what my configurations look like:

* Resource group: photos-app
* Storage account name: photosappstoragepost
* Region: East US
* Performance: Standard
* Redundancy: Locally Redundant Storage

![](/uploads/screenshot-2021-11-08-231432.png)

We can now review and create the storage account.

### Store photos in Blob Storage from the Flask App upon upload

We'll be using the Azure Storage Blob Python SDK in order to interact with the Blob Storage from the Flask App.

#### Retrieve Blob Storage keys from the Azure Portal

First, we'll need to get our Blob Storage credentials from the portal. These keys will allow us to access these resources securely. From the **Azure Portal Home**, click **All Resources**. You should see the **storage account photosappstoragepost**. Select **Access Keys** in the section **Security + Networking**. Click show keys, and copy the **connection string** in the section **key 1**.

#### Store Blob Storage Connection String as an environment variable.

We'll be storing the connection string as an environment variable locally,[ as it is more secure than storing it in the code]().

##### Windows

In cmd, copy and paste the following and substitute **<yourconnectionstring>** with the connection string you just copied.

    set AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"

##### Linux/macOS

In bash/terminal, copy and paste the following and substitute **<yourconnectionstring>** with the connection string you just copied.

    export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"

#### Install the Azure Blob Storage Python package

In our cmd/terminal, we'll install the Azure Blob Storage client library. This will allow us to access the Azure Blob Storage from the code.

    pip install azure-storage-blob

#### Store Images in Blob Storage from the Flask app

In the code, we'll add the functionality to store the uploaded pictures. We'll first start by importing the Azure Blob Storage library at the top of the file.

    from azure.storage.blob import BlobServiceClient

In the **/upload-photos** endpoint, we'll adjust the code to add the upload logic. We'll retrieve the Azure Blob Storage connection string, which we will need to pass in to use the Azure Blob Storage client library.