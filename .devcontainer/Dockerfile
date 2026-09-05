FROM node:slim

ARG TZ
ENV TZ="$TZ"

# Install basic development tools
RUN apt-get update && apt-get install -y --no-install-recommends \
  less \
  git \
  sudo \
  vim \
  # Attempting to get the notifier to work, still not passing through from container
  libnotify-bin

# Set up the dev container user
ARG USERNAME
# So we need the uid & gid to be the same as outside the container - this is
# typically 1000:1000 on WSL (seems to work on Mac too).
# In the node:slim base, the `node` user is already coming in
# with 1000:1000 so we repurpose the node user to be our USERNAME here
ARG USER_UID=1000
ARG USER_GID=1000

# rename group "node" → container user, set correct GID
RUN groupmod -n ${USERNAME} -g ${USER_GID} node

# rename user "node" → container user, set correct UID + attach to renamed group
RUN usermod -l ${USERNAME} -u ${USER_UID} -g ${USER_GID} node

# move home directory to new name
RUN usermod -d /home/${USERNAME} -m ${USERNAME}

# Ensure dev container user has access to /usr/local/share
RUN mkdir -p /usr/local/share/npm-global && \
  chown -R ${USERNAME}:${USERNAME} /usr/local/share


# Persist bash history.
RUN SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
  && mkdir /commandhistory \
  && touch /commandhistory/.bash_history \
  && chown -R ${USERNAME} /commandhistory

# Set `DEVCONTAINER` environment variable to help with orientation
ENV DEVCONTAINER=true

# Create workspace and config directories and set permissions
RUN mkdir -p /workspace /home/${USERNAME}/.opencode && \
  chown -R ${USERNAME}:${USERNAME} /workspace /home/${USERNAME}/.opencode

WORKDIR /workspace

# Set up non-root user
USER ${USERNAME}

# Install global packages
ENV NPM_CONFIG_PREFIX=/usr/local/share/npm-global
ENV PATH=$PATH:/usr/local/share/npm-global/bin

# Set the default shell to zsh rather than sh
ENV SHELL=/bin/bash

# Set the default editor and visual
ENV EDITOR=vim
ENV VISUAL=vim

# Install OpenCode
RUN npm install -g opencode-ai

# Create the opencode configuration file from the template
COPY opencode.jsonc /tmp/opencode.jsonc
RUN mkdir -p "/home/${USERNAME}/.config/opencode" && \
    cp /tmp/opencode.jsonc \
       "/home/${USERNAME}/.config/opencode/opencode.jsonc"

RUN chown -R "${USERNAME}:${USERNAME}" \
  "/home/${USERNAME}/.config/opencode"

# Install TPS counter plugin & notifier plugin
RUN opencode plugin oc-tps@latest --global
# (The notifier may not work from inside of docker)
RUN opencode plugin @mohak34/opencode-notifier@latest --global
