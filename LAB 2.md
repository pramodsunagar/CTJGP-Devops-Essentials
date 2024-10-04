## Git and GitHub Operations.

### Pre-requisites:
1. Create a **GitHub Account** & **Empty Public Repository** with name as **"hello-world"**

   **Note:** (To SignUp for GitHub Account, [Click Here](https://github.com/signup?ref_cta=Sign+up&ref_loc=header+logged+out&ref_page=%2F&source=header-home))
2. You need to generate a **Personal Access Token** (PAT) in GitHub.
   
   <details>
     <summary>Click Here To Generate the Token:</summary>
     
   * First, Go to your `GitHub homepage,` Click on the top right `profile Icon` and then `settings`
   * Click on `Developer settings` (At the bottom on the left side menu). Click on `Personal Access Token` and then Click on `Generate New Token.`
   * Under '**Select Scopes**' select all items. Click on '**generate token**'.
   * Once generated, **Copy and save the token in a safe location as it is not visible again in GitHub.**
   
      **Example:** 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
   
   </details>

3. Basic familiarity with **Git Commands.**

   Once the `GitHub Account` and `Empty repository` is ready, let's operate in the Jump Server.


### Task 1: Initializing the local git repository and committing changes

On the `Jump Server,` do the below:
```
cd ~
```
Check the GIT version (Git comes pre-installed on certain Linux machines by default). 
```
git --version
```
**(Optional)** If it does not exist, then you can install it using the below command. (Else no need to execute the below line):  
```
sudo apt install git -y
```
Download the **Java Code** that we are going to use in the CICD pipeline.
```bash
git clone https://github.com/sirinali07/DevOpsTraining-HelloWorld.git
```

```
cd DevOpsTraining-HelloWorld/
```
```
git init .
```
To set the `User Identity` ie... `email and user name.` you can execute below commands:

```
git config --global user.email "< Add your email >"
```
```
git config --global user.name "< Add your Name >"
```
```
git status
```
Move the code from `Working Directory` to `Staging Area (Index)`
```
git add .
```
```
git status
```
```
git log
```
Move the code from `Staging Area (Index)` to `Local Repository`
```
git commit -m "This is the first commit"
```
```
git log
```
```
git status
```
Currently our Code is in Local Repository.


### Task 2: Pushing the Code to your Remote GitHub Repository  

1. Create `Alias` as `Origin` to GitHub's Remote repository URL.
```
git remote add origin <Replace your Repository URL> 
```
(**Example:** `git remote add origin https://github.com/janjiralakirankumar/hello-world.git`)

2. To view a specific alias use the below command.
```
git remote show origin
```
3. Now you can push your code from `Local Repository` to Remote Repository using the below command.
```
git push origin master 
```
**Note:** When it asks for a password, enter the **Personal Access Token** and Press Enter

   (When you paste, PAT is invisible and It's the expected behavior.)




