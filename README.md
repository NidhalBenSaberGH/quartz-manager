# Quartz Manager (Spring WebFlux)
## Annotation Model
This branch rewrites the entire Spring with Quartz project using Spring 5's
WebFlux.  
In this branch I use the `Annotated Controllers ` similar to Spring MVC

```java
@RestController
@RequestMapping("/api/v1.0")
public class EmailResource {
    ...
    
    /**
     * GET /api/v1.0/jobs
     *
     * @return
     */
    @GetMapping(path = "/jobs")
    public Flux<JobDescriptor> findJobs() {
        return jobService.findJobs();
    }

    /**
     * GET /api/v1.0/groups/:group/jobs/:name
     *
     * @param group
     * @param name
     * @return
     */
    @GetMapping(path = "/groups/{group}/jobs/{name}")
    public Mono<ResponseEntity<JobDescriptor>> findJob(@PathVariable String group, @PathVariable String name) {
        return jobService.findJob(group, name)
                .map(ResponseEntity::ok)    // If job is found return 200
                .defaultIfEmpty(ResponseEntity.notFound().build()); // Return 404 if job is not found
    }
    ...
}
```
To understand what is happening in this project read the blog post at:

<http://juliuskrah.com/tutorial/2017/10/11/error-handling-in-a-rest-service-with-quartz/>

for a comprehensive overview.

## Quick Start
*Prerequisite:* Java 9

Clone this repository
```bash
> git clone -b v3.x https://github.com/juliuskrah/quartz-manager.git
> cd quartz-manager
> mvnw clean spring-boot:run
```

## Features
**CREATE**  
Method      : `POST: /api/v1.0/groups/:group/jobs`  
Status      : `201: Created`  
Body        :
```json
{
  "name": "manager",
  "subject": "Daily Fuel Report",
  "messageBody": "Sample fuel report",
  "to": ["juliuskrah@example.com", "juliuskrah@example.net"],
  "triggers":
    [
       {
         "name": "manager",
         "group": "email",
         "cron": "0 0 0 ? JAN MON 2018"
       }
    ]
}
```
Content-Type: `application/json`

**VIEW**  
Method      : `GET: /api/v1.0/groups/:group/jobs/:name`  
Status      : `200: Ok`  or `404: Not Found`  
Body        : NULL  
Accept      : `application/json`

**VIEW (By Group)**  
Method      : `GET: /api/v1.0/groups/:group/jobs/`  
Status      : `200: Ok`  
Body        : NULL  
Accept      : `application/json`

**VIEW (All)**  
Method      : `GET: /api/v1.0/jobs/`  
Status      : `200: Ok`  
Body        : NULL  
Accept      : `application/json`

**UPDATE**  
Method      : `PUT: /api/v1.0/groups/:group/jobs/:name`  
Status      : `204: No Content`  
Body        :
```json
{
  "name": "manager",
  "subject": "Daily Fuel Report",
  "messageBody": "Sample fuel report",
  "to" : ["juliuskrah@example.com", "juliuskrah@example.net"],
  "cc" : ["management@example.com", "management@example.net"],
  "bcc": ["bcc@example.com"]
}
```
Content-Type: `application/json`

**UPDATE (Pause)**  
Method      : `PATCH: /api/v1.0/groups/:group/jobs/:name/pause`  
Status      : `204: No Content`  
Body        : NULL  
Content-Type: `*/*`

**UPDATE (Resume)**  
Method      : `PATCH: /api/v1.0/groups/:group/jobs/:name/resume`  
Status      : `204: No Content`  
Body        : NULL  
Content-Type: `*/*`

**DELETE**  
Method      : `DELETE: /api/v1.0/groups/:group/jobs/:name`  
Status      : `204: No Content`  
Body        : NULL  
Content-Type: `*/*`
