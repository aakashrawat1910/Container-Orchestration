FROM node:22-alpine3.20
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3003
CMD ["node", "app.js"]