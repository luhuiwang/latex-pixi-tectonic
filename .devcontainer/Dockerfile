FROM mcr.microsoft.com/devcontainers/base:resolute

USER vscode
RUN mkdir -p /home/vscode/.pixi/bin \
    && curl -fsSL --retry 3 "https://gh-proxy.org/https://github.com/prefix-dev/pixi/releases/latest/download/pixi-$(uname -m)-unknown-linux-musl.tar.gz" \
    | tar -xzC /home/vscode/.pixi/bin pixi \
    && chmod +x /home/vscode/.pixi/bin/pixi \
    && git config --global safe.directory '*' \
    && git config --global core.autocrlf input

RUN sudo apt update \
    && sudo apt install -y locales fonts-noto-cjk bash-completion \
    && sudo locale-gen zh_CN.UTF-8 \
    && sudo update-locale LANG=zh_CN.UTF-8 LC_ALL=zh_CN.UTF-8

ENV PATH="/home/vscode/.pixi/bin:${PATH}"

RUN pixi global install opencode -c https://prefix.dev/github-releases \
    && pixi global install starship \
    && echo 'eval "$(pixi completion --shell bash)"' >> /home/vscode/.bashrc \
    && echo 'eval "$(starship init bash)"' >> /home/vscode/.bashrc
WORKDIR /workspace
