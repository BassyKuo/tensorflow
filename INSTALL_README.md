# Tensorflow r1.4 installation steps

## Select the tensorflow source version

### checkout `r1.4`

	```sh
	$ git checkout r.14
	```


## Update bazel

### Make sure `bazel version` > 0.5.4

   ```sh
   $ sudo apt-get upgrade bazel
   $ bazel version
   Build label: 0.7.0
   Build target: bazel-out/local-fastbuild/bin/src/main/java/com/google/devtools/build/lib/bazel/BazelServer_deploy.jar
   Build time: Wed Oct 18 14:27:19 2017 (1508336839)
   Build timestamp: 1508336839
   Build timestamp as int: 1508336839
   ```


## Install Tensorflow-1.4

### Link library path

	```sh
	$ export PATH=$HOME/local/bin:$PATH
	$ export LD_LIBRARY_PATH=$HOME/local/lib64:$LD_LIBRARY_PATH
	```

### Produce `.tf_configure.bazelrc` & `.bazelrc`

	Run `./configure` with below setting configures:

    ```sh
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
	```

### Build bazel package

	```sh
	$ bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package
	```

### Generate `.whl` in `/tmp/tensorflow_pkg`

	```sh
	$ bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
	```

### Install tensorflow via pip

	```sh
	$ pip install /tmp/tensorflow_pkg/tensorflow-1.4.0-cp36-cp36m-linux_x86_64.whl
	```

### Leave the repo folder & test

	```sh
	$ cd
	$ python -c 'import tensorflow as tf; print (tf.__version__)'
	1.4.0
	```

### (optional) Update your local `libstdc++.so` [one of solutions for anaconda users]

	If you build tensorflow successfully but FAIL to import it in python with the error message shown as:
	```python
	ImportError: /home/bass/anaconda3/envs/tf14/bin/../lib/libstdc++.so.6: version `CXXABI_1.3.8' not found (required by /home/bass/anaconda3/envs/tf14/lib/python3.6/site-packages/tensorflow/python/_pywrap_tensorflow_internal.so)
	```
	then you can re-link your `local_lib_path/libstdc++.so.6` from your system. Just do that:
	```sh
	$ rm $CONDA_PREFIX/lib/libstdc++.so*
	$ ln -s /usr/lib/x86_64-linux-gnu/libstdc++.so $CONDA_PREFIX/lib/libstdc++.so
	```
