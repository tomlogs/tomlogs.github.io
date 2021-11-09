---
layout: post
title: Building a photos application in Python with Azure App Service and Blob Storage
permalink: build-a-photos-application-with-azure-app-service-and-blob-storage
date: 2021-11-09 01:33:00 +0000

---
Photos are precious: they encapsulate memories, and looking at them brings joy. It makes sense to want to back them up on a web application. This would also make it easy to view and share pictures on any device, since the application would be accessible through any web browser.

In this blog post, we are going to build a Python web application from scratch, using the Flask framework and we will use Azure App Service and Azure Blob Storage to host our application and store our pictures. Specifically, the application will let us upload images, view the images and delete any image. Let's get started!

Table of contents:

1. Create the web application
2. Connect the web application to Blog Storage to store pictures
3. Deploy the web application using App Service

Prerequisites:

* Python 3.8.5 ([https://www.python.org/downloads/](https://www.python.org/downloads/ "https://www.python.org/downloads/"))
* Flask 1.1.2 ([https://flask.palletsprojects.com/en/2.0.x/](https://flask.palletsprojects.com/en/2.0.x/ "Flask"))

1. Create the web application

We'll start off by creating a simple Flask application with an upload form. This is where we will be uploading our pictures. The first endpoint will have a "/" route.  This endpoint will return HTML with a form to upload photos. (Eventually, we will display the uploaded pictures on this page). We'll define the second endpoint as having the route "/upload-photos" and expecting a POST request.

![Web Application (left) and source code (right)](/uploads/screenshot-2021-11-08-212154.png "Form Upload for Photos")

For the moment, the second endpoint will display the filenames of the uploaded files when they are submitted. This will indicate that the form is working as expected