FROM node:14-slim as dev

ENV DEBIAN_FRONTEND=noninteractive

ARG USERNAME=dev
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
    # utils
    && apt-get -y install git procps lsb-release  \
    # manage dev user
    && usermod -u 1001 node \
    #&& groupadd --gid $USER_GID $USERNAME \
    && useradd -m -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    # clean
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*



RUN yarn global add @vue/cli

USER $USERNAME

ENV HOME /home/$USERNAME

RUN mkdir -p $HOME/frequi/node_modules

WORKDIR $HOME/frequi

COPY ./package.json .

# install dependencies
RUN yarn install

EXPOSE 8080

FROM dev as vscode

WORKDIR $HOME

# load ssh key in bash terminal
RUN  echo "eval \$(ssh-agent) && ssh-add ~/.ssh/id_rsa"  >> .bashrc

# set variable bash history
RUN echo "export PROMPT_COMMAND='history -a'" >> .bashrc \
    && echo "export HISTFILE=~/commandhistory/.bash_history" >> .bashrc

# ensure folder owned by $USERNAME
RUN mkdir -p .vscode-server .vscode-server-insiders commandhistory

ENV DEBIAN_FRONTEND=
