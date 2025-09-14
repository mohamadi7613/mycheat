
# CICD

+ CICD is a tool that automates the process of building, testing, and deploying software
+ enabling teams to release updates faster, more reliably, and with fewer errors

## CD: Continuous Deployment
+ we dont deploy directly in production env, we deploy in satges: stage 1, stage 2, PROD
+ deployment to non-production is automated, but update to production is manual approval
+ 1. dev env --> 2. Unit testing --> 3. depoly to staging --> 4. Regression testing --> 5. depoly to PROD

## CI: Continuous Integration
+ when developer commits the code in git repository, it will trigger CI, now changes should be merged, so developers merge their code back to master branch


## type of tests
+ we have: 1. functional testing 2. non-functional testing 3. Specialized Testing
+ Functional Testing:
    1. `Unit Testing`: Tests individual components (e.g., functions, methods) in isolation.
    2. `Integration Testing`: Checks interactions between modules/services (e.g., API + database).
    3. `Regression testing` verifies that new code changes don’t break existing functionality
    4. `User Acceptance Testing (UAT)`: Final validation by end-users/clients before release



## Gitlab architecture
1. gitlab server
2. gitlab runners

> 1. Gitlab Server
    + gitlab server = gitlab instance = gitlab installation
    + is the central hub that hosts repositories, manages users, and orchestrates CI/CD workflows.
    + gitlab server manages and stores the results of the CI/CD pipeline

> 2. Gitlab runners
    + gitlab runners are agents that run CI/CD jobs defined in `.gitlab-ci.yml`
    + gitlab runners are separated from gitlab server and Can be installed anywhere (local machine, cloud, Kubernetes).
    + gitlab runner fetch the job from gitlab server and execute them in a specific executor
    + each job runs in a separate runner
    + 3 types of gitlab runners= 1. Shared runners 2. Specific runners 3. Group runners

> 3. Executor
    + An executor is a configuration setting in a GitLab Runner 
    + gitlab supports multiple execution environments: shell, docker, kubernetes, virtual machine, etc. 
    + default executor in shared runners is 'docker', but for self-hosted runners deponds on our configuration(if not specified is shell)

>> docker executor
    + GitLab deprecated `Docker Machine` (it was another executor)
    + commands in .gitlab-ci.yml are executed in a docker container
    + each job runs in a separate docker container on diff runners, and after finishing, the docker container is removed
    + we can change the default docker image executor in .gitlab-ci.yml 

>> Scope of runners
    + 3 types of runners: 1. shared runners, 2. group runners, 3. specific runners
    + `Shared Runners` are pre-configured CI/CD runners available in a gitLab.com (read first line of shell)
    + `Group Runners` if you have multiple projects in one group  and want them to share runners.
    + `Specific Runners` are self-hosted CI/CD runners. By default they are loced for one project but we can change it.


### Basic Concepts

1. Pipeline: A set of automated top-level steps (build, test, deploy). [the whole proccess of .gitlab-ci.yml]
2. Stage: A Group of jobs that run in parallel (e.g., build, test, deploy) [phase of pipeline]
3. Job: Individual task that runs scripts/commands within stage
4. Artifact: Built output passed between jobs (e.g., a .jar or .zip).
5. Runner: Agent that executes jobs



### common stages
+ Unit test ---> SAST test ---> build image ---> push to registry ---> deploy to dev env ---> deploy to staging ---> deploy to prod
+ `SAST (Static Application Security Testing)` is a white-box security testing method that analyzes source code, bytecode, or binaries without executing the program to identify vulnerabilities


## Configuration File (.gitlab-ci.yml)

+ If no stages are defined, all jobs default to one stage

```yaml
tests_job:                            # this block is a job, we can have multiple jobs
    before_script:                        # Use before_script for preparing the environment.
        - echo "running before script"
        - chmod +x test.sh           # make test.sh executable
        - ./test.sh                  # this file is inside the repo
    script:
        - echo "running tests"
    after_script:                         # Use after_script for cleaning up or collecting results like stopping services
        - echo "running after script"     # use after_script no matter success or failure.
                                          # stopping a service, cleaning up temp files, uploading logs for debugging, close db
build_job:                            # this block is another job
    script:
        - echo "building image"
```


