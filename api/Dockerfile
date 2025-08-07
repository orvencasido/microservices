FROM node:18-alpine

WORKDIR /app

COPY server.js .

RUN npm init -y && npm install express

EXPOSE 3000

CMD ["node", "server.js"]

#cat /etc/rancher/k3s/k3s.yaml