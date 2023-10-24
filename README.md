# apptainer
Storing .sif images

Here are the steps to make a new sif image from a def file:
```
$ srun --ntasks 1 --cpus-per-task 2 --pty -p a100 --mem 8  bash 
$ cd ~/apptainer
```
Add any packages or system installs you want inside this file, I already added the software tools you requested  (except for slurm since that does not persist inside a container) 
```
$ emacs code_server_tf.def 
```
Note:
 - you can make comments with # and you can uncomment the lines that uses the requirements.txt file and add a requirements file to do all your python package installs while building 
 - you have to build the image while on a compute node or you will get a permissions issue) 
```
$ apptainer build code_server_tf.sif code_server_tf.def  
```
above code_server_tf.sif is  your finished image and code_server_tf.def is what  it uses to build from
To test the image you can run :
```
$ apptainer shell code_server_tf.sif
Apptainer> which git
Apptainer> htop
```
If all the above works your image is ready to go. Next test it with OOD.
Select the new image in OOD : /home/username/apptainer/code_server_tf.sif

build a new .sif from an existing .sif image and it doesnt necessarily have to be from a docker image. To do that use this at the top of your .def file:
```
Bootstrap: localimage
From: /home/ycai/apptainer/tf.sif
```

This  can also be called without providing a definition file using the following shorthand.
```
$ apptainer build tf_new.sif tf.sif
```

To inspect what it did behind the scenes :
```
$ apptainer inspect --deffile tf_new.sif
bootstrap: localimage
from: tf.sif
```

Note that the third command may not seem very useful because you are just copying the container called tf.sif to a new container called tf_new.sif. But you can also use build in this way to convert a SIF file to a sandbox and back again:
```
$ apptainer build --sandbox tf-sand tf.sif
$ apptainer build tf-sif tf-sand/
```


Sandbox containers make them writable, and once it is writable you can enter as fakeroot and download packages directly in that container with the fakeroot and writeable flags.
```
$ apptainer shell --writable --fakeroot  tf.sif
```