### simple example

```yml
stages:
  - build
  - test
  - deploy

build_job:
  stage: build
  script:
    - echo "Building..."
    - make build

test_job:
  stage: test
  script:
    - echo "Running tests"
    - pytest

deploy_job:
  stage: deploy
  script:
    - echo "Deploying"
  only:
    - main
```


### Common Keywords

| Core Configuration    | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| `stage`               | Defines which pipeline stage the job belongs to                             |
| `script`              | Main shell commands to execute in the job                                   |
| `before_script`       | Commands executed before the main script                                    |
| `after_script`        | Commands executed after the main script (runs even if job fails)            |

| Execution Control     | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| `only`/`except`       | Basic control when jobs run (branches/tags/etc.)                            |
| `rules`               | Advanced conditional job execution (replaces only/except)                   |
| `when`                | When to run job (`always`, `on_success`, `on_failure`, `manual`, `delayed`) |
| `allow_failure`       | Whether pipeline should continue if job fails                               |

| Data Management       | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| `variables`           | Define CI/CD variables                                                      |
| `artifacts`           | Files to pass between jobs                                                  |
| `cache`               | Files/directories to cache between pipeline runs                            |
| `dependencies`        | Specify which artifacts to download from previous jobs                      |

| Docker Configuration  | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| `image`               | Primary Docker image to use                                                 |
| `services`            | Additional Docker images to link to the job                                 |
| `tags`                | Specify which runners can execute the job                                   |

| Deployment Conf       | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| `environment`         | Defines deployment target (name, URL, etc.)                                 |
| `deployment`          | Advanced deployment configuration                                          |
| `interruptible`       | Whether job can be canceled when newer pipeline starts                      |

| Advanced Elements     | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| `parallel`            | Number of parallel job instances to run                                     |
| `retry`               | Number of times to retry a failed job                                       |
| `timeout`             | Job timeout duration                                                        |
| `trigger`             | Downstream pipeline trigger                                                 |
| `include`             | Include external YAML files                                                 |
| `extends`             | Configuration inheritance                                                   |

## stage
+ Stages define the order of execution
+ By default, jobs run stage by stage
+ All jobs in one stage must finish before the next stage starts
+ Typical stages might be 1. build, 2. test 3. deploy
+ only if all jobs in the stage succeed, the next stage will be execute
+ Multiple jobs in the same stage are executed in parallel
+ Pipelines can be triggered by various events, like commits or merges

```yaml
# order of execution:  test_job ---> build_job
stages:          # define a list of stages
    - test         # order is imp
    - build       # build will run as a second stage
test_job:         
    stage: test             # this job runs in stage "test"
    script: 
        - echo "running tests"
build_job:
    stage: build           # this job is inside the stage "build"
    script: 
        - echo "running building"
```

### dependency between jobs

```yaml
stages:         
    - test
    - build
test_job:         
    stage: test  
    script: 
        - echo "running tests"
build_job:
    stage: build       # Run job out of order (DAG =Directed Acyclic Graph)
    needs:            # this job depends on another job 
        - test_job        # # Only waits for test_job, not whole test stage
    script:               # it does not matter if there are other jobs in test stage
        - echo "runs building"   # needs: [test_job]     # syntax: we can wire jobs in array
```

### global before_script

```yaml
before_script:                 # both job1 and job2 will first run the dependency setup, then their own script
  - echo "Install dependencies"      # before_script per job will overrides global
  - apt-get update -y

job1:                     # first run before_script
  script:
    - echo "Run tests"

job2:                     # first run before_script
  script:
    - echo "Build project"
```

### condition with branches

+ Each pipeline runs in a fresh environment
+ by default every time we push in `any branch` it automatically execute all the pipelines
+ `only` and `execpt` are deprecated in favor of `rules` but they are still work.

```yaml
build_image:
    only:           # specify for which branch this job should be execute or not be execute
        - main      # execute for this list of branches
        - hotfix    # use one of them (only/except)
    except:         # not execute for this branches
        - feature-branch     
    stage: build
    script:
        - docker build -t myimagename:tag .
```

### condition with files

