# CondaEnvironments
for my anaconda environments

Instructions adapted from 
https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html

# Exporting my environment
simply run 
```
conda activate environment
conda env export > environment.yml
```

# prepare build dependencies such as gcc
On ubuntu
suggested by : https://stackoverflow.com/a/60340115/16150356
it worked on my
``` 
sudo apt-get install build-essential 
```

# Importing my environment
simply run 
```
conda env create -f environment.yml
```

# adding my environment to jupyter
## install the ipykernel
```
 conda install -c anaconda ipykernel
 ```
 ## add the environment to jupyter
 ```
 python -m ipykernel install --user --name data-test --display-name="Python (ENVIRONMENTNAME)"
```
