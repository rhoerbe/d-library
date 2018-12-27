# Docker - Applicaiton Interfaces

## Runtime Environment Injection

In general the external dependencies environment should rather be injected into the application.
However, what is external or not is not obvious.
Therefore there should be an explicit contract that is being verified in unit tests, because it may be non-trivial.
Candidates for env-injection are:

* Application root directory (application contains only relative references)
* Python virtual env
* JAVA_HOME (but CLASS_PATH should be done in the application)
* Web server (e.g. django: replacing runserver with gunicorn etc.)
* Database

The application should set all values that it can know without making assumptions about the target environment,
like paths that are relative the the application root directory. 
OTOH, the application should not make assumtions deployment-specific values.

Constants that are fixed for all deployments should be declared explicitly.

## Shell environment

Centralized initialization in Unix shells happens in login shells, in general by executing /etc/profile
(.bashrc is executed many times - this may be OK as well, unless there are constructs like PATH=$PATH:xxx)

Login shells can be invoked with su - or bash -l. E.g.

    docker-compose run --rm $service bash -l -c /opt/PVZDweb/pvzdweb/wait_pg_become_ready.sh
    
Following construct has not been working (further research required):

    Dockerfile: SHELL ["/bin/bash", "-l", "-c"]
    docker-compose run --rm $service /opt/PVZDweb/pvzdweb/wait_pg_become_ready.sh
    
## Application startup



## Logging

Containers with a single process and standard logging should write to stdout/stderr.

## Lessons learned

## (Django) Applicaiton Configuration

Keep configuration in one place, llike django.settings

export config into shell (see config.py)
