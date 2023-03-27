# template-boot-angular-maven-multi-module

![CI](https://github.com/asaikali/template-boot-angular-maven-multi-module/workflows/Java%20CI%20with%20Maven/badge.svg)

This repo shows how to setup a project to use SpringBoot for implementing an API 
and Angular for the frontend without affecting the workflow of the backend 
springboot developer or the frontend angular developer. 

The SpringBoot api is located in the `backend` directory which follows the standard
springBoot / maven project structure and the `frontend` directory which contains
a standard angular project created via the Angular CLI.  

The workflow is the following.
* SpringBoot app runs on port 8080 and serves out the API 
* Angular app runs via `ng serve` on port `4200`
* Angular `ng serve` is configured to [proxy requests](https://angular.io/guide/build#proxying-to-a-backend-server) `http://localhost:4200/api/*` which 
to the spring boot application on `http://localhost:8080/api' 

In the `frontend` directory there is a `pom.xml` file that leverages the 
maven frontend plugin. The frontend plugin does the following.
* install node and npm locally into the `frontend/target/node' folder.
* build the angular code into the standard `dist` folder 
* package up the contents on `dist/frontend` into a jar file under the path `/static` 
* spring boot backend code has a maven dependency on `frontend.jar` which means that 
when `mvn package` is executed the SpringBoot backend contains all the complied 
Angular code in the boot app and `http://localhost:8080` would serve out both the 
compiled angular code and the boot based API. 

The setup of this project makes it possible to allow the frontend developer who
knows Angular but does not know spring boot to simple run the boot from the IDE
while running `ng serve` from the command line and everything just works.

## Angular proxy to Spring Boot Setup

To configure angular `ng serve` to [proxy](https://angular.io/guide/build#proxying-to-a-backend-server) 
requests to the spring boot backed following the steps below.

Create a `proxy.conf.json` file in the `/frontend/src` project wih the contents below 

```json
{
  "/api": {
    "target": "http://localhost:8080",
    "secure": false,
    "logLevel": "debug",
    "changeOrigin": true
  }
}
```
Modify the `src/frontend/angular.json` so that the `ng serve` command uses the proxy configuration
file by adding the `proxyConfig` option shown in the snippet below.
```json
 "architect": {
        "serve": {
          "builder": "@angular-devkit/build-angular:dev-server",
          "options": {
            "browserTarget": "frontend:build",
            "proxyConfig": "src/proxy.conf.json"
          },
}
```

## Issues 

* There is no security setup between the Angular App and the SpringBoot Backend.

* If you would like to gradle instead of maven checkout `https://ordina-jworks.github.io/architecture/2018/10/12/spring-boot-angular-gradle.html`
