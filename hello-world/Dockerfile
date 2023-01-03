FROM node:alpine 
WORKDIR /app
COPY . .
RUN yarn install
RUN yarn build
CMD node /app/dist/main.js