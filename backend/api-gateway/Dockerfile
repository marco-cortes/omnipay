FROM node:20-alpine
WORKDIR /app
RUN chown -R node:node /app
USER node

# Creamos un servidor HTTP básico para que el contenedor se mantenga vivo
RUN echo "const http = require('http'); \
const server = http.createServer((req, res) => { \
  res.writeHead(200, {'Content-Type': 'application/json'}); \
  res.end(JSON.stringify({ status: 'OK', service: 'Omnipay API Gateway' })); \
}); \
server.listen(3000, () => console.log('Gateway listening on port 3000'));" > main.js

EXPOSE 3000
CMD ["node", "main.js"]