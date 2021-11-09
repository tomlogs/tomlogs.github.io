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