## Instructions for using this GitHub Action

### Step 1: Change Branch Name

Change branch name in .github → workflows → create-android.yml

********************************************Default is Master********************************************

```yaml
on:
  push:
    branches:
			// TODO: CHANGE THE BRANCH ON WHICH YOU WANT TO RUN ACTION
      - master
```

### Step 2: Change the default directory if the Frontend is in subfolder of the repository

If your client is not in subfolder just comment the three lines.

```yaml
jobs:
  build:
    name: Build APK
    runs-on: ubuntu-latest
    # TODO: add the default directory - COMMENT IF IT'S NOT IN SUBFOLDER
    defaults:
       run:
         working-directory: ./frontend
```

### Step 3: Change the BUILD command for the frontend

Default is ****************************npm run build,**************************** if the framework you are using has a different build command replace it in line 38 of the file.

### Step 4: Create a resource folder in root of the directory for Splash Screen Generation

It will use ionic default icon if you don’t add the resources folder with app icon and splash screen

```yaml
resources/
├── icon-only.png
├── icon-foreground.png
├── icon-background.png
├── splash.png
└── splash-dark.png
```

- Icon files should be at least `1024px` x `1024px`.
- Splash screen files should be at least `2732px` x `2732px`.
- The format can be `jpg` or `png`.
- You can just add **icon.png** and **splash.png** and it will still work.

### Step 5: Generate Keystore Secrets for production build

You probably already have a key store to sign your app releases, but in case you don’t here’s how to create one:

1) Navigate to the folder where you want to store key store

2) Run the below command

```bash
keytool -genkey -v -keystore ./release.jks -keyalg RSA -keysize 2048 -validity 10000 -alias release
```

Follow the prompts until it asks you if the data is correct, enter *yes* to save your key. If you are generating keystore in the repository Don't forget to also add it to `.gitinore` so you don't accidentally commit it to your repo, the keystore should be kept secret.

3) But to use it in action we need to convert this generated keystore in base64 file

For linux users there’s a handy CLI utility called `base64` , which should be preinstalled on most Linux distros. Using it we can convert our binary file into a string representation

```bash
base64 android/release.jks > android/release.jks.base64
```

For windows users 

1. Open the Command Prompt by pressing the Windows key + R, typing "cmd," and pressing Enter.
2. Navigate to the directory containing the binary file using the **`cd`** command. For example, if the file is located in the "C:\MyFiles" directory, you would enter: **`cd C:\MyFiles`**
3. Run the following command to convert the binary file to base64:

```bash
CertUtil -encode release.jks release.jks.b64
```

### Step 6: Create Secrets of the actions

If we now open `release.jks.base64` we should see a normal text file.

Now we can add it as a secret named `RELEASE_KEYSTORE` for our action.

Navigate to settings → Secrets and actions → actions → New Repository Secret

- Create a repository secret named `RELEASE_KEYSTORE` and paste the content of b64 file we generated. Please don’t add first line that mentions **BEGIN CERTIFICATE only add the content of the file.**
- Create second secret named `RELEASE_KEYSTORE_PASSWORD` where you need to add the password you used to create release keystore in previous step.
- Create a third secret named `ACCESS_TOKEN` this is needed to output the files in release.
    - Create a personal access token by using this guide [https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
   
   
  ![actions secret](https://res.cloudinary.com/https-webdrip-in/image/upload/v1684861742/action-capacitor-secrets_s3kn47.png)
