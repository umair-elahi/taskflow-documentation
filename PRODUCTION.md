# Aetasaal 2.0 - Production Deployment Guide  

## 1. Update API Base URL  
Modify the API base URL in `taskflow-frontend\src\environments\environment.prod.ts` file:

Set the `apiBase` to:  

```ts
export const environment = {
  production: true,
  apiBase: 'https://aetasaal.duckdns.org/api/',
  apiImage: 'https://aetasaal.duckdns.org/images/',
  downloadimage: 'https://aetasaal.duckdns.org/upload/'
};
```

## 2. Build Frontend  
Run the following command in the `taskflow-frontend` directory:  

```bash
cd taskflow-frontend
ng build --prod
```

## 3. Move Static Files to Backend  
Move the generated frontend build to the backend's `web` directory:  

```bash
mv taskflow-frontend/dist/aetasaal-web/* taskflow-backend/web/
```

## 4. Modify Backend Configuration  
In the `taskflow-backend/src/server.ts` file:  
- **Uncomment the following code:**

```javascript
// import * as https from 'https';
// import * as fs from 'fs';
// import * as Koa from 'koa';
// import * as koaBody from 'koa-body';
// import * as cors from 'koa2-cors';
// import * as dotenv from 'dotenv';
// import * as bunyanLogger from 'koa-bunyan-logger';
// import config from './config/index';
// import pagination from './middleware/pagination';
// import errorMiddleware from './middleware/error';
// import response from './middleware/response';
// import routes from './route/index';
// import { Logger } from './utils/logger';

// import * as path from 'path';
// import * as serve from 'koa-static';
// import * as mount from 'koa-mount';


// dotenv.config();

// const whitelist: string | any[] = [
//   'http://localhost:4200',
//   'http://localhost:3000',
//   'http://localhost:8100',
//   'http://34.199.172.154:3000',
//   'http://34.199.172.154',
//   'http://localhost:3000/upload',
//   'https://aetasaal.duckdns.org',
//   'https://aetasaal.duckdns.org:3000',
//   'http://localhost:3000/api'
// ];
// function checkOriginAgainstWhitelist(ctx: Koa.Context) {
//   const requestOrigin = ctx.request.headers.origin || ctx.request.origin;
//   if (!whitelist.includes(requestOrigin)) {
//     return ctx.throw(403, `${requestOrigin} is not a valid origin`);
//   }
//   return requestOrigin;
// }

// //@ts-ignore
// export async function startServer(log: Bunyan) {
//   const app = new Koa();

//   try {
//     const privateKey = fs.readFileSync('/etc/letsencrypt/live/aetasaal.duckdns.org/privkey.pem', 'utf8');
//     const certificate = fs.readFileSync('/etc/letsencrypt/live/aetasaal.duckdns.org/cert.pem', 'utf8');
//     const ca = fs.readFileSync('/etc/letsencrypt/live/aetasaal.duckdns.org/chain.pem', 'utf8');

//     const httpsOptions = { key: privateKey, cert: certificate, ca: ca };

//     const server = https.createServer(httpsOptions, app.callback());

//     //@ts-ignore
//     app.use(cors({
//       //@ts-ignore
//       origin: (ctx: Koa.Context) => {
//         // Explicitly allow requests from the specified origin with HTTPS
//         return 'https://aetasaal.duckdns.org';
//       },
//       credentials: true,
//       allowHeaders: ['Origin', 'X-Requested-With', 'Content-Type', 'Accept', 'Authorization', 'appversion', 'platform'],
//     }));

//     //@ts-ignore
//     app.use(bunyanLogger(log));
//     app.use(async (ctx: Koa.Context, next: any) => {
//       ctx.set('Cache-Control', 'no-cache, no-store, must-revalidate');
//       await next();
//     });

//     //@ts-ignore
//     app.use(koaBody({ jsonLimit: '10mb', formLimit: '50mb', multipart: true, json: true }));
//     app.use(pagination);

//     const frontendDir = path.join(__dirname, '..', '..', 'taskflow-backend', 'web');
//     // console.log("frontend", frontendDir);
//     // @ts-ignore
//     app.use(mount('/', serve(frontendDir)));
//     app.use(errorMiddleware());
//     app.use(routes());
//     app.use(response());


//     // Serve the 'upload' directory as static
//     const uploadDir = path.join(__dirname, '..', '..', 'taskflow-backend', 'upload');
//     // console.log("Serving upload folder from:", uploadDir);

//     // Cast the middleware to Koa's Middleware type
//     app.use(mount('/upload', serve(uploadDir) as unknown as Koa.Middleware));


//     return new Promise<void>((resolve) => {
//       const p = process.env.PORT || config.server.port;
//       //@ts-ignore
//       server.listen(443, '0.0.0.0', () => {
// //@ts-ignore
//         log.info('server started on port %d with env=%s', p, config.env);
//         resolve();
//       });

//       // server.on('error', err => {
//       //   reject(err);
//       // });
//     });
//   } catch (err) {
//     throw new Error(`Error reading SSL certificate and private key files: ${err.message}`);
//   }
// }
```
- **Comment out all other existing code**.  

## 5. Commit and Push Changes  
Commit and push changes to the respective repositories:  

```bash
cd taskflow-frontend
git add .
git commit -m "Updated frontend build"
git push origin main

cd taskflow-backend
git add .
git commit -m "Updated backend with frontend build"
git push origin main
```

---

# Deploy on AWS EC2  

## 6. Launch EC2 Instance  
- Choose an **Ubuntu** instance.  
- Configure security group:  
  - Open **ports 80 and 443** for web traffic.  
  - Open **port 22** for SSH.  

## 7. Install NVM and Node.js 12  
Connect instance locally through `SSH`:  

```bash
ssh -i your-secrect-key ubuntu@<EC2_PUBLIC_IP>
```

Install **NVM (Node Version Manager)**.

Verify installation:  

```bash
nvm --version
```

Install **Node.js 12** and set it as the default version:  

```bash
nvm install 12
nvm use 12
```

## 8. Install PostgreSQL  
Install PostgreSQL:  

```bash
sudo apt update
sudo apt install -y postgresql postgresql-contrib
```

## 9. Pull Backend Repository  
Clone the backend repository:  

```bash
git clone <backend-repository-url>
cd taskflow-backend
```

Install dependencies:  

```bash
npm install
```

## 10. Setup Database  
Create and migrate the database:  

```bash
npm run createdb
npm run migrate
npm run seed
```

## 11. Install PM2  
Install PM2 to manage the Node.js application:  

```bash
npm install -g pm2
```

## 12. Configure SSL  
Get SSL certificates using Let's Encrypt:  

```bash
sudo apt install certbot
sudo certbot certonly --standalone -d aetasaal.duckdns.org
```

## 13. Run the Application  
First, test with `nodemon`:  

```bash
nodemon server.js
```

If no errors, start with PM2:  

```bash
pm2 start server.js
```

### ✅ Application is now running in production! 🚀  
