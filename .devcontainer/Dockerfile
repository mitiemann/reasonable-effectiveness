FROM ubuntu:22.04

# Avoid prompts from apt
ENV DEBIAN_FRONTEND=noninteractive

# Install system dependencies
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    git \
    build-essential \
    libgmp-dev \
    zlib1g-dev \
    libtinfo-dev \
    libzmq3-dev \
    libcairo2-dev \
    libpango1.0-dev \
    libmagic-dev \
    libblas-dev \
    liblapack-dev \
    python3 \
    python3-pip \
    python3-dev \
    && rm -rf /var/lib/apt/lists/*

# Install Python packages including Jupyter
RUN pip3 install --no-cache-dir \
    jupyter \
    jupyterlab \
    numpy \
    pandas \
    matplotlib \
    seaborn \
    scipy \
    scikit-learn \
    plotly

# Install GHC and Stack for Haskell
RUN curl -sSL https://get.haskellstack.org/ | sh
ENV PATH="/root/.local/bin:$PATH"

# Install Julia
RUN wget https://julialang-s3.julialang.org/bin/linux/x64/1.9/julia-1.9.4-linux-x86_64.tar.gz \
    && tar -xzf julia-1.9.4-linux-x86_64.tar.gz \
    && mv julia-1.9.4 /opt/julia \
    && ln -s /opt/julia/bin/julia /usr/local/bin/julia \
    && rm julia-1.9.4-linux-x86_64.tar.gz

# Install IHaskell
RUN git clone https://github.com/gibiansky/IHaskell /tmp/IHaskell
WORKDIR /tmp/IHaskell
RUN stack install --fast
RUN stack exec ihaskell -- install --stack

# Install IJulia
RUN julia -e 'using Pkg; Pkg.add("IJulia")'

# Create working directory
WORKDIR /workspace

# Expose Jupyter port
EXPOSE 8888

# Set up Jupyter configuration
RUN jupyter notebook --generate-config
RUN echo "c.NotebookApp.ip = '0.0.0.0'" >> ~/.jupyter/jupyter_notebook_config.py
RUN echo "c.NotebookApp.open_browser = False" >> ~/.jupyter/jupyter_notebook_config.py
RUN echo "c.NotebookApp.allow_root = True" >> ~/.jupyter/jupyter_notebook_config.py
RUN echo "c.NotebookApp.token = ''" >> ~/.jupyter/jupyter_notebook_config.py

# Start Jupyter Lab by default
CMD ["jupyter", "lab", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root", "--NotebookApp.token=''"]
