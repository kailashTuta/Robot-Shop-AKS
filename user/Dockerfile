FROM node:24-bookworm

ENV NODE_ENV=production \
    INSTANA_AUTO_PROFILE=true

EXPOSE 8080

WORKDIR /opt/server

COPY --chown=node:node package.json ./

RUN npm install --omit=dev --no-audit --no-fund \
    && npm cache clean --force

COPY --chown=node:node server.js ./

USER node
CMD ["node", "server.js"]