```yml
job_name:
    only:                            # if any of these files change run the job
        changes:                 # in only statement we can use $variables
            - 'folder/**/*'
    script:
        - echo "hello"
```

### rules

+ `rules:` is a way that control when a job should run (like 'only')
+ `when` gives us a fallback controll branch
+ `when` values: 
    1. on_success (default): Run if previous jobs succeed
    2. always: Run regardless of success/failure
    3. never: Don’t run if condition is True
    4. delayed: Run after delay (needs start_in)

```yaml
workflow:      # controls the whole pipeline   # write at the begining
    rules:       # also we can use rules inside jobs
        - if: '$CI_COMMIT_BRANCH == "main"'        # condition with if (like only: - main)
          when: never                             # never on branch main run the commands
          start_in: 5 minutes                    # required if you set 'delayed'
        - if:                                     # we can set multiple conditions
```

### variables
two types of variables: 1. predefined 2. custome

#### 1. env variables (predefined variables in gitlab)

```bash
# common conditions
$CI_COMMIT_BRANCH                                 # branch variable
$CI_COMMIT_BRANCH == "main"	                      # run only if branch is main
$CI_PIPELINE_SOURCE == "merge_request_event"     #	run only on merge requests
$CI_PIPELINE_SOURCE == "schedule"	            # run only on scheduled pipelines
$CI_COMMIT_TAG	                                 # git tag  # if: '$CI_COMMIT_TAG'   # Run only when a tag is pushed
$CI_COMMIT_MESSAGE =~ /skip/	               # Commit message contains "skip"
$CI_COMMIT_REF_NAME =~ /^v\d+/	               # Branch or tag starts with v + digits
$CI_JOB_STAGE	                                   # Current stage name
$CI_MERGE_REQUEST_TARGET_BRANCH_NAME == "main"	# MR targeting main branch
$MY_CUSTOM_VARIABLE == "true"                     # Run job only if a specific variable is set

# 1. Pipeline-Level Variables
$CI                              # Always set to true in GitLab CI/CD environment  
$CI_PIPELINE_ID                  # Unique ID of the pipeline  
$CI_PIPELINE_SOURCE              # Trigger type: push, merge_request_event, schedule, etc.  
$CI_PIPELINE_IID                 # Internal ID of the pipeline (sequential number)  
$CI_PIPELINE_URL                 # URL to the pipeline in the GitLab UI  


# 2. Job-Level Variables
$CI_JOB_ID                       # Unique ID of the current job  
$CI_JOB_NAME                     # Name of the job as defined in .gitlab-ci.yml  
$CI_JOB_STAGE                    # Stage this job belongs to (e.g., build, test)  
$CI_JOB_STATUS                   # Status of the job (running, failed, success)  
$CI_JOB_URL                      # URL to view the job in the GitLab UI  
$CI_JOB_TOKEN                    # Token used to authenticate with GitLab API  

# Commit / Branch / Tag Variables
branch                          # 'branch' is a variable with no $
$CI_COMMIT_SHA                   # Full commit SHA  
$CI_COMMIT_SHORT_SHA            # Shortened commit SHA (first 8 characters)  
$CI_COMMIT_REF_NAME             # Name of the branch or tag  
$CI_COMMIT_BRANCH               # Branch name (empty if triggered by tag)  
$CI_COMMIT_TAG                  # Tag name (only if pipeline is for a tag)  
$CI_COMMIT_TITLE                # Title of the commit message  
$CI_COMMIT_DESCRIPTION          # Description (body) of the commit message  

# Merge Request Variables
$CI_MERGE_REQUEST_ID            # ID of the merge request  
$CI_MERGE_REQUEST_IID           # Internal merge request ID (project-wide)  
$CI_MERGE_REQUEST_TITLE         # Title of the merge request  
$CI_MERGE_REQUEST_SOURCE_BRANCH_NAME  # Source branch of the MR  
$CI_MERGE_REQUEST_TARGET_BRANCH_NAME  # Target branch of the MR  

# Project-Level Variables
$CI_PROJECT_ID                  # Numeric ID of the project  
$CI_PROJECT_NAME                # Repository name  
$CI_PROJECT_PATH                # Full path (namespace/project_name)  
$CI_PROJECT_URL                 # Full URL to the project  
$CI_PROJECT_DIR                 # Directory where the repo is cloned  
$CI_DEFAULT_BRANCH              # Default branch (e.g., main or master)  

# Runner-Level Variables
$CI_RUNNER_ID                   # ID of the GitLab Runner used  
$CI_RUNNER_DESCRIPTION          # Description of the runner  
$CI_RUNNER_TAGS                 # Tags assigned to the runner  

# Environment Variables
$CI_ENVIRONMENT_NAME            # Name of the environment (e.g., production)  
$CI_ENVIRONMENT_URL             # URL of the environment (if set)  
$CI_ENVIRONMENT_SLUG             # Slug of the environment  

# Docker & Registry Variables
$CI_REGISTRY                   # GitLab Container Registry address  
$CI_REGISTRY_IMAGE             # Registry path for the project image  
$CI_REGISTRY_USER              # Username to authenticate (usually gitlab-ci-token)  
$CI_REGISTRY_PASSWORD          # Token used for Docker login  
$DOCKER_AUTH_CONFIG            # Docker config file (JSON) for authentication  
```

