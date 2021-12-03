FROM balenalib/raspberrypi3-debian-python:3-buster

CMD /bin/bash

#aggiorno i pacchetti ed installo quelli necessari per l'installazione
#di miniconda3
#RUN apt-get update && apt-get upgrade
RUN install_packages bzip2 git wget xz-utils xc3sprog

RUN wget http://repo.continuum.io/miniconda/Miniconda3-latest-Linux-armv7l.sh

# con -b eseguo lo script in modalità batch, senza interazione con l'utente
RUN bash Miniconda3-latest-Linux-armv7l.sh -b

ENV PATH=/root/miniconda3/bin:${PATH}
RUN export PATH
RUN conda update -y conda
#RUN rm Miniconda3-latest-Linux-x86_64.sh

#Da qui inizia lo script di Mattia

#taking the board model as build time argument
ARG BOARD_MODEL
WORKDIR /symb

#setting the enviroment variables
ENV INSTALL_DIR=/symb
ENV BOARD_MODEL=${BOARD_MODEL}
ENV FPGA_FAM=xc7

#additional variables
ENV MODE=null
ENV TOP_FILE=null
ENV PRJ_DIR=null

#cloning into symbiflow-examples
RUN git clone https://github.com/Mksiva1/symbiflow-examples && \
    cd symbiflow-examples

#creating the conda enviroment
RUN conda env create -f ${INSTALL_DIR}/symbiflow-examples/${FPGA_FAM}/environment.yml

#setting the shell inside the enviroment (xc7 -> FAMILY)
SHELL ["conda", "run", "-n", "xc7", "/bin/bash", "-c"]

#installing pyFPGA    
RUN git clone --single-branch --branch symbiflow-support https://github.com/PyFPGA/pyfpga.git && \
    cd pyfpga && \
    git clone https://github.com/PyFPGA/resources.git && \
    pip install -e . && \
    cd ..

#installing the toolchain
RUN mkdir -p $INSTALL_DIR/${FPGA_FAM}/install && \
    wget -qO- https://storage.googleapis.com/symbiflow-arch-defs/artifacts/prod/foss-fpga-tools/symbiflow-arch-defs/continuous/install/459/20211116-000105/symbiflow-arch-defs-install-ef6fff3c.tar.xz | tar -xJC $INSTALL_DIR/${FPGA_FAM}/install       && \
    wget -qO- https://storage.googleapis.com/symbiflow-arch-defs/artifacts/prod/foss-fpga-tools/symbiflow-arch-defs/continuous/install/459/20211116-000105/symbiflow-arch-defs-${BOARD_MODEL}_test-ef6fff3c.tar.xz | tar -xJC $INSTALL_DIR/${FPGA_FAM}/install


#linking the toolchain commands
ENV PATH="$PATH:/symb/${FPGA_FAM}/install/bin"

#Copy runner script
#COPY symbiflowrun.py /symb/

#manca l'entrypoint solo per test







