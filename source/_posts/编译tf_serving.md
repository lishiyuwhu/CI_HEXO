---
title: 编译tf_serving
date: 2019/9/22 15:48
tags: 
---


【踩坑实录】TensorFlow Serving 安装总结
https://zhuanlan.zhihu.com/p/55108743


TENSORFLOW-SERVING-GPU 本地编译并使用
https://www.cnblogs.com/YouXiangLiThon/p/8295499.html
<!-- more -->

# 安装依赖

```shell script
   40  java -version
   41  pip install -i https://pypi.tuna.tsinghua.edu.cn/simple pip -U
   42  pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
   43  man epel
   44  yum -y install freetype-devel libpng12-devel zip zlib-devel giflib-devel zeromq3-devel
   45  gcc -version
   46  gcc --version
   47  pip install numpy
   48  sudo yum -y install freetype-devel libpng12-devel zip zlib-devel giflib-devel zeromq3-devel
   49  pip install grpcio_tools mock grpcio
   50  pip install keras_applications,keras_preprocessing,h5py
   51  pip install keras_applications
   52  pip install keras_preprocessing
   53  pip install h5py
   54  sudo yum -y install libev libev-devel zlib zlib-devel openssl openssl-devel
   55  ls
   56  wget https://github.com/bazelbuild/bazel/releases/download/0.19.2/bazel-0.19.2-installer-linux-x86_64.sh
   57  rz
   58  ls
   59  chmod +x bazel-0.19.2-installer-linux-x86_64.sh
   60  ./bazel-0.19.2-installer-linux-x86_64.sh
```

>这里选择bazel0.19.2版本，在GitHub issue上提出，0.19.2和0.18.x版本对import StringIO这个python2特有的包做的处理比较好，不会出现“ImportError: No module named 'StringIO'”这样的错误。

然后git clone tf 1.11

```shell script
git clone -b r1.11 --recurse-submodules https://github.com/tensorflow/serving

cd serving
mv tools/bazel.rc ./.bazelrc

```

然后开始编译

注意python路径, 全部手工指定
```shell script
bazel build -c opt --verbose_failures --action_env PYTHON_BIN_PATH=/data0/shanqi/anaconda3/bin/python --action_env PYTHON_LIB_PATH=/data0/shanqi/anaconda3/lib/python3.7/site-packages --define PYTHON_BIN_PATH=/data0/shanqi/anaconda3/bin/python --define PYTHON_LIB_PATH=/data0/shanqi/anaconda3/lib/python3.7/site-packages  --python_path=/data0/shanqi/anaconda3/bin/python --force_python=py3 --host_force_python=py3 --define with_jemalloc=true --define with_gcp_support=true tensorflow_serving/...
```


报错
```shell script
ERROR: /data0/shanqi/.cache/bazel/_bazel_shanqi/68bcdaae61708a14b8d3d517b634806f/external/org_tensorflow/tensorflow/python/BUILD:3768:1: Linking of rule '@org_tensorflow//tensorflow/python:_pywrap_tensorflow_internal.so' failed (Exit 1): gcc failed: error executing command
  (cd /data0/shanqi/.cache/bazel/_bazel_shanqi/68bcdaae61708a14b8d3d517b634806f/execroot/tf_serving && \
  exec env - \
    PATH=/usr/local/lib/bazel/bin:/usr/local/bin:/data0/shanqi/anaconda3/bin:/data0/shanqi/anaconda3/condabin:/usr/local/sbin:/sbin:/bin:/usr/sbin:/usr/bin:/opt/puppetlabs/bin:/root/bin \
    PWD=/proc/self/cwd \
  /bin/gcc -shared -o bazel-out/host/bin/external/org_tensorflow/tensorflow/python/_pywrap_tensorflow_internal.so -Wl,--version-script bazel-out/host/bin/external/org_tensorflow/tensorflow/python/pywrap_tensorflow_internal_versionscript.lds '-Wl,-rpath,$ORIGIN/,-rpath,$ORIGIN/..' -Wl,-soname,_pywrap_tensorflow_internal.so -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread -pthread '-fuse-ld=gold' -Wl,-no-as-needed -Wl,-z,relro,-z,now -B/bin -B/usr/bin -pass-exit-codes -Wl,--gc-sections -Wl,-S -Wl,@bazel-out/host/bin/external/org_tensorflow/tensorflow/python/_pywrap_tensorflow_internal.so-2.params)
/bin/ld.gold: error: bazel-out/host/bin/external/org_tensorflow/tensorflow/core/kernels/_objs/dynamic_stitch_op/dynamic_stitch_op.pic.o: file is empty
/bin/ld.gold: error: bazel-out/host/bin/external/boringssl/_objs/crypto/cpu-arm.pic.o: file is empty
/bin/ld.gold: error: bazel-out/host/bin/external/jpeg/_objs/jpeg/jdapistd.pic.o: file is empty
collect2: error: ld returned 1 exit status
INFO: Elapsed time: 19682.787s, Critical Path: 2014.75s, Remote (0.00% of the time): [queue: 0.00%, setup: 0.00%, process: 0.00%]
INFO: 7791 processes: 7791 local.
FAILED: Build did NOT complete successfully

```

第一次编译时加了local_resources,  编译了6个小时然后最后报错了. 查询知道可能是资源不足,去掉
```--local_resources``` 
相关选项.同时安装了一下tensoflow(1.14)  

之后重新开始编译, 第二次就很快了

```shell script
INFO: Elapsed time: 117.158s, Critical Path: 45.59s, Remote (0.00% of the time): [queue: 0.00%, setup: 0.00%, process: 0.00%]
INFO: 2595 processes: 2595 local.
INFO: Build completed successfully, 3224 total actions
```


就得到了```bazel-bin/tensorflow_serving/model_servers/tensorflow_model_server```

# 验证得到的tensorflow_model_server

生成自带的测试mnist模型
```shell script
python tensorflow_serving/example/mnist_saved_model.py models/mnist
```

开服务
```shell script
bazel-bin/tensorflow_serving/model_servers/tensorflow_model_server --port=8042 --model_name=mnist --model_base_path=/data0/shanqi/workspace/tfserving/serving/models/mnist
```

client端要先装tfserving
```shell script
pip install tensorflow-serving-api
python tensorflow_serving/example/mnist_client.py --num_tests=1000 --server=127.0.0.1:8042
```

运行之后, 说明编译成功

``` 
> Inference error rate: 10.4%
```


