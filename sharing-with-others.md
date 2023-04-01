# CondaEnvironments
Adapted from [my original instructions](https://github.com/progressEdd/CondaEnvironments/blob/main/README.md). This version has $ breaks to prevent people from blindly copy and pasting

# Environment commands
## Exporting my environment
simply run and replace `$environment` with the name the assigned name
```
conda activate $environment
conda env export > environment.yml
```

## Importing my (linux) environment
simply run
```
conda env create -f environment.yml
```

## Importing my environment with a different name
simply run and replace `$renameThis` with the new desired name
```
conda env create --name $renameThis -f environment.yml
```

## removing my environment
simply run this command
[adapted from](https://stackoverflow.com/questions/49127834/removing-conda-environment)
```
conda remove --name $renameThis --all
```

## updating my environment with a new environment.yml file
```
conda env update --name $renameThis --file environment.yml --prune
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
simply run and replace `$environment` with the name the assigned name
```
conda activate $environment
python -m pip install -r requirements.txt
```

# adding my environment to jupyter

## install the ipykernel

```
 conda install -c anaconda ipykernel
```

## add the environment to jupyter

* simply run and replace `$renameThis` with the new desired name
* replace `Python (ENVIRONMENTNAME)` with the name you wish to see in jupyter

```
python -m ipykernel install --user --name `$renameThis` --display-name="Python (ENVIRONMENTNAME)"
```
