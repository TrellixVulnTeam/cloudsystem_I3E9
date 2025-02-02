<!--- 0. Title -->
# Transformer Language FP32 training

<!-- 10. Description -->
## Description

This document has instructions for running Transformer Language FP32 training in mlperf
Benchmark suits using Intel-optimized TensorFlow.

Detailed information on mlperf Benchmark can be found in [mlperf/training](https://github.com/mlperf/training/tree/master/translation/tensorflow/transformer)

<!--- 20. Download link -->
## Download link

[transformer_mlperf-fp32-training.tar.gz](https://storage.googleapis.com/intel-optimized-tensorflow/models/v2_1_0/transformer_mlperf-fp32-training.tar.gz)

<!--- 30. Datasets -->
## Datasets

Decide the problem you want to run to get the appropriate dataset.
We will get the training data of it as an example:

Download dataset for computing BLEU score
```
export DATASET_DIR=/home/<user>/transformer_data
mkdir $DATASET_DIR && cd $DATASET_DIR
wget https://nlp.stanford.edu/projects/nmt/data/wmt14.en-de/newstest2014.en
wget https://nlp.stanford.edu/projects/nmt/data/wmt14.en-de/newstest2014.de
```

For the training dataset, download and untar the model package.    
```
wget https://storage.googleapis.com/intel-optimized-tensorflow/models/v2_1_0/transformer_mlperf-fp32-training.tar.gz
tar -xzf transformer_mlperf-fp32-training.tar.gz

export PYTHONPATH=$PYTHONPATH:/home/<user>/transformer_mlperf-fp32-training/models/common/tensorflow
export DATASET_DIR=/home/<user>/transformer_data
    
cd /home/<user>/transformer_mlperf-fp32-training/models/language_translation/tensorflow/transformer_mlperf/training/fp32/transformer
python data_download.py --data_dir=$DATASET_DIR
```

Running `python data_download.py --data_dir=$DATASET_DIR` assumes you have a python environment similar to what the `intel/intel-optimized-tensorflow:2.3.0-ubuntu-18.04` container provides. One option would be to run the above within the `intel/intel-optimized-tensorflow:2.3.0-ubuntu-18.04` container eg: `docker run -u $(id -u):$(id -g) --privileged  --entrypoint /bin/bash -v /home/<user>:/home/<user> -it intel/intel-optimized-tensorflow:2.3.0-ubuntu-18.04`


<!--- 40. Quick Start Scripts -->
## Quick Start Scripts

Transformer Language in mlperf benchmark can run with full training or
fewer training steps. During training we can control if it will do the evaluation
or not.

| Script name | Description |
|-------------|-------------|
| [`fp32_training_demo.sh`](fp32_training_demo.sh) | Runs 100 training steps (run on a single socket of the CPU). |
| [`fp32_training.sh`](fp32_training.sh) | Runs 200 training steps, saves checkpoints and do evaluation (run on a single socket of the CPU). |
| [`fp32_training_mpirun.sh`](fp32_training_mpirun.sh) | Runs training in multi-instance mode "2 sockets in a single node for example" using mpirun for the specified number of processes. |

These quickstart scripts can be run in different environments:
* [Bare Metal](#bare-metal)
* [Docker](#docker)

<!--- 50. Bare Metal -->
## Bare Metal

To run on bare metal, the following prerequisites must be installed in your environment:
* Python 3
* [intel-tensorflow==2.3.0](https://pypi.org/project/intel-tensorflow/)
* numactl

After installing the prerequisites, download and untar the model package.
Set environment variables for the path to your `DATASET_DIR` and an
`OUTPUT_DIR` where log files will be written, then run a 
[quickstart script](#quick-start-scripts).

```
DATASET_DIR=<path to the dataset>
OUTPUT_DIR=<directory where log files will be written>

wget https://storage.googleapis.com/intel-optimized-tensorflow/models/v2_1_0/transformer_mlperf-fp32-training.tar.gz
tar -xzf transformer_mlperf-fp32-training.tar.gz
cd transformer_mlperf-fp32-training

quickstart/fp32_training.sh (or quickstart/fp32_training_demo.sh)
```

For training in multi-instance mode (2 sockets in a single node for example) in evaluation mode,
where we are "saving checkpoints" and "doing the evaluation", the following prerequisites must be installed in your environment:
* gcc-8
* g++-8
* libopenmpi-dev
* openmpi
* openssh
* horovod

Set environment variables for the path to your `DATASET_DIR` and an
`OUTPUT_DIR` where log files will be written, then run a
[quickstart script](#quick-start-scripts).

```
DATASET_DIR=<path to the dataset>
OUTPUT_DIR=<directory where log files will be written>

cd transformer_mlperf-fp32-training
quickstart/fp32_training_mpirun.sh
```

<!--- 60. Docker -->
## Docker

The model container includes the scripts and libraries needed to run 
Transformer Language FP32 training. To run one of the quickstart scripts 
using this container, you'll need to provide volume mounts for the dataset 
and an output directory.

```
DATASET_DIR=<path to the dataset>
OUTPUT_DIR=<directory where log files will be written>

docker run \
  --env DATASET_DIR=${DATASET_DIR} \
  --env OUTPUT_DIR=${OUTPUT_DIR} \
  --env http_proxy=${http_proxy} \
  --env https_proxy=${https_proxy} \
  --volume ${DATASET_DIR}:${DATASET_DIR} \
  --volume ${OUTPUT_DIR}:${OUTPUT_DIR} \
  --privileged --init -t \
  intel/language-translation:tf-2.3.0-imz-2.1.0-transformer-mlperf-fp32-training \
  /bin/bash quickstart/<script name>
```

<!--- 80. License -->
## License

[LICENSE](/LICENSE)

