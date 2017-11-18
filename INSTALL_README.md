# Tensorflow r1.4 installation steps

### checkout `r1.4`

    $ git checkout r.14

### (optional) Update Protocol Buffers

Check it: https://github.com/google/protobuf/blob/master/src/README.md

### Update bazel

make sure bazel version > 0.5.4

	$ sudo apt-get upgrade bazel
	$ bazel version
	Build label: 0.7.0
	Build target: bazel-out/local-fastbuild/bin/src/main/java/com/google/devtools/build/lib/bazel/BazelServer_deploy.jar
	Build time: Wed Oct 18 14:27:19 2017 (1508336839)
	Build timestamp: 1508336839
	Build timestamp as int: 1508336839


### Install Tensorflow-1.4

#### 1. Link library path

    $ export PATH=$HOME/local/bin:$PATH
    $ export LD_LIBRARY_PATH=$HOME/local/lib64:$LD_LIBRARY_PATH

#### 2. Produce `.tf_configure.bazelrc` & `.bazelrc`

Run `./configure` with below setting configures:

	WARNING: Running Bazel server needs to be killed, because the startup options are different.
	You have bazel 0.7.0 installed.
	Please specify the location of python. [Default is /home/bass/anaconda3/envs/tf14/bin/python]: 


	Found possible Python library paths:
	/home/bass/anaconda3/envs/tf14/lib/python3.6/site-packages
	Please input the desired Python library path to use.  Default is [/home/bass/anaconda3/envs/tf14/lib/python3.6/site-packages]

	Do you wish to build TensorFlow with jemalloc as malloc support? [Y/n]: 
	jemalloc as malloc support will be enabled for TensorFlow.

	Do you wish to build TensorFlow with Google Cloud Platform support? [Y/n]: 
	Google Cloud Platform support will be enabled for TensorFlow.

	Do you wish to build TensorFlow with Hadoop File System support? [Y/n]: N
	No Hadoop File System support will be enabled for TensorFlow.

	Do you wish to build TensorFlow with Amazon S3 File System support? [Y/n]: N
	No Amazon S3 File System support will be enabled for TensorFlow.

	Do you wish to build TensorFlow with XLA JIT support? [y/N]: 
	No XLA JIT support will be enabled for TensorFlow.

	Do you wish to build TensorFlow with GDR support? [y/N]: 
	No GDR support will be enabled for TensorFlow.

	Do you wish to build TensorFlow with VERBS support? [y/N]: 
	No VERBS support will be enabled for TensorFlow.

	Do you wish to build TensorFlow with OpenCL support? [y/N]: 
	No OpenCL support will be enabled for TensorFlow.

	Do you wish to build TensorFlow with CUDA support? [y/N]: Y
	CUDA support will be enabled for TensorFlow.

	Please specify the CUDA SDK version you want to use, e.g. 7.0. [Leave empty to default to CUDA 8.0]:        


	Please specify the location where CUDA 8.0 toolkit is installed. Refer to README.md for more details. [Default is /usr/local/cuda]: 


	Please specify the cuDNN version you want to use. [Leave empty to default to cuDNN 6.0]: 7


	Please specify the location where cuDNN 7 library is installed. Refer to README.md for more details. [Default is /usr/local/cuda]:


	Please specify a list of comma-separated Cuda compute capabilities you want to build with.
	You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
	Please note that each additional compute capability significantly increases your build time and binary size. [Default is: 6.1,6.1]5.2,6.1


	Do you want to use clang as CUDA compiler? [y/N]: 
	nvcc will be used as CUDA compiler.

	Please specify which gcc should be used by nvcc as the host compiler. [Default is /usr/bin/gcc]: 


	Do you wish to build TensorFlow with MPI support? [y/N]: 
	No MPI support will be enabled for TensorFlow.

	Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native]: 


	Add "--config=mkl" to your bazel command to build with MKL support.
	Please note that MKL on MacOS or windows is still not supported.
	If you would like to use a local MKL instead of downloading, please set the environment variable "TF_MKL_ROOT" every time before build.
	Configuration finished

#### 3. Build bazel package

	$ bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package


#### 4. Generate `.whl` in `/tmp/tensorflow_pkg`

	$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg

#### 5. Install tensorflow via pip

	$ pip install /tmp/tensorflow_pkg/tensorflow-1.4.0-cp36-cp36m-linux_x86_64.whl

#### 6. Leave the repo folder & test

	$ cd
	$ python -c 'import tensorflow as tf; print (tf.__version__)'
	1.4.0

