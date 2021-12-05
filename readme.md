# Instructions 
- Create folder, give it a name
- Run generic command to create new next.js project
- Need to update the next.config.js for hotreloading, looks something like this: 
    ```
        module.exports = {
        webpackDevMiddleware: config => {
            config.watchOptions = {
            poll: 1000,
            aggregateTimeout: 300,
            }
            return config
        },
        reactStrictMode: true,
        }
    ```
- Inside that folder, create a docker file that looks like this: 
    ```
        FROM node:alpine
        RUN mkdir -p /app
        WORKDIR /app
        COPY package*.json /app
        RUN npm install
        COPY . /app
        EXPOSE 3000
        CMD ["npm", "run", "dev"]
    ```
- Create a docker-compose file that looks like this in the main directory:
    ```
        version: '3'
        services:
        frontend:
            container_name: frontendtest
            ports:
            - '3000:3000'
            build:
            context: frontend
            dockerfile: Dockerfile
            volumes:
            - ./frontend:/app
            - /app/node_modules
            - /app/.next
    ```

    - Then can start with `docker-compose up --build` / `--down` and such 
    - Try and install additional npm modules (e.g., material, firebase) to make sure it works properly with new modules and the hotreload
    - Appears the steps are: 
        - remove everything: prune -> 
        - remove package-lock.json and node_modules 
        - then rebuild, `npm install` 
        - then do `docker-compose build --no-cache` 
        - then can probably do `docker-compose up` 