### PRI 2.0 - Docker deploy

### Prerequisites

* PostgreSQL installed and database created
* substitution of all environment variables (see `config.env.example` file)

### First launch

1. Create folder where you want to store the application and name it e.g. PRI.
2. Inside your folder create 3 new folders and name them UI, core and deploy.
3. Clone all 3 git repositories into respective folders.
4. In deploy folder, copy the config.env.example from "deploy/support files" into deploy folder and delete the ".example" form name of the file.
5. Provide all secrets given in the file e.g.:
   POSTGRES_URL=jdbc:postgresql://host.docker.internal:5432/  📢 IMPORTANT!: You use host.docker.internal when you deploy database outside of docker on Windows! On Linux it requires to add parameter `extra_hosts` in `docker-compose.yml`
   POSTGRES_USER=YourPostgresUser
   POSTGRES_PASSWORD=YourPostgresPassword
   POSTGRES_DB=NameOfYourDatabase
   JWT_SECRET=JwtSecret
   MAIL_HOST=host.docker.internal 📢 IMPORTANT!: Host docker internal when you deploy database outside of docker!
   MAIL_PORT=1025
   MAIL_USERNAME=coordinator1
   MAIL_PASSWORD=Haslo123
   EMAIL_UNIVERSITY_DOMAIN=example@email.email
   LDAP_URL=
   LDAP_BASE=
   LDAP_DOMAIN=
   📢 IMPORTANT!: Those 3 above can be empty when runing localy.
   FF_EMAIL_TO_UNIVERSITY_DOMAIN_ENABLED=true
   FF_LDAP_AUTHENTICATION_ENABLED=false
   SCHEDULED_JOBS_ENABLED=false
6. Run docker engine.
7. Open console in deploy folder and type:

   ```
   docker compose up -d
   ```

   It will run the file `docker-compose.yml` and download the build docker images of frontend and backend that are build after each commit to main branch.
   (📢 IMPORTANT!: It requires github actions CI-pipline to be configured. For that go to other resources like Google, Youtube, Chatbots ect.)

   📢 IMPORTANT!: Downloading images from ghcr.io (GitHub Actions) requires Authentication!

   `image: ghcr.io/projekt-inzynierski-pri-kkwj/ui-no-ssl:latest`

   `image: ghcr.io/projekt-inzynierski-pri-kkwj/core:latest`

   📢 IMPORTANT!: The `:latest`  will always download the newest modified conteiner.

   📢 IMPORTANT!: Change the links in the `docker-compose.yml` to links pointing to your repsoitory.
8. Wait till images are downloaded from github actions and deployed localy.
9. When images are deployed go to the project site in your browser.
10. First site that should open is initialization page for coordinator/admin of the system. ( If you run the project locally with mock users, use the exact logins specified in "core\pri-application\src\main\resources\ldap-mock-data.ldif" next to "uid:", name and last name specified next to "cn:", "sn:" values. uid=indexNumber. )
11. After successfull initialization login site should appear, log in to coordinator account.
12. When in the system, precced to data-feed tab to import supervisors, studnets and criteria in .csv files ( If  runing localy for development, import Test-mock students, supervisors and criteria from the support files folder in deploy directory ).
13. Before you will be able to add project you need to configure the supervisor availability in Project tab when clicking on Supervisor Availability button.

📢 IMPORTANT!: Password for mock ldap accounts is: "Haslo123".

📢 IMPORTANT!: If you want images with your changes to be build on github you need to configure GitHub actions (CI pipline). For how to do that, go to other resoureces like e.g.: Google, Youtube, Chatbots etc.

📢 IMPORTANT!: For lunching frontend and backend separatly go to specific README.md file in respective repositories.

## Feature flags

List of feature flags:

| Feature Flag                              | Bahaviour                                                                                                                                                   |
| ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `FF_EMAIL_TO_UNIVERSITY_DOMAIN_ENABLED` | `false` - e-mails which contains the domain defined in the variable `EMAIL_UNIVERSITY_DOMAIN` are not sent`` `true` - emails are sent to users always |
| `FF_LDAP_AUTHENTICATION_ENABLED`        | `false` - mock authaticantion is used `true` - real ldap is used for authentication                                                                     |

#### Secrets

See `config.env.example` file.

#### Authentication

To use mocked ldap authentication:

* the variable `ldap.authentication.enabled` has to be set to `false` (see `config.env.example` file)
* mocked ldap data are in the file `ldap-mock-data.ldif` in core project

#### Coordinator user

First site that apperas after system is initiated is initialization of coordinator unlees the user_data tabel is not empty.
( If you run the project locally with mock users, use the exact logins specified in "core\pri-application\src\main\resources\ldap-mock-data.ldif" next to "uid:", name and last name specified next to "cn:", "sn:" values. )

📢 IMPORTANT!: uid=indexNumber.

The coordinator user index number corresponds to the LDAP username.

#### Docker compose

The properties of building and delpying are specified in the `docker-compose.yml` file.

📢 IMPORTANT!: PRI 1.0 had two files one with implemented ssl certificats generated inside the applicaion, however it's bad programing practice to put that functionality inside the backend of application so this functionality isn't in use and the uniwersity has it's own proxy for this. Do to this factor there is some legacy code in the application acociated with this.

#### Profiles

Docker composes uses spring profile from `application\src\main\resources\application-docker-dev.properties` to build the enerviroment of the project.

### Starting the application

To run the application and rebuild Docker images use command:

```
docker compose -f docker-compose.yml --env-file ${path} up --build
```

To restart the application without Docker images rebuild use command:

```
docker compose -f docker-compose.yml --env-file ${path} up`
```

`${path}` path to the config.env file (e.g. `--env-file config.env`)

📢 IMPORTANT!: To enable the automatic building of the images on github after each commit, you need to enable github actions (CI pipline). Don't Forget to change the links in docker files and docker.compose.yml. For that see other resources like Google, Youtube, Chatbots, etc.
