# Build stage
FROM oven/bun:1.2-debian AS build

WORKDIR /app

# Copy package metadata
COPY bun.lock package.json bunfig.toml ./
COPY packages/backend/package.json ./packages/backend/
COPY packages/core/package.json ./packages/core/
COPY packages/lib/package.json ./packages/lib/
COPY packages/runtime/package.json ./packages/runtime/
COPY packages/search/package.json ./packages/search/
COPY packages/ssr/package.json ./packages/ssr/

# Install deps
RUN bun install --production --verbose --frozen-lockfile

# Copy code
COPY . .

# 🚀 Build backend dist (script now exists!)
RUN cd packages/backend && bun run build

# Build standalone executable
RUN bun build --compile --minify --sourcemap ./packages/backend/src/bun.index.ts --outfile nordcraft-backend

# Runtime
FROM gcr.io/distroless/base-debian12 AS runner

ENV NODE_ENV=production

ARG BUILD_APP_PORT=3000
ENV APP_PORT=${BUILD_APP_PORT}
EXPOSE ${APP_PORT}

WORKDIR /app

COPY --from=build /app/nordcraft-backend .
COPY --from=build /app/packages/backend/dist/ ./dist/

ENTRYPOINT ["./nordcraft-backend"]
