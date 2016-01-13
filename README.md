Jenkins Docker Image
====================

This repository contains Dockerfiles for a Jenkins Docker image intended for use with [OpenShift v3](https://github.com/openshift/origin)

For an example of how to use it, [see this sample.](https://github.com/openshift/origin/blob/master/examples/jenkins/README.md)

The image is pushed to DockerHub as openshift/jenkins-1-centos7.

Versions
---------------------------------
Jenkins versions currently provided are:
* jenkins-1.6x

RHEL versions currently supported are:
* RHEL7

CentOS versions currently supported are:
* CentOS7


Installation
---------------------------------
Choose either the CentOS7 or RHEL7 based image:

*  **RHEL7 based image**

    To build a RHEL7 based image, you need to run Docker build on a properly
    subscribed RHEL machine.

    ```
    $ git clone https://github.com/openshift/jenkins.git
    $ cd jenkins
    $ make build TARGET=rhel7 VERSION=1
    ```

*  **CentOS7 based image**

	This image is available on DockerHub. To download it run:

	```
	$ docker pull openshift/jenkins-1-centos7
	```

	To build a Jenkins image from scratch run:

	```
	$ git clone https://github.com/openshift/jenkins.git
	$ cd jenkins
	$ make build VERSION=1
	```

**Notice: By omitting the `VERSION` parameter, the build/test action will be performed
on all provided versions of Jenkins. Since we are currently providing only version `1`,
you can omit this parameter.**


Environment variables
---------------------------------

The image recognizes the following environment variables that you can set during
initialization by passing `-e VAR=VALUE` to the Docker run command.

|    Variable name          |    Description                              |
| :------------------------ | -----------------------------------------   |
|  `JENKINS_PASSWORD`       | Password for the 'admin' account            |



You can also set the following mount points by passing the `-v /host:/container` flag to Docker.

|  Volume mount point         | Description              |
| :-------------------------- | ------------------------ |
|  `/var/lib/jenkins`         | Jenkins config directory |

**Notice: When mounting a directory from the host into the container, ensure that the mounted
directory has the appropriate permissions and that the owner and group of the directory
matches the user UID or name which is running inside the container.**


Plugins
---------------------------------

In order to install additional Jenkins plugins, the OpenShift Jenkins image provides a way
how to add those by layering on top of this image. The derived image, will provide the same functionality
as described in this documentation, in addition it will also include all plugins you list in the `plugins.txt` file.

To create derived image, you have to write following `Dockerfile`:

```
FROM openshift/jenkins-1-centos7
COPY plugins.txt /opt/openshift/configuration/plugins.txt
RUN /usr/local/bin/plugins.sh /opt/openshift/configuration/plugins.txt
```

The format of `plugins.txt` file is:

```
pluginId:pluginVersion
```

For example, to install the github Jenkins plugin, you specify following to `plugins.txt`:

```
github:1.11.3
```

After this, just run `docker build -t my_jenkins_image -f Dockerfile`.

Furthermore,  there are now 2 plugins of note included in this Jenkins Docker image that specifically facilitate the creation
of CI/CD jobs and workflows for [OpenShift v3](https://github.com/openshift/origin):

* **OpenShift Pipeline plugin**

  See [the following](https://github.com/openshift/jenkins-plugin), as well an example use of the plugin's capabilities with the [OpenShift Sample Job](https://github.com/openshift/jenkins/tree/master/1/contrib/openshift/configuration/jobs/OpenShift%20Sample)
  included in this image.

* **Kubernetes plugin**

  See [the following](https://github.com/jenkinsci/kubernetes-plugin), as well as the examples for constructing Jenkins [masters](https://github.com/openshift/jenkins/tree/master/examples/master) and
  [slaves](https://github.com/openshift/jenkins/tree/master/examples/slave) that interact with [OpenShift v3](https://github.com/openshift/origin)

Usage
---------------------------------

For this, we will assume that you are using the `openshift/jenkins-1-centos7` image.
If you want to set only the mandatory environment variables and store the database
in the `/tmp/jenkins` directory on the host filesystem, execute the following command:

```
$ docker run -d -e JENKINS_PASSWORD=<password> -v /tmp/jenkins:/var/lib/jenkins openshift/jenkins-1-centos7
```


Jenkins admin user
---------------------------------

The admin user name is set to `admin` and you have to to specify the password by
setting the `JENKINS_PASSWORD` environment variable. This process is done
upon initialization.


Test
---------------------------------

This repository also provides a test framework which checks basic functionality
of the Jenkins image.

Users can choose between testing Jenkins based on a RHEL or CentOS image.

*  **RHEL based image**

    To test a RHEL7 based Jenkins image, you need to run the test on a properly
    subscribed RHEL machine.

    ```
    $ cd jenkins
    $ make test TARGET=rhel7 VERSION=5.5
    ```

*  **CentOS based image**

    ```
    $ cd jenkins
    $ make test VERSION=1
    ```

**Notice: By omitting the `VERSION` parameter, the build/test action will be performed
on all provided versions of Jenkins. Since we are currently providing only version `1`,
you can omit this parameter.**
