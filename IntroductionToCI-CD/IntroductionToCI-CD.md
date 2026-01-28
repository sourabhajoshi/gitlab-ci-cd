## **GitLab CI/CD**   

GitLab CI/CD is an automation system built into GitLab that automatically builds, tests, and deploys the code every time we push changes to a repository.

- CI (Continuous Integration) : Automatically test and build code when developers commit changes

- CD (Continuous Delivery / Deployment) : Automatically deploy tested code to servers or cloud environments

It is configured using a single file called .gitlab-ci.yml stored in your repository.

### **Without GitLab CI/CD**    

Priya is a Software / Full-Stack developer working on a food-delivery web application.

What Priya used to do
- Write code for a new feature (ex: online table booking)
- Push code
- Manually test (sometimes skipped)
- Manually deploy

What went wrong
- Bugs reached production
- App broke during peak hours
- Team blamed Priya

### **With GitLab CI/CD**

Priya is a Software / Full-Stack developer working on a food-delivery web application.

She writes code every day and pushes it to GitLab.

What Priya used to do
- Write code for a new feature (ex: online table booking)
- Push code
- GitLab automatically handles everything
- Same process every time
- Safe, fast releases

When Priya pushes code, GitLab CI/CD automatically compiles, tests, builds, and deploys the application without manual effort.

```
 WITHOUT CI/CD                WITH CI/CD
────────────────            ───────────────
Priya → Compile               Priya → Push
      → Test                         ↓
      → Build                  GitLab CI/CD
      → Deploy                   → Test
                                 → Build
                                 → Deploy
```

### **What is a Pipeline in GitLab**

A GitLab pipeline is an automated assembly line that takes your code and turns it into a running application step by step.

A pipeline in GitLab is an automated process that runs build, test, and deploy steps on every code change.

**Car Factory Analogy**

Think of building a car in a factory. A car is not built in one step. It passes through multiple stations, in a fixed order.

That assembly line is GitLab Pipeline.
```
Chassis → Engine → Paint → Quality Check → Delivery
```
In a car factory, the car is first built on a chassis, then the engine is installed, followed by painting, quality checks, and finally delivery to the customer.

```
Code → Build → Test → Verify → Deploy
```
A GitLab pipeline is an automated sequence where code is built, tested, validated, and deployed, similar to a car moving through an assembly line before delivery.

**What is .gitlab-ci.yml**

In GitLab,
.gitlab-ci.yml is the file where you define your pipeline:
- what should run
- in what order
- and under which conditions
- uses linux commands

Without this file, no pipeline exists.

**Why do we use Linux commands in .gitlab-ci.yml?**

Because GitLab uses Docker containers to run CI/CD jobs, and Docker containers are Linux-based, we use Linux commands in .gitlab-ci.yml.

GitLab CI ಪ್ರತಿಯೊಂದು job ಅನ್ನು Linux-based Docker container ಒಳಗೆ run ಮಾಡುವುದರಿಂದ, ಆ container ಗಳು Linux shell commands ಅನ್ನು ಅರ್ಥಮಾಡಿಕೊಳ್ಳುತ್ತವೆ. ಆದ್ದರಿಂದ .gitlab-ci.yml ನಲ್ಲಿ Linux commands ಬಳಸಲಾಗುತ್ತದೆ.

NOTE:
We use Windows commands also but only if:

- You use a Windows GitLab Runner
- configure PowerShell
```
script:
  - dir
```
This works only on Windows runners, not Linux.

**My first car_build app**
make sure project is created and creat a .gitlab-ci.yml file. 

This job runs inside a Linux Docker container, prints a message, creates a build folder, creates a file, and writes data into it step by step.

```
<!-- .gitlab-ci.yml -->

build_car:
    image: alpine
    script:
        - echo "Building the car"
        - mkdir build
        - touch car.txt
        - echo "chassis" > car.txt
```


**build_car:** is job name. It identifies this job in the pipeline UI.You can name it anything (build, compile_app, etc.)

**image: alpine** This tells GitLab which Docker image to use. GitLab creates a Docker container. That container is created from the alpine image. (alpha is very small Linux distribution)

**script:** This section contains commands to run. GitLab opens a Linux terminal inside the container. Executes commands top to bottom. If any command fails than job fails.

in rightside of the job step we can see 
```
Duration: 11 seconds
Finished: 9 minutes ago
Queued: 1 second
Timeout: 1h (from project)
Runner: #3737 (JOj8tQj3) AWS Linux 2025 - blue
Source: Push
```

A GitLab **Runner** is the machine (server) that actually executes your CI/CD job.
- GitLab itself does NOT run your code
- The runner runs your code

