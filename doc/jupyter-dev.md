# Monitor @ NERSC: Introducing `jupyter-dev`

## What is `jupyter-dev`?
  `Jupyter-Dev` is a system set up at NERSC to run jupyter notebooks on the cori filesystem by simply pointing your web browser to https://jupyter-dev.nersc.gov and using your NERSC credentials. It does take a bit of setup before the first time, but while the jupyter-hub system runs on a server external to Cori the `jupyter-dev` system has access to the Cori filesystem. Thus, it can be configured to run custom kernels set up in your home directory. This allows a user to create LSST stack enabled kernels where LSST software as well as any additional packages required are accessible in a `jupyter` notebook.
  
## Why bother with `jupyter-dev`?
We want to use `jupyter-dev` for our project in order to have a convenient portal to the [Twinkles](https://github.com/LSSTDESC/Twinkles/blob/master/README.md) outputs stored in science databases at NERSC. It also gives us a common platform in which to run analysis. The directions below point all users to run a shared version of the LSST stack. Therefore, any notebooks we create in `jupyter-dev` can be easily shared and will work for others using the shared stack in their own `jupyter-dev` browser window.

## Steps for setting up `jupyter-dev` for the Monitor

1. #### SSH in to Cori

  Before you are able to run `jupyter-dev` with an lsst kernel you'll need to do some setup on Cori.

2. #### Load the anaconda module to get ipython

  Once logged into Cori type: `module load python/2.7-anaconda`

3. #### Setup Kernel Spec

  Setup a `kernelspec` (instructions come from [this issue](https://github.com/jupyterhub/jupyterhub/issues/847#issuecomment-267044166)) by running:
  
  `ipython kernel install --user --name lsst`
  
  and it will create a directory:
  
  `~/.local/share/jupyter/kernels/lsst`
  
  with a file inside the directory named `kernel.json`.
  
4. #### Copy the script [lsst-kernel.sh](lsst-kernel.sh).

 You can copy it to wherever you'd like since in the next step you will be manually entering the path for the ipython kernel to follow. After you have a copy where you want it then replace `$HOME_DIR` in the script with where you have pserv and Monitor repos cloned. Finally, make sure to set the permissions on this file to user and group readable and exectuable:
 
 `chmod ug+rx lsst-kernel.sh`

5. #### Modify `~/.local/share/jupyter/kernels/lsst/kernel.json`.

  Change:
  
  ```
 "argv": [
  "/usr/bin/python",
  "-m",
  "ipykernel",
  "-f",
  "{connection_file}"
 ]
 ```
 
 to:
 
 ```
 "argv": [
  "/path/to/lsst-kernel.sh",   
  "-f",   
  "{connection_file}" 
 ]
 ```
 
6. #### Set up database connection

  Since we need to connect to the NERSC Science Databases to access [Twinkles](https://github.com/LSSTDESC/Twinkles/blob/master/README.md) data we need to set up the interface that will allow us to connect.
  * First create a `.lsst` folder in your home directory.
  * Contact the Monitor team for the username and password required to access the database.
  * Then create a file `.lsst/db-auth.paf` with the following:
  ```
  database: {
    authInfo: {
      host: scidb1.nersc.gov
      port: 3306
      user: $db_username
      password: $db_password
      }
    }
  ```
  NOTE: if you already have a `db-auth.paf` file you should add a new `authInfo:{}` inside the `database:{}`, rather than adding a new `database:{}`.
   * Go back to your home directory and change permissions:
   ```
   chmod 700 .lsst
   chmod 600 .lsst/db-auth.paf
   ```
 
7. #### Test it out!

 You should be good to go! Point your web browser to https://jupyter-dev.nersc.gov and login. Once you see the `jupyter` notebook interface go into the examples folder of your cloned version of `monitor` and open `lightcurve-example.ipynb`. Here's the important part: **switch your notebook so it is running in the `lsst` kernel** (to change kernels use the `Change Kernel` option under `Kernel` in the `jupyter` notebook menu bar). Then try running the first four cells of the example notebook. If you're all set up correctly you should see the same output as what you see [here](../examples/lightcurve_example.ipynb).
 
## Installing packages from within a notebook
  If you are in a `jupyter-dev` notebook and realize you want to add an external pip-installable package it's possible to do without leaving the notebook. Simply use:
  ```
  pip install <package_name> --user
  ```
  and pip will install a personal copy of the package. Then restart the kernel in your notebook and you should be able to import the package. Futhermore, the package will henceforth be available in any other notebooks you run with the `lsst` kernel.
  
## Troubleshooting Common Errors

  - You see "503 : Service Unavailable" when attempting to start a Jupyter-Dev server for the first time
    - This may be because you do not have `bash` as your default shell on Cori. Unfortunately it is currently necessary to use `bash` as your default shell to get Jupyter-Dev to work. NERSC developers are aware of the issue and it is on their to-do list.
    
  - You are trying to get the lsst kernel to work with a different version of the LSST stack but it crashes before starting
    - This may be because the version of the stack you are using does not include `ipykernel`. To fix this install a user version of the package with: `pip install ipykernel --user`.
    
## Further help

  If you are experiencing issues not documented here contact the NERSC help desk at: consult@nersc.gov.
