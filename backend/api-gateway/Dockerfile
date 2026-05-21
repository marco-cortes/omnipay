FROM node:20-alpine

# 1. Definimos el directorio de trabajo
WORKDIR /app

# 2. Le damos la propiedad de la carpeta al usuario seguro 'node'
RUN chown -R node:node /app

# 3. Cambiamos al usuario sin privilegios antes de ejecutar la aplicación
USER node

# Archivo temporal para simular la aplicación mientras escribimos el código real
RUN echo "console.log('Omnipay API Gateway Bootstrapped!');" > main.js

CMD ["node", "main.js"]