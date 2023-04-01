# CondaEnvironments

for my anaconda environments

Instructions adapted from 
https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html

# prepare build dependencies such as gcc
On ubuntu
suggested by : https://stackoverflow.com/a/60340115/16150356
it worked on my

```
sudo apt-get install build-essential 
```

# Environment commands
## Exporting my environment
simply run 
```
conda activate environment
conda env export > environment.yml
```

## Importing my (linux) environment
simply run 

```
conda env create -f environment.yml
```

## Importing my environment with a different name
simply run and replace `otherName` with the new desired name
```
conda env create --name otherName -f environment.yml
```

## removing my environment
simply run this command
[adapted from](https://stackoverflow.com/questions/49127834/removing-conda-environment)
```
conda remove --name $replaceThisValue --all
```

## updating my environment with a new environment.yml file
```
conda env update --name $replaceThisValue --file environment.yml --prune
```

## check environments
run this command to check if your environment was successfully installed
```
conda env list
```

## Importing my (cross-platform) environment

simply run the following commands

### Conda

```
conda env create -f cross-environment.yml
```

### pip

```
conda activate data
python -m pip install -r requirements.txt
```

# adding my environment to jupyter

## install the ipykernel

```
 conda install -c anaconda ipykernel
```

## add the environment to jupyter

* simply run and replace `otherName` with the new desired name
* replace `Python (ENVIRONMENTNAME)` with the name you wish to see in jupyter

```
python -m ipykernel install --user --name `otherName` --display-name="Python (ENVIRONMENTNAME)"
```