#### 2. custome variables
+ we can also define in `gitlab-ci.yml`

```yaml
variables:                   # global variables 
    my_name: "ali"       # for values we want to use multiple times

job_name:             
    variables:           # local variables         
        my_id: 45         # values for each job 
    script:         
        - echo "$my_id" , "$my_name"     
```

### 3. Protected and Masked Variables

+ our important custome variables should not be write inside `gitlab-ci.yml`, good for tokens and passwords
+ `Protected variables` run only on protected branches/tags.
+ `Masked variables` hide their value in job logs (only alphanumeric and underscores allowed).
+ Set in: GitLab UI > Settings > CI/CD > Variables
+ this variables will export in linux bash of runner

```yaml
job:
  script:
    - echo "Deploying to environment: $my_password"
```


#### 3. custome variables (File type)

+ we have 2 types of variables in Gitlab UI: 1. default 2. type
+ `variable type`: GitLab creates a temporary file and stores the variable’s value inside that file during the pipeline
+ Ideal for private keys, certificates, config files, etc.
+ go to:  settings ---> CI/CD ---> variables
+ write the 'name of temp file' in `key` field

```json
address: mysite.com         # wrtie this in `Value` section
env: app.mysite.com          # these values will store in a temp file
```

```yaml
push_image:
    stage: build
    script:
        - echo "$NAME_OF_TEMP_FILE"         # the value is the path of file when using 'echo'
        - cat $NAME_OF_TEMP_FILE           # print the content with 'cat'
```



### Docker executor

```yaml
image: node:17-alpine         # use this image as an executor, now each job runs in a diff container of node:17-alpine
run_tests:                    # we can use image in general or for specific job 
    image: python:17-alpine   # set an image for specific job
    script:                   # we can see the name of the image in the first line of logs
        - npm install         # if you need npm so you should change the defulat image
```


## Self-hosting


### 1. self-hosted runner
+ A self-hosted runner is your own machine that runs GitLab CI/CD jobs instead of using GitLab's shared runners.
+ Ideal for private projects,
+ usefull when we have diff environments, OS, or tools.
+ Faster and more reliable than shared runners
+ we can have multiple runners for example a runner in our developer system and a runner in aws account
+ we can see list of runners from: Settings > CI/CD > Runners
+ we can use each runner for specific job with `Tags`. for example: one for tests and one for build
+ when you run `sudo gitlab-runner register` it asks you to enter a list of tag names
+ the combination of tags should be unique for each runner

```bash
# windows
.\gitlab-runner install              
.\gitlab-runner status        # 
.\gitlab-runner register      # Token, Tags: go to: settings > CICD > runners and copy the token
# linux
sudo gitlab-runner register
sudo gitlab-runner start
sudo gitlab-runner stop
sudo gitlab-runner restart
```


###### Tags

+ Tags in GitLab CI/CD link your jobs to specific GitLab Runner
+ Tags come from installing self-hosted runners

