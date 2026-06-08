# ------------------------
# Base
# ------------------------
ARG NODE_VERSION=24.13.1
FROM node:${NODE_VERSION}-slim AS base

WORKDIR /app

# ------------------------
# Prepare
# ------------------------
FROM base AS prepare

RUN corepack enable
COPY . .

RUN pnpm dlx turbo prune web --docker

# ------------------------
# Builder
# ------------------------
FROM base AS builder
RUN corepack enable

COPY --from=prepare /app/out/json/ .

RUN pnpm install --frozen-lockfile
COPY --from=prepare /app/out/full/ .
RUN pnpm turbo build --filter=web

# ------------------------
# Runner
# ------------------------
FROM nginx:alpine AS runner

COPY --from=builder /app/apps/web/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]