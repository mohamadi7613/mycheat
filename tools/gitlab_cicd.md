
# CICD


## CD: Continuous Deployment
+ we dont deploy directly in production env, we deploy in satges: stage 1, stage 2, PROD
+ deployment to non-production is automated, but update to production is manual approval
+ 1. dev env --> 2. Unit testing --> 3. depoly to staging --> 4. Regression testing --> 5. depoly to PROD

## CI: Continuous Integration
+ when developer commits the code in git repository, it will trigger CI, now changes should be merged, so developers merge their code back to master branch


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
    + `Group Runners` are runners available to all projects in a group
    + `Specific Runners` are self-hosted CI/CD runners. By default they are loced for one project but we can change it.


### common stages
Unit test ---> SAST test ---> build image ---> push to registry ---> deploy to dev env ---> deploy to staging ---> deploy to prod

## structure
```yaml
# job: basic building block of pipelines   #  run_tests(job) ---> build_image(job)
run_tests:        # this block is a job, we can have multiple jobs
    before_script:
        - echo "running before script"
        - chmod +x test.sh           # make test.sh executable
        - ./test.sh                 # this file is inside the repo
    script:
        - echo "running tests"
    after_script:
        - echo "running after script"
build_image:      # this block is another job
    script:
        - echo "building image"
```

## stage
+ we can group multiple jobs into stages that run in a defined order
+ Stages define the order of execution
+ only if all jobs in the stage succeed, the next stage will be execute
+ Multiple jobs in the same stage are executed in parallel
+ Pipelines can be triggered by various events, like commits or merges

```yaml
stages:          # define a list of stages
    - test         # order is imp
    - build       # build will run as a second stage
run_tests:         
    stage: test             # this job runs in stage "test"
    script: 
        - echo "running tests"
run_tests2:
    stage: test           # this job is inside the stage "test"
    script: 
        - echo "running tests 2"
```

### dependency between jobs
```yaml
stages:         
    - test
    - build
run_tests:         
    stage: test  
    script: 
        - echo "running tests"
run_tests2:
    stage: test 
    needs:            # this job depends on another job so do not run this until the other job is done
        - run_tests   # do not run in parallel
    script: 
        - echo "running tests 2"
```
### condition with branches
+ Each pipeline runs in a fresh environment
+ by default every time we push in `any branch` it automatically execute all the pipelines

```yaml
build_image:
    only:           # specify for which branch this job should be execute
        - main      # list of branches
    except:         # not for this branches
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

### workflow rules

```yaml
workflow:      # controls the whole pipeline   # write at the begining
    rules:       # also we can use rules inside jobs
        - if: branch is not main        # condition
          when: never         # when branch is not main never execute the whole pipeline
        - when: always       # else execute it always
        
```

### variables
two types of variables: 1. predefined 2. custome

#### 1. env variables (predefined variables in gitlab)

```bash
branch                          # if branch is not main 
$CI_COMMIT_BRANCH                # if $CI_COMMIT_BRANCH != "main"      # text value
$CI_PIPRLINE_SOURCE               # if $CI_PIPRLINE_SOURCE == "merge_request_evnet"     # trigger for merge
```

#### 2. custome variables
+ our custome variables should not be write inside `gitlab-ci.yml`, good for tokens and passwords
+ go to:  settings ---> CI/CD ---> variables
+ this variables will export in linux bash of runner

```yaml
variables:                   # global variables 
    my_name: "ali"       # for values we want to use multiple times

job_name:             
    variables:           # local variables         
        my_id: 45         # values for each job 
    script:         
        - echo "$my_id" , "$my_name"     
```

#### 3. custome variables (File type)
+ go to:  settings ---> CI/CD ---> variables
+ if we have a variable inside a file we can change the `Type` field
+ write the name of temp file in `key` filed

```json
address: mysite.com         # wrtie this in `Value` section
env: app.mysite.com          # these values will store in a temp file
```


```yaml
push_image:
    stage: build
    script:
        - echo "$NAME_OF_TEMP_FILE"         # the value is the path of file
        - cat $NAME_OF_TEMP_FILE           # print the content
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
+ we can have multiple runners exp: in our developer system + a runner in aws account
+ we can see these two runners inside setting --> CICD --> specific runners
+ we can use each runner for specific job with `tags`. exp: one for tests and one for build


