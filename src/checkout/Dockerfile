# FROM node:20-alpine AS build
# WORKDIR /usr/src/app
# COPY --chown=node:node package*.json ./
# COPY --chown=node:node . .
# RUN yarn install --frozen-lockfile
# RUN yarn build
# USER node


# FROM public.ecr.aws/amazonlinux/amazonlinux:2023

# # We tell DNF not to install Recommends and Suggests packages, which are
# # weak dependencies in DNF terminology, thus keeping our installed set of
# # packages as minimal as possible.
# RUN dnf --setopt=install_weak_deps=False install -q -y \
#     nodejs20 \
#     shadow-utils \
#     && \
#     dnf clean all

# RUN alternatives --install /usr/bin/node node /usr/bin/node-20 90

# ENV APPUSER=appuser
# ENV APPUID=1000
# ENV APPGID=1000

# RUN useradd \
#     --home "/app" \
#     --create-home \
#     --user-group \
#     --uid "$APPUID" \
#     "$APPUSER"

# WORKDIR /app
# USER appuser

# COPY --chown=node:node --from=build /usr/src/app/node_modules ./node_modules
# COPY --chown=node:node --from=build /usr/src/app/dist ./dist



# ---------- Stage 1 : Build ----------
FROM node:22-alpine AS builder

WORKDIR /workspace

# Copy entire repository (monorepo)
COPY . .

# Go to checkout service
WORKDIR /workspace/src/checkout

# Install dependencies
RUN npm ci

# Build NestJS
RUN npm run build

# ---------- Stage 2 : Runtime ----------
FROM node:22-alpine

WORKDIR /app

ENV NODE_ENV=production

COPY --from=builder /workspace/src/checkout/package*.json ./

RUN npm ci --omit=dev

COPY --from=builder /workspace/src/checkout/dist ./dist

EXPOSE 8080

CMD ["node", "dist/main"]

# ENTRYPOINT [ "node", "dist/main.js" ]
