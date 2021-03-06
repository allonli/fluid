# Contributing

To ensure the efficiency of collaborative development, developers please do NOT configure your own development environment, but please use the `/Dockerfile` to build a Docker image as your development environment.

## Build the Development Environment as a Docker Image

```bash
git clone https://github.com/wangkuiyi/fluid
cd fluid
docker build -t fluid:dev .
```

The above commands create a Docker image named `fluid:dev`, which has all development tools installed, including clang, llvm, python, protoc, open-ssh etc.  For more details, please check the `/Dockerfile`.

## Run the Development Environment as a Docker Container

Use the following command to run the above Docker image as a container:

```bash
docker run -v $PWD:/fluid -d --rm -P --name fluid fluid:dev
```

The `--name fluid` flag gives the container a name `fluid`.

The `-v $PWD:/fluid` flag maps the current directory, which is supposed to be the local `fluid` repo, to the `/fluid` directory in the container.

The `-d` flag runs the container in the background. But the default entry-point of the container starts the SSH service, which accepts users to log in as `root`, and the password is also `root`.

The `-P` flag maps all listening ports inside the container, particularly, the SSH service port 22, to a port of the host computers, which could be revealed by the following command

```bash
docker port fluid 22
```

Suppose that the above command prints the host network address `0.0.0.0:32770`, we could log in by running

```bash
ssh root@localhost -P 32770
```


## Build and Test in the Development Environment

Within the SSH session, type the following command to build the Fluid source code:

```bash
cd /fluid
mkdir build
cd build
cmake ..
make -j10
```

The `cd /fluid` works because that the flag `-v $PWD:/fluid` in the Docker container startup command maps the source directory on the host to directory `/fluid` in the container.

The mapping also enables developers to run the editors (Emacs, VIM, Eclipse, etc) on the host computer -- whenever they save the edits, the changes are mapped into the container, so the developers can run

```bash
make -j10
```

in the SSH session again to rebuild.

The following command lists all unit tests:

```bash
ctest -N
```

The following command runs a specific test and output verbose:

```bash
ctest -V -R test_fit_a_line
```
