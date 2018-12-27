# Docker - Jenkins Integration

## Faster turn-around

start a new build job with the commit using the Jenkins CLI, e.g.:

    ssh -p 8022 admin@localhost build pvzd/d-PVZDweb -v -p nocache= -p start_clean=1 -p pushimage= -p keep_running=

## Proper cleanup

Make sure that container and volumes are removed at the beginning of a build/setup/test job, 
unless explicitly testing for existing ones. Note:

 * docker-compose down fails if the container is restarting
 * docker-compose down -v will not remove named volumes
 
## Using docker-compose.yaml settings with plain docker CLI

read yaml configuration into shell variables, like with config.py


## Scripts and error handling

In general: fail fast and early! Invoke shells as bash -e

Therefore: script return codes > 0 should be reserved for errors.
Jenkis shells should be able to run with set -e to properly report any error.
Consequently, functions need to return results via stdout, like:

    if_xyz() { ... echo "is_running" }
    
    if [[ $(if_xyz) == 'is_running' ]]; then ...
    
Caveat: some commands need to be followd by `|| true`.
E.g. grep might return a code > 0 just do indicate that something was not found. 

Hint: Jenkins does not point to a particualr error in a multi-line scriptlet.
Adding an error handler like this helps:

    ./dcshell/build ||  (rc=$?; echo "build failed with rc=rc?"; exit $rc)

    
## Scripts and current directory

For consistency, scripts should be always executed from either  

 1. project home, 
 2. script directory or 
 3. from anywhere.
 
For option 3 the assumptions are:
 a) the called does not need to care about chaing the directory before each call
 b) the script must address resources relative to its own location, or via injected env varaibles. 
 
    # load a file in the same directory with an absolute path
    scriptsdir=$(cd "$(dirname ${BASH_SOURCE[0]})" && pwd)
    source $scriptsdir/setenv.sh
    proj_home=$(dirname $scriptsdir) 
