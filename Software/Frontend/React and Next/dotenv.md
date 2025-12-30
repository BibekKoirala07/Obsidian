For vite use, the name that starts with `VITE_NODE_ENV`. yes even for `NODE_ENV`. vite only recognize the .env which starts with `VITE`.
Starting for .env 
```
VITE_NODE_ENV=production
VITE_PROD_BACKEND_URL=https://my-portfolio-9uct.onrender.com
VITE_DEV_BACKEND_URL=http://localhost:3000
VITE_PROD_FRONTEND_URL=https://my-portfolio-fronted.onrender.com
VITE_DEV_FRONTEND_URL=http://localhost:5173 // for vite, it should be starting with VITE
```

For advanced uses, rather use two dotenv for `production` and `development`. Vite takes care of the automatically.
1. `.env.production`
```
VITE_SOME_API_URL=http://localhost:5000/api
VITE_FRONTEND_URL=http://localhost:3000
VITE_BACKEND_URL=http://localhost:5000
```
2. `.env.development`
```
VITE_SOME_API_URL=http://localhost:5000/api
VITE_FRONTEND_URL=http://localhost:3000
VITE_BACKEND_URL=http://localhost:5000
```
So, Vite automatically will takes of that. When we type `npm run dev`, Vite will use `.env.development` and when we use `npm run build`, which we do while deploying, Vite will use `.env.production`.

We can get dotenv variable like this
```
const frontendURL = import.meta.env.VITE_FRONTEND_URL;
```
