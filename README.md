# Deploying Vite React Apps with GitHub

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Setting Up Your GitHub Repository](#setting-up-your-github-repository)
3. [Preparing Your React App for Deployment](#preparing-your-react-app-for-deployment)
4. [Configuring GitHub Actions for CI/CD](#configuring-github-actions-for-cicd)
5. [Deployment Options](#deployment-options)
   - [GitHub Pages](#github-pages)
   - [Vercel](#vercel)
   - [Netlify](#netlify)
   - [AWS Amplify](#aws-amplify)
6. [Best Practices](#best-practices)
7. [Troubleshooting](#troubleshooting)

## Prerequisites

Before you begin, make sure you have the following:

- A Vite React application ready for deployment
- Git installed on your local machine
- A GitHub account
- Node.js and npm installed
- Vite installed globally or as a project dependency

## Setting Up Your GitHub Repository

1. **Create a new repository on GitHub**
   - Go to [GitHub](https://github.com) and sign in
   - Click on the '+' icon in the top-right corner and select 'New repository'
   - Name your repository and add an optional description
   - Choose public or private visibility
   - Click 'Create repository'

2. **Initialize your local repository and push to GitHub**
   ```bash
   # Navigate to your React app directory
   cd your-react-app
   
   # Initialize Git repository (if not already done)
   git init
   
   # Add all files to staging
   git add .
   
   # Commit the files
   git commit -m "Initial commit"
   
   # Add the remote repository
   git remote add origin https://github.com/your-username/your-repo-name.git
   
   # Push to GitHub
   git push -u origin main
   ```

## Preparing Your React App for Deployment

1. **Update vite.config.js for deployment**
   - Add the base property to specify the base URL for GitHub Pages
   ```javascript
   // vite.config.js
   import { defineConfig } from 'vite'
   import react from '@vitejs/plugin-react'

   export default defineConfig({
     plugins: [react()],
     base: '/your-repo-name/', // Add this line for GitHub Pages deployment
   })
   ```

2. **Install required deployment packages**
   ```bash
   # For GitHub Pages
   npm install --save-dev gh-pages
   
   # Add deployment scripts to package.json
   # Add these to the "scripts" section:
   "predeploy": "npm run build",
   "deploy": "gh-pages -d dist"
   ```

3. **Build your application**
   ```bash
   npm run build
   ```
   This will create a `dist` directory with your built application.

## Configuring GitHub Actions for CI/CD

1. **Create a GitHub Actions workflow file**
   - Create a directory `.github/workflows` in your project root
   - Create a file named `deploy.yml` inside this directory

2. **Add the workflow configuration**
   ```yaml
   name: Deploy React App
   
   on:
     push:
       branches: [ main ]
     pull_request:
       branches: [ main ]
   
   jobs:
     build-and-deploy:
       runs-on: ubuntu-latest
       
       steps:
         - name: Checkout code
           uses: actions/checkout@v3
           
         - name: Set up Node.js
           uses: actions/setup-node@v3
           with:
             node-version: '18'
             cache: 'npm'
             
         - name: Install dependencies
           run: npm ci
           
         - name: Run tests
           run: npm test -- --passWithNoTests
           
         - name: Build
           run: npm run build
           
         - name: Deploy to GitHub Pages
           uses: JamesIves/github-pages-deploy-action@v4
           with:
             folder: dist
             branch: gh-pages
   ```

3. **Commit and push the workflow file**
   ```bash
   git add .github/workflows/deploy.yml
   git commit -m "Add GitHub Actions workflow"
   git push
   ```

## Deployment Options

### GitHub Pages

1. **Manual deployment**
   ```bash
   npm run deploy
   ```

2. **Configure GitHub repository settings**
   - Go to your repository on GitHub
   - Navigate to Settings > Pages
   - Under 'Source', select the 'gh-pages' branch
   - Click 'Save'
   - Your site will be published at `https://your-username.github.io/your-repo-name`

### Vercel

1. **Sign up for Vercel**
   - Go to [Vercel](https://vercel.com) and sign up with your GitHub account

2. **Import your GitHub repository**
   - Click 'Import Project'
   - Select 'Import Git Repository'
   - Choose your React app repository
   - Configure project settings (framework preset should be automatically detected)
   - Click 'Deploy'

3. **Configure automatic deployments**
   - Vercel automatically sets up webhooks in your GitHub repository
   - Every push to your main branch will trigger a new deployment

### Netlify

1. **Sign up for Netlify**
   - Go to [Netlify](https://netlify.com) and sign up with your GitHub account

2. **Import your GitHub repository**
   - Click 'New site from Git'
   - Select GitHub as your Git provider
   - Authorize Netlify to access your repositories
   - Select your React app repository
   - Configure build settings:
     - Build command: `npm run build`
     - Publish directory: `dist`
   - Click 'Deploy site'

3. **Configure custom domain (optional)**
   - Go to Site settings > Domain management
   - Click 'Add custom domain'
   - Follow the instructions to set up your domain

### AWS Amplify

1. **Sign up for AWS**
   - Create an AWS account if you don't have one

2. **Set up AWS Amplify**
   - Go to the AWS Management Console
   - Search for 'Amplify' and select it
   - Click 'New app' > 'Host web app'
   - Choose GitHub as your repository provider
   - Authorize AWS Amplify to access your repositories
   - Select your React app repository
   - Configure build settings:
     - Build command: `npm run build`
     - Output directory: `dist`
   - Click 'Save and deploy'

## Best Practices

1. **Use environment variables for sensitive information**
   - Create a `.env` file for local development
   - Add environment variables in your deployment platform settings
   - Never commit sensitive information to your repository

2. **Implement proper error handling and logging**
   - Use error boundaries in your React components
   - Set up monitoring tools like Sentry

3. **Optimize your Vite build**
   - Vite provides built-in optimizations, but you can further enhance performance:
   - Use dynamic imports for code splitting: `const Component = () => import('./Component.jsx')`
   - Implement React.lazy and Suspense for component lazy loading
   - Configure build optimizations in vite.config.js:
     ```javascript
     // vite.config.js
     export default defineConfig({
       plugins: [react()],
       build: {
         minify: 'terser',
         rollupOptions: {
           output: {
             manualChunks: {/* custom chunk configuration */}
           }
         }
       }
     })
     ```
   - Use Vite's asset handling for optimizing images and other static assets

4. **Set up proper branching strategy**
   - Use feature branches for development
   - Implement pull request reviews
   - Protect your main branch with branch protection rules

5. **Implement testing**
   - Write unit tests for components
   - Set up end-to-end testing
   - Configure your CI/CD pipeline to run tests before deployment

## Troubleshooting

1. **Build failures**
   - Check your build logs for errors
   - Ensure all dependencies are properly installed
   - Verify that your code doesn't have any syntax errors

2. **Deployment issues**
   - Check if your repository has the correct permissions
   - Verify that your build output directory is correctly configured
   - Check if your GitHub Actions workflow is properly set up

3. **Routing problems**
   - For single-page applications, ensure your server is configured to serve `index.html` for all routes
   - Add a `404.html` page that redirects to your main page

4. **Environment variable issues**
   - Ensure environment variables are properly set in your deployment platform
   - For Vite, client-side environment variables must be prefixed with `VITE_` (e.g., `VITE_API_URL`)
   - Create a `.env` file in your project root for local development
   - For production, set these variables in your deployment platform settings

---

By following this guide, you should be able to successfully deploy your React application using GitHub and set up a continuous integration and deployment pipeline. Happy coding!