FROM node:18-bullseye

# 安装 act 工具
RUN curl -s https://raw.githubusercontent.com/nektos/act/master/install.sh | bash -s -- -b /usr/local/bin

# 设置工作目录
WORKDIR /github/workspace

# 设置默认命令（可选）
CMD ["act", "--help"]