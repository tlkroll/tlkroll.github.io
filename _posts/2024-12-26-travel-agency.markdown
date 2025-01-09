---
layout: post
title:  Travel Agency
categories: code
comments: true
excerpt: "Full Stack application with a Java backend and SQL database" 
---
This was the final project for my Backend Programming course at WGU. This was a good introduction to full stack applications, which was not an easy thing to pick up. Most of this application was built beforehand, but gave me good insight into how front end and back end interact with one another and communicate with a database. These full stack classes were actually great experience for building this website as well! Because of the size of this application, I included a repository link instead of posting the code here directly.

This is an application for a fictional travel agency created using a Spring framework with an Angular front end, Java back end, and a MySQL database for storing company and customer information.

<h2>Languages and Framework</h2>

- Java
- HTML
- MySQL
- Spring
- Angular

<h2>Concepts</h2>

- Full Stack Development
- Database Management
- REST API

<h2>Requirements followed for this project</h2>

A.   Create a new Java project using Spring Initializr, with each of the following dependencies:

  - Spring Data JPA (spring-boot starter-data-jpa)

  - Rest Repositories (spring-boot-starter-data-rest)

  - MySQL Driver (mysql-connector-java)

  - Lombok

Note: Since the application properties will be empty, you will need to copy over the supplied application properties.

B.   Create your subgroup and project by logging into GitLab using the web link provided and do the following:

  - connect your new Java project
  - commit with a message and push when you complete each of the tasks listed below (parts B to F, etc.)

Note: Any submissions that do not have a commit after each task will not be evaluated.

Note: You may commit and push whenever you want to back up your changes, even if a task is not complete.

  - Submit a copy of the git repository URL and a copy of the repository branch history retrieved from your repository, which must include the commit messages and dates.

Note: Wait until you have completed all the following prompts before you create your copy of the repository branch history.

C.   Construct four new packages, one for each of the following: controllers, entities, dao, and services. The packages will need to be used for a checkout form and vacations packages list.

Note: The packages should be on the same level of the hierarchy.

Note: Construct a package named config and copy the RestDataConfig.java provided in the laboratory environment to the package. Modify it so that the package and imports have the correct package and import addresses. Copy the application.properties file that is provided in the laboratory environment into your application properties resource file.

D.   Write code for the entities package that includes entity classes and the enum designed to match the UML diagram.

E.   Write code for the dao package that includes repository interfaces for the entities that extend JpaRepository, and add cross-origin support.

F.   Write code for the services package that includes each of the following:

  - a purchase data class with a customer cart and a set of cart items

  - a purchase response data class that contains an order tracking number

  - a checkout service interface

  - a checkout service implementation class

G.   Write code to include validation to enforce the inputs needed by the Angular front-end.

H.   Write code for the controllers package that includes a REST controller checkout controller class with a post mapping to place orders.

Note: You do not need to duplicate REST functionality for each repository by creating methods in Java.

I.   Add five sample customers to the application programmatically.

Note: Make sure the customer information is not overwritten each time you run the application.

J.   Run your integrated application by adding a customer order for a vacation with two excursions using the unmodified Angular front-end. Provide screenshots for the following:

  - that your application does not generate a network error when adding the data

  - your database tables using MySQL Workbench to show the data was successfully added

Note: The screenshot should include the front-end view and the inspection console in the browser.

K.   Demonstrate professional communication in the content and presentation of your submission.

<h2>Repository Link</h2>
<a href="https://github.com/tlkroll/TravelAgency" target="_blank">Travel Agency</a>

{% if page.comments %} 

<br><br>
<p>
<div id="disqus_thread"></div>
<script>
    (function() { 
        var d = document, s = d.createElement('script');
        
        s.src = 'https://tlkroll.disqus.com/embed.js';
        
        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
</p>

{% endif %} 