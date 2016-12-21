## Steps for setting up jupyter-dev for Monitor

1. ####SSH in to Cori

  Before you are able to run jupyter-dev with an lsst kernel you'll need to do some setup on Cori.

2. ####Load the anaconda module to get ipython

  Once logged into Cori type: `module load python/2.7-anaconda`

3. ####Setup Kernel Spec

  Setup a `kernelspec` (instructions come from [this issue](https://github.com/jupyterhub/jupyterhub/issues/847#issuecomment-267044166)) by running:
  
  `ipython kernel install --user --name lsst`
  
  and you'll get a directory under:
  
  `~/.local/share/jupyter/kernels/lsst`
  
  with a file named `kernel.json`
  
4. ####Copy the script [lsst-kernel.sh](lsst-kernel.sh).

 Replace $HOME_DIR with where you have pserv and Monitor repos cloned.

5. ####Modify `~/.local/share/jupyter/kernels/lsst/kernel.json`.

  Change:
  
  ```
 "argv": [
  "/usr/bin/python",
  "-m",
  "ipykernel",
  "-f",
  "{connection_file}"
 ],
 ```
 
 to:
 
 ```
 "argv": [   
   "/path/to/lsst-kernel.sh",   
   "-f",   
   "{connection_file}" 
 ],
 ```
 
6. ####Test it out!

 You should be good to go! Point you web browser to https://jupyter-dev.nersc.gov and login. Once you see the jupyter notebook interface go into the examples folder of your cloned version of `monitor` and open `lightcurve-example.ipynb`. Here's the important part: **switch your notebook so it is running in the `lsst` kernel** (to change kernels use the `Change Kernel` option under `Kernel` in the jupyter notebook menu bar). Then try running the first four cells of the example notebook. If you're all set up correctly you should see the same output as what you see [here](../examples/lightcurve_example.ipynb).
 
 The rest of the notebook will not work yet because we are still working on establishing database connections from jupyter-dev, but once that is done we will amend this.