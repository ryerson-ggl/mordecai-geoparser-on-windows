# Mordecai Geoparser on Windows

Richard Wen <rrwen.dev@gmail.com>  

* [Releases](https://github.com/ryerson-ggl/mordecai-geoparser-on-windows/releases)
 
Instructions for installing the mordecai python full text geoparsing library on windows 10.

## Install

1. Install [git](https://git-scm.com/)
2. Install [Anaconda Python](https://www.anaconda.com/)
3. Install [Docker for Windows](https://docs.docker.com/docker-for-windows/release-notes/)

In a console, enter the following commands to check that they are working:

```
git --help
conda --help
docker --help
```

## Setup

### Step 1. Create the mordecai-2.0.1.post1 Python environment

In a command console, clone this repository and move into the cloned folder:

```
git clone https://github.com/ryerson-ggl/mordecai-geoparser-on-windows
cd mordecai-geoparser-on-windows
```

Add the `conda-forge` channel, and create the Python environment with `conda`:

```
conda config --add channels conda-forge
conda env create -f mordecai_2_0_1_post1_env.yml
```

### Step 2. Download the spaCy NLP model

Activate the `mordecai-2.0.1.post1` Python environment:

```
activate mordecai-2.0.1.post1
```

Inside your `mordecai-2.0.1.post1` environment, download the [spaCy](https://spacy.io/) Natural Language Processing (NLP) model:

```
python -m spacy download en_core_web_lg
```

### Step 3. Download the geonames index and uncompress it into a folder

* Download the index at [https://s3.amazonaws.com/ahalterman-geo/geonames_index.tar.gz](https://s3.amazonaws.com/ahalterman-geo/geonames_index.tar.gz)
* The file should be around 1.58 GB, which may take a while
* When complete, extract the downloaded `geonames_index.tar.gz` file into a folder named `geonames_index`

*NOTE: Keep in mind the full path of your `geonames_index` folder*

### Step 4. Run the geonames index docker

In a command console, run:

```
docker run --name geonames_index -d -p 127.0.0.1:9200:9200 -v <PATH_TO>/geonames_index/:/usr/share/elasticsearch/data elasticsearch:5.5.2
```

*NOTE: Replace `<PATH_TO>` with the full path to your `geonames_index` folder directory (for example, d:/users/me/downloads)*

### Step 5. Check that the geonames index docker is running

In the console, check the `geonames_index` docker container:

```
docker ps -f name=geonames_index
```

The command should have output similar to the following:

```
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                NAMES
2cf6772da252        elasticsearch:5.5.2   "/docker-entrypoint.…"   3 minutes ago       Up 3 minutes        127.0.0.1:9200->9200/tcp, 9300/tcp   geonames_index
```

### Step 6. Check that the geonames index is accessible

Open a web browser and go to:  
  
[http://localhost:9200/_cat/indices?v](http://localhost:9200/_cat/indices?v)

Your browser should output text similar to the following:

```
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   geonames eWVK3y2ETaufWKEFZmeK2Q   1   1   11741135            0      2.8gb          2.8gb
```

### Step 7. Check that mordecai is running properly

In a command console, activate the `mordecai-2.0.1.post1` Python environment:

```
activate mordecai-2.0.1.post1
```

Enter the Python console:

```
python
```

In the Python console, enter the following code:

```python
# Load libraries
from mordecai import Geoparser # may take a little to load
from pprint import pprint

# Create geoparser and parse example text
geo = Geoparser()
result = geo.geoparse("I traveled from Oxford to Ottawa.")

# Print the geoparsed text results
pprint(result)
```

After printing the result with `pprint(result)`, you should get:

```
[{'country_conf': 0.96474487,
  'country_predicted': 'GBR',
  'geo': {'admin1': 'England',
          'country_code3': 'GBR',
          'feature_class': 'P',
          'feature_code': 'PPLA2',
          'geonameid': '2640729',
          'lat': '51.75222',
          'lon': '-1.25596',
          'place_name': 'Oxford'},
  'spans': [{'end': 6, 'start': 0}],
  'word': 'Oxford'},
 {'country_conf': 0.83302397,
  'country_predicted': 'CAN',
  'geo': {'admin1': 'Ontario',
          'country_code3': 'CAN',
          'feature_class': 'P',
          'feature_code': 'PPLC',
          'geonameid': '6094817',
          'lat': '45.41117',
          'lon': '-75.69812',
          'place_name': 'Ottawa'},
  'spans': [{'end': 6, 'start': 0}],
  'word': 'Ottawa'}]
```

If your output looks like the above, the mordecai geoparser library should be installed correctly.

## Tips

### Useful conda commands

If you are inside the `mordecai-2.0.1.post1` Python environment, you can deactivate it by simply entering:

```
deactivate
```

### Useful docker commands

If you are done with `geonames_index` docker container, you may wish to stop it with:

```
docker stop geonames_index
```

To start the `geonames_index`, you can use:

```
docker start geonames_index
```

To remove the `geonames_index` permanently, use:

```
docker container rm geonames_index
```

## Additional Information

These instructions were tested on a machine with the following specifications:

* **Operating System**: Windows 10 Professional 64-bit
* **Processor**: i7-6700K Quad-Core @ 4.00GHz
* **Memory**: 16 GB
* **Storage**: 256 GB + 512 GB SSD

The following software was installed on the above machine:

* git version 2.19.0.windows.1 [Download](https://github.com/git-for-windows/git/releases/download/v2.19.0.windows.1/Git-2.19.0-64-bit.exe)
* Anaconda 5.2.0 Python 3.7 Version (64 bit) [Download](https://repo.continuum.io/archive/Anaconda3-5.2.0-Windows-x86_64.exe)
* Docker Community Edition 2.0.0.2 2019-01-16 [Download](https://download.docker.com/win/stable/30215/Docker%20for%20Windows%20Installer.exe)

## Citation

As noted by the authors in the [mordecai repository](https://github.com/openeventdata/mordecai), please use the following citation information if you are using the mordecai software:

```
@article{halterman2017mordecai,
  title={Mordecai: Full Text Geoparsing and Event Geocoding},
  author={Halterman, Andrew},
  journal={The Journal of Open Source Software},
  volume={2},
  number={9},
  year={2017},
  doi={10.21105/joss.00091}
}
```

## References

* [mordecai Github Repository](https://github.com/openeventdata/mordecai)
* [mordecai geonames index Github Repository](https://github.com/openeventdata/es-geonames)
* [conda environments](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)
* [docker command reference](https://docs.docker.com/engine/reference/commandline/docker/)
* [spaCy Natural Language Processing](https://spacy.io/)
* [git for windows releases](https://github.com/git-for-windows/git/releases)
* [Anaconda installer archive](https://repo.continuum.io/archive/)
* [Docker Windows releases](https://docs.docker.com/docker-for-windows/release-notes/)