```yml
run_tests:                      # in remote-runner we should specify tags
    tags:                      # these tags means that we want to run this job in a runner that have these tags
        - aws                   # the combination of tags should be unique for each runner
        - local                 # we use tags to select a specific runner from the list of all runners
run_node:                        # for this job we should specify diff unique tags
    image: node:17-alpine       # if I use a docker as excecutor in aws runenr or local runner, I can specify the image
    tags:                      # but if the excecutor is not docker like shell, I can not specify the image 
        - aws
        - docker
```



### 2. self-hosted server(instance)

+ Ideal for companies, private projects, or offline development.
+ gitlab server and gitlab runners are installed on separate servers
+ ensuring compatibility: if they have a diff versions, they will still work together
+ it has lost of steps for installing but just know one of them : `apt install gitlab-ce` 
+ gitlab-ce = giltab community edition







### Artifacts
+ Artifacts are job outputs
+ Artifacts are files or directories that are saved after a job and passed to later jobs
+ we can download artifacts from jobs and passing them between jobs
+ by default we can download `job.log` as an artifact
+ Go to Build > Pipelines 

```yaml
job_name:
  stage: build
  script:
    - make build
  artifacts:
    paths:                 # List of files or directories to keep as artifacts.
      - build/output/
    exclude: 
      -  *.log               # Optional   #  Skip log files in artifacts
    expire_in: 1 week       # Optional and default is forever          #  Common values: 30 min, 1 hour, 1 day, 2 weeks, never
    when: always            # Optional and default is on_success        # Common values: on_success, always, on_failure even      
    name: artifact_name     # Optional    # Custom name for the artifact archive
```

##### Use Artifacts in Later Jobs

```yaml
build:
  script:
    - make build
  artifacts:
    paths:
      - build/

test:
  dependencies:
    - build                # Pull artifacts from 'build' job
  script:                  # If `dependencies` is omitted, artifacts from all previous jobs in the same stage are downloaded
    - run-tests build/
```

##### Artifacts: reports

+ GitLab can process various report types to display results in the UI
+ report types like: 
    1. junit:               JUnit test results
    2. cobertura:           Code coverage reports
    3. codequality:         Code Quality reports
    4. sast:                Static Application Security Testing reports
    7. dast:                Dynamic Application Security Testing reports
    5. dependency_scanning: Dependency scanning reports
    6. container_scanning:  Container scanning reports
    8. license_management:  License compliance reports
    9. performance:         Performance metrics
    10. metrics:            General metrics


```yaml
test:
  stage: test
  script:
    - npm run test > junit-report.xml          # with jest-junit in js we can create a junit format report
  artifacts:
    paths:
        - app/test-reports/
    reports:                              # Special section for structured data (like JUnit, coverage)
      junit: junit-report.xml           # my file name
      cobertura: coverage/cobertura-coverage.xml
    expire_in: 1 week                      
    when: always                          # on_success, always, on_failure even
```

### pass a file between jobs
+ when we have a file inside a job(server) we can pass this file to another job(another server)
+ the artifacts are sent to gitlab after finishing the job, and can be downloaded by another job
+ by default all the jobs in later stages automatically download all the artifacts from the previous stages
+ for the jobs in the same stage the artifacts wont be downloaded automatically ---> use `dependencies`
+ `needs` and `dependencies` have same functionallity but they have diff perposes
+ with `needs` artifacts from the job listed will be downloaded automatically by default, and you dont need to use `dependencies` separately
+ to prevent access of a job to artifacts from previous stages use `dependencies: []` or we can name some of them (increase speed)

```yaml
build_image:                       # both the jobs are in the same stage
    script:
        - echo "my version" > version_file.txt 
    artifacts:
        paths:
            - version_file.txt           # this file will be sent to gitlab
another_job:
    dependencies:
        - build_image
    script:
        - echo version_file.txt          # we access this file in another job
```


#### pass a variable between jobs: .env format
```yaml
build_image:
    script:
        - echo "VERSION=$VERSION" > version_file.env            # env file for nodejs
    artifacts:                                 # gitlab provide dotenv format specificly
        reports:                               # by reports we dont need to use `paths`
            dotenv: version_file.env            # by dotenv gitlab automatically export them in linux and we dont need to read them from file
another_job:
    dependencies:
        - build_image
    script:
        - echo version_file.env          # we access this file in another job
        - echo $VERSION                  # .env export attributes in linux
```


