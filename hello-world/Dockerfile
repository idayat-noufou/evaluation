FROM ubuntu
COPY . .
RUN yarn build
RUN yarn install
CMD node /app/dist/main.js