```bash
.\gitlab-runner install              # windows
.\gitlab-runner status        # 
.\gitlab-runner register      # Token, Tags: go to: settings ---> CICD ---> specific runners and copy the token
```

```yml
run_tests:      # in remote-runner we should specify tags
    tags:       # these tags means that we want to run this job in a runner that have these tags
        - aws      # the combination of tags should be unique for each runner
        - local       # we use tags to select a specific runner from the list of all runners
run_node:              # for this job we should specify diff unique tags
    image: node:17-alpine            # if I use a docker as excecutor in aws runenr or local runner, I can specify the image
    tags:                      # but if the excecutor is not docker like shell, I can not specify the image 
        - aws
        - docker
```



### 2. self-hosted server(instance)
+ gitlab server and gitlab runners are installed on separate servers
+ ensuring compatibility: if they have a diff versions, they will still work together







### Artifacts
+ Artifacts are job outputs. artifacts are files generated by jobs and saved after the job finishes
+ we can download artifacts from jobs, passing them between jobs
+ by default we can download `job.log` as an artifact
+ go to: settings ---> CICD ---> artifacts

```yaml
job_name:
  stage: build
  script:
    - make build
  artifacts:
    paths:                 # List of files or directories to keep as artifacts.
      - build/output/
    expire_in: 1 week       # Optional and default is forever
```

##### Artifacts: reports
+ GitLab provides a special way to handle report files so they can be viewed directly in the GitLab UI.

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
    expire_in: 1 week                      
    when: always                          # on_success, always, on_failure even
```

### Packages and registries
+ 3 types of registries: 1. package registery 2. container registry 3. Infrastructure registry
+ package registries: we can push and pull packages in npm, maven, PyPI, etc.
+ container registries: we can push and pull docker images ---> for every project we have its own registry
+ infrastructure registries: terraform

#### 1. package registry
#### 2. container registry
+ in container registry we can store multiple image repositories
+ address: registry.gitlab.com/username/my-app
+ Eeach image repository can store multiple image versions
+ `Root image` is the base image for all unnamed images

```yaml
build_image:                                 # CI_REGISTRY = registry.gitlab.com
    stage: build                      # CI_REGISTRY_PASSWORD = Token for authentication
    before_script:                    # CI_REGISTRY_USER = Authenticated user
        - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD   registry.gitlab.com      # bcz its a private registry we need login
        - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD   $CI_REGISTRY         # $CI_REGISTERY_IMAGE = full image name
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

```yaml
deploy_to_staging:                     # in gitlab ui go to operate --> environments
  stage: deploy                     # by this feature we know which verion depolyed to the environment
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


#### pass a file between jobs: .env format
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

### Cache
+ we can use cache to speed up the build
+ 1. jobs in second execution will use the cache 
+ 2. if two runners use the same cache, one of them download the cache from first runner
+ `Artifacts` get uploaded to gitlab server but `Cache` is for dependencies like `node modules` and store in `runners` directly.
+ its better to use less runners instead of caching in lots of servers. but if we have lots of runners, its better to use a `central caching server`.
+ we can create a job only for building and generating the cache with `policy: push` and other jobs should have `policy:pull`
+ caching is an optimisation and there is no garantee that it should always work, so jobs should never depond on a cache.
+ we can check `duration time` in gitlab ui, for example when we run the same job for second time
+ in gitlab ui we can delete caches by clicking `Clear runner cache`, but it physically remain in the runner
+ for clearing completely the cache, we should use docker commands or linux commands.


```yaml
job_name:
    stage: "mystage"                      # both jobs are in the same stage and execute in parallel
    script:                               # like artifacts we can use cache for each job
        - npm install                    # like artifacts we should specify a path
    cache:                               # CI_COMMIT_REF_NAME represents the branch name   
        key: $CI_COMMIT_REF_NAME                    # give a unique name for each job, if not set the default key is "default"
        paths:                           # location of the cache. which files should be cache and where you want to save?
            - ./node_modules             # default policy is pull-push
        policy: pull-push                 # pull-push means job download the cache from internet and push its changes at the end
another_job:
    stage: "mystage"                    # this job should be read-only since it is in the same stage and uses the same cache
    script:
        - npm install                    # check after this command inside the logs, for caching it will say "up to date"
    cache:
        key: $CI_COMMIT_REF_NAME                # this job will use the same cache
        policy: pull                       # pull means only downlad the cache and dont upload
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












