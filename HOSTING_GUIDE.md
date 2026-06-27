# Free Hosting Guide for Placement Cell Project

This guide shows you how to take this Flask project from your computer to a live free website step by step.

You do not need to know GitHub or deployment before starting. This guide begins from zero and ends with a working public link.

## What We Will Use

1. GitHub for storing the code online.
2. MongoDB Atlas free tier for the database.
3. Render free web service for the live website.
4. GitHub Desktop so you can upload code without using command line Git commands.

## Important Project Files

These files already exist in the project and are important for hosting:

- `app.py` - the Flask app
- `requirements-web.txt` - the smaller package list for hosting
- `Procfile` - tells Render how to start the app
- `create_admin.py` - creates the first admin account in the hosted database

## Before You Start

If you have never done this before, do these things first:

1. Make sure Python is installed on your computer.
   - Download it from `python.org`.
   - During installation, check `Add Python to PATH`.

2. Install GitHub Desktop.
   - Download it from `desktop.github.com`.
   - GitHub Desktop is the easiest way to upload this project to GitHub.

3. Create free accounts on:
   - GitHub
   - MongoDB Atlas
   - Render

4. Keep this project folder ready:
   - `C:\Users\vikra\OneDrive\Desktop\placement\3i-cell`

## Step 1: Understand The Hosting Flow

The project has 3 parts:

1. The code lives on GitHub.
2. The data lives in MongoDB Atlas.
3. The website runs on Render.

When everything is connected:

- You edit code on your computer.
- You push the code to GitHub.
- Render automatically redeploys the updated code.
- MongoDB Atlas stores users, students, jobs, files, and other app data.

## Step 2: Create Your GitHub Account

If you already have a GitHub account, skip this step.

1. Go to `github.com`.
2. Click `Sign up`.
3. Create your account with email, username, and password.
4. Verify your email.

Once this is done, GitHub can store your project online.

## Step 3: Install and Open GitHub Desktop

1. Download GitHub Desktop from `desktop.github.com`.
2. Install it.
3. Open GitHub Desktop.
4. Sign in with your GitHub account.

GitHub Desktop will let you upload the project without learning Git commands first.

## Step 4: Add This Project To GitHub Desktop

1. In GitHub Desktop, click `File`.
2. Click `Add Local Repository`.
3. Select this folder:
   - `C:\Users\vikra\OneDrive\Desktop\placement\3i-cell`
4. If GitHub Desktop says it is not a repository yet, choose the option to create one there.

After this, GitHub Desktop will show the files that changed.

## Step 5: Check What You Are About To Upload

Before publishing, look at the list of changed files.

Keep these important files:

- `app.py`
- `requirements-web.txt`
- `Procfile`
- `utils/`
- `routes/`
- `templates/`
- `static/`

Do not worry if you see Python cache files like:

- `__pycache__`
- `.pyc` files

They are not important for hosting.

## Step 6: Commit The Project

1. In GitHub Desktop, type a commit message.
   - Example: `Initial project upload`
2. Click `Commit to main`.

A commit is just a saved snapshot of your project.

## Step 7: Publish The Repository

1. After committing, click `Publish repository`.
2. Keep the repository public or private based on what you want.
   - Public is simplest for beginners.
3. Click `Publish repository` again to finish.

Now your project code is on GitHub.

## Step 8: Create A Free MongoDB Atlas Cluster

This app uses MongoDB for users, student profiles, jobs, notifications, forms, and file storage.

1. Go to `mongodb.com/atlas`.
2. Sign in or create a free MongoDB account.
3. Create a new deployment.
4. Choose the free shared cluster, usually called `M0`.
5. Pick any cloud provider and region that Atlas offers for free.
6. Wait for the cluster to finish creating.

### Create A Database User

1. In Atlas, go to `Database Access`.
2. Create a new database user.
3. Choose a username and password.
4. Save both somewhere safe.

### Allow Access To The Database

1. Go to `Network Access`.
2. Add an IP access list entry.
3. For easiest setup, use `0.0.0.0/0`.

This allows the Render server to connect.

Note: this is simple and beginner-friendly, but later you can make it stricter if you want better security.

### Get The Connection String

1. Go to your cluster.
2. Click `Connect`.
3. Choose the option for connecting with an application.
4. Copy the connection string.

It will look similar to this:

```text
mongodb+srv://USERNAME:PASSWORD@clustername.mongodb.net/?retryWrites=true&w=majority
```

Replace:

- `USERNAME` with your database username
- `PASSWORD` with your database password

If your password has special characters, you may need to URL-encode it.

## Step 9: Create The Render Web Service

Render will host the live Flask app.

1. Go to `render.com`.
2. Sign in or create a free account.
3. Click `New`.
4. Choose `Web Service`.
5. Connect your GitHub account if Render asks.
6. Select the repository you published.

### Render Build Settings

Set these values:

