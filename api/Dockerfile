FROM node:20 AS base
RUN npm i -g pnpm

FROM base AS dependencies
WORKDIR /usr/src/app
COPY package.json pnpm-lock.yaml ./
RUN pnpm install

FROM base AS build
WORKDIR /usr/src/app
COPY . .
COPY --from=dependencies /usr/src/app/node_modules ./node_modules
RUN pnpm build
RUN pnpm prune --prod

FROM node:20-alpine3.19 as deploy
WORKDIR /usr/src/app
RUN npm i -g pnpm prisma
RUN apk add --no-cache bash
COPY --from=build /usr/src/app/dist ./dist
COPY --from=build /usr/src/app/node_modules ./node_modules
COPY --from=build /usr/src/app/package.json ./package.json
COPY --from=build /usr/src/app/prisma ./prisma
COPY --from=build /usr/src/app/wait-for-it.sh ./wait-for-it.sh
RUN ls /usr/src/app
RUN pnpm prisma generate
RUN chmod a+x ./wait-for-it.sh

CMD [ "./wait-for-it.sh", "postgres:5432", "--", "pnpm", "start" ]