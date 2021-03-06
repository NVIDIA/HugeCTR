# CRITEO CTR SAMPLE #
In this sample we aim to demonstrate the basic usage of SparseEmbeddingHash and multiple slots.

## Setup the HugeCTR Docker Environment ##
The quickest way to run the sample here is with a docker container, which provides a self-contained, isolated, and reproducible environment for repetitive experiments. HugeCTR is available as buildable source code, but the easiest way to install and run HugeCTR is to use the pre-built Docker image available from the NVIDIA GPU Cloud (NGC). If you want to build the HugeCTR docker image on your own, please refer to [Use Docker Container](../docs/mainpage.md#use-docker-container).

You can choose either to pull the NGC docker or to build on your own.

#### Pull the NGC Docker ####
Pull the HugeCTR NGC docker using this command:
```bash
$ docker pull nvcr.io/nvidia/merlin/merlin-inference:0.4
```
Launch the container in interactive mode (mount the HugeCTR root directory into the container for your convenience) by running this command:
```bash
$ docker run --runtime=nvidia --rm -it -u $(id -u):$(id -g) -v $(pwd):/hugectr -w /hugectr nvcr.io/nvidia/merlin/merlin-inference:0.4
```

#### Build on Your Own ####
Please refer to [Build HugeCTR Docker Containers](../../tools/dockerfiles#build-container-for-model-training) to build on your own and set up the docker container. Please make sure that HugeCTR is built and installed to the system path `/usr/local/hugectr` within the docker container. Please launch the container in interactive mode in the same manner as above.

## Dataset and Preprocess ##
In running this sample, [Criteo 1TB Click Logs dataset](https://ailab.criteo.com/download-criteo-1tb-click-logs-dataset/) is used.
The dataset contains 24 files, each of which corresponds to one day of data.
To spend less time on preprocessing, we use only one of them.
Each sample consists of a label (1 if the ad was clicked, otherwise 0) and 39 features (13 integer features and 26 categorical features).
The dataset also has the significant amounts of missing values across the feature columns, which should be preprocessed accordingly.

#### Download the Dataset ####

Go to [this link](https://ailab.criteo.com/download-criteo-1tb-click-logs-dataset/),
and download one of 24 files into the directory "${project_root}/tools", 
or execute the following command:
```
$ cd ${project_root}/tools
$ wget http://azuremlsampleexperiments.blob.core.windows.net/criteo/day_1.gz
```
- **NOTE**: Replace 1 with a value from [0, 23] to use a different day.

In preprocessing, we will further reduce the amounts of data to speedup the preprocessing, fill missing values, remove the feature values whose occurrences are very rare, etc.

#### Preprocessing by Perl ####
```shell
$ bash preprocess.sh 1 criteo_data perl 10
```
- **NOTE**: The first argument represents the dataset postfix.  For instance, if `day_1` is used, it is 1.
- **NOTE**: the second argument `criteo_data` is where the preprocessed data is stored.
You may want to change it in case where multiple datasets for different purposes are generated concurrently.
If you change it, `source` and `eval_source` in your JSON config file must be changed as well.
- **NOTE**: the last arguemnt represents the number of slots used in this sample.

## Training with HugeCTR ##
```shell
$ huge_ctr --train ../samples/criteo_multi_slots/criteo_multi_slots.json
```