### Packages and registries

+ `registry.gitlab.com` is GitLab's container registry, secure, private space to store and manage Docker container images for your projects.
+ GitLab lets you store and manage packages, artifacts, and Docker images right inside your GitLab project using:
    1. Package Registry
    2. Container Registry
    3. Infrastructure registry
+ package registries: we can push and pull packages in npm, maven, PyPI, etc.
+ container registries: we can push and pull docker images ---> Every project have its own registry space
+ infrastructure registries: terraform

#### 1. package registry
#### 2. container registry
+ in container registry we can store multiple image repositories
+ address: registry.gitlab.com/username/my-app
+ Eeach image repository can store multiple image versions
+ `Root image` is the base image for all unnamed images

```yaml
# predefined Variables:
# $CI_REGISTRY_USER = Authenticated user
# $CI_REGISTRY_PASSWORD = Token for authentication
# $CI_REGISTRY = registry.gitlab.com
# $CI_REGISTERY_IMAGE = full image name
# $CI_JOB_TOKEN = used to authenticate

build_image:                                 
    stage: build                      
    before_script:                    
        - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD   registry.gitlab.com      # bcz its a private registry we need login
        - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD   $CI_REGISTRY         
    script:                           # build and push
        - docker build -t myimage .     # --tag = -t     # dot is for location of dockerfile
        - docker push myimage             # we can use another job to push the image
        - docker push $CI_REGISTRY_IMAGE/myimageName:1.0        # if you dont write myimageName it will be unnamed
```


### deploy to server
+ after `build_image` and pushing the image to the registry, we need to deploy the image to the server
+ we use ssh approch for deploy to production (not using gitalb)

```yaml
variables:                                # StrictHostKeyChecking means that we dont want to check with "Enter" key
    SERVER_IP: 89.0.142.86                # cotation in ssh used for commands in remote after connecting
    SERVER_USER: ubuntu                   # before "docker compose up" you need to login for pulling the image from registry
    SERVER_PATH: /home/ubuntu/app          # SSH_KEY should be set as a variable file in gitlab ui settings for security
deploy_to_prod:                            # we want to ssh to the gitlab for getting the image and run it 
    stage: deploy                             #with ssh our files are inside gitlab server and not inside the remote runner
    before_script:                          # our files are available for remote runner but they are not inside the remote runner
        - chmod +x deploy.sh                # another way is to write our script in this file
        - chmod 400 $SSH_KEY                # give permissions with "chmod" to this file
    script:                                 # its good idea to stop docker-compose before running a new one
        - scp -o StrictHostKeyChecking=no -i $SSH_KEY -r $SERVER_USER@$SERVER_IP:$SERVER_PATH $SERVER_PATH
        - ssh -o StrictHostKeyChecking=no -i $SSH_KEY $SERVER_USER@$SERVER_IP "cd $SERVER_PATH && docker-compose up -d --build"
        # there is no need for pulling image from registry since when we run a docker that is not available it will pull
```

###### gitlab environments

+ Environments in GitLab CI/CD represent deployment targets for your application (like staging, production, etc.)
+ it has a url section which makes it a clickable link in GitLab's UI
+ Each environment allows you to:
    - Track what code is deployed where
    - Access live environments via GitLab UI
    - Integrate with Kubernetes, monitoring, and review apps

```yaml
deploy_to_staging:                     # in gitlab ui go to operate --> environments
  stage: deploy                     # by this feature we know which verion and which commit depolyed to the environment
  script:
    - npm start
  environment:
    name: staging                              # desired name of the environment
    url: https://staging.example.com           # Adds a link to the live app in gitalb ui
```



### types of versioning
1. Semantic versioning
    + 1.4.2
    + 1 --> Major change like replacing a framework, major code refactoring, add major feature
    + 4 --> Minor change like add a minor feature, bigger bug fix
    + 2 --> Patch change like fix a bug
2. Unaray versioning
3. Date of release versioning


### dynamic versions (not hard code)

