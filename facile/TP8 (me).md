Dans le fichier .dockerignore 

node_modules
.env

dans le fichier dockerfile 1 

FROM node:18
COPY package.json package-lock.json ./
COPY . .
RUN npm install 
COPY . .
CMD ["node", "app.js"]

Dans le fichier dockerfile 2 

FROM node:18 as builder
WORKDIR /app
COPY package.json package-lock.json ./
COPY . .
RUN npm install 