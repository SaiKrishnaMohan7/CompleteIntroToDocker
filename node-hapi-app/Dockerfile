FROM node:12-stretch

USER node

# WORKDIR creates this dir as the root user and not USER node, so we create the folder as the USER node
RUN mkdir /home/node/code

WORKDIR /home/node/code

# Copy everything from host to container
COPY --chown=node:node . .

# npm ci - install from package-lock.json
RUN npm ci

CMD ["node", "index.js"]