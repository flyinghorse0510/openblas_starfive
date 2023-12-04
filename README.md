# Starfive OpenBLAS(LAPACK)
## 1. Introduction
This repository focused on building & running OpenBLAS(LAPACK) library/test suite on linux/amd64 and linux/riscv64 platforms.

Besides, as part of the port & build work, several docker container images are built and uploaded to the Dockerhub(flyinghorse0510), which facilitate the procedure of build & test dramatically. All Dockerfiles used for building these images can be found in the repository [Starfive Builder](http://192.168.110.45/sag/starfive_builder).

## 2. Build
Use customized and self-build docker container images to compile the benchmark suite. All Dockerfiles and scripts can be found within [Starfive Builder](http://192.168.110.45/sag/starfive_builder)

> Notice: currently you need to have sudo/root access to compile natively for `linux/riscv64` platform since it utilizes Linux kernel's binfmt_misc features which invokes QEMU user space emulator to run the `riscv64` version container directly on an `amd64` machine.

### 2.1 Clone source
```bash
# clone repo
git clone http://192.168.110.45/sag/openblas-starfive --depth 1
```

### 2.2 Build for `linux/amd64` platform
```bash
# enter source directory
cd openblas-starfive
# build with container
docker build --platform=linux/amd64 -t openblas_runner -f Dockerfile.openblas_runner_builder .
# or you can just use the pre-built and uploaded container images
docker pull --platform=linux/amd64 flyinghorse0510/openblas_runner && docker tag flyinghorse0510/openblas_runner openblas_runner
# run the container for inspecting/testing openblas(lapack) library/test binaries (you can now log into the container using ssh with local port 6622(username: starfive, password:starfive) or docker exec command) which are under `/opt` directory
docker run -itd --rm -p 6622:22 --name openblas_runner openblas_runner
# obtain the openblas(lapack) library/test binaries
docker cp openblas_runner:/opt ./openblas_runner
# if everything goes well, you can find all openblas(lapack) binaries under the openblas_runner directory
```

### 2.3 Build for `linux/riscv64` platform
> make sure you have the sudo/root access and the `binfmt` kernel module has been enabled for the current running kernel

```bash
# register for kernel's binfmt_misc
sudo docker run --privileged --rm flyinghorse0510/multiarch-riscv64 --reset
# enter source directory
cd openblas-starfive
# build with container
docker build --platform=linux/riscv64 -t openblas_runner -f Dockerfile.openblas_runner_builder .
# or you can just use the pre-built and uploaded container images
docker pull --platform=linux/riscv64 flyinghorse0510/openblas_runner && docker tag flyinghorse0510/openblas_runner openblas_runner
# run the container for inspecting/testing openblas(lapack) library/test binaries (you can now log into the container using ssh with local port 6622(username: starfive, password:starfive) or docker exec command) which are under `/opt` directory
docker run -itd --rm -p 6622:22 --name openblas_runner openblas_runner
# obtain the openblas(lapack) library/test binaries
docker cp openblas_runner:/opt ./openblas_runner
# if everything goes well, you can find all openblas(lapack) binaries under the openblas_runner directory
```

## 3. Usage
under the `/opt` directory:

```bash
./verify_blas.sh
# I highly recommend you adjust the threadList for the last `LAPACK Multithread Test` in this script, especially running on the emulator/FPGA, which can consume a horrible period of time to complete!
# minor error rate for the last test is normal and acceptable
```