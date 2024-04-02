FROM node:20 AS base
RUN npm i -g pnpm

FROM base AS dependencies
WORKDIR /usr/src/app
COPY package.json pnpm-lock.yaml ./
RUN pnpm install

