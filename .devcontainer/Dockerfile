FROM mcr.microsoft.com/devcontainers/base:debian

USER vscode
RUN mkdir -p /home/vscode/.pixi/bin \
    && curl -fsSL --retry 3 "https://github.com/prefix-dev/pixi/releases/latest/download/pixi-$(uname -m)-unknown-linux-musl.tar.gz" \
    | tar -xzC /home/vscode/.pixi/bin pixi \
    && chmod +x /home/vscode/.pixi/bin/pixi \
    && git config --global safe.directory '*' \
    && git config --global core.autocrlf input

ENV PATH="/home/vscode/.pixi/bin:${PATH}"

RUN pixi global install opencode -c https://prefix.dev/github-releases
WORKDIR /workspace
