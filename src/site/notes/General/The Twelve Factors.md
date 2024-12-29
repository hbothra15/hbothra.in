---
{"dg-publish":true,"comments":true,"tags":["design","12Factor","general","principals"],"dg-metatags":{"description":"Mnemonic and details on 12-factor app development","title":"12 Factors","og:title":"12 Factors","og:type":"article","og:article:author":"Hemant Bothra","og:article:tag":["design","general","principals"],"og:article:section":"Technology"},"permalink":"/general/the-twelve-factors/","metatags":{"description":"Mnemonic and details on 12-factor app development","title":"12 Factors","og:title":"12 Factors","og:type":"article","og:article:author":"Hemant Bothra","og:article:tag":["design","general","principals"],"og:article:section":"Technology"},"dgPassFrontmatter":true}
---

12 factor is a methodology for designing SaaS (Software as a Service). This methodology aims to make code cloud agnostic, provide an automated deployment method for faster onboarding, and minimise the code difference between Prod and Dev.

We can read more in detail at the official site, i.e. [12factor](https://12factor.net/)

It is difficult to remember, so I have devised a 

---
**A** farm has **2 B**unnies, **3 c**ats, **3 D**ogs, **2 P**arrots and **1 L**ovebird.

---
### **Breakdown:**

1. **A** - **Admin Processes**  
    Run admin/management tasks as one-off processes.
    
2. **B** - **Backing Services**  
    Treat backing services as attached resources.
    
3. **B** - **Build, Release, Run**  
    Strictly separate the build, release, and run stages.
    
4. **C** - **Codebase**  
    One codebase is tracked in version control, and many deploys.
    
5. **C** - **Config**  
    Store configuration in the environment.
    
6. **C** - **Concurrency**  
    Scale out via the process model.
    
7. **D** - **Dependencies**  
    Explicitly declare and isolate dependencies.
    
8. **D** - **Disposability**  
    Maximise robustness with fast startup and graceful shutdown.
    
9. **D** - **Dev/Prod Parity**  
    Keep development, staging, and production as similar as possible.
    
10. **P** - **Processes**  
    Execute the app as one or more stateless processes.
    
11. **P** - **Port Binding**  
    Export services via port binding.
    
12. **L** - **Logs**  
    Treat logs as event streams.

## Admin Process
Before performing maintenance tasks (like database migrations or changing log levels), always run these processes in a digital clone of the product. This clone should have the same operating system, configuration, and codebase to ensure consistency.
Locally, we can directly execute the task, but in production, it has to be done via SSH or any other remote commands.
## Backing Services
This concept aligns closely with Configuration but emphasises the use of external resources by applications, such as databases, Redis, SMTP servers, etc. These services should not be tightly coupled with the application. Accessing these resources via a URL, TCP, or any form of locator should facilitate easy switching from one service to another. For example, if we use MySQL locally, it should be transitionable to a hosted MySQL without any code changes.
## Build, Release, Run
The build process involves compiling or interpreting code into machine code and preparing it for execution. During the release phase, environment-specific properties are injected into the build, tailoring it to its target environment. Finally, the run phase represents the actual execution of the code, where the application is launched and begins serving its purpose.
It's essential to maintain a clear separation between these three steps: build, release, and run. For instance, in the context of deploying a Java application to Kubernetes (K8), the build step can involve using Maven to compile the application code. The release stage would occur when a Docker image is created from this build, which encapsulates the application along with its environment-specific configurations. Lastly, the run phase involves deploying that Docker image to a Kubernetes cluster, where it executes and provides the intended services. This structured approach ensures clarity and efficiency in software deployment.
## Codebase
We should have a codebase that is easier to track, i.e., we should have a version-controlled code base like having it over GIT / BitBucket, etc.
Reason: so that it's easier to track the 3W's ( i.e. When, What and Who) of code changes.
## Config
It's important to store configuration settings externally, which needs to be different for each environment. For example, we can use Kubernetes Secrets or `.env` files. This approach ensures that we do not need to hard-code configuration values directly into our codebase.
## Concurrency
The application should be open for Horizontal scaling and not restricted to only vertical scaling, i.e. when the need arises, we can increase the replica of the application rather than increasing memory and CPU. 
## Dependency
We should always have a clear dependency for each application, ensuring there is no chance of leaking dependency via the external environment. Let's consider Java; we have maven as a package manager that has a clear dependency for each app via pom.xml, and for Python, we have pip, which has a clear dependency in requirement.txt
## Disposability
The application should have a very short startup time so that as soon as the need arises, they can cater for the need ASAP. Also, the app should be designed for Graceful shutdown, i.e. when they receive SIGTERM (Signal for Termination in Unix), they should gracefully get things done and terminate. Our app should also be considered for Sudden death, i.e. say our app is doing some DB transaction on sudden death transaction was incomplete hence, the transaction should be rolled back.
## DEV/Prod Parity
It is natural to have differences between Dev and Prod A few features which we are working on are available in Dev but not in Prod, but the ask is to have it as minimal as possible instead of having Monthly or weekly releases, it should be an on-demand release. In place of using different stacks like SQLLite in Dev or for Testing MySQL/Postgres in Production, it should never be the case as this can lead to some edge cases working in SQLLite but failing in prod as MySQL/Postgres behave differently, finding and fixing this over-application lifetime can be a costly affair.
## Processes
The application should never store anything within itself; it should use stateful backing services like DB, S3, etc. It can use caching temporarily but shouldn't fail if the cache gets evicted and shouldn't share the memory and CPU with another copy of the application.
## Port-Binding
The application should be bound to a specific port rather than determined at runtime. This approach provides predictability: for example, we know that a Java application will run on port 8080, while React will run on port 3000 by default. Running multiple applications on the same port can lead to confusion. 
Now, the question arises about how to handle specific domain requests. In this case, we have a routing layer that accepts requests over port 443 (HTTPS). Based on certain conditions, it can then direct those requests to either port 3000 (for the front end) or port 8080 (for the back end).
## Logs
As per the 12 factors, the application shouldn't own how to store and maintain log files, like what should be the file name, where to place it, what should be the rolling policy etc. They should be sending out all events to STDOUT/STDERR and letting env decide on managing the same. Ideally, we should be moving all these logs to some commonplace for viewing/Archiving/realtime analysis