![[Pasted image 20241224203940.png]]

### Understanding Github Actions
```
name : some-name

# Defines when the workflow will run 
on: 
	push: 
		branches: [ main ] 
	pull_request: 
		branches: [ main ]
		
	schedules and manual triggers

# Environment variables used across jobs 
env: 
	NODE_VERSION: '18.x'

jobs:
	test:
		runs-on: ubuntu-latest
		steps:
			- name: Checkout code 
			  uses: actions/checkout@v3 # Action to check out the code from the repository
		

```

### Github Pages Deploy
Github pages doesn't support SSR, so Next.js isn't available rather we can use static export but still not good. If you don't understand it, then just use Vite rather than next.js For this go to Vite docs deployment to github pages. From meta point of view, you need to add `homepage` and some script to `package.json` and need to add `base` to `vite.config.js` and type where it will be displayed. Our app will be displayed there. as mostly, our frontend is in `client` folder. Don't worry our new code will be in new branch `gh-pages` when we type `npm run deploy`.

### Github Actions with CI/CD Pipelines
1. Frontend-deploy.yml 
```
name: Deploy Frontend to GitHub Pages

# Trigger the workflow on pushes to the 'main' branch
on:
  push:
    branches:
      - main  # You can also add other branches if necessary

  # Optionally, you can also trigger it manually or on other events
  workflow_dispatch: # This allows you to trigger the workflow manually from GitHub UI

env: 
	CI: false // this is for warning like eslint. making it false, it ignore the warning. in production don't write this. if it is set to false, then deployment will fail

jobs: // kk chalxa
  deploy:
    runs-on: ubuntu-latest  # This specifies the runner environment. You can use windows-latest or macos-latest if needed

    # Job steps for building and deploying the frontend
    steps:
      # Step 1: Checkout the repository (this is necessary to get the code)
      - name: Checkout Repository
        uses: actions/checkout@v3 // it is spotless for java. it is just command in the in built package.
        with:
          ref: main  # You can specify the branch you want to deploy from

      # Step 2: Set up Node.js environment (use the right version for your project)
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'  # Change this to the appropriate Node.js version for your project
          cache: 'npm'  # Optionally, you can enable npm cache to speed up installs

      # Step 3: Install dependencies (install frontend dependencies from /client)
      - name: Install Dependencies
        run: |
          cd client
          npm install
          
      # Step 4: Run a linter (Optional but good practice to catch errors early)
      - name: Lint Frontend Code
        run: |
          cd client
          npm run lint  # You can replace with your linter command if you have one

      # Step 5: Build the frontend project
      - name: Build Frontend
        run: |
          cd client
          npm run build  # This command should generate your production-ready files in ./client/dist
          env: 
	          // wirte the secrets from github secrets.

      # Step 6: Upload build artifacts (optional, to keep track of build artifacts)
      - name: Upload Build Artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-artifacts
          path: client/dist  # You can change the path if your build output is different

      # Step 7: Deploy to GitHub Pages
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.GITHUB_TOKEN }}  # Use GitHub's automatic token for authentication
          publish_dir: ./client/dist  # Directory containing the built frontend
          cname: www.yourdomain.com  # Optional: if you have a custom domain, specify the CNAME file here
          commit_message: 'Deploy frontend to GitHub Pages'  # Custom commit message for the GitHub Pages update

      # Step 8: Clear the cache (optional, but good for cleaning up after deployment)
      - name: Clear Cache
        uses: actions/cache@v3
        with:
          path: ~/.npm
          key: ${{ runner.os }}-build-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-build-

```
or it can also be done in this way.