```yaml
build_image:
    stage: build                                    # get version for nodejs
    before_script:                               # jq for parsing json [make sure jq is installed]]
        - cat app/package.json | jq -r '.version'            # get the value of verion attribute in package.json
        - export VERSION_PACKAGE_JSON=$(cat app/package.json | jq -r '.version')      # export the value as a global variable in linux
        - export VERSION= $VERSION_PACKAGE_JSON.$CI_PIPELINE_IID          # $CI_PIPELINE_IID is unique number for each pipeline
        - # by this appraoch even we dont change the version in package.json we can use CI_PIPELINE_IID
    script:           
        - docker build -t $IMAGE_NAME:$VERSION .             # $VERSION is inside linux
```



### Cache

+ cache is a mechanism that saves files or directories between pipeline jobs to speed up the pipeline 
+ cache is used to avoiding repetitive work—like reinstalling dependencies.
+ Best for Dependencies (node_modules, gems, pip packages in .venv) and resuing them
+ 1. jobs in second execution will use the cache 
+ 2. if two runners use the same cache, one of them download the cache from first runner
+ `Artifacts` get uploaded to gitlab server but `Cache` is for dependencies like `node modules` and store in `runners` directly.
+ its better to use less runners instead of caching in lots of servers. but if we have lots of runners, its better to use a `central caching server`.
+ 
+ cache policies:
    + pull: Only restore from cache
    + push: Only save to cache
    + pull-push: Default (both restore and save)

+ we can create a job only for building and generating the cache with `policy: push` and other jobs should have `policy:pull`
+ caching is an optimisation and there is no garantee that it should always work, so jobs should never depond on a cache.
+ we can check `duration time` in gitlab ui, for example when we run the same job for second time
+ in gitlab ui we can delete caches by clicking `Clear runner cache`, but it physically remain in the runner
+ for clearing completely the cache, we should use docker commands or linux commands.
+ `key` is an unique identifier for job which determines whether to reuse an existing cache or create a new one

```yaml
job_name:                               # both jobs are in the same stage and execute in parallel
    stage: "mystage"                      # we can use cache field for each job
    script:                               
        - npm install                    
    cache:                               #  key is optioan but powerful  
        key: $CI_COMMIT_REF_NAME        # key gives a unique name for each job, if not set the default key is "default"
        paths:                           # path means which files should be cache and where you want to save?
            - ./node_modules             # like artifacts we should specify a path for location of cache files
        policy: pull-push                 # pull-push means job download the cache from internet and push its changes at the end
another_job:
    stage: "mystage"                    # this job should be read-only since it is in the same stage and uses the same cache
    script:
        - npm install                    # check after this command inside the logs, for caching it will say "up to date"
    cache:
        key: $CI_COMMIT_REF_NAME                # this job will use the same cache
        policy: pull                       # pull means only downlad the cache and dont upload

#  Common key strategies:

# 1. Branch-based Key (Common)
key: ${CI_COMMIT_REF_SLUG}  # Creates separate caches per branch (Good when branches have different dependencies)
key: ${CI_COMMIT_REF_NAME}    # represents the branch name
# 2. Job-based Key (Common)
key: ${CI_JOB_NAME}         # Creates separate caches per job (Good when jobs have different dependencies)
# 3. File-based Key (Recommended for dependencies)
key:
  files:
    - package-lock.json  # Changes to this file invalidate cache
# 4. Composite Key (Combines multiple variables)
key: ${CI_COMMIT_REF_SLUG}-${CI_JOB_NAME}
key: ${CI_JOB_NAME}-${CI_COMMIT_REF_SLUG}
# 5. Static Key
key: global-cache                    # Single cache shared across all pipelines/jobs (can cause cache collisions)
# 6. default
# if not set the default key is "default"
# This can cause cache collisions between different branches or jobs
# Without a key, GitLab uses a global cache and all jobs using the default key share the same cache
```


#### cache in docker runner
+ in docker executor our files are not inside file system, they are in docker container.
+ jobs get executed inside a new container and when job finishes the container will remove. 
+ so we should use `docker volume` to store these date inside file system of runner.


```toml
# config.toml                 # 
# sudo vim /etc/gitlab-runner/config.toml                   # change the behaviour of runner
# C:\\GitLab-Runner\\config.toml"
###########################
[[runners]]
name =""
token = "e2j3m"
executor = "docker"
cache_dir = "/cache"                         # add this line
volumes = ["/cache"]                      # save the volume in the same location of cache
```

