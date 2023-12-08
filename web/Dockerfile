# NodeJSはbuildでも使用するので、共通のimageとして作成しておく（その際に命名は base とする）
FROM node:18 AS base
CMD ["bash"]

# Reactのアプリケーションをbuidするためのimage（baseのimageを継承する）
FROM base AS build
# COPY {ホスト側の相対パス} {コンテナ側の相対パス} でコピーする -> Dockerfileと同じ階層にある全てのファイルを, コンテナ側の/workspace配下にペーストする
COPY . /workspace
# 作業する階層を指定する（cd して階層を移動しているのと近いイメージ）
WORKDIR /workspace
# 本番環境ではdocker-compose.ymlファイルに記述している環境変数の値は参照できない -> DockerfileのARGに記述すれば環境変数の値を本番環境でも呼び出すことができる
# github actions の中でdockerのイメージをビルドする処理を記述するが、その時にARGで定義したREACT_APP_API_SERVERの中に必要な値を設定するようにする
ARG REACT_APP_API_SERVER
RUN npm ci && npm run build

# Nginxを使ってホストするためのimage
FROM nginx:latst AS production
# build image で作られた /workspace/build のディレクトリを nginxコンテナの /var/www 配下にペーストする
COPY --from=build /workspace/build /var/www
# nginx.conf のファイルを nginxコンテナの指定の場所にペーストする
COPY nginx.conf /etc/nginx/conf.d/default.conf
