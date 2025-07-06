
# Pip

```bash
pip install package                                # Install a package              
pip uninstall package                              # Uninstall a package            
pip install git+https://github.com/user/repo.git    # install from github
pip install package==1.2.3                         # Install specific version       
pip install 'package>=1.0,<2.0'                    # Install with version range     
pip install -r requirements.txt                    # Install from requirements file 
pip freeze > requirements.txt                      # Save installed packages        
pip list                                           # List installed packages        
pip show package                                   # Show details of a package      
pip check                                          # Check for broken dependencies  
pip cache purge                                    # Clear pip cache                
pip install --upgrade pip                           # upgrade pip
pip install --upgrade package                       # upgrade a package
pip install package==old_version                   # downgrade a package
```

# python venv
```bash
# alternative: poetry, pipenv, etc.
# env is installed by python by default
# your proj folder should be outside of env folder
python -m venv myname     # create a virtual env (a folder which contains inner folders and files and a config file)
# before install any package you should activate virtual env
source myname/bin/activate     # activate virtual env in linux
myname/bin/activate            # activate virtual env in windows
pip freeze                  # see list of installed packages in virtual env
python -m pip freeze      # if pip not works
pip freeze > requirement.txt          # create requirement.txt for sharing this proj with someone else
```



### poetry

```bash

```
















