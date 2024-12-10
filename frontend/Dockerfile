FROM node:22.2.0-alpine3.20 AS builder
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
RUN corepack enable
WORKDIR /app
COPY pnpm-lock.yaml package.json /app/
RUN --mount=type=cache,id=pnpm,target=/pnpm/store pnpm install --frozen-lockfile


FROM builder AS dev
EXPOSE 5173
CMD [ "pnpm", "run", "dev" ]
