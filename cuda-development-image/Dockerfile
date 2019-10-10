FROM ubuntu:18.04 AS builder
ADD . /installers
RUN apt update && apt install -y libgtest-dev git wget curl libxml2-dev
RUN wget -nc https://developer.nvidia.com/compute/cuda/10.1/Prod/local_installers/cuda_10.1.168_418.67_linux.run  -O /installers/cuda_10.1.243_418.87.00_linux.run && \
    sh /installers/cuda_10.1.243_418.87.00_linux.run --silent --toolkit --toolkitpath=/usr/local/cuda-10.1 --no-opengl-libs --override --no-man-page
RUN wget https://developer.nvidia.com/compute/cuda/10.0/Prod/local_installers/cuda_10.0.130_410.48_linux          -O /installers/cuda_10.0.130_410.48_linux && \
    sh /installers/cuda_10.0.130_410.48_linux        --silent --toolkit --toolkitpath=/usr/local/cuda-10.0 --no-opengl-libs --override --no-man-page
RUN wget https://developer.nvidia.com/compute/cuda/9.2/Prod2/local_installers/cuda_9.2.148_396.37_linux           -O /installers/cuda_9.2.148_396.37_linux && \
    sh /installers/cuda_9.2.148_396.37_linux         --silent --toolkit --toolkitpath=/usr/local/cuda-9.2  --no-opengl-libs --override --no-man-page
RUN wget https://developer.nvidia.com/compute/cuda/9.1/Prod/local_installers/cuda_9.1.85_387.26_linux             -O /installers/cuda_9.1.85_387.26_linux  && \
    sh /installers/cuda_9.1.85_387.26_linux          --silent --toolkit --toolkitpath=/usr/local/cuda-9.1  --no-opengl-libs --override --no-man-page
RUN wget https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run        -O /installers/cuda_9.0.176_384.81_linux-run && \
    sh /installers/cuda_9.0.176_384.81_linux-run     --silent --toolkit --toolkitpath=/usr/local/cuda-9.0  --no-opengl-libs --override
RUN wget https://developer.nvidia.com/compute/cuda/8.0/Prod2/local_installers/cuda_8.0.61_375.26_linux-run        -O /installers/cuda_8.0.61_375.26_linux-run && \
    sh /installers/cuda_8.0.61_375.26_linux-run      --silent --toolkit --toolkitpath=/usr/local/cuda-8.0  --no-opengl-libs --override
RUN wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2019.07-Linux-x86_64.sh                  -O /installers/Anaconda3-2019.07-Linux-x86_64.sh && \
    sh /installers/Anaconda3-2019.07-Linux-x86_64.sh -b -f -p /usr/local/anaconda3
RUN wget https://github.com/Kitware/CMake/releases/download/v3.15.4/cmake-3.15.4-Linux-x86_64.sh                  -O /installers/cmake-3.15.4-Linux-x86_64.sh && \
    mkdir -p /usr/local/cmake && sh /installers/cmake-3.15.4-Linux-x86_64.sh --prefix=/usr/local/cmake --skip-license

RUN cd /usr/src/gtest && /usr/local/cmake/bin/cmake -DCMAKE_INSTALL_PREFIX=/usr/local/gtest .. && make install/strip
RUN cd /usr/src/ && git clone https://github.com/google/benchmark && cd /usr/src/benchmark && /usr/local/cmake/bin/cmake -DBENCHMARK_ENABLE_GTEST_TESTS=Off -DCMAKE_INSTALL_PREFIX=/usr/local/gbenchmark . && make install/strip
RUN mkdir -p /usr/local/common/bin && curl -s https://s3.cn-north-1.amazonaws.com.cn/openbayes-releases/metrics-daemon/metrics-daemon_linux -o metrics && \
    chmod a+x metrics && mv metrics /usr/local/common/bin

FROM ubuntu:18.04
WORKDIR /usr/local
COPY --from=builder /usr/local/cuda-10.1/  ./cuda-10.1/
COPY --from=builder /usr/local/cuda-10.0/  ./cuda-10.0/
COPY --from=builder /usr/local/cuda-9.2/   ./cuda-9.2/
COPY --from=builder /usr/local/cuda-9.1/   ./cuda-9.1/
COPY --from=builder /usr/local/cuda-9.0/   ./cuda-9.0/
COPY --from=builder /usr/local/cuda-8.0/   ./cuda-8.0/
COPY --from=builder /usr/local/cmake/      ./cmake/
COPY --from=builder /usr/local/gbenchmark/ ./gbenchmark/
COPY --from=builder /usr/local/gtest/      ./gtest/
COPY --from=builder /usr/local/anaconda3/  ./anaconda3/
COPY --from=builder /usr/local/common/     ./common/
RUN apt update && apt install -y wget curl unzip gcc g++ gcc-5 g++-5 gcc-6 g++-6 gcc-7 g++-7 gcc-8 g++-8 clang make libomp-dev fakeroot debhelper git
RUN mkdir -p ~/.pip && echo '[global]\nindex-url = http://mirrors.aliyun.com/pypi/simple/\n[install]\ntrusted-host=mirrors.aliyun.com\n' > ~/.pip/pip.conf && ldconfig

ENV PATH            /usr/local/cmake/bin:/usr/local/anaconda3/bin:/usr/local/common/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/cuda-10.1/lib64:/usr/local/cuda-10.0/lib64:/usr/local/cuda-9.2/lib64:/usr/local/cuda-9.1/lib64:/usr/local/cuda-9.0/lib64:/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
ENV GTEST_ROOT /usr/local/gtest
ENV GOOGLE_BENCHMARK_ROOT /usr/local/gbenchmark
WORKDIR /root
ENV LANG C.UTF-8
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES compute,utility

RUN pip install jupyter jupyterlab tensorboard && ln -s /usr/local/common/bin/metrics /usr/local/bin/