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