#### (optional) 7. Update your local `libstdc++.so` [one way for anaconda users]
   If you build tensorflow successfully but FAIL to import it in python with the error message shown as:
    ```
	ImportError: /home/bass/anaconda3/envs/tf14/bin/../lib/libstdc++.so.6: version `CXXABI_1.3.8' not found (required by /home/bass/anaconda3/envs/tf14/lib/python3.6/site-packages/tensorflow/python/_pywrap_tensorflow_internal.so)
    ```

   then you can re-link your `local_lib_path/libstdc++.so.6` from your system. Just do that:
```sh
$ rm $CONDA_PREFIX/lib/libstdc++.so*
$ ln -s /usr/lib/x86_64-linux-gnu/libstdc++.so $CONDA_PREFIX/lib/libstdc++.so
```

#### (optional) 8. Using pre-built `.whl`
You can also install tensorflow-1.4 via pre-built `tensorflow-1.4.0-cp36-cp36m-linux_x86_64.whl` in this folder.

	$ pip install tensorflow-1.4.0-cp36-cp36m-linux_x86_64.whl


---
## (optional) Install Tensorflow-1.4 with GDR, VERBS, XLA, OpenCL

#### 1. Install libibverbs

	sudo apt-get update && sudo apt-get install libibverbs-dev

#### 2. Download [computecpp SDK](https://www.codeplay.com/products/computesuite/computecpp) manually and unzip. Then rename the folder name to `/usr/local/computecpp`.

#### 3. Installing RDMA cm library from https://github.com/ofiwg/librdmacm

* Follow the README instructions to install the library. (root only)

* Before installing, check the `autoreconf` you have. You can use `apt` to get `autoreconf`.
```sh
sudo apt-get install dh-autoreconf
```

#### 4. Run `./configure`

```sh
You have bazel 0.7.0 installed.
Please specify the location of python. [Default is /home/bass/anaconda3/bin/python]: 


Found possible Python library paths:
  /home/bass/anaconda3/lib/python3.6/site-packages
Please input the desired Python library path to use.  Default is [/home/bass/anaconda3/lib/python3.6/site-packages]

Do you wish to build TensorFlow with jemalloc as malloc support? [Y/n]: 
jemalloc as malloc support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Google Cloud Platform support? [Y/n]: 
Google Cloud Platform support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Hadoop File System support? [Y/n]: N
No Hadoop File System support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Amazon S3 File System support? [Y/n]: N
No Amazon S3 File System support will be enabled for TensorFlow.

Do you wish to build TensorFlow with XLA JIT support? [y/N]: Y
XLA JIT support will be enabled for TensorFlow.

Do you wish to build TensorFlow with GDR support? [y/N]: Y
GDR support will be enabled for TensorFlow.

Do you wish to build TensorFlow with VERBS support? [y/N]: Y
VERBS support will be enabled for TensorFlow.

Do you wish to build TensorFlow with OpenCL SYCL support? [y/N]: Y
OpenCL SYCL support will be enabled for TensorFlow.

Please specify which C++ compiler should be used as the host C++ compiler. [Default is /usr/bin/g++]: 


Please specify which C compiler should be used as the host C compiler. [Default is /usr/bin/gcc]: 


Do you wish to build TensorFlow with ComputeCPP support? [Y/n]: 
ComputeCPP support will be enabled for TensorFlow.

Please specify the location where ComputeCpp for SYCL 1.2 is installed. [Default is /usr/local/computecpp]: 


Do you wish to build TensorFlow with CUDA support? [y/N]: Y
CUDA support will be enabled for TensorFlow.

Please specify the CUDA SDK version you want to use, e.g. 7.0. [Leave empty to default to CUDA 8.0]: 


Please specify the location where CUDA 8.0 toolkit is installed. Refer to README.md for more details. [Default is /usr/local/cuda]: 


Please specify the cuDNN version you want to use. [Leave empty to default to cuDNN 6.0]: 7


Please specify the location where cuDNN 7 library is installed. Refer to README.md for more details. [Default is /usr/local/cuda]:


Please specify a list of comma-separated Cuda compute capabilities you want to build with.
You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
Please note that each additional compute capability significantly increases your build time and binary size. [Default is: 6.1,6.1]5.2,6.1


Do you want to use clang as CUDA compiler? [y/N]: 
nvcc will be used as CUDA compiler.

Please specify which gcc should be used by nvcc as the host compiler. [Default is /usr/bin/gcc]: 


Do you wish to build TensorFlow with MPI support? [y/N]: 
No MPI support will be enabled for TensorFlow.

Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native]: 


Add "--config=mkl" to your bazel command to build with MKL support.
Please note that MKL on MacOS or windows is still not supported.
If you would like to use a local MKL instead of downloading, please set the environment variable "TF_MKL_ROOT" every time before build.
Configuration finished

```

#### 5. Add builtin link in `third_party/gpus/crosstool/CROSSTOOL_nvcc.tpl`

	$ vim third_party/gpus/crosstool/CROSSTOOL_nvcc.tpl
	183 +++ cxx_builtin_include_directory: "/usr/local/cuda/include"

#### 6. Build bazel package

	$ bazel build --copt=-march="broadwell" --config=cuda //tensorflow/tools/pip_package:build_pip_package

#### 7. Generate `.whl` in `/tmp/tensorflow_pkg`

	$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg

#### 8. Install tensorflow via pip

	$ pip install /tmp/tensorflow_pkg/tensorflow-1.4.0-cp36-cp36m-linux_x86_64.whl

#### 9. Leave the repo folder & test

	$ cd
	$ python -c 'import tensorflow as tf; print (tf.__version__)'
	1.4.0

## How to use XLA/JIT?

Turn ON the `global_jit_level` in your Session configure:

```python
# in your code

config = tf.ConfigProto()
config.graph_options.optimizer_options.global_jit_level = tf.OptimizerOptions.ON_1
config.gpu_options.allow_growth=True	# optional if you want to control gpu used-memory dynamic

with tf.Session(config=config) as sess:
	...
```

you will get the following message when executing:

	I tensorflow/compiler/xla/service/service.cc:162] XLA service 0x7fbf92814f60 executing computations on platform CUDA. Devices:
	I tensorflow/compiler/xla/service/service.cc:170]   StreamExecutor device (0): GeForce GTX 1080 Ti, Compute Capability 6.1

