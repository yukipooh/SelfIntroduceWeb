# ステージ1: Reactアプリのビルド
FROM node:18-alpine AS build

# npmをインストール
RUN apk add --no-cache npm

# Yarnをインストール (これがないとyarn buildとかできないよね alpineだと最初から入ってないのかも)
RUN npm install -g yarn --force

# 非rootユーザーを作成
RUN adduser -D appuser

# 推奨ディレクトリを作成し、非rootユーザーに所有権を付与
RUN mkdir -p /usr/src/app && chown -R appuser:appuser /usr/src/app

WORKDIR /usr/src/app

# 非rootユーザーに切り替え
USER appuser

# 必要なファイルをコピーし、適切な所有権を設定
COPY --chown=appuser:appuser ./app/package.json ./app/yarn.lock /usr/src/app/
RUN yarn install --frozen-lockfile

#マウント時の権限のエラー回避のためにtmpにマウントしておいたappをコンテナ内にコピー
COPY --chown=appuser:appuser ./app/ /usr/src/app/
RUN yarn build

# ステージ2: Nginxを使用してReactのビルド成果物を提供
FROM nginx:alpine

# ビルド成果物をNginxにコピー
COPY --from=build /usr/src/app/build /usr/share/nginx/html

# Nginxの設定をコピー
COPY ./nginx/default.conf /etc/nginx/conf.d/

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]