**Duration: 11 seconds** : The runner took 11 seconds to execute all commands in the job.

This includes:
- Starting the container
- Running all script commands
- Finishing the job

**Finished: 9 minutes ago** : The job completed 9 minutes before you opened this page.

**Queued: 1 second** : Your job waited 1 second before a runner picked it up.

Why jobs get queued:
- No free runner available
- Many jobs running at the same time

1 second queue = runner was almost immediately available 

**Timeout: 1h** : If this job runs longer than 1 hour, GitLab will automatically stop it.

Why timeout exists:
- Prevent stuck or infinite jobs
- Save server resources

Your job finished in 11 seconds, so timeout didn’t matter here.

Note:

1. File name must be .gitlab-ci.yml
- File name must be .gitlab-ci.yml (GitLab only looks for this exact file name, even a small spelling change = pipeline ignored)
- Correct file name : .gitlab-ci.yml
- Incorrect file names : gitlab-ci.yml, .gitlab-ci.yaml, .gitlab-ci.YML


2. File must be in the root of the project 

```
<!-- Correct location -->
project-root/
├── .gitlab-ci.yml
├── src/
├── package.json
```
GitLab scans only the root directory and it does not search inside folders.
```
<!-- Incorrect location -->

project-root/
├── ci/
│   └── .gitlab-ci.yml
```

3. Auto DevOps must be disabled 

A GitLab feature that auto-generates pipelines. Can override or conflict with your custom .gitlab-ci.yml

```
✔ File name & location
✔ YAML syntax valid
✔ Jobs exist
✔ No skip rules
✔ Correct branch
✔ Runner available
✔ Tags match
✔ CI/CD enabled
```

### **What is a Job in GitLab CI/CD?**

A job in GitLab CI/CD is a set of commands that runs in a pipeline to perform a specific task.

Imagine making tea
```
Boil water → Add tea → Add sugar → Serve
```
This whole activity is one job.

Important rules about jobs
- Each job runs in a fresh environment
- If a job fails, the pipeline stops
- Jobs in the same stage run in parallel (if runners are available)

What jobs do NOT do
- Jobs do not decide order (stages do)
- Jobs do not share files by default

Now add one more job (car_test) into .gitlab-ci.yml File
```
build_car:
    image: alpine
    script:
        - echo "Building the car"
        - mkdir build
        - cd build
        - touch car.txt
        - echo "chassis" > car.txt

test_car:
    image: alpine
    script:
        # check car.txt exist
        - test -f build/car.txt 
        # check it has right content
        - grep 'chessis' in car.txt 
```

Commit the changes, pipeline start and through error
```
Getting source from Git repository
00:03
Gitaly correlation ID: 01KF08CRTH3QD035KKBYH0JVV0
Fetching changes with git depth set to 20...
Initialized empty Git repository in /builds/in0jrbh/joshi-first-pipeline/.git/
Created fresh repository.
Checking out 53e4ec35 as detached HEAD (ref is main)...
Skipping Git submodules setup
Executing "step_script" stage of the job script
00:00
$ # Festo pre build script # collapsed multi-line command
$ test -f build/car.txt
Cleaning up project directory and file based variables
00:01
ERROR: Job failed: command terminated with exit code 1
```

To find the error, look at the last command printed before the red error message, because that is the command that failed and caused the job to stop.

**Debugging rule in gitlab CI/CD**

```
Pipeline fails
↓
Find last executed command
↓
Understand what that command expects
↓
Check why expectation was not met
```

If you don’t specify a stage, GitLab automatically assigns the job to the default stage called test.

GitLab has built-in default stages:
```
build → test → deploy
```
If you do not define stages:, GitLab still uses these defaults internally.

What happened in your case
```
build_car:
  image: alpine
  script:
    - echo "Building the car"
```

GitLab internally converts it to:
```
build_car:
  stage: test   # DEFAULT
  image: alpine
  script:
    - echo "Building the car"
```
So your job runs in the test stage.

**Execution flow**
```
Push code
   ↓
GitLab finds .gitlab-ci.yml
   ↓
Job found (build_car)
   ↓
No stage mentioned
   ↓
Assign default stage = test
   ↓
Runner executes job
```

### **What is Stages in GitLab CI/CD?**

Stages in GitLab CI/CD define the order in which jobs run in a pipeline.

Stages in GitLab CI/CD define the execution order of jobs in a pipeline.

Imagine washing clothes:
```
Wash → Rinse → Dry
```
You cannot dry before washing. That order is what stages do.

In GitLab:
```
build → test → deploy
```
- build runs first
- test runs only after build succeeds
- deploy runs only after test succeeds

