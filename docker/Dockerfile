FROM pytorch/pytorch:2.5.1-cuda12.4-cudnn9-devel

# Install necessary dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    git \
    vim \
    build-essential \
    libcairo2 libcairo2-dev \    
    # ← 开发头文件也要装
    pkg-config \
    libffi-dev \
    cuda-compiler-12-4 \
    libaio-dev \
    && rm -rf /var/lib/apt/lists/*

# meson (plus helper) is needed to build pycairo
RUN pip install --upgrade pip setuptools wheel meson meson-python

# Install Jupyter Notebook, clone and install star-vector and dependencies
RUN pip install --upgrade pip && pip install jupyter deepspeed \
    && git clone https://github.com/joanrod/star-vector.git /tmp/star-vector \
    # install the project without build isolation so that torch is available when
    # flash_attn wheels are built (the default isolated build env doesn't see
    # the base image's torch package and the build fails).
    && pip install --no-build-isolation /tmp/star-vector \
    && rm -rf /tmp/star-vector

# Cleanup unneeded packages
RUN apt-get purge -y --auto-remove \
    git \
    build-essential \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Expose Jupyter Notebook port
EXPOSE 8888

WORKDIR /workspace

# Launch Jupyter Notebook
CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root"]
