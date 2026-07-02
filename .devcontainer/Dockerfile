FROM mcr.microsoft.com/devcontainers/base:debian

ARG PIXI_VERSION=v0.72.0
ARG GITHUB_MIRROR=https://ghproxy.net/

RUN curl -fsSL --retry 3 \
    "${GITHUB_MIRROR}https://github.com/prefix-dev/pixi/releases/download/${PIXI_VERSION}/pixi-$(uname -m)-unknown-linux-musl.tar.gz" \
    | tar -xzC /usr/local/bin pixi \
    && chmod +x /usr/local/bin/pixi \
    && git config --global safe.directory '*' \
    && git config --global core.autocrlf input

ENV PATH="/home/vscode/.pixi/bin:${PATH}"

USER vscode
RUN pixi global install opencode -c https://prefix.dev/github-releases
WORKDIR /workspace
