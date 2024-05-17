Docker file explained below:

This is a Dockerfile for a Node.js application using the Next.js framework. It uses a multi-stage build process to create an efficient production Docker image. Here's a breakdown of what each part does:

1. `FROM node:18-alpine AS base`: This line is setting up the base image for subsequent stages. It's using Node.js version 18 on an Alpine Linux base image.

2. `FROM base AS deps`: This line starts a new build stage named `deps` (short for dependencies). It inherits from the `base` stage.

3. `RUN apk add --no-cache libc6-compat`: This installs the `libc6-compat` library, which might be needed for some Node.js applications.

4. `WORKDIR /app`: This sets the working directory for any subsequent instructions.

5. `COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./`: This copies the package.json and any lock files into the Docker image.

6. The `RUN` command that follows checks for the existence of a lock file and installs dependencies using the appropriate package manager.

7. `FROM base AS builder`: This starts a new build stage named `builder`. It also inherits from the `base` stage.

8. `COPY --from=deps /app/node_modules ./node_modules`: This copies the `node_modules` directory from the `deps` stage to the current `builder` stage.

9. `RUN yarn build`: This runs the `build` script defined in package.json, which typically compiles the application.

10. `FROM base AS runner`: This starts a new build stage named `runner`, which will be the final stage used to run the application.

11. `ENV NODE_ENV production`: This sets the `NODE_ENV` environment variable to `production`.

12. `RUN addgroup --system --gid 1001 nodejs` and `RUN adduser --system --uid 1001 nextjs`: These commands create a new system group and user for running the application.

13. `COPY --from=builder /app/public ./public`: This copies the `public` directory from the `builder` stage to the current `runner` stage.

14. `RUN mkdir .next` and `RUN chown nextjs:nodejs .next`: These commands create the `.next` directory and change its ownership to the `nextjs` user and `nodejs` group.

15. `COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./` and `COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static`: These commands copy the `.next` directory from the `builder` stage to the `runner` stage, changing the ownership to the `nextjs` user and `nodejs` group.

16. `USER nextjs`: This changes the user for subsequent commands to `nextjs`.

17. `EXPOSE 3000`: This informs Docker that the container listens on port 3000.

18. `ENV PORT 3000` and `ENV HOSTNAME "0.0.0.0"`: These set environment variables for the port and hostname.

19. `CMD ["node", "server.js"]`: This is the command that will be run when the Docker container is started. It starts the Node.js application.



## Docker Compose for local development

- setup EC2 instance with SG enabled and install docker --> https://docs.docker.com/engine/install/ubuntu/

- setup additional env variables, if necessary in the below file

```bash
docker-compose.yml
```

- After the necessary configurations run below command :

```bash
docker-compose up -d
```
