exercice 1 : 

FROM node:18

exercice 2 : 

WORKDIR /app

exercice 3 : 

COPY . .

exercice 4 : 

RUN npm install

exercice 5 : 

EXPOSE 3000

exercice 6 : 

CMD ["node", "app.js"]

exercice 7 : 

docker build -t mon-app .

exercice 8 :

docker build -t mon-app:1.0 .

exercice 9 : 

docker run -d -p 3000:3000 mon-app:1.0

exercice 10 : 

ENV NODE_ENV=production