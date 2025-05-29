# PlamettoRL
Running DRL frameworks with containers on Palmetto Cluster using headless mode.

## Downloading a container
1. Containers are processed as .sif file.
2. Container being used for coppeliaSim simulator is : docker://asalvi179/husky_cpsim:all
3. In a scratch directory (on a non-login node), run :
   
```
apptainer pull docker://asalvi179/husky_cpsim:all
'''
