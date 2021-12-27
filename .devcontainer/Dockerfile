FROM python:latest

# Copy pip-requirements.txt
COPY pip-requirements.txt /tmp/

# Copy galaxy-requirements.yml
COPY galaxy-requirements.yml /tmp/

# Create and activate virtual environment
ENV VIRTUAL_ENV=/opt/ansiblevenv
RUN python3 -m venv $VIRTUAL_ENV
ENV PATH="$VIRTUAL_ENV/bin:$PATH"

# Add backports Repo
RUN echo deb "http://deb.debian.org/debian buster-backports main" >> /etc/apt/sources.list

# Update apt, install sshpass, install git from backports
RUN apt-get update && apt-get install -y sshpass openssh-client && \
    apt-get -y -t buster-backports install git git-lfs

# Enable auto completion 
RUN apt-get update && \
    apt-get install -y bash-completion && \
    echo "if [ -f /etc/bash_completion ]; then" >> /root/.bashrc && \
    echo "    . /etc/bash_completion" >> /root/.bashrc && \
    echo "fi" >> /root/.bashrc

# Upgrade pip, install ansible and dependencies from pip-requirements.txt
RUN pip install --upgrade pip && \
    pip install -r /tmp/pip-requirements.txt

# Install pre-commit
RUN pip install pre-commit --ignore-installed

# Configure pre-commit
RUN DIR=~/.git-template && \
    git config --global init.templateDir ${DIR} && \
    pre-commit init-templatedir -t pre-commit ${DIR}

# Install Galaxy Collections
RUN ansible-galaxy role install -r /tmp/galaxy-requirements.yml && \
    ansible-galaxy collection install -r /tmp/galaxy-requirements.yml