#### delete cache in docker runner

```bash
docker volume ls                          # list of docker volumes
docker voulme inspect  e2dk2               # inspect one of them
# use inspect command to find the location of volume
```


### templates
1. job templates
    + A CI/CD `component` = `job template` is a reusable pipeline configuration and a boilerplate of .gitlab-ci.yml content.
    1. local job templates (DRY code)
    2. gitlab offical templates 
    3. job templates from other projects
    
2. pipeline templates
    + suggested predefined templates for each language and framework, in the page of CICD before creating a `.gitlab-ci.yml`
    + with pipeline template we dont need to write our yaml file from scartch for each language
    + when you have no cicd yet go to `CICD ---> pipelines` and find your boilerplate



#### offical job templates
+ for find the list of job templaets from official gitlab main repo: `https://gitlab.com/gitlab-org/gitlab`
+ offical gitalb repo provides job templates like SAST, DAST, ...
    + SAST (static code security testing)  --> analyse the code whitout runing it
    + DAST (dynamic code security testing)  --> testing when app is running
    + SAST job creates a artifact report which we can download from gitlab ui


```yaml
other_jobs:                    # for using job template in your yaml file just mention that file in your config
    script:
        - npm install
include:                            # SAST.gitlab-ci.yml file includes multiple jobs for security in the pipeline
    - template: Jobs/SAST.gitlab-ci.yml            # this path comes from job templates inside gitlab ui
sast:                               # this trigger the SAST file
    stage: test                      # "template" key is for offical gitlab templates
```

#### local job templates
```yaml
######### my job_template.yml
variables:                     # variables are Optional but good for clean code
    IMAGE_TAG: ""
    IMAGE_NAME: ""
my_job_file:
    before_script:
        - cd app/
    script:
        - docker build -t $IMAGE_NAME:$IMAGE_TAG
######## new_file.yml
include:
    - local: '.my_job_template.yml'             # create this file with empty variables
my_job_file:                               # use the job in another file
    tags:                            # we can add additional information
        - local
    before_script:                   # this will override from parent job
        - echo "something"            # if you want to add information you should copy the caommnds and write them again and the add new commands
```


#### job templates from another repository

+ `monorepo`: a Microserice project in one git repo (deploy them separately but keep them in one repo)   ----> easire, good for small Microserices
+ `polyrepo`: a Microserice project in multiple git repo ---> complete isolation, separate teams ---> use `group runners`

```yml
job_name:                   # good for Microserices
    include:              # create another repository and inside it create all your yaml files
        project: group_project_repo_name/repository_name_of_template       # if you add repo of template in gitlab_project_group
        project: username/repository_name_of_template                     # use this if repo is not inside in gitlab_project_group
        ref: main                                           # branch name or commit hash
        file:                                                # list of yaml file in that repository
            - build.yml
            - test.yml
        # we can add job_templates from multiple sources with : 1. local 2. project 3. rempte 4. template
        remote: 'other_urls'                 # use 'remote' for someone else project
```




### extends in cicd
+ is used to reuse configuration
+ job inherits configuration from another job
+ `extends` is Job-level but `include` is File-level


```yaml
.job_father:                           # dot before job name make it hidden bcz if this job run, it will fail since it has empty variables   
    variables:                               # like a base schema
        - APP_PORT = ""                   # pass these variables from child
    script:
        - echo $APP_PORT
        
job_child:                         # extends supports 11 level of inheritance
    extends: .job_father            # use dot since it is part of the name
    stage: "testing"
    variables:
        - APP_PORT = "3000"
```

### Manual job

```yaml
manual_job:                 # in real life we use manual approval for production
    stage: production       # its good for human review
    script:                  # adds a button in gitlab ui
        - echo "manual job"
    when: manual
```


#### Group runners
+ we can use a runner for different projects, for this click to edit runners (by defailt you cant)
+ in gitlab there is something called `groups`, which means that we can group multiple projects that belong together
+ in the repo of `group project` we have a new setting for CI/CD which ic `group runners` 
+ we should create `gitlab-ci.yml` for each project separately