**Why stages are needed**

Without stages: Jobs may run randomly and No clear order

With stages: Clear flow, Safer deployments and Fewer mistakes

In the below example
```
stages:
    - build
    - test

build_car:
    image: alpine
    stage: build
    script:
        - echo "Building the car"
        - mkdir build
        - cd build
        - touch car.txt
        - echo "chassis" > car.txt

test_car:
    image: alpine
    stage: test
    script:
        # check car.txt exist
        - test -f build/car.txt 
        # check it has right content
        - grep 'chessis' in car.txt 
```
build_car will execute first. If it succeeds test_car runs else pipeline stops.

All jobs in one stage must finish successfully before the next stage starts.

NOTE:

- Stages do NOT share files amd do NOT run commands.
- job do the work and stage control the order.

**Job vs Stage vs Pipeline**
```
┌──────────────────────────────────────────────┐
│                PIPELINE                      │
│  (Complete CI/CD process from start to end)  │
│                                              │
│   ┌───────────────┐   ┌───────────────┐      │
│   │    STAGE:     │   │    STAGE:     │      │
│   │    BUILD      │ → │    TEST       │ → …  │
│   │               │   │               │      │
│   │ ┌───────────┐ │   │ ┌───────────┐ │      │
│   │ │  JOB:     │ │   │ │  JOB:     │ │      │
│   │ │ build_app │ │   │ │ test_app  │ │      │
│   │ └───────────┘ │   │ └───────────┘ │      │
│   │ ┌───────────┐ │   │ ┌───────────┐ │      │
│   │ │  JOB:     │ │   │ │  JOB:     │ │      │
│   │ │ build_ui  │ │   │ │ test_ui   │ │      │
│   │ └───────────┘ │   │ └───────────┘ │      │
│   └───────────────┘   └───────────────┘      │
│                                              │
└──────────────────────────────────────────────┘
```

### **What is a Job Artifact in GitLab CI/CD?**

A job artifact is a file or folder created by one job and saved by GitLab so it can be downloaded or used by later jobs.

Why job artifacts are needed : Each job runs in a new machine, and all files are deleted when the job ends.

This is why our test_car job is failing—after the build_car job finishes, its files are removed, so when test_car starts, the required files do not exist and the job throws an error.

```
1. Build job runs  
2. File is created  
3. GitLab saves the file as an artifact  
4. Job ends (machine is destroyed)  
5. Next job starts  
6. GitLab downloads the artifact  
7. File is available in the next job  
```

```
build_car:
  stage: build
  script:
    - mkdir build
    - echo "chassis" > build/car.txt
  artifacts:
    paths:
      - build/car.txt
```

The final working script file is
```
stages:
    - build
    - test

build_car:
    image: alpine
    stage: build
    script:
        - echo "Building the car"
        - mkdir build
        - cd build
        - touch car.txt
        - echo "chessis" > car.txt
    artifacts:
        paths:
            - build/

test_car:
    image: alpine
    stage: test
    script:
        - test -f build/car.txt 
        - grep 'chessis' build/car.txt
```

### **GitLab CI/CD Architecture**

GitLab CI/CD architecture describes how code moves from a developer’s laptop to automated build, test, and deployment using GitLab Server and GitLab Runner.

GitLab CI/CD has two core components: GitLab Server and GitLab Runner

**GitLab Server** manages the CI/CD process by creating pipelines, assigning jobs to runners, and storing logs and results.
- Hosts the source code repository
- Reads .gitlab-ci.yml file
- Creates pipelines, stages, and jobs
- Assigns jobs to available runners
- Stores logs, artifacts, and job results

For example, when a developer pushes code, the GitLab Server reads the .gitlab-ci.yml file and creates a pipeline with build and test jobs.

**GitLab Runner** executes the jobs created by the GitLab Server by running commands in a Linux or Docker environment.
- A machine or agent that executes CI/CD jobs
- Polls GitLab Server for jobs
- Runs job commands in isolated environments (usually Docker/Linux)
- Sends execution logs and status back to GitLab Server

For example, when the Server assigns a build job, the Runner runs commands like mkdir, echo, or npm test and sends the result back.

```
Developer push
→ GitLab Server creates pipeline
→ GitLab Server assigns job
→ GitLab Runner executes job
→ Results sent back to GitLab Server
```

### **ow to write comments in a .yml (YAML) file**

In YAML, comments are written using the # symbol.
```
# This is a comment
build_job:
  script:
    - echo "Hello"
```

YAML does NOT support block comments like /* */
```
# This job builds the car
# It creates required files
# Output is saved as artifacts
build_car:
  script:
    - echo "Building the car"
```
