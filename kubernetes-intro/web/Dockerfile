FROM node:16.17.0-bullseye-slim
ARG UID=1001

WORKDIR /app

ADD package.json /app/package.json
RUN npm install

ADD app.js /app/app.js
ADD homework.html /app/homework.html
ADD index.html /app/index.html

EXPOSE 8000

CMD [ "npm", "start" ]
