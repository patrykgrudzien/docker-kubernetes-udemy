FROM node:alpine
WORKDIR "/app"
COPY ./package.json ./
RUN npm install
COPY . .
# changed (npm run dev) to -> (npm run start)
CMD ["npm", "run", "start"]
