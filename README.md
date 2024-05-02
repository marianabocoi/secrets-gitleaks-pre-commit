# GitLeaks + Pre-commit

This guide helps you explore how to prevent commiting secrets with [GitLeaks](https://github.com/gitleaks/gitleaks?tab=readme-ov-file#pre-commit) and [Pre-commit](https://pre-commit.com/#installation).

🔴 Note: This unfortunately relies on humans setting up their environment correctly. You make sure you enfore it on server side with [pre-receive hooks](https://docs.github.com/en/enterprise-server@3.9/admin/policies/enforcing-policy-with-pre-receive-hooks/about-pre-receive-hooks) on projects. Pre-receive hooks unfortunately are not available in github.com but they can be set in most enterprise git platforms.

## Open this exercise GitPods
You can continue reading the guide in GitPods from here.

[![Start with Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/marianabocoi/secrets-gitleaks-pre-commit)

ℹ️ If it asks you to create an account, log in with GitHub.

## Setup pre-commit
Install the pre-commit hook with:
```
pre-commit install
```

ℹ️ GitPod will ask you for permisiion to copy

## Try to commit a secret
When you try to commit a secret, for example the `bucket_s3.py` file from [GitGuardian sample secrets](https://github.com/GitGuardian/sample_secrets/blob/main/bucket_s3.py), you should see an error and not be able to commit.
```
wget https://github.com/GitGuardian/sample_secrets/blob/da09702283c7c14d2048eddfb448f19ea6a3da08/bucket_s3.py
git add .
git commit -m "these are not the secrets you are looking for"
```

## (Optional) Write a custom rule
We notice it does not detect one of the secrets in [`bucket_s3.py`](https://github.com/GitGuardian/sample_secrets/blob/main/bucket_s3.py), we can add a [custom rule](https://github.com/GitGuardian/sample_secrets/blob/main/bucket_s3.py)

Create a file `.gitleaks.toml` on the top level containing:
```
title = "GitLeaks additional config"

[extend]
# useDefault will extend the base configuration with the default gitleaks config:
# https://github.com/zricethezav/gitleaks/blob/master/config/gitleaks.toml
useDefault = true

[[rules]]
id = "mongo-auth-url"
description = "Detect passwords in mongoDb URLs."
regex = '''mongodb(\+srv)?\:(\/\/)?(\w+)?:(\w+)@'''
keywords = [
    "mongo", "mongoDb", "db",
]

```
You can read more about detecting mongo sectets from [GitGuardian blog](https://blog.gitguardian.com/mongodb-credentials-detector/)
