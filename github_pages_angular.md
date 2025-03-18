Deploying an Angular application to GitHub Pages involves the following steps:

---

### **Step 1: Install Angular CLI (if not installed)**
Make sure you have the Angular CLI installed globally:

```sh
npm install -g @angular/cli
```

Check the version:

```sh
ng version
```

---

### **Step 2: Create or Navigate to Your Angular Project**
If you don’t have an Angular project, create one:

```sh
ng new my-angular-app
cd my-angular-app
```

If you already have a project, just navigate to its root directory.

---

### **Step 3: Build the Project for Production**
Run the following command to generate production-ready files:

```sh
ng build --output-path docs --base-href /your-repo-name/
```

- `--output-path docs`: Generates files inside the `docs` folder (GitHub Pages can serve from `docs`).
- `--base-href /your-repo-name/`: Replace `your-repo-name` with the actual repository name.

---

### **Step 4: Configure GitHub Pages**
1. Commit and push the `docs` folder to GitHub.
2. Go to your repository on GitHub.
3. Navigate to **Settings > Pages**.
4. Under **Branch**, select `main` (or the branch where your project is) and set the folder to `/docs`.
5. Click **Save**.

---

### **Step 5: Automate Deployment (Optional)**
To automate deployment, install the `angular-cli-ghpages` package:

```sh
npm install -g angular-cli-ghpages
```

Then deploy using:

```sh
ng deploy --base-href=/your-repo-name/
```

If prompted, authorize GitHub Pages for the first deployment.

---

### **Step 6: Access Your Deployed App**
Your Angular app will be available at:

```
https://your-github-username.github.io/your-repo-name/
```

---

### **Troubleshooting**
- If assets are not loading properly, ensure you set `base-href` correctly.
- If you get a **404 error**, make sure your repository is public or check the GitHub Pages settings.

Would you like help setting up a GitHub Actions workflow for automatic deployment? 🚀