- `Name`: anything you want
- `Branch`: `main`
- `Root Directory`: leave blank unless your project is inside a subfolder
- `Runtime`: Python

### Build Command

Use this command:

```bash
pip install -r requirements-web.txt
```

### Start Command

Use this command:

```bash
gunicorn app:app --bind 0.0.0.0:$PORT
```

This is important because Render gives the app a port number at runtime.

### Plan

Choose the free plan.

## Step 10: Add Environment Variables In Render

Environment variables are settings that keep secrets out of your code.

In Render, open your web service and add these variables:

### Required Variables

1. `MONGO_URI`
   - Paste your MongoDB Atlas connection string.

2. `SECRET_KEY`
   - Use a long random string.
   - Example: a 32-byte random hex string.

3. `SESSION_COOKIE_SECURE`
   - Set this to `true`.

4. `FORCE_HTTPS`
   - Set this to `true`.

### Optional Local Testing Variable

If you ever test locally and want HTTPS settings off, you can leave these false on your computer.

## Step 11: Deploy The App

1. Click `Create Web Service` or `Deploy`.
2. Wait for Render to build the app.
3. Watch the logs.
4. If the build finishes successfully, Render will give you a live URL.

The URL will usually look like:

```text
https://your-service-name.onrender.com
```

## Step 12: Create The First Admin Account

After the database is live, you need the first admin user.

This project includes `create_admin.py` for that.

### Run It From Your Computer

1. Open PowerShell in this project folder.
2. Set the same MongoDB URI used by Render:

```powershell
$env:MONGO_URI="your Atlas connection string"
```

3. Run the script:

```powershell
python create_admin.py
```

That script creates the super admin account in the hosted database.

### Default Admin Login

The script currently uses these example credentials:

- Email: `Placementcell@cbscollege`
- Password: `Placement123`

Important: change these later if you plan to keep the site public.

## Step 13: Test The Website

After deployment:

1. Open the Render URL.
2. Try logging in as admin.
3. Try registering a student.
4. Try uploading a resume.
5. Check that the data appears in MongoDB Atlas.

Because this app stores files in GridFS now, uploaded resumes should survive Render restarts.

## Step 14: How To Update The Site Later

When you change the code:

1. Open the project in your editor.
2. Save your changes.
3. Open GitHub Desktop.
4. Commit the changes.
5. Push them to GitHub.

Render will redeploy automatically from GitHub.

## Step 15: Local Development Option

If you want to test on your own computer before deploying:

1. Install dependencies:

```powershell
pip install -r requirements-web.txt
```

2. Set `MONGO_URI` to either:
   - a local MongoDB server, or
   - your Atlas connection string

3. Run the app:

```powershell
python app.py
```

4. Open the local URL shown in the terminal, usually:

```text
http://127.0.0.1:5000
```

## Common Problems And Fixes

### 1. Render build fails

Possible reasons:

- You used `requirements.txt` instead of `requirements-web.txt`
- A package is missing
- The `Procfile` start command is wrong

Fix:

- Use `requirements-web.txt`
- Use `gunicorn app:app --bind 0.0.0.0:$PORT`

### 2. Website opens but database does not work

Possible reasons:

- Wrong `MONGO_URI`
- Wrong username or password
- MongoDB Atlas IP access not allowed

Fix:

- Re-copy the Atlas connection string
- Check the Atlas IP access list
- Make sure `MONGO_URI` is added in Render

### 3. Login does not work

Possible reasons:

- You forgot to run `create_admin.py`
- The admin record was not created in Atlas

Fix:

- Run `python create_admin.py` with the Atlas URI in `MONGO_URI`

### 4. Uploaded files disappear

Possible reasons:

- Local disk storage was used instead of database file storage

Fix:

- This repo now stores resume updates in GridFS, which is more suitable for free hosting

### 5. The site is slow after some time

This is normal on free hosting.

Free Render services can sleep when inactive and take a little time to wake up again.

### 6. You changed code but nothing updated online

Possible reasons:

- You forgot to commit
- You forgot to push to GitHub
- Render is still building

Fix:

- Commit in GitHub Desktop
- Push to GitHub
- Wait for Render to redeploy

## Very Short Version

If you want the fastest summary:

1. Create GitHub account.
2. Install GitHub Desktop.
3. Publish this project to GitHub.
4. Create MongoDB Atlas free cluster.
5. Copy the Atlas connection string.
6. Create Render web service from GitHub.
7. Use `requirements-web.txt` and `Procfile`.
8. Add `MONGO_URI`, `SECRET_KEY`, `SESSION_COOKIE_SECURE=true`, `FORCE_HTTPS=true`.
9. Deploy.
10. Run `python create_admin.py` with the Atlas URI.
11. Open the Render link and test login.

## Final Note

If you get stuck at any step, do not worry. The usual problems are just:

- wrong database string
- missing environment variable
- wrong start command
- not pushing the code to GitHub

This guide is enough to get the project online for free.
