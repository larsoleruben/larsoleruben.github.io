---
layout: page
title: DevOps
permalink: /diary/devops
---
## Using azure private artifacts
This will stop you pulling your hair out and stop you going insane!!
Somehow the f..... keyring was suppose to do it for you, but intermittently it fails completely.
So azure DevOps asks you for your credentials and you happily type username and password in, only to see error something like this:
```bash
 Keyring is skipped due to an exception: Failed to create get credentials
```
And then you start going crazy.
Fear no more.
The solution is here, because Microsoft was not intelligent enough to tell you that what you are missing is not your normal password, but rather a token you must obtain from [azure devops](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=Windows).
Read this [explanation](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=Windows) and stop eating your pills against hypertension
**You must use the token from here as your password.**
