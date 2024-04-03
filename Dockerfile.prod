FROM node:20 AS base

RUN npm i -g pnpm

FROM base as dependencies
WORKDIR /usr/src/app
COPY package.json ./
RUN pnpm install

FROM base as build

WORKDIR /usr/src/app
COPY .  .
COPY --from=dependencies /usr/src/app/node_modules ./node_modules
RUN  pnpm build
RUN pnpm prune --prod

FROM node:20-alpine as deploy
WORKDIR  /usr/src/app
RUN npm i -g pnpm prisma
COPY --from=build /usr/src/app/dist ./dist
COPY --from=build /usr/src/app/node_modules ./node_modules
COPY --from=build /usr/src/app/package.json ./package.json
COPY --from=build /usr/src/app/prisma ./prisma
RUN pnpm prisma generate

ENV DATABASE_URL="file:./db.sqllite"
ENV API_BASE_URL="http://localhost:3333"

EXPOSE 3333
CMD [ "pnpm", "start" ]