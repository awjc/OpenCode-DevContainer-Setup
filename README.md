# OpenCode-DevContainer-Setup
Simple VSCode dev container setup for running OpenCode


This repo contains a `.devcontainer` folder. To use this in VSCode Dev Containers:
  - clone the repo and copy the `.devcontainer` folder to the root of your project directory
  - open the project folder in VSCode - it should prompt you to 'Reopen in Container'
  - This will run the .devcontainer/Dockerfile (using settings from .devcontainer/devcontainer.json)
  - The container will now be open and Terminal should verify you are the `devcon` user
      - The UID:GID should be 1000:1000 which lets it seamlessly work with WSL filesystem so new files created will be editable as normal
  - The default settings will be imported from the template file `.devcontainer/opencode.jsonc` - this includes a default Ollama remote configuration that you can replace
    with your own host name (e.g. if you are running your own Ollama model via a desktop and then pointing opencode to it on a laptop)
  - OpenCode should be installed and ready to use in VSCode in a fully sandboxed way
