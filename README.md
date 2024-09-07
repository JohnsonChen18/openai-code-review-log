# AI Code Reviewer Quick Start
Note: This repo is the log repo of my AI code reviewer. It dose not contain any source code.
# 1. Apply ChatGLM key
First, you need to apply the key of ChatGLM at https://open.bigmodel.cn/usercenter/apikeys. Keep your key, we'll use it later.

ChatGLM has various models, GLM-4-Flash is the one I used. You can choose it but any model should be fine.  

ChatGLM gives a lot of free Tokens(25 million). That is pretty enough for personal use.  

# 2. Create a Github repo to store review result.
On Github, create a repo to store review result. You cloud name it like "ai-code-review-log-repo". You can make public or private, as you wish.  

Keep the url of the repo, we'll use it later.

# 3. Get Github token
Go to https://github.com/settings/tokens -> click Personal access tokens -> click Token(classic) -> click Generate new token (classic) -> in Select Scopes, make sure you enable "write:packages" -> generate token.
Keek your token, we'll use it later.

# 4. Github Secrets config
Go to the repo that you want to apply ai code reviewer, go to Setting -> Secrets and variables -> Actions -> Repository secrets -> New repository secret
Create following key and value.

| Name               | Secret                                                             |
|--------------------|--------------------------------------------------------------------|
| CHATGLM_APIHOST     | https://open.bigmodel.cn/api/paas/v4/chat/completions              |
| CHATGLM_APIKEYSECRET| your ChatGLM key from step1.        |
| CODE_REVIEW_LOG_URI | Your log repo url from step 2.|
| CODE_TOKEN          | Your token from step 3.               |
| WEBHOOK      | The webhook for message push. If you don't know how to get webhook, for example, if you are using discord, you can just google “how to get webhook of discord channel” |

# 5. Github Action config
Go to the repo that you want to apply ai code reviewer, go to Actions -> set up a workflow yourself -> copy the config below

```yml
name: AI-Code-Review

on:
  push:
    branches:
      - '*'
  pull_request:
    branches:
      - '*'

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2
        with:
          fetch-depth: 2

      - name: Set up JDK 11
        uses: actions/setup-java@v2
        with:
          distribution: 'adopt'
          java-version: '11'

      - name: Create libs directory
        run: mkdir -p ./libs

      - name: Download openai-code-review-sdk JAR
        run: wget -O ./libs/openai-code-review-sdk-1.0.jar https://github.com/JohnsonChen18/openai-code-review-log/releases/download/v1.0/openai-code-review-sdk-1.0.jar

      - name: Get repository name
        id: repo-name
        run: echo "REPO_NAME=${GITHUB_REPOSITORY##*/}" >> $GITHUB_ENV

      - name: Get branch name
        id: branch-name
        run: echo "BRANCH_NAME=${GITHUB_REF#refs/heads/}" >> $GITHUB_ENV

      - name: Get commit author
        id: commit-author
        run: echo "COMMIT_AUTHOR=$(git log -1 --pretty=format:'%an<%ae>' | tr -d ' ')" >> $GITHUB_ENV

      - name: Get commit message
        id: commit-message
        run: echo "COMMIT_MESSAGE=$(git log -1 --pretty=format:'%s')" >> $GITHUB_ENV

      - name: Print repository, branch name, commit author, and commit message
        run: |
          echo "Repository name is ${{ env.REPO_NAME }}"
          echo "Branch name is ${{ env.BRANCH_NAME }}"
          echo "Commit author is ${{ env.COMMIT_AUTHOR }}"
          echo "Commit message is ${{ env.COMMIT_MESSAGE }}"      

      - name: Run Code Review
        run: java -jar ./libs/openai-code-review-sdk-1.0.jar
        env:
          GITHUB_REVIEW_LOG_URI: ${{ secrets.CODE_REVIEW_LOG_URI }}
          GITHUB_TOKEN: ${{ secrets.CODE_TOKEN }}
          COMMIT_PROJECT: ${{ env.REPO_NAME }}
          COMMIT_BRANCH: ${{ env.BRANCH_NAME }}
          COMMIT_AUTHOR: ${{ env.COMMIT_AUTHOR }}
          COMMIT_MESSAGE: ${{ env.COMMIT_MESSAGE }}

          CHATGLM_APIHOST: ${{ secrets.CHATGLM_APIHOST }}
          CHATGLM_APIKEYSECRET: ${{ secrets.CHATGLM_APIKEYSECRET }}

          DISCORD_WEBHOOK: ${{secrets.WEBHOOK}}
```
# 6. Ready to go
Push code to the target repo. After each push, a messeage should be sent through the webhook. The message contains a link directing you to the review result log page.

# 7. Have problem?
   Step1. Check if all variables are named properly (they are case-sensitive)
   
   Step2. Go to ChatGLM to check if they changed API. If so, copy the new one to CHATGLM_APIHOST in step 4.
   
   Step3. Email me (js.chen.swe@gmail.com) with the error message or error screen shot, I will debug asap.
