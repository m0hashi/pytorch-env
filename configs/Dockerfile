#================================================================================
# Make conda jupyterlab environment
#================================================================================
FROM pytorch/pytorch:1.4-cuda10.1-cudnn7-devel as jupyterlab-base
RUN apt-get update --fix-missing && \
    apt-get install -y  bzip2 ca-certificates libglib2.0-0 libxext6 libsm6 libxrender1 \
            git mercurial subversion openssh-server libzmq3-dev && \
    apt-get clean

#install jupyter lab extensions
#for  details, see -> https://qiita.com/canonrock16/items/d166c93087a4aafd2db4
RUN conda install -c conda-forge jupyterlab=1.2.0 nodejs xeus-cling
#RUN conda install -c conda-forge/label/broken jupyterlab 
#RUN conda install -c conda-forge nodejs
RUN jupyter labextension install \
  @lckr/jupyterlab_variableinspector \
  @jupyterlab/toc \
  jupyterlab_vim 
#  @jupyterlab/git

#npm kernel installation
RUN git clone https://github.com/3Nigma/nelu-kernelu.git
RUN npm install ./nelu-kernelu/

#julia 1.4 installation
RUN wget https://julialang-s3.julialang.org/bin/linux/x64/1.4/julia-1.4.0-linux-x86_64.tar.gz
RUN tar xvzf julia-1.4.0-linux-x86_64.tar.gz && \
    rm julia-1.4.0-linux-x86_64.tar.gz && \
    mv julia-1.4.0 /opt/julia && \
    ln -s /opt/julia/bin/julia /usr/bin/julia
RUN julia -e 'using Pkg; Pkg.add("IJulia");'

#================================================================================
# Set startup configurations
#================================================================================
FROM jupyterlab-base

#ssh-login setting
RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN echo "PermitRootLogin yes" >> /etc/ssh/sshd_config

WORKDIR /workspace
COPY ./files/docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
ENTRYPOINT [ "bash", "/usr/local/bin/docker-entrypoint.sh" ]
#CMD ["jupyter-lab","--port","8888","--ip","0.0.0.0", "--allow-root" , "--notebook-dir=/workspace", "--NotebookApp.token=''"]





