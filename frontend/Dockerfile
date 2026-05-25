FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev", "--", "--host"]
#                            ↑
#                     חובה! בלי זה Vite לא חושף את הפורט החוצה מהקונטיינר